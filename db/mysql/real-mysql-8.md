

# Real MySQL 8.0



# #1 소개

# #2 설치와 설정

# #3 사용자 및 권한

# #4 아키텍처

# #5 트랜잭션과 잠금

# #6 데이터 압축

- MySQL 서버에서 디스크에 저장된 데이터 파일의 크기는 일반적으로 쿼리의 처리 성능과도 직결되지만 백업 및 복구 시간과도 밀접하게 연결된다.
- 디스크 데이터 파일이 크면 클수록 비용, 속도문제, 성능문제 등으로 많은 DBMS가 이런 문제점들을 해결하기 위해 데이터 압축 기능을 제공한다.



## 페이지 압축

- MySQL 서버에서는 디스크에 저장하는 시점에 데이터 페이지가 압축되어 저장되고, 반대로 MySQL 서버가 디스크에서 데이터 페이지를 읽어올 때 압축이 해제된다.
  - 버퍼 풀에 데이터 페이지가 적재되면 InnoDB 스토리지 엔진은 압축이 해제된 상태로만 데이터 페이지를 관리한다.
  - 이때 문제가 되는게 예를 들어 16kb 데이터 페이지를 압축한 결과가 용량이 얼마나 될지 예측이 불가능하고 하나의 테이블은 동일한 크기의 페이지로 통일돼야 한다는 것
- 그래서 페이지 압축 기능은 운영체제별로 특정 버전의 파일 시스템에서만 지원되는 펀치홀이라는 기능을 사용한다.

`펀치홀 동작방식`

1. 16kb 페이지를 압축 (압축 결과를 7kb로 가정)
2. MySQL 서버는 디스크에 압축된 결과 7kb를 기록 (이때 MySQL 서버는 압축 데이터 7kb에 9kb의 빈 데이터를 기록)
3. 디스크에 데이터를 기록한 후, 7kb 이후의 공간 9kb에 대해 펀치 홀을 생성
4. 파일 시스템은 7kb만 남기고 나머지지 디스크의 9kb공간은 다시 운영체제로 반납

`펀치홀 단점`

- 펀치홀 기능은 운영체제뿐만 아니라 하드웨어 자체에서도 해당 기능을 지원해야 한다.
- 아직 파일 시스템 관련 명령어가 펀치홀을 지원하지 못한다.
  - ex: 펀치홀이 적용되어 1gb로 압축되었다고 하더라도 `cp` 명령어로 복사하면 펀치홀이 다시 채워져 원본 크기인 10gb가 될 수 있다.
- 이런한 이유로 실제 페이지 압축은 많이 사용되지 않는다.



`페이지 압축을 이용하기`

```sql
// 테이블 생성시
mysql> CREATE TABLE t1 (c1 INT) COMPRESSION="zlib"

// 테이블 변경시
mysql> ALTER TABLE t1 COMPRESSION="zlib"
mysql> OPTIMIZE TABLE t1;
```



## 테이블 압축

- 테이블 압축은 운영체제나 하드웨어에 대한 제약이 없기 때문에 일반적으로도 활용도가 높다.
- 하지만 아래와 같은 단점이 있다.

`테이블 압축의 단점`

- 버퍼 풀 공간 활용률이 낮다.
- 쿼리 처리 성능이 낮다.
- 빈번한 데이터 변경시 압축률이 떨어진다.



### 압축 테이블 생성

- 테이블 압축을 사용하기 위한 전제 조건으로 압축을 사용하려는 테이블이 별도의 테이블 스페이스를 사용해야 한다.
  - `innodb_file_per_table` 시스템 변수가 ON으로 설정된 상태에서 테이블이 생성돼야 한다.
- 테이블 압축을 사용하는 테이블은 테이블을 생성할 때 `ROW_FORMAT=COMPRESSED` 옵션을 명시해야 한다.
- `KEY_BLOCK_SIZE`(2n, n 값은 2 이상)을 이용해 압축된 페이지의 타깃 크기를 명시한다.
  - InnoDB 스토리지엔진의 페이지크기가 16kb라면 4kb 또는 8kb만 설정이 가능하다.
- 페이지크기가 32kb 또는 64kb인 경우에는 테이블 압축을 적용할 수 없다.

