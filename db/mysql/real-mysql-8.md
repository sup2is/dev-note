

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



### 코스트 모델 (Cost Model)

- MySQL 서버가 쿼리를 처리하려면 다음과 같은 다양한 작업을 필요로 한다.
  - 디스크로부터 데이터 페이지 읽기
  - 메모리(InnoDB 버퍼 풀)로부터 데이터 페이지 읽기
  - 인덱스 키 비교
  - 레코드 평가
  - 메모리 임시 테이블 작업
  - 디스크 임시 테이블 작업
- MySQL 서버는 사용자의 쿼리에 대해 이러한 다양한 작업이 얼마나 필요한지 예측하고 전체 작업 비용을 계산한 결과를 바탕으로 최적의 실행 계획을 찾는다.
- 이렇게 전체 쿼리의 비용을 계산하는데 필요한 단위 작업들의 비용을 코스트 모델이라고 한다.
- MySQL 5.7 이전 버전까지는 이런 작업들의 비용을 MySQL 서버 소스 코드에 상수화해서 사용했지만 이 작업들의 비용이 하드웨어에 따라 달라질 수 있어서 최적의 실행 계획 수립에 있어서는 방해 요소였다.
- MySQL 5.7 버전부터는 각 단위의 작업 비용을 DBMS 관리자가 조정할 수 있게 됐지만 인덱스되지 않은 칼럼의 데이터 분포(히스토그램)나 메모리에 상주중인 페이지 비율 등 비용 계산과 연관된 부분의 정보가 부족했다.
- MySQL 8.0 부터 히스토그램과 각 인덱스별 메모리 적재 페이지 비율이 관리되고 옵티마이저의 실행 계획 수립에 사용되기 시작했다.

  

- MySQL 8.09 서버의 코스트 모델은 다음 2개의 테이블에 저장되어 있다. (mysql db)
  - `server_cost`
    - 인덱스를 찾고 레코드를 비교하고 임시테이블 처리에 대한 비용 관리
  - `engine_cost`
    - 레코드를 가진 데이터 페이지를 가져오는 데 필요한 비용 관리



`server_cost와 engine_cost의 공통 칼럼`

- `cost_name`
  - 코스트 모델의 각 단위 작업
- `default_value`
  - 각 단위 작업의 비용(기본값이며 이 값은 MySQL 서버 소스코드에 설정된 값)
- `cost_value`
  - DBMS 관리자가 설정한 값(이 값이 NULL이면 MySQL 서버는 `default_value` 칼럼의 비용 사용)
- `last_updated`
  - 단위 작업의 비용이 변경된 시점(단순 정보성. 옵티마이저와 연관 없음)
- `comment`
  - 비용에 대한 추가 설명(단순 정보성. 옵티마이저와 연관 없음)



`engine_cost의 추가 2개 칼럼`

- `engine_name`
  - 비용이 적용된 스토리지 엔진
  - 기본값은 default. default 인 경우 모든 스토리지 엔진에 적용된다.
  - MEMORY, MyISAM, InnoDB 에 대해 단위 작업의 비용일 달리 설정하고자 한다면 이 칼럼을 이용하면 된다.
- `device_type`
  - 디스크 타입
  - MySQL 8.0 기준으로 아직 이 칼럼의 값을 활용하지 않는다. 따라서 0만 설정할 수 있다.



`MySQL 8.0 버전의 코스트 모델에서 지원하는 단위 작업 목록`

| cost_name       | cost_name                    | default_value | 설명                             |
| --------------- | ---------------------------- | ------------- | -------------------------------- |
| **engine_cost** | io_block_read_cost           | 1.00          | 디스크 데이터 페이지 읽기        |
|                 | memory_block_read_cost       | 0.25          | 메모리 데이터 페이지 읽기        |
| **server_cost** | disk_temptable_create_cost   | 20.00         | 디스크 임시 테이블 생성          |
|                 | disk_temptable_row_cost      | 0.50          | 디스크 임시 테이블의 레코드 읽기 |
|                 | key_compare_cost             | 0.05          | 인덱스 키 비교                   |
|                 | memory_temptable_create_cost | 1.00          | 메모리 임시 테이블 생성          |
|                 | memory_template_row_cost     | 0.10          | 메모리 임시 테이블의 레코드 읽기 |
|                 | row_evaluate_cost            | 0.10          | 레코드 비교                      |

- `row_evaluate_cost`는 스토리지 엔진이 반환한 레코드가 쿼리의 조건에 일치하는지 평가하는 단위 작업. 이 값이 증가할수록 풀 테이블 스캔과 같이 많은 레코드를 처리하는 쿼리의 비용이 높아지고 레인지 스캔과 같이 상대적으로 적은 수의 레코드를 처리하는 쿼리의 비용이 낮아진다.
- `key_compare_cost` 값이 높아질수록 레코드 정렬과 같이 키 값 비교 처리가 많은 경우 쿼리의 비용이 높아진다.



`MySQL 서버에서 각 실행 계획의 계산된 비용을 확인하는 방법`

```sql
EXPLAIN FORMAT=TREE
SELECT *
FROM employees WHERE first_name='Matt' \G

```



```sql
EXPLAIN FORMAT=JSON
SELECT *
FROM employees WHERE first_name='Matt' \G

```

- MySQL 서버의 실행 계획에 표시되는 비용을 직접 계산해보고 싶을 수 있지만 이는 상당히 어렵다.
- 코스트 모델에서 중요한 것은 각 단위 작업에 설정되는 비용 값이 커지면 어떤 실행 계획들이 고비용으로 바뀌고 어떤 실행 계획들이 저비용으로 바뀌는지를 파악하는 것이다.
- 이 값들을 사용자가 변경할 수 있다고 해서 꼭 바꿔서 사용하라는 뜻은 아니다. 하드웨어와 MySQL 서버 내부처리 방식에 대한 깊은 이해도가 있을 경우에만 변경하자!



`각 단위 작업의 비용이 변경되면 예상할 수 있는 결과 목록 (언제까지나 참고 사항. 기본값들도 잘 된다)`

