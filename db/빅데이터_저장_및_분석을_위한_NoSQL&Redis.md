



# #1 NoSQL 개념

## NoSQL 개념

- 데이터가 기하급수적으로 늘어남에 따라 기존 RDB의 저장 및 관리 기술 만으로 이를 처리하게 많은 부담을 느끼고 있음
- 2000년 초반에 새로운 데이터 저장관리 기술이 등장하기 시작했는데 이를 NoSQL이라고 함
- 기존 RDB는 클라이언트/서버 플랫폼을 기반으로 한다면 NoSQL은 클라우드 컴퓨팅과 클라이언트/서버 플랫폼 모두를 기반으로 한다는 점이 가장 큰 차이임
- 기존 SQL 기능 뿐 아니라 SQL이 할 수 없는 영역의 기술도 제공하는 대체기술 또는 보완기술이다 라는 의미로 이해하는것이 올바른 해석임



**RDB에 대비한 NoSQL 장단점**

1. **클라우드 컴퓨팅 환경에 적합함**
   - 최근 플랫폼은 데이터 공유 및 시스템 운용의 효율성 향상을 위해 클라우드 컴퓨팅 환경이 적극적으로 도입되고 있음
   - 그러나 데이터 관리 기술은 여전히 RDB로 구축 운영하다 보니 비용 및 성능 지연문제와 같은 다양한 문제점에 부딪히게 됨
   - 최적화된 새로운 저장 관리 기술이 바로 NoSQL
   - RDB는 분산된 서버만큼의 라이센스를 지불해야하지만 NoSQL은 대부분 오픈소스와 엔터프라이즈 라이센스를 두개 동시에 제공함
2. **유연한 데이터 모델임**
   - 기존 RDB는 DB분석, 설계, 구축, 물리 모델링, 논리 모델링, 정규화 등등 매우 많은 시간과 비용이 발생함 이를
   - NoSQL은 비정형 데이터를 다루기때문에 RDB에서 사용하는 데이터의 타입이나 제약조건에 해당하는 비용을 감소시킬 수 있음
   - 물론 비정형 구조도 이를 분석하고 설계하는 단계는 필요하지만 RDB의 정형구조처럼 까다롭지는 않음
3. **빅데이터 처리에 효과적임**
   - 빅데이터 환경에서 빠른 쓰기/읽기 작업은 반드시 필요함 그러나 기존 RDB로는 실현 불가능
   - NOSQL은 2000년대 초반에 개발되면서 빠른 성능이 보장될 수 있도록 설계됨 NoSQL을 잘 활용한다면 최소 3배 이상의 성능을 기대할 수 있음

## NoSQL 종류

### NoSQL 제품

- RDB에는 오라클, MySQL, DB2 등등 모두 관계형 데이터 저장 구조를 사용함
- NoSQL은 MongoDB, Redis, Cassandra 등등이 있지만 모두 같은 데이터 저장 구조를 사용하지 않음
- NoSQL은 키-밸류 DB, 컬럼-패밀리 DB, 도큐먼트 DB, 그래프 DB와 같이 크게 4가지 유형의 데이터 구조를 제공함
- Redis는 키-밸류, MongoDB는 도큐먼트 DB 유형의 제품이라고 설명할 수 있음
- 이렇게 4가지로 분류하는 이유는 데이터의 유용성, 일관성, 지속성을 고려한 결과임

### 빅데이터 데이터 모델링을 위한 가이드라인

1. **초당 5만건 이상의 데이터가 발생하는가?**
   - 새롭게 구축하는 시스템이 초당 5~10만 건의 데이터가 발생한다면 기존 RDB로는 처리 불가함 이때는 이런 데이터를 고려할 필요도 없었음
2. **트랜잭션 제어가 필요한가?**
   - RDB에서는 Commit 또는 Rollback을 통해 트랜잭션을 제어함 
   - 기본적으로 Hadoop Eco System은 파일 시스템을 기반으로 하기 때문에 사용자가 직접 트랜잭션을 제어할 수 없음
   - 트랜잭션 제어가 필요한 환경이면 NoSQL 제품을 선택해야함
3. **데이터 무결성이 요구되는가?**
   - RDB에는 다양한 제약조건이 존재함 <- 성능 지연문제
   - 이런 무결성이 필요하지 않다면 키-밸류 또는 컬럼-패밀리 DB를 선택하는게 바람직 함
   - 도큐먼트 DB는 무결성을 지원함 (안하는 것도 있음)
4.  **수평적 DataSet인가 ?**
   - RDB 테이블 구조를 일반적으로 2차원 수평적 데이터 구조라고 함
   - 이런 수평적 데이터셋 구조로 저장 관리해야한다면 도큐먼트 DB를 선택해야 함
5. **계층형 데이터인가?**
   - 계층형 구조는 도큐먼트DB로도 충분히 저장 관리할 수 있음
   - 계층 Depth가 10~20레벨인 경우 선택적으로 그래프 DB 사용을 권장함