```sql
mysql> SET GLOBAL innodb_file_per_table=ON;

-- // ROW_FORMAT 옵션과 KEY_BLOCK_SIZE 옵션을 모두 명시
mysql> CREATE TABLE compressed_table (
  c1 INT PRIMARY KEY
)
ROW_FORMAT=COMPRESSED
KEY_BLOCK_SIZE=8;

-- //KEY_BLOCK_SIZE 옵션만 명시
mysql> CREATE TABLE compressed_table (
  c1 INT PRIMARY KEY
)
KEY_BLOCK_SIZE=8;

-- //위와 같은 경우에는 자동으로 ROW_FORMAT=COMPRESSED 옵션이 추가되어 생성된다.
-- //KEY_BLOCK_SIZE의 옵션값은 kb

```



`InnoDB 스토리지 엔진이 압축을 적용하는 방법`

- InnoDB 스토리지 엔진의 데이터 페이지 크기가 16kb, `KEY_BLOCK_SIZE`= 8 이라고 가정
  1. 16kb의 데이터 페이지를 압축
     1. 압축된 결과가 8kb 이하이면 그대로 디스크에 저장 (압축 완료)
     2. 압축된 결과가 8kb를 초과하면 원본 페이지를 스플릿해서 2개의 페이지에 8kb씩 저장

  2. 나뉜 페이지 각각에 대해 1번 단계를 반복 실행  

- 테이블 압축 방식에서 가장 중요한 것은 원본 데이터 페이지의 압축 결과가 목표크기(`KEY_BLOCK_SIZE`) 보다 작거나 같을 때까지 반복해서 페이지를 스플릿하는 것이다.
- 목표 크기가 잘못 설정되면 MySQL 서버의 처리 성능이 급격히 떨어질 수 있음을 주의!



### KEY_BLOCK_SIZE 결정

- `KEY_BLOCK_SIZE` 가 테이블 압축에서 가장 중요하기 때문에 4kb, 8kb로 테이블을 생성해서 샘플데이터를 저장해보고 적절한지 판단하는 것이 좋다.
  - 최소한 테이블의 데이터 페이지가 10개 정도는 되어야 유의미한 테스트가 된다.



`테스트해보기 (KEY_BLOCK_SIZE=4)`

```sql
CREATE TABLE `employees_comp4k` (
  `emp_no` int NOT NULL,
  `birth_date` date NOT NULL,
  `first_name` varchar(14) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
  `last_name` varchar(16) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
  `gender` enum('M','F') CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,
  `hire_date` date NOT NULL,
  PRIMARY KEY (`emp_no`),
  KEY `ix_hiredate` (`hire_date`),
  KEY `ix_gender_birthdate` (`gender`,`birth_date`),
  KEY `ix_firstname` (`first_name`)
) ROW_FORMAT=COMPRESSED
KEY_BLOCK_SIZE=4;

-- // 인덱스별로 압축 실행 횟수와 성공횟수를 기록하기 위한 설정
SET GLOBAL innodb_cmp_per_index_enabled=ON;

-- // employees 테이블의 데이터를 압축 테스트 테이블로 저장
INSERT INTO employees_comp4k SELECT * FROM employees;

-- // 인덱스 별로 압축 횟수와 성공 횟수, 압축 실패율을 조회
SELECT table_name, index_name, compress_ops, compress_ops_ok, (compress_ops-compress_ops_ok)/compress_ops * 100 as compression_failure_pct
FROM information_schema.INNODB_CMP_PER_INDEX;
```



![1](./images/real-mysql-8/1.png)

- PK는 전체 18653번을 압축을 실행하고 그 중에서 13478번 성공했다.
- 5175(18653 - 13478)번 실패했는데 압축의 결과가 4kb를 초과해서 데이터 페이지를 스플릿해서 다시 압축을 실행했다는 의미다.
- PK 압축 실패율 외에도 나머지 인덱스 2개도 압축 실패율이 상대적으로 높은편인데 `KEY_BLOCK_SIZE` 는 압축 실패율을 3~5% 사이로 둘 수 있게 설정하는게 좋다.

`테스트해보기 (KEY_BLOCK_SIZE=8)`

![2](./images/real-mysql-8/2.png)