- `key_compare_cost` 을 높이면 MySQL 서버 옵티마이저가 가능하면 정렬을 수행하지 않는 방향의 실행 계획을 선택할 가능성이 높아진다.
- `row_evaluate_cost`을 높이면 풀 스캔을 실행하는 쿼리들의 비용이 높아지고, MySQL 서버 옵티마이저는 가능하면 인덱스 레인지 스캔을 사용하는 실행 계획을 선택할 가능성이 높아진다.
- `disk_temptable_create_cost`, `disk_temptable_row_cost` 을 높이면 MySQL 서버 옵티마이저는 디스크에 임시 테이블을 만들지 않는 방향의 실행 계획을 선택할 가능성이 높아진다.
- `memory_temptable_create_cost`, `memory_template_row_cost` 을 높이면 MySQL 서버 옵티마이저는 메모리 임시 테이블을 만들지 않는 방향의 실행 계획을 선택할 가능성이 높아진다.
- `io_block_read_cost` 비용이 높아지면 MySQL 서버 옵티마이저는 가능하면 InnoDB 버퍼 풀에 데이터 페이지가 많이 적재돼 있는 인덱스를 사용하는 실행 계획을 선택할 가능성이 높아진다.
- `memory_block_read_cost` 비용이 높아지면 MySQL 서버는 InnoDB 버퍼 풀에 적재된 데이터 페이지가 상대적으로 적다고 하더라도 그 인덱스를 사용할 가능성이 높아진다.

  



## 실행 계획 확인

- MySQL 서버의 실행 계획은 `DESC` 또는 `EXPLAIN` 명령으로 확인할 수 있다.
- MySQL 8.0 부터 `EXPLAIN` 에 새로운 옵션이 추가되었다.



### 실행 계획 출력 포맷

- 이전 버전에서 `EXPLAIN EXTENDED` 또는 `EXPLAIN PARTITIONS` 명령이 구분돼 있었지만 MySQL 8.0 부터는 모든 내용이 통합, 개선되면서 이 옵션들은 문법에서 제거됐다.
- MySQL 8.0 버전부터는 `FORMAT` 옵션을 사용해 실행 계획의 표시 방법을 JSON, TREE, 단순 테이블 형태로 선택할 수 있다.



```sql
EXPLAIN
SELECT *
FROM employees e
	INNER JOIN salaries s ON s.emp_no=e.emp_no
WHERE first_name='ABC'\G

*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: e
   partitions: NULL
         type: ref
possible_keys: PRIMARY,ix_firstname
          key: ix_firstname
      key_len: 58
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
*************************** 2. row ***************************
           id: 1
  select_type: SIMPLE
        table: s
   partitions: NULL
         type: ref
possible_keys: PRIMARY
          key: PRIMARY
      key_len: 4
          ref: employees.e.emp_no
         rows: 9
     filtered: 100.00
        Extra: NULL
```





```sql
EXPLAIN FORMAT=TREE
SELECT *
FROM employees e
	INNER JOIN salaries s ON s.emp_no=e.emp_no
WHERE first_name='ABC'\G

*************************** 1. row ***************************
EXPLAIN: -> Nested loop inner join  (cost=2.71 rows=9)
    -> Index lookup on e using ix_firstname (first_name='ABC')  (cost=0.76 rows=1)
    -> Index lookup on s using PRIMARY (emp_no=e.emp_no)  (cost=1.95 rows=9)

1 row in set (0.00 sec)

```





```sql
EXPLAIN FORMAT=JSON
SELECT *
FROM employees e
	INNER JOIN salaries s ON s.emp_no=e.emp_no
WHERE first_name='ABC'\G



*************************** 1. row ***************************
EXPLAIN: {
  "query_block": {
    "select_id": 1,
    "cost_info": {
      "query_cost": "2.71"
    },
    "nested_loop": [
      {
        "table": {
          "table_name": "e",
          "access_type": "ref",
          "possible_keys": [
            "PRIMARY",
            "ix_firstname"
          ],
          "key": "ix_firstname",
          "used_key_parts": [
            "first_name"
          ],
          "key_length": "58",
          "ref": [
            "const"
          ],
          "rows_examined_per_scan": 1,
          "rows_produced_per_join": 1,
          "filtered": "100.00",
          "cost_info": {
            "read_cost": "0.66",
            "eval_cost": "0.10",
            "prefix_cost": "0.76",
            "data_read_per_join": "136"
          },
          "used_columns": [
            "emp_no",
            "birth_date",
            "first_name",
            "last_name",
            "gender",
            "hire_date"
          ]
        }
      },
      {
        "table": {
          "table_name": "s",
          "access_type": "ref",
          "possible_keys": [
            "PRIMARY"
          ],
          "key": "PRIMARY",
          "used_key_parts": [
            "emp_no"
          ],
          "key_length": "4",
          "ref": [
            "employees.e.emp_no"
          ],
          "rows_examined_per_scan": 9,
          "rows_produced_per_join": 9,
          "filtered": "100.00",
          "cost_info": {
            "read_cost": "1.01",
            "eval_cost": "0.94",
            "prefix_cost": "2.71",
            "data_read_per_join": "150"
          },
          "used_columns": [
            "emp_no",
            "salary",
            "from_date",
            "to_date"
          ]
        }
      }
    ]
  }
}

1 row in set (0.00 sec)


```



> \G 옵션을 사용하면 쿼리 결과를 수직으로 볼 수 있어서 더 쉽게 확인할 수 있다.



### 쿼리의 실행 시간 확인

- MySQL 8.0.18 버전부터 쿼리의 실행 계획과 단계별 소요 시간 정보를 확인할 수 있는 `EXPLAIN ANALYZE` 기능이 추가됐다.
- `SHOW PROFILE` 명령으로 어떤 부분에서 시간이 많이 소요됐는지 확인할 수 있지만 실행 계획의 단계별로 소요된 시간 정보를 보여주진 않는다.
- `EXPLAIN ANALYZE` 명령은 항상 TREE 명령으로 보여주기 때문에 `FORMAT` 옵션을 사용할 수 없다.