## 키-밸류 DB 활용

### 장점

**Redis Server의 기술적 주요 특징**

1. **Redis DB는 인-메모리 기반의 데이터 저장 구조임**
   - 기존 RDB는 파일 기반의 데이터 저장 구조이고 Redis는 순수 인메모리 기반 데이터 저장 구조임
   - 어떠한 설정이 없는 이상 모든 데이터는 메모리상에서만 존재함
   - 메모리를 사용하기때문에 데이터 유실, 메인 DB로 사용하는것은 한계가 있음
2. **키 밸류 데이터구조는 하나의 Key와 데이터 값으로 구성됨**
   - Redis에서 데이터 저장 구조를 테이블이라고 함
   - Redis에서도 인덱스 개념이 있지만 RDB의 인덱스와 조금 다르기때문에 한계가 있음
   - RDB의 데이터 구조를 그대로 매핑해서 사용할 수는 없고 데이터의 일부를 잠시 보관하거나 또는 가공 처리된 상태로 데이터를 임시 보관할 수 있음
3. **가공처리가 요구되는 비지니스 환경에서 적합함**
   - Redis나 Memcached는 메인 DB로 선택된 제품들은 파일 기반의 저장 구조이다 보니 디스크 IO문제로 인해 발생하는 성능 지연 문제를 해소하기 위해 키-밸류 DB를 보조로 사용하는 것임
   - 데이터의 가공처리, 통계 분석, 데이터 캐싱, 메시지 큐 등의 용도로 사용 가능

### 활용 영역

1. **실시간 분석 영역**
   - Redis는 실시간 가공 처리 및 분석이 요구되는 환경에서 장점이 가장 드러남
   - 메인 DB에느 ㄴ현재 시점 또는 과거 시점 데이터를 저장하고 필요에 따라 데이터를 읽은 다음 가공처리 및 분석 작업은 Redis 서버에서 수행하게 한 뒤 메모리에 저장하고 사용자에게 그 데이터를 제공
2. **IOT 영역**
   - NoSQL 기술 중 인메모리 기반의 Redis는 다른 도큐먼트DB, 컬럼-패밀리DB, 그래프DB와 비교했을때 매우 경량 이고 IOT와 쉽게 연결 가능하기때문에 매우 적합함
3. **계측 정보수집 영역**
   - 기존에 온도, 습도, 조도, 위치정보 등의 데이터의 수집 및 활용에 한계가 있었으나 Redis서버는 이런 데이터를 쉽고 빠르게 저장하고 가공할 수 있음
4. **개인화 정보 관리 영역**
   - 웹사이트에서 사용자 개인의 특성과 기호에 맞게 페이지 화면을 편집해서 볼 수 있듯이 Redis 에 저장되어 있는 데이터를 사용자의 필요에 따라 쉽게 가공 처리하여 편집이 요구되는 비지니스 환경에 활용 가능
5. **전자 상거래 비지니스 영역**
   - 기존 비지니스에 사용하는 RDB역할을 적용시킬 수 있지만 과거 시점의 데이터를 지속적으로 저장하는 것은 한계가 있고 시스템 크래시에 장애가 일어나므로 분산 복제 시스템을 구성해야 함

## NoSQL 선정 방법

1. **Simple API를 지원하는가?**
   - 오픈소스이기때문에 잘 안되는 부분이 있을 수도 있음 충분한 고려가 필요함
2. **Easy Distributed 기술을 제공하는가?**
   - 샤딩 또는 파티셔닝에 대한 기술이 제공되는지에 대한 검토가 필요함
3. **Easy Replication이 지원되는가?**
   - 데이터 유실 방지를 위한 복제 시스템을 제공하는지에 대한 검토 필요
4. **스케일 아웃이 가능한가?**
   - 기존 RDB는 라이센스 문제때문에 스케일아웃에 폐쇄적이였으나 NoSQL은 오픈소스가 많기때문에 스케일아웃에 개방적임
5. **유지보수 비용이 저렴한가?**
6. **비정형 데이터 구조를 지원하고 트랜잭션 제어가 가능한가?**
   - 대부분의 NoSQL은 Auto-Commit을 기본적으로 제공하지만 몇몇 제품은 사용자가 트랜잭션을 직접 제어할 수 있음
   - 트랜잭션 제어가 필요하면 반드시 고려해야함
7. **오픈소스인가?**





# #2 Redis 설치 및 데이터 처리

## 주요 특징

- Redis는 **RE**mote **DI**rectory **S**ystem 의 약어로 Vmware 가 스폰서였고 지금은 RedisLab에 의해 상용 라이센스가 개발되어 지원되고 있음