- `KEY_BLOCK_SIZE` 가 8kb여도 PK 키의 압축 실패율이 높기때문에 이 결과를 기준으로 압축을 적용하면 압축 실패율이 꽤 높아서 InnoDB 버퍼풀에서 디스크로 기록되기 전에 압축하는 과정에서 시간이 꽤 걸릴 수 있다.
- 성능에 민감한 서비스라면 이 테이블은 압축을 적용하지 않는 것이 좋다고 판단할 수 있다.

`압축된 모습`

![3](./images/real-mysql-8/3.png)

- `KEY_BLOCK_SIZE` 가 4kb나 8kb나 압축후 결과가 크게 차이안나므로 이 경우 8kb를 사용하는게 좋다.
- 압축 실패율이 높다고해서 압축을 사용하지 말라는 뜻은 아니다. 스플릿이 일어나더라도 데이터파일의 크기가 큰 폭으로 줄어든다면 큰 손해는 아니다. 하지만 데이터가 매우 빈번하게 조회되고 변경된다면 압축 실패율이 낮더라도 압축을 고려하지 않는게 좋다.
- 테이블 압축은 zlib을 이용해 압축을 실행하는데 이 압축 알고리즘은 많은 cpu 자원을 소모한다.



### 압축된 페이지의 버퍼 풀 적재 및 사용

- InnoDB 스토리지 엔진은 디스크에서 읽은 상태 그대로의 데이터 페이지 목록을 관리하는 LRU 리스트와 압축된 페이지들의 압축 해제 버전인 Unzip_LRU 리스트를 별도로 관리한다.
- MySQL 서버에는 압축된 테이블과 압축되지 않은 테이블이 공존하므로 결국 LRU 리스트는 다음과 같이 두 개의 페이지를 모두 가질 수 있다.
  - 압축이 적용되지 않은 테이블의 데이터 페이지
  - 압축이 적용된 테이블의 압축된 데이터 페이지
- InnoDB 스토리지 엔진은 압축된 테이블에 대해서 버퍼 풀의 공간을 이중으로 사용함으로써 메모리를 낭비하는 효과를 가진다.
- 하지만 압축 및 압축 해제 작업은 CPU를 상대적으로 많이 소모하기 때문에 Unzip_LRU 리스트를 별도로 관리하고 있다가 MySQL 서버로 유입되는 요청 패턴에 따라 적절히 다음과 같은 처리를 수행한다.
  - InnoDB 버퍼 풀의 공간이 필요한 경우엔 LRU 리스트에서 원본 데이터 페이지는 유지하고 Unzip_LRU 리스트에서 압축 해제된 버전은 제거해서 버퍼풀의 공간을 확보한다.
  - 압축된 데이터 페이지가 자주 사용되는 경우에는 Unzip_LRU 리스트에 압축 해제된 페이지를 계속 유지하면서 압축 및 압축 해제 작업을 최소화한다.
  - 압축된 데이터 페이지가 사용되지 않아서 LRU 리스트에서 제거되는 경우에는 Unzip_LRU 리스트에서도 함께 제거된다.



`InnoDB 스토리지 엔진의 어댑티브 알고리즘`

- CPU 사용량이 높은 서버에는 가능하면 압축과 압축 해제를 피하기 위해 Unzip_LRU 비율을 높여서 유지한다.
- Disk IO 사용량이 높은 서버에는 가능하면 Unzip_LRU 리스트의 비율을 낮춰서 InnoDB 버퍼 풀의 공간을 더 확보하도록 작동한다.



### 테이블 압축 관련 설정

- 테이블 압축과 연관된 시스템 변수

`innodb_com_per_index_enabled`

- 테이블 압축이 사용된 테이블의 모든 인덱스별로 압축 성공 및 압축 실행 횟수를 수집하도록 설정한다.
- OFF 일 경우 테이블 단위의 압축 성공 및 압축 실행 횟수만 수집된다.
- 테이블 단위로 수집된 정보는 `infomation_schema.INNODB_CMP`
- 인덱스 단위로 수집된 정보는 `infomation_schema.INNODB_CMP_PER_INDEX`

`innodb_compression_level`