```sql
EXPLAIN ANALYZE
SELECT e.emp_no, avg(s.salary)
FROM employees e
	INNER JOIN salaries s ON s.emp_no=e.emp_no
		AND s.salary>50000
		AND s.from_date<='1990-01-01'
		AND s.to_date>'1990-01-01'
WHERE e.first_name='Matt'
GROUP BY e.hire_date \G
```



> 위 쿼리를 실행하다가 아래 에러를 만났는데
>
> Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'employees.e.emp_no' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
>
> 5.7 버전부터 `sql_mode` 가 생겼고 GROUP BY 절에 집계되지 않은 열을 참조하는 쿼리를 거부하는 모드인 것 같다.
>
> 해결 방법은 모든 컬럼을 GROUP BY 또는 집계함수에 포함해서 SELECT 하는 방법이고 다른 방법은 `only_full_group_by` 를 꺼주는 방법이다.
>
> ```sql
> mysql> set global sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';
> 
> mysql> set session sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';
> ```



![14](./images/real-mysql-8/14.png)



- 들여쓰기가 같은 레벨에서는 상단에 위치한 라인이 먼저 실행된다.
- 들여쓰기가 다른 레벨에서는 가장 안쪽에 위치한 라인이 먼저 실행된다.

`실행 순서`

1. D) Index lookup on e using ix_firstname 
2. F) Index lookup on s using PRIMARY
3. E) Filter: ((s.salary > 50000) and (s.from_date <= DATE'1990-01-01') and (s.to_date > DATE'1990-01-01'))
4. C) Nested loop inner join
5. B) Aggregate using temporary table
6. A) Table scan on \<temporary\>

> D 랑 F랑 순서가 바뀐게 아닐까..? 하는 생각이 듦..

1. `employees` 테이블의 `ix_firstname` 인덱스를 통해 `first_name='Matt'` 조건에 일치하는 레코드를 찾고
2. `salaries` 테이블의 PRIMARY 키를 통해 `emp_no`가 (1)번 결과의 `emp_no`와 동일한 레코드를 찾아서
3. `((s.salary > 50000) and (s.from_date <= DATE'1990-01-01') and (s.to_date > DATE'1990-01-01'))` 조건에 일치하는 건만 가져와
4. 1번과 3번의 결과를 조인해서
5. 임시 테이블에 결과를 저장하면서 `GROUP BY` 집계를 실행하고
6. 임시테이블의 결과를 읽어서 결과를 반환한다.



`실행계획 F) 라인에 나열된 필드들의 의미`

- `actual time=0.281..0.289`
  - 테이블에서 읽은 `emp_no` 값을 기준으로 `salaries` 테이블에서 일치하는 레코드를 검색하는데 걸린 평균 시간
- `rows=10`
  - `employees` 테이블에서 읽은 `emp_no`에 일치하는 `salaries` 테이블의 평균 레코드 건수
- `loops=233`
  - `employees` 테이블에서 읽은 `emp_no`를 이용해 `salaries` 테이블의 레코드를 찾는 작업이 반복된 횟수

  

- `EXPLAIN ANALYZE` 명령은 `EXPLAIN` 명령과 달리 실행 계획만 추출하는 것이 아니라 실제 쿼리를 실행하고 사용된 실행 계획과 소요된 시간을 보여주기 때문에 쿼리 실행시간이 오래걸릴수록 확인도 늦어진다.
- 쿼리의 실행 계획이 아주 나쁜 경우라면 `EXPLAIN ANALYZE` 이전에 `EXPLAIN` 으로 튜닝 후 사용하는게 좋다.



## 실행 계획 분석

### id 칼럼

- 실행 계획에서 가장 왼쪽에 표시되는 `id` 칼럼은 단위 `SELECT` 쿼리별로 부여되는 식별자 값이다.
- 테이블을 조인하는 경우 테이블의 개수만큼 실행 계획 레코드가 출력되지만 같은 `id` 값이 부여된다.

```sql
EXPLAIN
SELECT e.emp_no, e.first_name, s.from_date, s.salary
FROM employees e, salaries s
WHERE e.emp_no=s.emp_no LIMIT 10;
```

![15](./images/real-mysql-8/15.png)

```sql
EXPLAIN
SELECT
( (SELECT COUNT(*) FROM employees) + (SELECT COUNT(*) FROM departments) ) AS total_count;
```

![16](./images/real-mysql-8/16.png)



- `id` 칼럼이 테이블의 접근 순서를 의미하지는 않는다.

```sql
EXPLAIN
SELECT * FROM dept_emp de
WHERE de.emp_no = (SELECT e.emp_no
                  FROM employees e
                  WHERE e.first_name='Georgi'
                  	AND e.last_name='Facello' LIMIT 1);
```

![17](./images/real-mysql-8/17.png)

```sql
EXPLAIN FORMAT=TREE
SELECT * FROM dept_emp de
WHERE de.emp_no = (SELECT e.emp_no
                  FROM employees e
                  WHERE e.first_name='Georgi'
                  	AND e.last_name='Facello' LIMIT 1)\G
```

![18](./images/real-mysql-8/18.png)

- `EXPLAIN FORMAT=TREE` 을 통해 실제 실행계획을 확인해보면 `employees` 테이블의 `ix_firstname` 인덱스를 먼저 조회한것을 확인할 수 있다.



### select_type 칼럼

- 각 단위 `SELECT` 쿼리가 어떤 타입의 쿼리인지 표시되는 칼럼이다.

#### SIMPLE

- `UNION`이나 서브쿼리를 사용하지 않는 단순한 `SELECT` 쿼리인 경우 해당 쿼리 문장의 `select_type`  은 `SIMPLE`로 표시된다.
- 일반적으로 제일 바깥 `SELECT` 쿼리의 `select_type` 이 `SIMPLE` 로 표시된다.
- 한개만 존재한다.



#### PRIMARY

- `UNION` 이나 서브쿼리를 가지는 `SELECT` 쿼리의 실행 계획에서 가장 바깥쪽에 있는 단위 쿼리는 `select_type` 이 `PRIMARY`로 표시된다.
- 한개만 존재한다.



#### UNION