1. Redis는 키밸류 데이터베이스로 분류되는 NoSQL이며 2009년 Salvatore Sanfilippo가 처음 개발했고 2015년부터 RedisLab에 의해 상용 SW가 개발어 지원중임
2. 키밸류 DB면서 대표적인 인메모리 기반의 데이터 처리 및 저장 기술을 제공하기 때문에 다른 NoSQL 제품에 비해 상대적으로 빠른 Read/Write가 가능함
3. String, Set, Sotred Set, Hash, List, HyperLogLogs 유형의 데이터를 저장할 수 있음
4. Dump 파일과 AOF(Append Of File) 방식으로 메모리 상의 데이터를 디스크에 저장할 수 있음
5. Master/Slave Replication 기능을 통해 데이터의 분산 복제 기능을 제공하며 Query Off Loading 기능을 통해 Master는 Read/Write를 수행하고 Slave는 Read만 수행할 수 있음
6. 파티셔닝을 통해 동적인 스케일 아웃인 수평 확장이 가능함
7. Expriation 기능은 일정 시간이 지났을 때 메모리 상의 데이터를 자동 삭제할 수 있음



**레디스 주요 업무 영역**

1. 임메모리 DB가 제공하는 최대 장점인 빠른 쓰기 읽기 작업은 가능하지만 데이터를 메모리에 저장할 수 없기 때문에 지속적인 관리가 요구되는 환경에서는 제한적일 수밖에 없음 기업의 비지니스 영역과 데이터 성격에 따라 다르겠지만 보통 세컨더리 DB로 사용됨
2. 주된 영역은 데이터 캐싱을 통한 빠른 쓰기/읽기 작업, IOT 를 활용한 데이터 수집 및 처리 영역, 실시간 분석 및 통계 분석 영역 등등
3. MQ 머신러닝, 검색엔진 영역에서 기존 RDB와 타 NoSQL에 비해 효율적으로 사용 가능



## 제품 유형

1. **커뮤니티 에디션**
2. **엔터프라이즈 에디션**



## 다운로드 및 설치





...





## 데이터 처리

### 용어 설명

1. Table: 하나의 DB에서 데이터를 저장하는 논리적 구조(RDB의 논리적 개념인 테이블과 동일함)
2. Data Sets: 테이블을 구성하는 논리적 단위 하나의 데이터 셋은 하나의 Key와 한개 이상의 Field/Element로 구성됨
3. Key: 하나의 Key는 하나 이상의 조합된 값으로 표현 가능함예를 들어 주문번호 또는 주문번호 + 순번 등등
4. Values: 해당 Key에 대한 구체적인 데이터 값을 표현함 밸류는 하나 이상의 Field 또는 Element로 구성됨



### 데이터 crud

- set: 데이터를 저장
- get: 저장된 데이터 검색
- rename: 저장된 데이터 값을 변경
- randomkey: 저장된 key 중 하나의 key를 랜덤하게 검색
- keys: 저장된 모든 key를 검색
- exist: 검색 대상 key가 존재하는지 여부
- mset/mget 여러 개의 key와 value를 한번 저장하고 검색

<br>

- Redis에 데이터를 crud하기 위해서는 반드시 Redis 서버에서 제공하는 명령어를 사용하고 하나의 key에 대해 하나 이상의 field 또는 element로 표현해야함

```
some-redis:6379> 
some-redis:6379> 
some-redis:6379> set 1113 "HYEON"
OK
some-redis:6379> set 1114 "SEOP"
OK
some-redis:6379> get 1111
"CHOI"
some-redis:6379> get 1112
"SUP2IS"
some-redis:6379> keys *
1) "1113"
2) "1112"
3) "1114"
4) "1111"
some-redis:6379> 
some-redis:6379> keys *2
1) "1112"
some-redis:6379> 
some-redis:6379> del 1112
(integer) 1
some-redis:6379> keys *
1) "1113"
2) "1114"
3) "1111"
some-redis:6379> rename 1113 1116
OK
some-redis:6379> keys *
1) "1116"
2) "1114"
3) "1111"
some-redis:6379> randomkey
"1116"
OK
some-redis:6379> keys *
(empty array)
some-redis:6379> setex 1111 30 "choi"
OK
some-redis:6379> ttl 1111
(integer) 27
some-redis:6379> ttl 1111
(integer) 24
some-redis:6379> ttl 1111
(integer) 23
some-redis:6379> 
some-redis:6379> ttl 1111
(integer) 16
some-redis:6379> 
some-redis:6379> ttl 1111
(integer) 2
some-redis:6379> 
some-redis:6379> 
some-redis:6379> ttl 1111
(integer) 0
some-redis:6379> keys *
(empty array)
some-redis:6379> 
some-redis:6379> ttl 1111
(integer) -2
some-redis:6379> mset 1113 "Nosql" 1115 "choi"
OK
some-redis:6379> get 1113
"Nosql"
some-redis:6379> get 1115
```



### 데이터 타입

- strings: 문자, binary 유형 데이터 저장
- List: 하나의 Key에 여러 개의 배열 값을 저장
- Hash: 하나의 Key에 여러 개의 Fields와 Value로 구성된 테이블을 저장
- Set Sorted set: 정렬되지 않은 String 타입 Set과 Hash를 결합한 타입 
- Bitmaps: 0 & 1로 표현하는 데이터 타입
- HyperLogLogs: Element 중에서 Unique  한 개수의 Element만 계산
- Geospatial: 좌표 데이터를 저장 및 고나리하는 데이터 타입