- 이 변수를 사용해서 압축률을 설정할 수 있다.
- 0~9의 값
- 값이 작을수록 압축 속도는 빨라지지만 저장 공간은 커질 수 있다. 반대로 값이 커질수록 속도는 느려질 수 있지만 압축률은 높아진다.
- 기본값은 6
- 압축속도가 빨라진다 = CPU 자원을 적게 사용한다.

`innodb_compression_failure_threshold_pct와 innodb_compression_pad_pct_max`

- 테이블 단위로 압축 실패율이 `innodb_compression_failure_threshold_pct` 보다 커지면 압축을 실행하기 전 원본 데이터 페이지의 끝에 의도적으로 빈 패딩 공간을 추가한다.
- 추가된 빈 공간은 압축률을 높여서 압축 결과가 `KEY_BLOCK_SIZE` 보다 작아지게 만드는 효과를 낸다.
- 이 패딩 공간은 압축 실패율이 높아질수록 계속 증가된 크기를 갖는데 추가할 수 있는 패딩공간의 최대 공간을 `innodb_compression_pad_pct_max` 으로 설정할 수 있다. 
- `innodb_compression_pad_pct_max` 는 % 값이고 전체 데이터 페이지 크기 대비 패딩 공간의 비율을 의미한다.

`innodb_log_compressed_pages`

- MySQL 서버가 비정상적으로 종료됐다가 다시 시작되는 경우 압축 알고리즘의 버전 차이가 있더라도 복구 과정이 실패하지 않도록 InnoDB 스토리지 엔진은 압축된 데이터 페이지를 그대로 리두 로그에 기록한다. 
- 이는 압축 알고리즘을 업그레이드할 때 도움이 되지만, 데이터 페이지를 통째로 리두 로그에 저장하는 것은 리두 로그 증가량에 상당한 영향력을 미칠 수 있다.
- 압축을 적용한 후 리두 로그 용량이 매우 빠르게 증가한다거나 버퍼 풀로부터 더티 페이지가 한꺼번에 많이 기록되는 패턴으로 바뀌었다면 `innodb_log_compressed_pages` 을 OFF로 바꾼뒤 모니터링해보는 것이 좋다.
- 기본값은 ON, ON으로 유지시키는것을 권장한다.







# #7 데이터 암호화



# #8 인덱스



# #9 옵티마이저와 힌트



# #10 실행 계획

- 대부분의 DBMS의 목적은 사용자의 데이터를 안전하게 저장 및 관리하고 원하는 데이터를 빠르게 조회할 수 있게 해주는 것이 주 목적이다.
- 이러한 목적을 달성하려면 옵티아미어가 사용자의 쿼리를 최적으로 처리할 수 있게 하는 실행 계획을 수립할 수 있어야 하는데 옵티마이저는 사용자, 관리자의 개입 없이 항상 좋은 실행 계획을 만들어내는 것은 아니다.
- DBMS 서버는 이러한 문제점을 관리자나 사용자가 보완할 수 있도록 `EXPLAIN` 명령으로 옵티마이저가 수립한 실행 계획을 확인할 수 있게 해준다.



## 통계 정보

- MySQL 서버는 5.7 버전까지 테이블과 인덱스에 대한 개괄적인 정보를 가지고 실행 계획을 수집했지만 이 방법은 테이블 칼럼의 값들이 실제 어떻게 분포돼 있는지에 대한 정보가 없기 때문에 실행 계획의 정확도가 떨어지는 경우가 많았다.
- MySQL 8.0 부터는 데이터 분포도를 수집해서 저장하는 히스토그램 정보가 도입됐다.



### 테이블 및 인덱스 통계 정보

- 비용 기반 최적화에서 가장 중요한 것은 통계 정보다.
  - 1억건의 레코드가 저장된 테이블의 통계 정보가 갱신되지 않아 10만건으로 알고 있다면 옵티마이저는 전혀 엉뚱한 실행계획으로 쿼리를 수행할 수 있다.
- MySQL은 다른 DBMS 보다 통계 정보의 정확도가 높지 않고 통계 정보의 휘발성이 강했다.
- MySQL 5.6부터는 통계 정보의 정확성을 높일 수 있는 방법이 제공되기 시작했지만 아직도 많은 사용자가 기존 방식을 그대로 사용한다.



### MySQL 서버의 통계 정보