- `UNION` 으로 결합하는 단위  `SELECT` 쿼리 가운데 첫번째를 제외한 두 번째 이후 단위  `SELECT` 쿼리의  `select_type` 은 `UNION` 으로 표시된다. 
- `UNION` 의 첫번째 단위  `SELECT` 는  `select_type` 이 `UNION` 이 아니라 `UNION` 되는 쿼리들을 모아서 저장하는 임시테이블(`DERIVED`)이  `select_type` 으로 표시된다.

```sql
EXPLAIN
SELECT * FROM (
	(SELECT emp_no FROM employees e1 LIMIT 10) UNION ALL
	(SELECT emp_no FROM employees e2 LIMIT 10) UNION ALL
	(SELECT emp_no FROM employees e3 LIMIT 10)
) tb;
```

![19](./images/real-mysql-8/19.png)

- 첫번째 (e1) 테이블은 `UNION` 결과를 대표하는  `select_type` 으로 설정됐다.
- 세 개의 서브쿼리로 조회된 결과를 `UNION ALL` 로 결합해 임시 테이블을 만들어서 사용하고 있으므로 `DERIVED`  라는  `select_type` 을 갖는다. 



#### DEPENDENT UNION

-  `DEPENDENT UNION`또한 `UNION`  `select_type` 과 같이 `UNION` 이나 `UNION ALL` 로 집합을 결합하는 쿼리에서 표시된다.
-  `DEPENDENT` 는 `UNION` 이나 `UNION ALL`로 결합된 단위 쿼리가 외부 쿼리에 의해 영향을 받는 것을 의미한다.

```sql
EXPLAIN
SELECT *
FROM employees e1 WHERE e1.emp_no IN (
	SELECT e2.emp_no FROM employees e2 WHERE e2.first_name='Matt'
  UNION
	SELECT e3.emp_no FROM employees e3 WHERE e3.first_name='Matt'
);
```



![20](./images/real-mysql-8/20.png)

- 예제 쿼리의 경우 MySQL 옵티마이저는 IN 내부의 서브쿼리를 먼저 처리하지 않고 외부의 employees 테이블을 먼저 읽은 다음 서브쿼리를 실행하는데 이때 employees 테이블의 칼럼값이 서브쿼리에 영향을 준다.
- 이렇게 내부 쿼리가 외부의 값을 참조해서 처리될 때  `select_type` 에 `DEPENDENT` 키워드가 표시된다.



#### UNION RESULT

- `UNION RESULT` 는 `UNION` 결과를 담아두는 테이블을 의미한다.
- MySQL 8.0 이전 버전에서는 `UNION ALL` 이나 `UNION` 쿼리는 모두 결과를 임시 테이블로 생성했는데 MySQL 8.0 버전부터 `UNION ALL` 의 경우 임시 테이블을 사용하지 않도록 기능이 개선됐다. (`UNION` , `UNION DISTINCT` 는 여전히 임시테이블에 결과를 버퍼링한다. )

```sql
EXPLAIN
SELECT emp_no FROM salaries WHERE salary>10000
UNION DISTINCT
SELECT emp_no FROM dept_emp WHERE from_date>'2001-01-01';
```



![21](./images/real-mysql-8/21.png)

- `UNION RESULT` 는 실제 쿼리에서 단위 쿼리가 아니기 때문에 별도의 id 값은 부여되지 않는다.
- `table` 컬럼에서 1, 2는 id 값이 1과 2인 단위 쿼리의 조회 결과를 `UNION` 했다는 의미다.
- 같은 쿼리를 `UNION ALL`로 실행하면 임시 테이블을 사용하지 않기 때문에 `UNION RESULT` 라인이 없어지게 된다.

```sql
EXPLAIN
SELECT emp_no FROM salaries WHERE salary>10000
UNION ALL
SELECT emp_no FROM dept_emp WHERE from_date>'2001-01-01';
```

![22](./images/real-mysql-8/22.png)



#### SUBQUERY

- 여기서 말하는  `select_type` 의 `SUBQUERY` 는 `FROM` 절 이외에서 사용되는 서브쿼리만을 의미한다.
- MySQL 서버의 실행계획에서 `FROM` 절에 사용된  `select_type` 은 `DERIVED` (파생 테이블) 로 표시된다.
- 서브쿼리는 사용하는 위치에 따라 각기 다른 이름을 갖는다.

`중첩된 쿼리 (Nested Query)`

- SELECT 되는 칼럼에 사용된 서브쿼리를 네스티드 쿼리라고 한다.

`서브쿼리(Subquery)`

- WHERE 절에 사용된 경우 일반적으로 그냥 서브쿼리라고 한다.

`파생 테이블(Derived Table)`

- FROM 절에 사용된 서브쿼리를 MySQL에서는 파생 테이블이라고 하며, RDBMS에서는 보통 인라인 뷰, 또는 서브 셀렉트 라고 부른다.

  

- 서브쿼리가 반환하는 값의 특성에 따라 다음과 같이 구분하기도 한다.

`스칼라 서브쿼리(Scalar Subquery)`

- 하나의 값만(칼럼이 단 하나인 레코드 1건만) 반환하는 쿼리

`로우 서브쿼리(Row subquery)`

- 칼럼의 개수와 상관없이 하나의 레코드만 반환하는 쿼리



#### DEPENDENT SUBQUERY

- 서브쿼리가 바깥쪽  `SELECT` 쿼리에서 정의된 칼럼을 사용하는 경우  `select_type` 에서 `DEPENDENT SUBQUERY` 이라고 표시된다.

```sql
EXPLAIN
SELECT e.first_name,
	(SELECT COUNT(*)
  FROM dept_emp de, dept_manager dm
  WHERE dm.dept_no=de.dept_no AND de.emp_no=e.emp_no) AS cnt
FROM employees e
WHERE e.first_name='Matt';
	
```

![23](./images/real-mysql-8/23.png)

- 안쪽의 서브쿼리 결과가가 바깥쪽  `SELECT` 쿼리 칼럼에 의존적이기 때문에 `DEPENDENT` 라는 키워드가 붙는다.
- 외부쿼리가 먼저 수행된 후 내부쿼리가 실행돼야 하므로 일반 서브쿼리보다는 처리속도가 느릴 때가 많다.