1. **Hash**

- 컨테이너 타입중에 하나
- Hash타입은 RDB에서 PK와 하나 이상의 컬럼으로 구성된 테이블 구조와 매우 흡사함
- 하나의 Key는 오브젝트명과 하나 이상의 필드값을 콜론(:)기호로 결합하여 표현할 수 있음 ex: order:201809123
- 문자 값을 저장할 때는 인용부호("")를 사용하며 숫자 값을 저장할 때는 이용 부호가 필요하지 않음
- 기본적으로 필드 개수의 제한 없음
- Hash 타입의 데이터를 처리할 때는 hmset,hget, hgetall, hkey, hlen 명령어를 사용함



```

some-redis:6379> hmset order:201809123 customer_name "Wanman & Sports" emp_name "Magee" total 601100 payment_type "Credit" order_filled "Y" shop_date 20180925

OK
some-redis:6379> 
some-redis:6379> 
some-redis:6379> hget order:2018*
some-redis:6379> hget order:201809123 customer_name
"Wanman & Sports"
some-redis:6379> hget order:201809123 shop_date
"20180925"
some-redis:6379> hgetall order:201809123
 1) "customer_name"
 2) "Wanman & Sports"
 3) "emp_name"
 4) "Magee"
 5) "total"
 6) "601100"
 7) "payment_type"
 8) "Credit"
 9) "order_filled"
10) "Y"
11) "shop_date"
12) "20180925"
some-redis:6379> hexists order:201809123 product_name
(integer) 0
some-redis:6379> hexists order:201809123 customer_name
(integer) 1
some-redis:6379> 
some-redis:6379> 
some-redis:6379> hdel order:201809123 shop_date
(integer) 1
some-redis:6379> hgetall order:201809123
 1) "customer_name"
 2) "Wanman & Sports"
 3) "emp_name"
 4) "Magee"
 5) "total"
 6) "601100"
 7) "payment_type"
 8) "Credit"
 9) "order_filled"
10) "Y"
some-redis:6379> hmset order:201809123 ship_date 20180925
OK
some-redis:6379> hgetall order:201809123
 1) "customer_name"
 2) "Wanman & Sports"
 3) "emp_name"
 4) "Magee"
 5) "total"
 6) "601100"
 7) "payment_type"
 8) "Credit"
 9) "order_filled"
10) "Y"
11) "ship_date"
12) "20180925"
some-redis:6379> hkeys order:201809123
1) "customer_name"
2) "emp_name"
3) "total"
4) "payment_type"
5) "order_filled"
6) "ship_date"
some-redis:6379> hvals order:201809123
1) "Wanman & Sports"
2) "Magee"
3) "601100"
4) "Credit"
5) "Y"
6) "20180925"
some-redis:6379> hlen order:201809123
(integer) 6
```





2. **List**

- List타입은 기존의 관계형 테이블에는 존재하지 않는 데이터 유형이고 일반 프로그래밍 언어에서의 Array와 유사함
- 기본적으로 String 타입의 경우 배열에 저장할 수 있는 데이터 크기는 512mb임
- List타입의 데이터를 처리할때는 lpush, lrange, rpush, rpop, llen, lindex 명령어를 사용함