- MySQL 5.5 버전까지는 각 테이블의 통계 정보가 메모리에만 관리되었고 MySQL 서버가 재시작되면 지금까지 수집한 통계 정보가 모두 사라진다. 그 외에도 아래와 같은 경우에 통계정보가 관리자, 사용자 모르게 갱신됐다
  - 테이블이 새로 오픈된 경우
  - 테이블의 레코드가 대량으로 변경되는 경우 (테이블의 전체 레코드 중에서 1/16 정도의 `UPDATE` 또는 `INSERT`나 `DELETE`가 실행되는 경우)
  - `ANALYZE TABLE` 명령이 실행되는 경우
  - `SHOW TABLE STATUS` 명령이나 `SHOW INDEX FROM` 명령이 실행되는 경우
  - InnoDB 모니터가 활성화되는 경우
  - `innodb_stats_on_metadata` 시스템 설정이 ON 인 상태에서 `SHOW TABLE STATUS` 명령이 실행되는 경우
- MySQL 5.6 버전 부터는 InnoDB 스토리지 엔진을 사용하는 테이블에 대한 통계 정보를 영구적으로 관리할 수 있게  `mysql` 데이터베이스의 `innodb_index_stats` 테이블과 `innodb_table_stats` 테이블로 관리할 수 있게 개선됐다.

![4](./images/real-mysql-8/4.png)

- MySQL 5.6에서 테이블 생성시 `STATS_PERSISTENT` 옵션을 설정할 수 있는데 이 설정값으로 테이블 단위 영구적인 통계 정보를 보관할지 말지를 결정할 수 있다.

`STATS_PERSISTENT=0` 

- 테이블의 통계 정보를 MySQL 5.5 이전 방식대로 관리한다. (메모리로만 관리)

`STATS_PERSISTENT=1`

- 테이블의 통계 정보를 `innodb_index_stats` 테이블과 `innodb_table_stats` 테이블로 관리한다.

`STATS_PERSISTENT=DEFAULT`

- `STATS_PERSISTENT` 을 지정하지 않았을때와 동일하며 `innodb_stats_persistent` 시스템 변수의 값을 통해 설정한다.
- `innodb_stats_persistent` 의 기본 설정값은 ON(1) 이다.

```SQL
CREATE TABLE tab_persistent (fd1 INT, fd2 VARCHAR(20), PRIMARY KEY(fd1)) 
ENGINE=InnoDB STATS_PERSISTENT=1;

CREATE TABLE tab_transient (fd1 INT, fd2 VARCHAR(20), PRIMARY KEY(fd1)) 
ENGINE=InnoDB STATS_PERSISTENT=0;
```



![5](./images/real-mysql-8/5.png)

![6](./images/real-mysql-8/6.png)



- 아래 명령어를 통해 이미 생성된 테이블의 설정을 변경할 수 있다.

```SQL
ALTER TABLE tab_transient STATS_PERSISTENT=1;
```

![7](./images/real-mysql-8/7.png)

![8](./images/real-mysql-8/8.png)

- 통계정보의 각 칼럼

  - `innodb_index_stats.stat_name='n_diff_pfx%'`

    - 인덱스가 가진 유니크한 값의 개수

  - `innodb_index_stats.stat_name='n_leaf_pages'`

    - 인덱스의 리프 노드 페이지 개수

  - `innodb_index_stats.stat_name='size'`

    - 인덱스 트리의 전체 페이지 개수

  - `innodb_index_stats.n_rows`

    - 테이블의 전체 레코드 건수

  - `innodb_index_stats.clustered_index_size`

    - 프라이머리 키의 크기 (innoDB 페이지 개수)

  - `innodb_index_stats.sum_of_other_index_size`

    - 프라이머리 키를 제외한 인덱스의 크기 (innoDB 페이지 개수)

    - 이 값은 테이블의 `STATS_AUTO_RECALC` 옵션에 따라 0으로 보일 수 있는데 아래 명령어를 실행하면 통계값이 저장된다

    - ```sql
      ANALYZE TABLE employees.employees;
      ```





- MySQL 5.6 에서 영구적인 통계 정보가 도입되면서 `innodb_stats_auto_recalc` 시스템 설졍 변수의 값을 통해 통계 정보가 자동으로 갱신되는 것을 막을 수 있다.
  - OFF로 설정하면 영구적인 통계 정보를 이용한다는 뜻. 기본값은 ON