#### DERIVED

- MySQL 5.5 버전까지는 서브쿼리가 `FROM` 절에 사용된 경우 항상  `select_type` 이 `DERIVED`인 실행계획을 만든다.
- MySQL 5.6 버전부터는 옵티마이저 옵션에 따라 `FROM`절의 서브쿼리를 외부 쿼리와 통합하는 형태의 최적화가 수행되기도 한다.
- `DERIVED` 는 단위  `SELECT`  쿼리의 실행 결과로 메모리나 디스크에 임시 테이블을 생성하는 것을 의미한다.
- MYSQL 5.5 버전까지는 파생 테이블에 인덱스가 전혀 없었으므로 다른 테이블과 조인할 떄 성능상 불리했지만 MySQL 6.5 버전부터 옵티마이저 옵션에 따라 쿼리의 특성에 맞게 임시 테이블에도 인덱스를 추가해서 만들 수 있게 최적화 됐다.

```sql
EXPLAIN
SELECT *
FROM (SELECT de.emp_no FROM dept_emp de GROUP BY de.emp_no) tb,
	employees e
WHERE e.emp_no=tb.emp_no;
```

![24](./images/real-mysql-8/24.png)



- 가능하면 `DERIVED` 형태의 실행 계획을 조인으로 해결할 수 있게 쿼리를 바꿔주는게 좋다.
- MySQL 8.0 버전부터는 `FROM` 절의 서브쿼리에 대한 최적화도 많이 개선되어 가능하다면 불필요한 서브쿼리는 조인으로 쿼리를 재작성해서 처리한다.
- 옵티마이저에 의존하기보다는 직접 최적화된 쿼리를 작성하는 것이 중요하다.
- 서브쿼리를 조인으로 해결할 수 있는 경우라면 반드시 조인을 사용하자!



#### DEPENDENT DERIVED

- MySQL 8.0이전 버전에서는 `FROM` 절의 서브쿼리는 외부 칼럼을 사용할 수가 없었는데 MySQL 8.0에서 래터럴(`LATERAL JOIN`) 조인 기능이 추가되면서 `FROM` 절의 서브쿼리에서도 외부 칼럼을 참조할 수 있게 됐다

```sql
EXPLAIN
SELECT *
FROM employees e
LEFT JOIN LATERAL
	(SELECT *
	FROM salaries s
	WHERE s.emp_no=e.emp_no
	ORDER BY s.from_date DESC LIMIT 2) AS s2 ON s2.emp_no=e.emp_no;
```



![25](./images/real-mysql-8/25.png)

-  `DEPENDENT DERIVED` 은 해당 테이블이 레터럴 조인으로 사용된 것을 의미한다.



#### UNCACHEABLE SUBQUERY

- 하나의 쿼리 문장에 서브쿼리가 하나만 있더라도 그 서브쿼리가 여러번 실행될 수 있다.
- 조건이 똑같은 서브쿼리가 실행될 때는 다시 실행하지 않고 이전 실행 결과를 그대로 사용할 수 있게 서브쿼리의 결과를 내부적인 캐시 공간에 담아둔다.
- 일반 `SUBQUERY`는 바깥쪽의 영향을 받지 않으므로 처음 한번만 실행해서 그 결과를 캐시하고 필요할 때 캐시된 결과를 이용한다.
- `DEPENDENT SUBQUERY`는 의존하는 바깥쪽 쿼리의 칼럼의 값 단위로 캐시해두고 사용한다.
-  `select_type` 이 `UNCACHEABLE SUBQUERY` 인 경우는 서브쿼리에 포함된 요소에 의해 캐시 자체가 불가능해졌을때다.

`UNCACHEABLE SUBQUERY가 발생하는 요소`

- 사용자 변수가 서브쿼리에 사용된 경우
- `NOT-DETERMINISTIC` 속성의 스토어드 루틴이 서브쿼리 내에 사용된 경우
- `UUID()`나 `RAND()`와 같이 결과값이 호출할 때마다 달라지는 함수가 서브쿼리에 사용된 경우



```sql
EXPLAIN
SELECT *
FROM employees e WHERE e.emp_no = (
	SELECT @status FROM dept_emp de WHERE de.dept_no='d005'
);
```



![26](./images/real-mysql-8/26.png)



#### UNCACHEABLE UNION

- `UNCACHEABLE UNION` 이란 `UNION` 과 `UNCACHEABLE` 속성이 혼합된  `select_type` 을 의미한다.

#### MATERIALIZED

- MySQL 5.6 버전부터 도입됐다.
- 주로 `FROM` 절이나 `IN(subquery)` 형태의 쿼리에서 사용된 서브쿼리의 최적화를 위해 사용된다.

```sql
EXPLAIN
SELECT * 
FROM employees e
WHERE e.emp_no IN (SELECT emp_no FROM salaries WHERE salary BETWEEN 100 AND 1000);
```

![27](./images/real-mysql-8/27.png)

- MySQL 5.6 버전까지는 `employees` 테이블을 읽어서  `employees` 테이블의 레코드마다 `salaries` 테이블을 일근ㄴ 서브쿼리가 실행되는 형태로 처리됐다
- MySQL 5.7 버전부터는 서브쿼리의 내용을 임시 테이블로 구체화(materialization)한 후 임시 테이블과 `employees` 테이블을 조인하는 형태로 최적화되어 처리된다.



### table 칼럼

- MySQL 서버의 실행 계획은 단위  `SELECT` 기준이 아니라 테이블 기준으로 표시된다.
- `table` 칼럼엔 `<derived N>` 또는 `<union M,N>` 과 같은 이름이 `<>` 로 둘러 쌓인 이름은 임시 테이블을 의미한다.
- 또한 N값은 단위 SELECT 쿼리의 id 값을 지칭한다.

![24](./images/real-mysql-8/24.png)

- 여기에서  `<derived2>`는 단위 SELECT 쿼리의 id 값이 2인 실행 계획으로부터 만들어진 파생 테이블이라는 뜻이다.

`id 칼럼, select_type 칼럼, table 칼럼을 기반으로 위 실행계획 해석하기`