```
some-redis:6379> lpush order_detail:201809123 "<item_id>1</item_id><product_name>Bunnty Boots</product_name>" "<item_id>2</item_id><product_name>ProSki Boots</product_name>"
(integer) 2
some-redis:6379> 
some-redis:6379> 
some-redis:6379> lrange order_detail:201809123
(error) ERR wrong number of arguments for 'lrange' command
some-redis:6379> 
some-redis:6379> lrange order_detail:201809123 0 10
1) "<item_id>2</item_id><product_name>ProSki Boots</product_name>"
2) "<item_id>1</item_id><product_name>Bunnty Boots</product_name>"
some-redis:6379> keys *
1) "order:201809123"
2) "order_detail:201809123"
some-redis:6379> rpush order_detail:201809123 "<item_id>3</item_id><product_name>Bunnty Boots</product_name>"
(integer) 3
some-redis:6379> 
some-redis:6379> lrange order_detail:201809123 0 10
1) "<item_id>2</item_id><product_name>ProSki Boots</product_name>"
2) "<item_id>1</item_id><product_name>Bunnty Boots</product_name>"
3) "<item_id>3</item_id><product_name>Bunnty Boots</product_name>"
some-redis:6379> 
some-redis:6379> rpop order_detail:201809123
"<item_id>3</item_id><product_name>Bunnty Boots</product_name>"
some-redis:6379> lrange order_detail:201809123 0 10
1) "<item_id>2</item_id><product_name>ProSki Boots</product_name>"
2) "<item_id>1</item_id><product_name>Bunnty Boots</product_name>"
some-redis:6379> llen order_detail:201809123
(integer) 2
some-redis:6379> lindex order_detail:201809123 0
"<item_id>2</item_id><product_name>ProSki Boots</product_name>"
some-redis:6379> 
some-redis:6379> lindex order_detail:201809123 1
"<item_id>1</item_id><product_name>Bunnty Boots</product_name>"
some-redis:6379> lindex order_deㅣㅔㅕtail:201809123 2
(nil)
some-redis:6379> lset order_detail:201809123 0 "<item_id>5</item_id><product_name>Bunnty Boots</product_name>"
OK
some-redis:6379> 
some-redis:6379> lrange order_detail:201809123 0 10
1) "<item_id>5</item_id><product_name>Bunnty Boots</product_name>"
2) "<item_id>1</item_id><product_name>Bunnty Boots</product_name>"
some-redis:6379> lpushx order_detail:201809123 "<item_id>4</item_id><product_name>Bunnty Boots</product_name>"
(integer) 3
some-redis:6379> lrange order_detail:201809123 0 10\
(error) ERR value is not an integer or out of range
some-redis:6379> 
some-redis:6379> 
some-redis:6379> lrange order_detail:201809123 0 10
1) "<item_id>4</item_id><product_name>Bunnty Boots</product_name>"
2) "<item_id>5</item_id><product_name>Bunnty Boots</product_name>"
3) "<item_id>1</item_id><product_name>Bunnty Boots</product_name>"
some-redis:6379> lpush order_detail:201809124 "{}
Invalid argument(s)
some-redis:6379> lpush order_detail:201809124 "{item_id:1, product_name:Bunny}"

(integer) 1
some-redis:6379> 
some-redis:6379> lrange order_detail:201809124 0 10
1) "{item_id:1, product_name:Bunny}"
some-redis:6379> keys *
1) "order_detail:201809124"
2) "order:201809123"
3) "order_detail:201809123"
some-redis:6379> 
```



3. **Set**

- List타입은 하나의 필드에 여러개의 배열 값을 저장할 수 있는 데이터구조라면 Set은 배열 구조가 아닌 여러개의 엘리먼트로 데이터 값을 표현함
- Set타입의 데이터를 처리할 때는 sadd, smember, scard, sdiff, sunion 명령어를 사용함

```
some-redis:6379> sadd product "id:11, product_name:Sky Pole" "id:12, product_name:Bunny"
(integer) 2
some-redis:6379> sadd product "id:13, product_name:Pants"
(integer) 1
some-redis:6379> 
some-redis:6379> smember product
(error) ERR unknown command `smember`, with args beginning with: `product`, 
some-redis:6379> smembers product
1) "id:13, product_name:Pants"
2) "id:12, product_name:Bunny"
3) "id:11, product_name:Sky Pole"
some-redis:6379> scard product
(integer) 3
some-redis:6379> sadd product_old "id:91, product_name:Old Sky Pole"
(integer) 1
some-redis:6379> 
some-redis:6379> smembers product_old
1) "id:91, product_name:Old Sky Pole"
some-redis:6379> sdiff product product_old
1) "id:13, product_name:Pants"
2) "id:12, product_name:Bunny"
3) "id:11, product_name:Sky Pole"
some-redis:6379> 
some-redis:6379> sdiff product_old product
1) "id:91, product_name:Old Sky Pole"
some-redis:6379> sdiffstore product_diff product product_old
(integer) 3
some-redis:6379> smembers product_diff
1) "id:13, product_name:Pants"
2) "id:12, product_name:Bunny"
3) "id:11, product_name:Sky Pole"
some-redis:6379> sunionstore product_new product product_old
(integer) 4
some-redis:6379> smembers product_new
1) "id:91, product_name:Old Sky Pole"
2) "id:13, product_name:Pants"
3) "id:12, product_name:Bunny"
4) "id:11, product_name:Sky Pole"
some-redis:6379> srem product_new "id:11, product_name:Sky Pole"
(integer) 1
some-redis:6379> smembers product_new
1) "id:91, product_name:Old Sky Pole"
2) "id:13, product_name:Pants"
3) "id:12, product_name:Bunny"
some-redis:6379> 
some-redis:6379> spop product_new 1
1) "id:12, product_name:Bunny"
some-redis:6379> smembers product_new
1) "id:91, product_name:Old Sky Pole"
2) "id:13, product_name:Pants"
some-redis:6379> 
```



4. **Sourced Set 타입**

- Sorted Set 타입은 Set타입과 동일하고 데이터 값이 분류된 상태면 정렬, 아니라며 그냥 Set타입임
- 데이터를 처리할 때는 zadd, zrange, zcard, zcount, zrank, zrevrank 명령어를 사용함