- 통계 정보를 자동으로 수집할지 여부도 테이블을 생성할때 `STATS_AUTO_RECALC` 옵션을 이용해 테이블 단위로 조정할 수 있다.

`STATS_AUTO_RECALC=1`

- 테이블의 통계 정보를 MySQL 5.5 이전의 방식대로 수집한다.

`STATS_AUTO_RECALC=0`

- 테이블의 통계 정보는 ANALYZE TABLE 명령을 실행할 때만 수집된다.

`STATS_AUTO_RECALC=DEFAULT`

- 별도로 `STATS_AUTO_RECALC` 을 지정하지 않았을 때와 동일하며 `innodb_stats_auto_recalc` 시스템 변수의 값으로 설정한다.

  

- MySQL 5.5 버전에서 테이블의 통계 정보를 수집할 때 몇 개의 InnoDB 테이블 블록을 샘플링할지 결정하는 innodb_stats_sample_pages 시스템 변수는 5.6 버전부터 없어졌다.(Deprecated) 대신 이 시스템 변수는 아래 2개로 분리 되었다.

`innodb_stats_transient_sample_pages`

- 기본값은 8
- 자동으로 통계 정보 수집이 실행될 때 8개 페이지만 임의로 샘플링해서 분석하고 그 결과를 통계 정보로 활용함을 의미한다.

`innodb_stats_persistent_sample_pages`

- 기본값은 20
- `ANALYZE TABLE` 명령이 실행되면 임의로 20개 페이지만 샘플링해서 분석하고 그 결과를 영구적인 통계 정보 테이블에 저정하고 활용함을 의미한다.
- 더 정확한 통계정보를 수집하고 싶다면 이 값에 높은 값을 설정하면 되지만 값이 너무 높을 경우 정보 수집 시간이 길어지므로 주의해야 한다.



### 히스토그램

- MySQL 5.7 버전까지의 통계 정보는 단순히 인덱스된 칼럼의 유니크한 값의 개수정도만 가지고 있었는데 이는 옵티마이저가 최적의 실행 계획을 수립하기에는 많이 부족했다.
- MySQL 8.0 버전부터 히스토그램을 도임해서 칼럼의 데이터 분포도를 참조할 수 있게 됐다.



#### 히스토그램 정보 수집 및 삭제

- MySQL 8.0 부터 히스토그램 정보는 칼럼 단위로 관리된다.
- 자동으로 수집되지 않고 `ANALYZE TABLE ... UPDATE HISTOGRAM` 명령을 실행해 수동으로 수집 및 관리한다.
- 수집된 히스토그램 정보는 시스템 딕셔너리에 함께 저장되고, MySQL 서버가 시작될 때 딕셔너리의 히스토그램 정보를 `information_schema` 데이터베이스의 `column_statistics` 테이블로 로드한다.



```sql
ANALYZE TABLE employees.employees UPDATE HISTOGRAM ON gender, hire_date;
```

```sql
SELECT *
FROM information_schema.COLUMN_STATISTICS
WHERE SCHEMA_NAME='employees'
	AND TABLE_NAME='employees'\G
```



![9](./images/real-mysql-8/9.png)



- MySQL 버전에서는 아래와 같은 2종류의 히스토리그램 타입이 지원된다.

`Singleton(싱글톤 히스토그램)`

- 칼럼값 개별로 레코드 건수를 관리하는 히스토그램
- Value-Based 히스토그램 또는 도수 분포라고 불린다.

`Equi-Height(높이 균형 히스토그램)`

- 칼럼값의 범위를 균등한 개수로 구분해서 관리하는 히스토그램
- Height-Balanced 히스토그램이라고도 불린다.

  

- 히스토그램은 버킷 단위로 구분되어 레코드 건수나 칼럼값의 범위가 관리된다
- 싱글톤 히스토그램은 칼럼이 가지는 값별로 버킷이 할당되고 높이 균형 히스토그램에서는 개수가 균등한 칼럼 값의 범위별로 하나의 버킷이 할당된다.
- 싱글톤 히스토그램은 각 버킷이 칼럼의 값, 발생 빈도의 비율. 2개의 값을 갖는다.
- 높이 균형 히스토그램은 각 버킷 범위 시작값, 마지막 값, 발생 빈도율, 버킷에 포함된 유니크한 값의 개수. 4개의 값을 갖는다.