1. 첫 번째 라인의 테이블이  `<derived2>` 라는 것으로 보아 이 라인보다 id 값이 2인 라인이 먼저 실행되고 그 결과가 파생 테이블로 준비돼야 한다는 것을 알 수 있다.
2. 세 번째 라인을 보면  `select_type`  칼럼의 값이 `DERIVED` 로 표시되어 있다. 즉 이 라인은 `table` 칼럼에 표시된 `dept_emp` 테이블을 읽어서 파생 테이블을 생성하는 것을 알 수 있다.
3. 첫 번째 라인과 두 번째 라인은 같은 id 값을 가지고 있는 것으로 봐서 2개 테이블이 조인되는 쿼리라는 사실을 알 수 있다.  `<derived2>` 테이블이 먼저 표시됐기 때문에  `<derived2>` 가 드라이빙 테이블이 되고 `e` 테이블은 드리븐 테이블이 된다는 것을 알 수 있다. 즉  `<derived2>` 테이블을 먼저 읽어서 `e` 테이블로 조인을 실행했다는 것을 알 수 있다.



- MySQL 8.0에서  `select_type` 이 `MATERIALIZED` 인 실행 계획에서는 `<subquery N>` 과 같은 값이 `table` 표시된다. 이는 서브쿼리의 결과를 구체화해서 임시테이블로 만들었다는 의미이고 실제로는  `<derived N>` 과 같은 방법으로 해석하면 된다.



### partition 칼럼

- MySQL 5.7 버전까지는 옵티마이저가 사용하는 파티션들의 목록은 `EXPLAIN PARTITION` 명령을 이용해 확인 가능했다.
- MySQL 8.0 버전부터는 `EXPLAIN` 명령으로 파티션 관련 실행 계획까지 모두 확인할 수 있게 변경됐다.

```sql
CREATE TABLE employees_2 (
	emp_no int NOT NULL,
  birth_date DATE NOT NULL,
  first_name VARCHAR(14) NOT NULL,
  last_name VARCHAR(16) NOT NULL,
  gender ENUM('M', 'F') NOT NULL,
  hire_date DATE NOT NULL,
  PRIMARY KEY (emp_no, hire_date)  -- 파티션 제약사항으로 인해 pk에 hire_date를 포함
) PARTITION BY RANGE COLUMNS(hire_date)
(
	PARTITION p1986_1990 VALUES LESS THAN ('1990-01-01'),
	PARTITION p1991_1995 VALUES LESS THAN ('1996-01-01'),
	PARTITION p1996_2000 VALUES LESS THAN ('2000-01-01'),
	PARTITION p2001_2005 VALUES LESS THAN ('2006-01-01')
);

INSERT INTO employees_2 SELECT * FROM employees;


EXPLAIN
SELECT *
FROM employees_2
WHERE hire_date BETWEEN '1999-11-15' AND '2000-01-15';
```

​	![28](./images/real-mysql-8/28.png)

- 옵티마이저는 `p1996_2000` 과 `p2001_2005` 파티션에만 필요한 데이터가 있는것을 파악해서 해당 파티션에 대해서만 분석한다.
- 이처럼 파티션이 여러 개인 테이블에서 불필요한 파티션을 빼고 쿼리를 수행하기 위해 접근해야 할 것으로 판단되는 테이블만 골라내는 과정을 파티션 프루닝이라고 한다.
- 위에서 주목할만한 점은 `type` 칼럼이 `ALL` , 테이블 풀 스캔이다. MySQL을 포함한 대부분의 RDBMS는 파티션을 개별 테이블처럼 물리적으로 별도의 저장 공간에 저장한다. `p1996_2000` 과 `p2001_2005` 만 풀 스캔한 것이다.



### type 칼럼

- 일반적으로 쿼리를 튜닝할 때 인덱스를 효율적으로 사용하는지 확인하는 것이 중요하므로 실행 계획에서 `type` 칼럼은 반드시 체크해야할 중요 정보다.
- MySQL의 매뉴얼에서는 `type` 칼럼을 '조인 타입'으로 설명하지만 각 테이블의 접근 방법으로 해석하면 된다.

`type 칼럼에 올 수 있는 속성들 (성능이 빠른 순)`

- `system`
- `const`
- `eq_ref`
- `ref`
- `fulltext`
- `ref_or_null`
- `unique_subquery`
- `index_subquery`
- `range`
- `index_merge` <- 유일하게 인덱스를 2개 이상 사용한다.
- `index`
- `all` <- 유일하게 index를 사용하지 않는다.





#### system

- 레코드가 1건만 존재하는 테이블 또는 한 건도 존재하지 않는 테이블을 참조하는 형태의 접근 방법을 `system` 이라고 한다.
- InnoDB에서는 없고 MyISAM이나 MEMORY에서 사용한다.

```sql
CREATE TABLE tbl_dual (fd1 int NOT NULL) ENGINE=MyISAM;
INSERT INTO tb_dual VALUES (1);

EXPLAIN SELECT * FROM tb_dual;
```

![29](./images/real-mysql-8/29.png)

> 실제로 MyISAM 테이블을 생성하고 1개의 row만 넣었는데 `type` 값에 `ALL` 이 나와서 당황 ...



#### const

- 테이블의 레코드 건수에 관계 없이 쿼리가 프라이머리 키나 유니크 키 칼럼을 이용하는 `WHERE` 조건절을 가지고 있으며 반드시 1건을 반환하는 쿼리의 처리 형식을 `contst`라고 한다.
- 다른 DBMS에서는 이것을 유니크 인덱스 스캔이라고도 표현한다.

```sql
EXPLAIN
SELECT * FROM employees WHERE emp_no=10001;
```

![30](./images/real-mysql-8/30.png)

- 다중 칼럼으로 구성된 프라이머리 키나 유니크 키 중에서 인덱스의 일부 칼럼만 조건으로 사용할 때는 `const` 타입의 접근 방법을 사용할 수 없다.
- 프라이머리 키의 일부만 조건으로 사용할 때는 `const` 가 아닌 `ref` 로 표시된다.
- 다중 칼럼이라도 모든 칼럼을 전부 동등 조건으로 명시하면 `const` 접근 방법을 사용한다.
- `const`인 실행 계획은 MySQL의 옵티마이저가 쿼리를 최적화하는 단계에서 쿼리를 먼저 실행해서 통째로 상수화하기 때문에 `const`라는 이름을 갖는다.