```
(integer) 3
some-redis:6379> 
some-redis:6379> 
some-redis:6379> zrange order_detail 201809123 0 -1
(error) ERR syntax error
some-redis:6379> zrange order_detail:201809123 0 -1
1) "{item_id:1, product_name:Bunny}"
2) "{item_id:2, product_name:Bunny2}"
3) "{item_id:3, product_name:Bunny3}"
some-redis:6379> zadd order_detail:201809123 3 "{item_id:4, product_name:Bunny4}"
(integer) 1
some-redis:6379> zrange order_detail:201809123 0 -1
1) "{item_id:1, product_name:Bunny}"
2) "{item_id:2, product_name:Bunny2}"
3) "{item_id:3, product_name:Bunny3}"
4) "{item_id:4, product_name:Bunny4}"
some-redis:6379> zcard order_detail:201809123
(integer) 4
some-redis:6379> zrem order_dtail:201809123 "{item_id:1, product_name:Bunny}"
(integer) 0
some-redis:6379> 
some-redis:6379> zrange order_detail:201809123 0 -1
1) "{item_id:1, product_name:Bunny}"
2) "{item_id:2, product_name:Bunny2}"
3) "{item_id:3, product_name:Bunny3}"
4) "{item_id:4, product_name:Bunny4}"
some-redis:6379> zrank order_detail:201809123 "{item_id:1, product_name:Bunny}"
(integer) 0
some-redis:6379> zrank order_detail:201809123 "{item_id:1, product_name:Bunny}"
(integer) 0
some-redis:6379> zrank order_detail:201809123 "{item_id:2, product_name:Bunny2}
"
(integer) 1
some-redis:6379> 
```



5. **Bit**

- 이진수 데이터
- setbit, getbit, bitcount



6. **Geo**

- 위치 정보 데이터의 분석 및 검색에 사용
- geoadd, geopos, geodist, georadius, geohash



7. **HyperLogLogs 타입**

- RDB에서 Check 제약 조건과 유사함
- pfadd, pfcount, pfmerge 명령어를 사용





## Redis확장 Module

- 레디스 소스를 활용해서 레디스 확장모듈을 만들었음

**모듈종류**

1. REJSON: JSON 타입을 이용해서 데이터를 처리할 수 있는 모듈
2. RediSQL: Redis Server에서 SQL로  데이터를 저장할 수 있는 모듈
3. RediSearch: Redis DB내에 저장된 데이터에 대한 검색엔진을 사용할 수 있는 모듈
4. Redis-ML: 머신러닝 모델 서버를 Redis 서버에서 사용할 수 있는 모듈
5. Redis-sPiped: Redis server로 전송되는 데이터를 암호화 할 수 있는 모듈

<br>

### REJSON

- Redis 서버 내에서도 JSON데이터 타입 저장을 가능하게 해주는 모듈
- 도커로 올림

```
[root@kafka-broker-base-1 rejson]# docker run -d -p 6379:6379 --name redis-redisjson --network mybridge redislabs/rejson

[root@kafka-broker-base-1 rejson]# docker run -it --net mybridge --rm redis redis-cli -h redis-redisjson
```



```
redis-redisjson:6379> JSON.SET 1101 . '"CHOI"'
OK
redis-redisjson:6379> JSON.GET 1101
"\"CHOI\""
redis-redisjson:6379> JSON.TYPE 1101
string
redis-redisjson:6379> JSON.STRLENT 1101
(error) ERR unknown command `JSON.STRLENT`, with args beginning with: `1101`, 
redis-redisjson:6379> JSON.STRLEN 1101
(integer) 4
redis-redisjson:6379> JSON.STRAPPEND 1101 . '"SEOUL"'
(integer) 9
redis-redisjson:6379> JSON.GET 1101
"\"CHOISEOUL\""
redis-redisjson:6379> JSON.NUMINCRBY orderno . 1
(error) ERR could not perform this operation on a key that doesn't exist
redis-redisjson:6379> JSON.NUMINCRBY orderno . 1
(error) ERR could not perform this operation on a key that doesn't exist
redis-redisjson:6379> 
redis-redisjson:6379> 
redis-redisjson:6379> JSON.SET orderno . 2018redis-redisjson:6379> JSON.NUMINCRBY orderno 1
"2019"
redis-redisjson:6379> JSON.NUMINCRBY orderno 2
"2021"
redis-redisjson:6379> JSON.SET amoreinterestingexample . '[true, {"age": 52}, null]'
OK
redis-redisjson:6379> JSON.GET amoreinterestingexample 
"[true,{\"age\":52},null]"
redis-redisjson:6379> json.get amoreinterestingexample [1].age
"52"
redis-redisjson:6379> json.del amoreinterestingexample 
(integer) 0
redis-redisjson:6379> json.del amoreinterestingexample [-1]
(integer) 0
redis-redisjson:6379> json.get amoreinterestingexample [1].age
(nil)
redis-redisjson:6379> json.get amoreinterestingexample
(nil)
redis-redisjson:6379> JSON.SET array_data . []
OK
redis-redisjson:6379> jsonarrappend array_data . 0
(error) ERR unknown command `jsonarrappend`, with args beginning with: `array_data`, `.`, `0`, 
redis-redisjson:6379> JSON.ARRAPPEND array_data . 0
(integer) 1
redis-redisjson:6379> json.get array_data
"[0]"
redis-redisjson:6379> JSON.ARRINSERT array_data . 0 -1 -2
(integer) 3
redis-redisjson:6379> json.get array_data
"[-1,-2,0]"
redis-redisjson:6379> JSON.ARRTRIM array_data . 1 1
(integer) 1
redis-redisjson:6379> json.get array_data
"[-2]"
redis-redisjson:6379> JSON.ARRPOP array_data
"-2"
redis-redisjson:6379> json.arrpop array_da
(error) WRONGTYPE Operation against a key holding the wrong kind of value
redis-redisjson:6379> json.arrpop array_data
(nil)
redis-redisjson:6379> json.set 1101 . '{"name":"choi", "age":"27"}'
OK
redis-redisjson:6379> 
redis-redisjson:6379> JSON.OBJLEN 1101
(integer) 2
redis-redisjson:6379> JSON.OBJkeys 1101
1) "name"
2) "age"
```