`employees 테이블의 gender 칼럼 히스토그램 데이터 (싱글톤)`



![10](./images/real-mysql-8/10.jpeg)



- 싱글톤 히스토그램은 ENUM('M', 'F') 타입인 gender 칼럼이 가질 수 있는 2개의 값에 대해 누적된 레코드 건수의 비율을 갖고 있다.
- 싱글톤 히스토글매은 주로 코드 값과 같이 유니크한 값의 개수가 상대적으로 적은(히스토그램의 버킷 수 보다 적은) 경우 사용된다. 
- `gender` 칼럼 값이 'M' 인 레코드의 비율은 0.5998 정도이고 'F'인 레코드의 비율은 1로 표시된다.
- 히스토그램의 모든 레코드 건수 비율은 누적으로 표시되기 때문에 gnder 칼럼의 값이 'F'인 레코드의 비율은 (1 - 0.5998) 이다.



`employees 테이블의 hire_date 칼럼 히스토그램 데이터 (높이 균형)`

![11](./images/real-mysql-8/11.jpeg)



- 높이 균형 히스토그램은 컬럼값의 각 범위에 대해 레코드 건수 비율이 누적으로 표시된다.
- 버킷 범위가 뒤로 갈수록 비율이 높아지는 것으로 보이지만 사실 범위별로 비율이 같은 수준에서 `hire_date` 칼럼의 범위가 선택된 것이다.
- 위 그래프의 경우 기울기가 일정한 것을 보면 각 범위가 비슷한 값을 가진다는 것을 알 수 있다.



`information_schema.column_statistics 테이블의 HISTOGRAM 칼럼이 가진 나머지 필드들`

- `sampling-rate`
  - 히스토그램 정보를 수집하기 위해 스캔한 페이지의 비율
  - 샘플링 비율이 0.35라면 전체 데이터 페이지의 35%를 스캔해서 이 정보가 수집됐다는 것을 의미
  - 샘플링 비율과 정확도는 비례하지만 테이블을 전부 스캔하는 것은 부하가 높고 시스템의 자원을 많이 소모한다.
  - `histogram_generation_max_mem_size` 시스템 변수에 설정된 메모리 크기에 맞게 적절히 샘플링한다. (기본값 20mb)
  - MySQL 8.0.19 미만의 버전까지는 `histogram_generation_max_mem_size` 변수와 상관 없이 풀스캔으로 히스토그램을 생성했다. 8.0.19 미만이라면 히스토그램 수집시 주의할 것!
- `histogram-type`
  - 히스토그램의 종류를 저장한다.
- `number-of-buckets-specified`
  - 히스토그램을 생성할 때 설정했던 버킷의 개수
  - 기본값은 100, 최대 1024개를 설정할 수 있지만 일반적으로 100개면 충분한 것으로 알려져 있다.



`히스토그램 삭제`

- 히스토그램의 삭제 작업은 테이블의 데이터를 참조하는 것이 아니라 딕셔너리의 내용만 삭제하기 때문에 쿼리 처리의 성능에 영향을 주지 않고 즉시 완료된다.
- 하지만 히스토그램이 사라지면 실행계획이 달라질 수 있으므로 주의하자.

```sql
ANALYZE TABLE employees.employees
DROP HISTOGRAM ON gender, hire_date;
```

- 히스토그램을 삭제하지 않고 MySQL 옵티마이저가 히스토그램을 사용하지 않게 하려면 다음과 같이 `optimizer_switch` 시스템 변수의 값을 변경하면 된다.

```sql
SET GLOBAL optimizer_switch='condition_fanout_filter=off'
```

- `condition_fanout_filter` 옵션에 의해 영향받는 다른 최적화 기능들도 사용되지 않을 수 있으니 주의하자!
- 특정 커넥션, 특정 쿼리에서만 히스토그램을 사용하지 않고자 한다면 다음과 같은 방법을 사용하면 된다.