```sql
SELECT COUNT(*)
FROM employees e1
WHERE first_name=(SELECT first_name FROM employees e2 WHERE emp_no=100001);

-- 최적화되는 시점에 다음 쿼리로 변환된다.


SELECT COUNT(*)
FROM employees e1
WHERE first_name='Jasminko' -- 사번이 100001인 사원의 first_name

```

#### 



#### eq_ref

- `eq_ref` 접근 방법은 여러 테이블이 조인되는 쿼리의 실행 계획에서만 표시된다.
- 조인에서 처음 읽은 테이블의 칼럼 값을, 그 다음 읽어야 할 테이블의 pk나 유니크 키 칼럼의 검색 조건에 사용할 때를 가리켜 `eq_ref` 라고 한다.
- 이때 두번째 이후에 읽는 테이블의 `type` 칼럼에 `eq_ref`가 표시된다. 또한 두번째 이후에 읽히는 테이블을 유니크 키로 검색할 때 그 유니크 인덱스는 NOT NULL 이어야 하며 다중 칼럼으로 만들어진 pk나 유니크 인덱스라면 인덱스의 모든 칼럼이 비교 조건에 사용 되어야만 `eq_ref` 접근 방법이 사용될 수 있다.
- 즉 조인에서 두번째 이후에 읽는 테이블에서 반드시 1건만 존재한다는 보장이 있어야 사용할 수 있는 접근 방법이다.

```sql
EXPLAIN
SELECT * FROM dept_emp de, employees e
WHERE e.emp_no=de.emp_no AND de.dept_no = 'd005';
```

![31](./images/real-mysql-8/31.png)

- `id` 가 같기 때문에 두 개의 테이블이 조인으로 실행된다는 것을 알 수 있다.
- `dept_emp` 테이블이 실행계획 위쪽에 있기 때문에 먼저 읽고 `e.emp_no=de.emp_no` 조건을 통해 `employees` 테이블을 검색한다.
- `employees` 테이블의 `emp_no` 는 pk라서 실행 계획의 두 번째 라인읜 `eq_ref` 로 표시된다.



#### ref

- `ref` 접근 방법은`eq_ref`와는 달리 조인의 순서와 관계 없이 사용되고 pk나 유니크키의 제약조건도 없다.
- 인덱스 종류와 관계없이 동등 조건으로 검색할 때는 ref접근 방법이 사용된다.
- 반환되는 레코드가 반드시 1건이라는 보장이 없기 때문에 `const`, `eq_ref`보다는 느리지만 그래도 매우 빠른 레코드 조회 방법중 하나다.

```sql
EXPLAIN
SELECT * FROM dept_emp WHERE dept_no='d005';
```

![32](./images/real-mysql-8/32.png)



`지금 까지 확인한 실행 계획`

- `const`, `eq_ref`, `ref` 는 모두 `WHERE` 조건절에 사용하는 비교 연산자는 동등 비교 연산자여야 하는 공통점이 있다.
- 세가지 모두 매우 좋은 접근 방법이고 인덱스 분포도가 나쁘지 않다면 성능상의 문제를 일으키지 않는 방법이다.





#### fulltext

- `fulltext` 접근 방법은 MySQL의 전문검색 인덱스를 사용해 레코드를 읽는 접근 방법을 의미한다.
- 쿼리에서 전문 인덱스를 사용하는 조건과 그 이외의 일반 인덱스를 사용하는 조건을 사용하면 일반 인덱스의 접근 방법이 `const`, `eq_ref`, `ref` 가 아니라면 일반적으로 전문 인덱스를 사용할 만큼 MySQL 서버에서 전문 검색 조건은 우선순위가 상당히 높다.
- 전문검색은 `MATCH (...) AGAINST (...)` 구문을 사용하는데 테이블에 전문 검색 인덱스가 없다면 쿼리는 오류가 발생하고 중지된다. 따라서 반드시 전문 검색 인덱스가 테이블에 정의되어 있어야 한다.

```sql
CREATE TABLE employee_name (
  emp_no int NOT NULL,
  first_name varchar(14) NOT NULL,
  last_name varchar(16) NOT NULL,
  PRIMARY KEY (emp_no),
  FULLTEXT KEY fx_name (first_name, last_name) WITH PARSER ngram
) ENGINE=InnoDB;


-- const 실행계획
EXPLAIN
SELECT *
FROM employee_name
WHERE emp_no=10001 -- emp_no 가 pk, 1건만 조회
	AND emp_no BETWEEN 10001 AND 10005
	AND MATCH(first_name, last_name) AGAINST('Facello' IN BOOLEAN MODE);

-- const 실행계획
EXPLAIN
SELECT *
FROM employee_name
WHERE emp_no BETWEEN 10001 AND 10005
	AND MATCH(first_name, last_name) AGAINST('Facello' IN BOOLEAN MODE);

```

![33](./images/real-mysql-8/33.png)

- 일반적으로 쿼리에 전문 검색 조건을 사용하면 MySQL은 아무런 주저없이 `fulltext` 접근 방식을 사용하는 경향이 있지만 일반 인덱스를 이용하는 `range` 접근 방법이 더 빨리 처리되는 경우가 많다.
- 따라서 전문 검색 쿼리를 사용할 때는 조건별로 성능을 확인해보는게 좋다.



#### ref_or_null

- 이 접근 방법은 `ref` 접근 방법과  같은데 NULL 비교가 추가된 형태다.
- 많이 활용되지 않지만, 사용된다면 나쁘지 않은 접근 방법 정도로 기억해두면 된다.

```sql
EXPLAIN
SELECT * FROM titles
WHERE to_date='1985-03-01' OR to_date IS NULL;
```

![34](/Users/a10300/Choi/Git/dev-note/db/mysql/images/real-mysql-8/34.png)





#### unique_subquery