### REDISQL

- REDISQL을 사용하면 Redis 서버와 RDB인 SQLite를 연동해서 사용 가능

```
[root@kafka-broker-base-1 rejson]# docker run -d -p 6379:6379 --name redis-redisql --network mybridge siscia/redisql:latest

[root@kafka-broker-base-1 rejson]# docker run -it --net mybridge --rm redis redis-cli -h redis-redisql
```





```
redis-redisql:6379> REDISQL.CREATE_DB SALES
OK
redis-redisql:6379> REDISQL.EXEC "CREATE TABLE emp(no int, name text, address text, deptno int);"
(error) Wrong number of arguments, it accepts 3, you provide 2
redis-redisql:6379> 
redis-redisql:6379> 
redis-redisql:6379> REDISQL.EXEC SALES "CREATE TABLE emp(no int, name text, address text, deptno int);"
1) DONE
2) (integer) 0
redis-redisql:6379> 
redis-redisql:6379> REDISQL.EXEC "INSERT INTO emp VALUES (1101, 'CHOI', 'SEOUL', 10);"
(error) Wrong number of arguments, it accepts 3, you provide 2
redis-redisql:6379> 
redis-redisql:6379> REDISQL.EXEC SALES "INSERT INTO emp VALUES (1101, 'CHOI', 'SEOUL', 10);"
1) DONE
2) (integer) 1
redis-redisql:6379> 
redis-redisql:6379> REDISQL.EXEC SALES "SELECT * FROM emp"
1) 1) (integer) 1101
   2) "CHOI"
   3) "SEOUL"
   4) (integer) 10
redis-redisql:6379> 
```





## Lua Function & Script

1. Lua는 가볍고 내장 가능한 스크립트 언어, 절차형, 객체지향, db비간 프로그래밍을 수행 가능
2. 간단한 프로시저와 배열로 결합 가능하고 동적으로 코딩 가능하며 가상머신 기반에서 바이트 코드를 해석하여 실행 가능하고 증분 가비지 컬렉션으로 메모리를 자동 관리
3. Lua는 브라질리오 데 자네이로 교황청 카톨릭 PUC-Rio 팀에서 개발 및 유지보수중
4. Redis Sever에 내장된 Lua 인터프리터를 통해 미리 작성된 Lua 스크립트 또는 function 사용 가능
5. Redis Server는 다양한 Lua function을 제공하고 이러 인해 검색, 수정 ,삭제 가능



<br>

- 기본문법은 어느정도 한계가 있기 때문에 Lua, java, ruby같은 언어로 연동할 수 있음



**Lua의 특징**

1. 초경량 스크립트중 하나이고 절차형 프로그래밍, 객체지향 프로그램이 가능하고 데이터베이스를 기반으로 하는 프로그래밍을 할 수 있는 특징
2. 간단한 절차형 프로시저와 배열로 데이터를 저장 및 결합 가능하고 동적으로 코딩, 가상 머신 기반에서 바이트 코드를 해석하여 실행할 수 있음 증분 가비지 컬렉션으로 메모리를 자동 관리할 수 있음
3. 다양한 함수를 제공하고 crud를 할 수 있음 대표적으로 eval, evalsha 함수







# #3 트랜잭션 제어 & 관리



## Isolation & Lock

1. 읽기 일관성과 데이터 공유를 위해 DataSets Lock을 제공함
2. 트랜잭션 제어를 위해 Read Uncommited 와 ReadCommited 타입 두가지를 제공함

> Global Lock, Database Loc, Object Lock, Page Lock, Key/Value Lock



- 하둡같은 경우는 트랜잭션 제어가 없음 때문에 데이터 일관성 공유에 매우 취약함 이와같은 문제점을 개선하기 위해 제공되는 기술이 nosql이지만 모든 nosql 제품이 트랜잭션을 지원하지는 않음
- redis는 트랜잭션을 지원함 commit, rollback <- Read Uncommitted 라고함