```sql

-- //현재 커넥션에서 실행되는 쿼리만 히스토그램을 사용하지 않게 설정
SET SESSION optimizer_witch='condition_fanout_filter=off';

-- //현재 쿼리만 히스토그램을 사용하지 않게 설정
SELECT /*+ SET_VAR(condition_fanout_filter='condition_fanout_filter=off')*/ *
FROM ...
```



#### 히스토그램의 용도

- MySQL 서버에 히스토그램이 도입되기 이전에도 테이블과 인덱스에 대한 통계 정보는 존재했지만 이는 테이블의 전체 레코드 건수와 인덱스된 칼럼이 가지는 유니크한 값의 개수 정도였다
  - 예를 들어 테이블의 레코드가 1000건이고 어떤 칼럼의 유니크한 값의 개수가 100개였다면 MySQL 서버는 이 칼럼에 대한 동등 비교 검색을 하면 대략 10개의 레코드가 일치할 것이라고 예측한다.
  - 하지만 실제 응용 프로그램에서의 데이터는 항상 균등한 분포도를 갖지 않는다.
  - 어떤 사용자는 주문 레코드를 많이 갖고 있고 또 다른 사용자들은 주문 정보가 하나도 없을 수 있다.
- 히스토그램은 특정 칼럼이 가지는 모든 값에 대한 분포도 정보를 가지지는 않지만 각 범위별로 레코드의 건수와 유니크한 값의 개수 정보를 가지기 때문에 훨씬 정확한 예측을 할 수 있다.



`히스토그램을 사용할때와 사용하지 않을 때 차이`



- 히스토그램을 사용하지 않을때

```sql
EXPLAIN
SELECT *
FROM employees
WHERE first_name='Zita'
	AND birth_date BETWEEN '1950-01-01' AND '1960-01-01';
```



![12](./images/real-mysql-8/12.png)



- 히스토그램을 사용할때

```sql
ANALYZE TABLE employees
UPDATE histogram ON first_name, birth_date;

EXPLAIN
SELECT *
FROM employees
WHERE first_name='Zita'
	AND birth_date BETWEEN '1950-01-01' AND '1960-01-01';

```



![13](./images/real-mysql-8/13.png)



- 히스토그램이 없을때는 11.11%의 birth_date가 1950년대일 것으로 추측했지만 히스토그램을 사용했을때는 63.30%이 1950년대 출생인 것을 알 수 있다.
- 단순 통계 정보만 이용한 경우와 히스토그램을 이용한 경우의 차이가 매우 큰 것을 알 수 있다.
- **히스토그램이 없으면 옵티마이저는 데이터가 균등하게 분포돼 있을 겻으로 예측한다.**
- **히스토그램이 있으면 특정 범위의 데이터가 많고 적음을 식별할 수 있다. 이는 쿼리의 성능에 상당한 영향을 미칠 수 있다.**



#### 히스토그램과 인덱스

- 히스토그램과 인덱스는 완전히 다른 객체이기 때문에 서로 비교할 대상은 아니지만 부족한 통계 정보를 수집하기 위해 사용된다는 측면에서 어느정도 공통점을 가진다고 볼 수 있다.
- MySQL 서버에서는 쿼리의 실행 계획을 수립할 때 사용 가능한 인덱스들로부터 조건절에 일치한다 레코드 건수를 대략 파악하고 최종적으로 가장 나은 실행계획을 선택한다.
- 이때 조건절에 일치하는 레코드 건수를 예측하기 위해 옵티마이저는 실제 인덱스의 B-Tree를 샘플링해서 살펴본다 (인덱스 다이브 Index Dive)
- MySQL 8.0 서버에서는 인덱스된 칼럼을 검색 조건으로 사용하는 경우 그 칼럼의 히스토그램은 사용하지 않고 실제 인덱스 다이브를 통해 직접 수집한 정보를 활용한다.
- 이는 실제 검색 조건의 대상 값에 대한 샘플링을 실행하는 것이므로 항상 히스토그램보다 정확한 결과를 기대할 수 있기 때문이다.
- **MySQL 8.0 에서 히스토그램은 주로 인덱스되지 않은 컬럼에 대한 데이터 분포도를 참조하는 용도로 사용된다.** (IN 절과 같이 인덱스 다이브 비용이 높은 경우 히스토그램을 활용하는 최적화 기능이 MySQL 서버에 추가될 가능성이 있지 않을까? 라는 저자의 추정..)