- `WHERE` 조건절에서 사용될 수 있는 `IN(subquery)` 형태의 쿼리를 위한 접근 방법이다.
- 서브쿼리에서 중복되지 않는 유니크한 값만 반환할 때 이 접근방법을 사용한다.

```sql
EXPLAIN
SELECT * FROM departments
WHERE dept_no IN (SELECT dept_no FROM dept_emp WHERE emp_no=10001);
```

![35](/Users/a10300/Choi/Git/dev-note/db/mysql/images/real-mysql-8/35.png)

- 8.0 으로 올라가면서 `WHERE` 조건절에서 사용될 수 있는 `IN(subquery)` 형태의 세미 조인을 최적화하기 위한 많은 기능이 도입됐다.
- `unique_subquery` 실행계획을 보고싶다면 아래 옵션을 비활성화해주면 된다.

```java
SET optimizer_switch='semijoin=off';
```

> 참고로 위 쿼리는 위 설정으로하면 전혀 다른 실행계획이 나온다



#### index_subquery

- `IN` 연산자의 특성상  `IN(subquery)` 또는 `IN(상수 나열)` 형태의 조건은 괄호 안에 있는 값의 목록 중에서 중복된 값이 먼저 제거되어야 한다.
- `unique_subquery` 의 경우 중복된 값을 만들어내지 않는다는 보장이 있으므로 별도의 중복처리는 하지 않아도 된다.
- 업무 특성상  `IN(subquery)` 에서 subquery가 중복된 값을 반환할 수도 있는데 이때 중복된 값을 인덱스를 사용해서 제거할 수 있을때 `index_subquery`방법이 사용된다.



#### range

- `range`는 인덱스 레인지 스캔 형태의 접근 방법이다.
- `range`는 인덱스를 하나의 값이 아니라 범위로 검색하는 경우를 의미한다.
-  `<>`, `IS NULL`, `BETWEN`, `IN`, `LIKE` 등의 연산자를 이용해 인덱스를 검색할 때 사용한다.
- 일반적으로 애플리케이션 쿼리에서 가장 많이 사용되는 방식이고 우선순위가 아래에 있지만 `range` 까지만 나와줘도 최적의 성능이 보장된다고 볼 수 있다.
- 위에서 언급한 `const`, `ref`, `range` 를 모두 통틀어 인덱스 레인지 스캔, 또는 레인지 스캔으로 언급할 때가 많으니 참고하자.



#### index_merge

- `index_merge` 는 2개 이상의 인덱스를 이용해 각각의 검색 결과를 만들어 낸 후, 그 결과를 병합해서 처리하는 방식이다.

`특징`

1. 여러 인덱스를 읽어야 하므로 일반 `range` 스캔보다는 효율성이 떨어진다.
2. 전문 검색 인덱스를 사용하는 쿼리에서는 `index_merge`가 적용되지 않는다.
3. `index_merge` 접근 방식으로 처리된 결과는 항상 2개 이상의 집합이 되기 때문에 그 두 집합의 교집합이나 합집합 또는 중복 제거와같은 부가적인 작업이 더필요하다.

  

```sql
EXPLAIN 
SELECT * FROM employees
WHERE emp_no BETWEEN 10001 AND 11000
	OR first_name='Smith';
```

![36](/Users/a10300/Choi/Git/dev-note/db/mysql/images/real-mysql-8/36.png)



#### index

- 접근 방법이 `index` 라서 익숙하지 않은 사람들은 오해할 수 있다.
- 이 방법은 인덱스 풀 스캔을 의미한다.
- 테이블 전체를 읽는건 풀 스캔과 동일하지만 인덱스를 사용하기 떄문에 훨씬 효율적이라고 할 수 있다.

`index 접근 방법 사용하기 (1 + 2 or 1 + 3)`

1. `range`나 `const` 또는 `ref`와 같은 접근 방식으로 인덱스를 사용하지 못하는 경우
2. 인덱스에 포함된 칼럼으로 처리할 수 있는 쿼리인 경우 즉, 데이터 파일을 읽지 않아도 되는 경우
3. 인덱스를 이용해 정렬이나 그룹핑 작업이 가능한 경우 즉, 별도의 정렬 작업을 피할 수 있는 경우

```sql
EXPLAIN
SELECT * FROM departments ORDER BY dept_no DESC LIMIT 10;
```

![37](/Users/a10300/Choi/Git/dev-note/db/mysql/images/real-mysql-8/37.png)

#### ALL

- 풀 테이블 스캔 접근 방법은 가장 비효율적인 방법이다.
- 일반적으로 `index`, `ALL` 접근 방법은 웹서비스, 온라인 트랜잭션 처리 환경에서는 적합하지 않다.
- InnoDB도 다른 DBMS와 같이 풀 테이블 스캔, 인덱스 풀 스캔과 같은 대량의 디스크 I/O를 유발하는 작업을 위하 한꺼번에 많은 페이지를 읽어들이는 기능인 리드 어 헤드 (Read Ahead)를 제공한다. 이 방법은 잘못 튜닝된 쿼리보다 더 나은 접근 방법이 될 수 있다.

`MySQL 8.0의 병렬 쿼리`

- MySQL 인접한 페이지가 연속해서 몇 번 읽히면 백그라운드로 작동하는 읽기 스레드가 최대 64개의 페이지씩 한꺼번에 읽을 수 있다.
- `innodb_read_ahead_threshold` 시스템 변수와 `innodb_random_read_ahead` 시스템 변수를 이용해 언제 리드 어헤드를 실행할지 제어할 수 있다.
- MySQL 8.0에서는 병렬 쿼리기능이 도입됐는데 아직은 초기 구현 상태여서 조건 없이 전체 테이블 건수를 가져오는 쿼리정도만 병렬로 실행할 수 있다.

```sql
SELECT /*+ SET_VAR(innodb_parallel_read_threads=1)*/ COUNT(*) FROM big_table;

SELECT /*+ SET_VAR(innodb_parallel_read_threads=4)*/ COUNT(*) FROM big_table;

SELECT /*+ SET_VAR(innodb_parallel_read_threads=32)*/ COUNT(*) FROM big_table;
```