## CAS(Check And Set)

- 데이터 일관성 공유를 위해서는 동시 처리가 발생하 때 먼저 작업을 요구한 사용자에게 우선권을 보장하고 나중에 작업을 요구한 사용자의 세션에서는 해당 트랜잭션에 충돌이 발생했음을 인지할 수 있도록 해야하는데 redis에서는 이걸 CAS라고함

```
redis-redisql:6379> WATCH A
OK
redis-redisql:6379> MULTI
OK
redis-redisql:6379> SET 1 BLA
QUEUED
redis-redisql:6379> GET 1
QUEUED
redis-redisql:6379> GET 1
QUEUED
redis-redisql:6379> EXEC


다른 쉘에서 MULTI > SET 이후에 데이터 변경했지만 에러 안났음
```

## Commit & Rollback

- 최종 저장시 exec 취소시 discard

```
redis-redisql:6379> flushall
OK
redis-redisql:6379> set 3 sup2is
OK
redis-redisql:6379> flushall
OK
redis-redisql:6379> multi
OK
redis-redisql:6379> set 3 sup2is
QUEUED
redis-redisql:6379> discard
OK
redis-redisql:6379> 
redis-redisql:6379> 
redis-redisql:6379> keys *
(empty array)
redis-redisql:6379> 
```

## Index 유형 및 생성

- Redis는 기본적으로 하나의 Key와 하나 이상의 Field/Element 값으로 구성되는데 Key에는 빠른 검색을 위해 기본적으로 인덱스가 생성됨 이를 Primary Key Index라고 함
- 또 사용자의 필요에 따라 추가적인 인덱스를 생성할 수 있는데 Secondary Index라고 함
- 인덱스 키를 통해 검색할 때 유일한 값을 검색하는 경우에 Exact Match By a Secondary Index 라고 하고 일정 범위의 값을 검색 조건으로 부여하는 경우를 Range By a Secondary Index라고함

1. Sorted Set 타입 인덱스

```
redis-redisql:6379> zadd order.ship_date.index 2 '201809124:20180926'
(integer) 1
redis-redisql:6379> zadd order.ship_date.index 1 '201809123:20180925'
(integer) 1
redis-redisql:6379> zrange order.ship_date.index 0 - 1
(error) ERR value is not an integer or out of range
redis-redisql:6379> zrange order.ship_date.index 0 -1
1) "201809123:20180925"
2) "201809124:20180926"
redis-redisql:6379> zscan order.ship_date.index 0 match 201809124*
1) "0"
2) 1) "201809124:20180926"
   2) "2"
redis-redisql:6379> 
redis-redisql:6379> zadd order.no.index 1 201809123
(integer) 1
redis-redisql:6379> zadd order.no.index 2 201809124
(integer) 1
redis-redisql:6379> zrange order.no.index 0 -1
1) "201809123"
2) "201809124"
```



2. LexicoGraphical Index

```
redis-redisql:6379> zadd product.name.index 0 "Sky Pole"\
Invalid argument(s)
redis-redisql:6379> zadd product.name.index 0 "Sky Pole"
(integer) 1
redis-redisql:6379> zadd product.name.index 0 "Bunny"
(integer) 1
redis-redisql:6379> 
redis-redisql:6379> zadd product.name.index 0 "test"
(integer) 1
redis-redisql:6379> zadd product.name.index 0 "Star"
(integer) 1
redis-redisql:6379> ZRANGEBYLEX product.name.index [S +
1) "Sky Pole"
2) "Star"
3) "test"
redis-redisql:6379> ZRANGEBYLEX product.name.index [B [S
1) "Bunny"
redis-redisql:6379> 

```



3.  Hash 타입 인덱스

책대로 안됨





## 사용자 생성 및 인증/보안/Roles

- Redis 서버는 인가된 사용자만  안전하게 데이터를 관리할 수 있도록 다양한 액세스 권한과 인증 방법들을 제공하고 있음

1. **액세스 컨트롤 권한(Access Control Privilege)**

- RDB에서도 가장 기본적인 접속 권한 중에 하나가 액세스 컨트롤임
- 미리 DB내 사용자 계정과  암호를 생성해 두고 redis 서버에 접속하려는 사용자에게 계정과 암호로 인증 허가를 받음
- 클러스터를 구성하면 conf 파일 내에 requirepass, masterauth 파라미터를 통해 클러스터 전역에 인증관련 설정을 해야함

2. **인증 방법(Authorization Methd)**

- redis는 2가지 인증 방법을 제공함
- 첫번째는 os 인증방법임 conf파일에 접속할 클라이언트의 ip를 미리 지정함 
- 두번째는 internal 인증 방법임 auth 명령어로 미리 생성해둔 사용자 계정과 암호를 입력하여 권한을 부여받는 방법임
- 

