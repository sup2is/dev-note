# MongoDB 완벽 가이드

예제 참고

- https://github.com/mongodb-the-definitive-guide-3e/mongodb-the-definitive-guide-3e

# #1 몽고 DB 소개

- 몽고DB의 특징
  - 보조 인덱스
  - 범위 쿼리
  - 정렬
  - 집계
  - 공간 정보 인덱스

## 손쉬운 사용

- 도큐먼트 지향 데이터베이스
  - 몽고DB는 관계형 데이터베이스가 아니라 도큐먼트 지향 데이터베이스다.
  - 행 개념 대신에 보다 유연한 모델인 도큐먼트를 사용한다. 
  - 내장 도큐먼트와 배열을 허용함으로써 도큐먼트 지향 모델은 복잡한 계층관계를 하나의 레코드로 표현할 수 있다.
  - 이 방식은 최신 객체지향언어를 사용하는 개발자의 관점에서 매우 적합하다.
- 스키마리스
  - 고정된 스키마가 없어서 필요할때마다 쉽게 필드를 추가하거나 제거할 수 있다.
  - 스키마 리스로 개발 과정을 빠르게 반복할 수 있어서 개발 속도가 향상된다.

## 확장 가능한 설계

- 몽고 DB는 분산 확장을 염두에 두고 설계했다.
  - 도큐먼트 지향 데이터 모델은 데이터를 여러 서버에 더 쉽게 분산하게 해준다.

## 다양한 기능

`인덱싱`

- 몽고 DB는 일반적인 보조 인덱스를 지원하며 고유, 복합, 공간 정보, 전문 인덱싱 기능도 제공한다.
- nested 도큐먼트 및 배열과 같은 계층 구조의 보조 인덱스도 지원한다.
- 개발자는 모델링 기능을 자신의 애플리케이션에 가장 적합한 방식으로 최대한 활용할 수 있다.

`집계`

- 몽고DB는 데이터 처리 파이프라인 개념을 기반으로 한 집계 프레임워크를 제공한다.
- 집계 파이프라인은 데이터베이스 최적화를 최대한 활용해, 서버 측에서 비교적 간단한 일련의 단계로 데이터를 처리함으로써 복잡한 분석 엔진을 구축하게 해준다.

`특수한 컬렉션 유형`

- 몽고 DB는 로그와 같은 최신 데이터를 유지하고자 세션이나 고정 크기 컬렉션과 같이 특정 시간에 만료해야 하는 데이터에 대해 유효 시간 TTL 컬렉션을 지원한다.
- 기준 필터와 일치하는 도큐먼트에 한정된 부분 인덱스를 지원함으로써 효율성을 높이고 필요한 저장 공간을 줄인다.

`파일 스토리지`

- 몽고DB는 큰 파일과 파일 메타데이터를 편리하게 저장하는 프로토콜을 지원한다.

## 고성능

- 몽고DB의 주요 목표 = 성능
  - 몽고DB에서는 동시성과 처리량을 극대화하기 위해 와이어드타이거 스토리지 엔진에 기회적 락을 사용했다. 따라서 캐시처럼 제한된 용량의 램으로 쿼리에 알맞는 인덱스를 자동으로 선택할 수 있게 해준다.
- 몽고DB는 강력한 성능을 제공하면서도 관계형 시스템의 많은 기능을 포함한다.

## 몽고DB의 철학

- 몽고DB의 주 관심사는 확장성이 높으며 유연하고 빠른, 즉 완전한 기능을 갖춘 데이터 스토리지를 만드는 일이다.

# #2 몽고DB 기본

- 몽고DB 기본 개념
  - 몽고 DB 데이터의 기본 단위는 도큐먼트이고 이는 관계형 데이터베이스의 행과 유사하지만 더 강력하다.
  - 같은 맥락에서 몽고DB의 컬렉션은 동적 스키마가 있는 RDB 테이블과 같다.
  - 몽고DB단일 인스턴스는 자체적인 컬렉션을 갖는 여러개의 독립적인 데이터베이스를 호스팅한다.
  - 모든 도큐먼트는 컬렉션 내에서 고유한 특수키인 "_id"를 가진다.
  - 몽고DB는 몽고 셸이라는 간단하지만 강력한 도구와 함께 배포된다 몽고셸은 몽고DB인스턴스를 관리하고 몽고DB 쿼리 언어로 데이터를 조작하기 위한 내장 지원을 제공한다 또한 사용자가 다양한 목적으로 자신의 스크립트를 만들고 로드할 수 있는 완전한 기능의 자바스크립트 해석기다.

## 도큐먼트

- 몽고DB의 핵심
  - 정렬된 키와 연결된 값으로 이뤄진 도큐먼트

```json
// "greeting" 이라는 키에 "Hello, world!"라는 값을 가진 도큐먼트
{"greeting": "Hello, world!"}

// "greeting" 이라는 키에 "Hello, world!"라는 값을 갖고 "view"라는 키에 3이라는 값을 가진 도큐먼트
{"greeting": "Hello, world!", "view" : 3}
```

- 도뮤컨트의 키는 문자열이다. 다음 예외 몇가지를 제외하면 어떤 UTF-8 문자든 쓸 수 있다.
  - 키는 \\0(null)을 포함하지 않는다 \\0은 키의 끝을 나타내는 데 사용된다.
  - .과 $문자는 몇가지 특별한 속성을 가지며 특정 상황에서만 사용해야 한다. 이들은 예약어이기 떄문에 부적절하게 사용하면 드라이버에서 경고를 발생한다.
- 몽고 DB는 데이터형과 대소문자를 구별한다.
- 또한 몽고DB에서는 키가 중복될 수 없다.

```json
{"count": 5}
{"count": "5"}

{"count": 5}
{"Count": 5}
```

## 컬렉션

- 컬렉션은 도큐먼트의 모음이다.
- 몽고 DB의 도큐먼트가 관계형 데이터베이스의 행에 대응된다면 컬렉션은 테이블에 대응된다고 볼 수 있다.

### 동적 스키마

- 컬렉션은 동적 스키마를 가진다.
  
  - 하나의 컬렉션 내 도큐먼트들이 모두 다른 구조를 가질 수 있다.
  
  - ```json
    {"greeting": "Hello, world!", "view" : 3}
    {"signoff": "Good night, and good luck"}
    ```

- 그럼에도 하나의 컬렉션에 다양한 도큐먼트들을 넣지 않는 이유
  
  - 같은 컬렉션에 다른 종류의 도큐먼트를 저장하면 개발자와 관리자에게 번거로운 일이 생길수도 있다.
  - 컬렉션별로 목록을 뽑으면 한 컬렉션 내 특정 데이터형별로 쿼리해서 목록을 뽑을 때보다 훨씬 빠르다.
  - 같은 종류의 데이터를 하나의 컬렉션에 모아두면 데이터 지역성에 좋다.
  - 인덱스를 만드려면 도큐먼트는 특정 구조를 가져야 한다. 인덱스는 컬렉션별로 정의한다. 같은 유형의 도큐먼트를 하나의 컬렉션에 넣음으로써 컬렉션을 효율적으로 인덱싱할 수 있다.

### 네이밍

- 컬렉션은 이름으로 식별된다. 몇가지 제약조건
  - 빈 문자열은 유효한 컬렉션 명이 아니다.
  - \\0(null)문자는 컬렉션명의 끝을 나타내는 문자이므로 컬렉션명에 사용할 수 없다.
  - system.으로 시작하는 컬렉션명은 시스템 컬렉션에서 사용하는 예약어이므로 사용할 수 없다. 예를 들어 system.users 컬렉션에는 데이터베이스 사용자 정보가, system.namespace 컬렉션에는 데이터베이스 내 모든 컬렉션의 정보가 들어있다.
  - 사용자가 만든 컬렉션은 이름에 예약어인 $를 포함할 수 없다.

`서브 컬렉션`

- 서브컬렉션은 네임스페이스에 .(마침표) 문자를 사용해 컬렉션을 체계화할 수 있다.
  - ex: blog.post, blog.authors
  - 이는 단지 체계화하기 위함이며 blog 컬렉션이나 자식 컬렉션과는 아무런 관계가 없다. (심지어 blog 컬렉션은 없어도 된다.)
- 서브컬렉션은 특별한 속성은 없지만 여러 몽고DB 툴에서 지원하므로 유용하다.
  - 큰 파일을 저장하는 프로토콜인 GridFS는 콘텐츠 데이터와 별도로 메타데이터를 저장하는 데 서브컬렉션을 사용한다. (GridFS는 6장에서 자세히 .. )
  - 대부분의 드라이버는 특정 컬렉션의 서브컬렉션에 접근하는 몇 가지 편리한 문법을 제공한다.
- 서브컬렉션은 몽고DB의 데이터를 체계화하는 훌륭한 방법이다.

## 데이터베이스

- 몽고 DB는 데이터베이스(스키마)에 컬렉션을 그룹지어 놓는다.
  - 데이터베이스 > 컬렉션 > 도큐먼트
- 몽고 DB의 단일 인스턴스와 데이터베이스
  - 몽고 DB의 단일 인스턴스는 여러 데이터베이스를 호스팅할 수 있다.
  - 각 데이터베이스를 완전히 독립적으로 취급할 수 있다.
- 데이터베이스 명에대한 몇가지 제약
  - 빈 문자열은은 유효한 데이터베이스 이름이 아니다.
  - 데이터베이스 이름은 다음 문자를 포함할 수 없다. `\, /, ., ' ', *, <, >, :, |, ?, $, \0`
  - 데이터베이스 이름은 대소문자를 구별한다.
  - 데이터베이스 이름은 최대 64바이트다.
- 예약된 데이터베이스 이름들
  - admin: 데이터베이스 인증과 권한 부여 역할을 한다.
  - local: 단일 서버에 대한 데이터를 저장한다.
  - config: 샤딩된 몽고DB 클러스터는 config 데이터베이스를 사용해 각 샤드의 정보를 저장한다.

## 몽고DB 시작

```
docker run --name some-mongo -d mongo:tag
```

- mongod는 인수 없이 실행하면 기본 데이터 디렉토리로 /data/db 를 사용한다. 데이터 디렉토리가 존재하지 않거나 쓰기 권한이 없을 때는 서버가 시작되지 않으므로 주의하자.



## 몽고DB 셸 소개

- 몽고 DB 명령행에서는 몽고DB 인스턴스와 상호작용하는 자바스크립트 셸을 제공한다.
- 셸은 관리 기능, 실행중인 인스턴스를 점검하거나 간단한 기능을 시험하는 데 매우 유용하다.

### 셸 실행

- 셸은 완전한 자바스크립트 해석기이고 임의의 자바스크립트 프로그램을 실행한다.

```
mongo
MongoDB shell version v5.0.9

> x = 200;
200
> x / 5;
40
```

- 표준 자바스크립트 라이브러리의 모든 기능을 활용할 수 있다.

```javascript
> Math.sin(Math.PI / 2)
1
> new Date("2019/1/1");
ISODate("2019-01-01T00:00:00Z")
> "Hello, World!".replace("World", "MongoDB");
Hello, MongoDB!
```

- 자바스크립트 함수를 정의하고 호출할 수도 있다.

```javascript
> function factorial (n) {
... if (n <= 1) return 1;
... return n * factorial(n - 1);
... }
> factorial(5);
120
```

### 몽고DB 클라이언트

- 셸은 시작할 때 test 데이터베이스에 연결하고 데이터베이스 연결을 전역 변수 db에 할당한다.

```
> db
test

// video 데이터베이스 선택
> use video
switched to db video

> db
video

// db변수에서 컬렉션 접근하기
> db.movies
video.movies
```

### 셸 기본 작업

- 셸에서 데이터를 조작하거나 보려면 생성, 읽기, 갱신, 삭제의 네가지 기본적인 작업을 한다.

`생성`

- insertOne 함수는 컬렉션에 도큐먼트를 추가한다.

```json
> movie = {"title" : "Star Wars: Episode IV - A New Hope", "director" : "George Lucas", "year" : 1977}
{
    "title" : "Star Wars: Episode IV - A New Hope",
    "director" : "George Lucas",
    "year" : 1977
}
> db.movies.insertOne(movie)
{
    "acknowledged" : true,
    "insertedId" : ObjectId("62e3e6439bac1dcffb872981")
}
```

- find() 로 찾기

```json
> db.movies.find().pretty()
{
    "_id" : ObjectId("62e3e6439bac1dcffb872981"), // 갑툭튀한 _id 필드는 아래에서 설명
    "title" : "Star Wars: Episode IV - A New Hope",
    "director" : "George Lucas",
    "year" : 1977
}
```

`읽기`

- find와 findOne은 컬렉션을 쿼리하는데 사용한다
- 컬렉션에서 단일 도큐먼트를 읽으려면 findOne을 사용한다.

```json
> db.movies.findOne()
{
    "_id" : ObjectId("62e3e6439bac1dcffb872981"),
    "title" : "Star Wars: Episode IV - A New Hope",
    "director" : "George Lucas",
    "year" : 1977
}
```

- find와 findOne은 쿼리 도큐먼트 형태로 조건 전달도 가능하다.
- 셸은 기본적으로 find와 일치하는 도큐먼트를 20개까지 자동으로 출력한다. (쿼리는 4장에서 ...)

`갱신`

- 게시물을 갱신하려면 updateOne을 사용한다.
- updateOne의 매개변수
  - 첫번째는 수정할 도큐먼트를 찾는 기준이다.
  - 두 번째는 갱신 작업을 설명하는 도큐먼트다.
- 갱신을위해 $set연산자를 사용한다.

```json
> db.movies.updateOne({title : "Star Wars: Episode IV - A New Hope"}, {$set : {reviews: []}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }

> db.movies.findOne()
{
    "_id" : ObjectId("62e3e6439bac1dcffb872981"),
    "title" : "Star Wars: Episode IV - A New Hope",
    "director" : "George Lucas",
    "year" : 1977,
    "reviews" : [ ]
}
```

`삭제`

- deleteOne과 deleteMany는 도큐먼트를 데이터베이스에서 영구적으로 삭제한다.

```json
> db.movies.deleteOne({title : "Star Wars: Episode IV - A New Hope"})
{ "acknowledged" : true, "deletedCount" : 1 }

> db.movies.findOne()
null
```

## 데이터형

### 기본 데이터형

- 몽고DB 의 데이터 타입 
  - https://www.mongodb.com/docs/mongodb-shell/reference/data-types/
  - https://www.geeksforgeeks.org/datatypes-in-mongodb/
- 몽고DB는 모든 데이터를 JSON으로 표현하기 때문에 간결하지만 단점도 있다. (json에서는..)날짜형이 없어서 날짜 다루기가 일반적으로 더 성가시고 (json에서는..)부동소수점형과 정수형을 표현하는 방법은 없으며 (json에서는..)32비트와 64비트도 구별되지 않는다. (json에서는..)함수나 정규표현식과 같은 흔히 쓰는 데이터형을 표현하는 방법도 없다.

`null`

- null 값과 존재하지 않는 필드를 사용한다.

```
{"x": nul}
```

`불리언`

- 참과 거짓 값에 사용한다.

```
{"x": true}
```

`숫자`

- 셸은 64비트 부동소수점 수를 기본으로 사용한다.

```
{"x": 3.14}
{"x": 3}
```

`문자열`

- 어떤 UTF-8 문자열이든 문자열형으로 표현할 수 있다.

```
{"x": "foobar"}
```

`날짜`

- 몽고DB는 1970년 1월 1일부터의 시간을 1/1000초 단위로 나타내는 64비트 정수로 날짜를 저장한다. 표준 시간대는 저장하지 않는다.

```
{"x": new Date()}
```

`정규 표현식`

- 쿼리는 자바스크립트의 정규 표현식 문법을 사용할 수 있다.

```
{"x": /foobar/i}
```

`배열`

- 값의 셋이나 리스트를 배열로 표현할 수 있다.

```
{"x": ["a", "b", "c"]}
```

`내장 도큐먼트`

- 도큐먼트는 부모 도큐먼트의 값으로 내장된 도큐먼트 전체를 포함할 수 있다.

```
{"x": {"foo": "bar"}}
```

`객체 ID`

- 객체 ID는 도큐먼트용 12바이트 ID다.

```
{"x": ObjectId()}
```

- 상대적으로 덜 사용되는 데이터형 목록들
  - 이진데이터
  - 코드
  
    - 쿼리와 도큐먼트는 임으의 자바스크립트 코드를 포함할 수 있다.
  
    - ```
      {"x" : function() { /*...*/ }}
      ```
  
      

### 날짜

- 자바스크립트에서 Date클래스는 몽고DB의 날짜를 표현하는 데 사용한다.
- 새로운 Date 객체를 생성할 때는 반드시 new Date()를 사용해야 한다.
- 셸에서는 날짜가 현지 시간대 설정을 이용해 표시된다. 하지만 데이터베이스의 날짜는 1970년 1월1일부터의 시간을 1/10000초 단위로 저장하며 표준 시간대는 없다. (표준 시간대 정보를 또 다른 키 값으로 저장하면 되긴 한다.)

### 배열

```
{"things": ["pie", 3.14]}
```

- 배열은 서로 다른 데이터형을 값으로 포함할 수 있다.
- 배열값은 일반적인 키/값 쌍을 지원하는 어떤 데이터형 값이든 될 수 있다.
- 몽고DB에서 배열의 장점
  - 몽고DB는 배열의 구조를 이해해서 배열의 내용에 작업을 수행하기 위해 내부에 도달하는 방법을 알고 있다. 
  - 따라서 인덱스를 생성해 쿼리의 속도를 향상시킬 수 있다.
  - 배열 내부에 도달해서 원자적으로 배열의 내용을 수정하게 할 수 있다.

### 내장 도큐먼트

- 내장 도큐먼트를 사용해 데이터를 키/값 쌍의 평면적인 구조보다는 좀 더 자연스러운 방법으로 구성할 수 있다.

```json
{
  "name": "John Doe",
  "address": {
    "street": "123 Park Street",
    "city": "Anytown",
    "state": "NY"
  }
}
```

- 배열과 마찬가지로 몽고DB는 내장 도큐먼트의 구조를 이해하고 인덱스를 구성하고 쿼리하며 갱신하기 위해 내장 도큐먼트 내부에 접근한다.
- 이런 내장 도큐먼트의 특징은 데이터를 다 때려박을 수 있다는건데 정규화가 있는 관계형 데이터베이스보다 유연하다는 장점이 있지만 데이터 중복과 조인에서 얻을 수 있는 간결함을 얻을 수 없다.

### _id와 ObjectId

- 몽고DB에 저장된 모든 도큐먼트는 _id 키를 갖는다.
- _id는 어떤 데이터형도 상관없지만 기본적으로 ObjectId 가 기본이다.
- 모든 도큐먼트는 고유한 _id 값을 가지며 이 값은 컬렉션 내 모든 도큐먼트가 고유하게 식별되게 한다.

`ObjectIds`

- ObjectId를 사용하는 이유
  - 자동증가 아이디가 아니라 ObjectId를 사용하는 주요 이유는 몽고DB의 분산 특성때문이다.
  - 여러 서버에 걸쳐서 자동으로 증가하는 기본 키를 동기화하는 작업은 어렵고 시간이 걸린다.
  - 몽고DB는 분산 데이터베이스로 설계됐기 때문에 샤딩된 환경에서 고유 식별자를 생성하는 것이 매우 중요했다.
- ObjectId
  - ObjectId는 12바이트 스토리지를 사용하여 24자리 16진수 문자열 표현이 가능하다.
  - 바이트당 2자리를 사용한다. 실제로 문자열은 저장된 데이터의 두 배만큼 길다는 점을 알아두자.
- ObjectId의 타임스탬프
  - ObjectId의 첫 4바이트는 1970년 1월 1일부터의 시간을 1/1000초 단위로 저장하는 타임스탬프다.
  - 타임스탬프의 몇가지 유용한 속성
    - 타임스탬프는 그 다음 5바이트와 묶일 때 초 단위의 유일성을 제공한다.
    - 타임스탬프가 맨 처음에 온다는 것은 ObjectId가 대략 입력 순서대로 정렬된다는 의미다. 이는 확실히 보장되진 않지만 ObjectId를 인덱싱하는 장점을 얻을 수 있다.
    - 이 4바이트에는 각 도큐먼트가 생성된 때의 잠재적인 타임스탬프가 존재한다. 대부분의 드라이버는 ObjectId로부터 이런 정보를 추출하는 방법을 제공한다.
  - 타임스탬프를 사용한다고해서 서버의 시간을 동기화해야할 필요는 없다. 타임스탬프의 실제 값은 ObjectId와 상관없다.
- ObjectId의 앞9바이트는 1초동안 여러 장비와 프로세스에 걸쳐 유일성을 보장한다. 마지막 3바이트는 단순히 증분하는 숫자로 1초 내 단일 프로세스의 유일성을 보장한다.
- 고유한 ObjectId는 프로세스당 1초에 1677만개까지 생성된다.

`_id 자동생성`

- 도큐먼트를 입력할 때 "_id" 키를 명시하지 않으면 입력된 도큐먼트에 키가 자동으로 추가된다.
- 이는 몽고DB 서버에서 관리할 수 있지만 일반적으로 클라이언트 쪽 드라이버에서 관리한다.

## 몽고DB 셸 사용

- 셸은 사용자 시스템에서 연결할 수 있는 어떤 몽고DB 인스턴스든 연결할 수 있다.
- 셸을 시작할 때 호스트명, 포트, 데이터베이스를 명시해야 한다.

```
mongo some-host:30000/myDB
```

### 셸 활용 팁

- mongo는 자바스크립트 셸이므로 온라인 자바스크립트 문서를 참고하면 유용하다
- help로 셸에 내장된 도움말을 볼 수 있다.
  - 데이터베이스 수준의 도움말은 db.help()
  - 컬렉션 수준의 도움말은 db.foo.help()
- 함수의 기능을 알고 싶으면 함수명을 괄호 없이 입력하면 된다.

```javascript
> db.movies.findOne
function(query, fields, options, readConcern, collation) {
    var cursor = this.find(query, fields, -1 /* limit */, 0 /* skip*/, 0 /* batchSize */, options);

    if (readConcern) {
        cursor = cursor.readConcern(readConcern);
    }

    if (collation) {
        cursor = cursor.collation(collation);
    }

    if (!cursor.hasNext())
        return null;
    var ret = cursor.next();
    if (cursor.hasNext())
        throw Error("findOne has more than 1 result!");
    if (ret.$err)
        throw _getErrorWithCode(ret, "error " + tojson(ret));
    return ret;
}
```

### 셸에서 스크립트 실행하기

- 다음과 같이 자바스크립트 파일을 셸로 넘길 수도 있다.

```
mongo script1.js script2.js ...
```

- load 함수를 사용할 수도 있다.

```
load("script1.js")
```

- 스크립트는 db 변수에 대한 접근 권한을 가진다. 하지만 use db나 show collections와 같은 셸 보조자는 파일에서 작동하지 않는다.
- 셸 보조자와 대응하는 자바스크립트 함수

| 셸 보조자            | 같은 의미의 자바스크립트           |
| ---------------- | ----------------------- |
| use video        | db.getSisterDB("video") |
| show dbs         | db.getMongo().getDBs()  |
| show collections | db.getCollectionNames() |

- 스크립트를 사용해 셸에 변수를 입력할 수도 있다.

```javascript
//defineConnectTo.js

var connectTo = function(port, dbname) {
 if (!port) {
 port = 27017;
 }
 if (!dbname) {
 dbname = "test";
 } 
 db = connect("localhost:"+port+"/"+dbname);
 return db;
};
```

- 이 스크립트를 셸에 로드하면 connectTo가 비로소 정의된다.

```
> typeof connectTo
undefined
> load('defineConnectTo.js')
> typeof connectTo
function
```

- 기본적으로 셸은 셸을 시작한 디렉터리에서 스크립트를 찾는다. 셸을 시작한 디렉터리를 확인하려면 run("pwd") 명령어를 사용한다.
- 스크립트가 현재 디렉터리에 없다면 상대 또는 절대경로를 제공하면 된다.

### .mongorc.js 만들기

- 자주 로드되는 스크립트 (셸이 시작할 때마다 실행되는)를 .mongorc.js 파일에 넣을 수 있다.
- .mongorc.js의 역할
  - 사용하고 싶은 전역변수 설정
  - alias 설정
  - 내장 함수 재정의
  - 위험한 셸 보조자 제거 (dropDatabase, deleteIndexes ... )
- 셸을 시작할때 --norc 옵션을 사용해 .mongorc.js의 로딩을 비활성화할 수도 있다.

### 프롬프트 커스터마이징하기

- 기본 셸 프롬프트는 문자열이나 함수에 prompt 변수를 설정해 재정의한다.

```
// 마지막 작업이 완료된 시각을 얻는 프롬프트
> prompt = function() {
...   return (new Date()) + "> ";
... }
function() {
  return (new Date()) + "> ";
}
Tue Oct 18 2022 13:27:25 GMT+0000 (UTC)> 
Tue Oct 18 2022 13:27:27 GMT+0000 (UTC)> 
Tue Oct 18 2022 13:27:27 GMT+0000 (UTC)> 
Tue Oct 18 2022 13:27:28 GMT+0000 (UTC)> 
Tue Oct 18 2022 13:27:28 GMT+0000 (UTC)> 
```

- 프롬프트 함수는 문자열을 반환하고 예외를 잡는 데 주의를 기울여야 한다.
- 항상 사용자 정의 프롬프트를 사용하려면 .mongorc.js를 사용하자.

### 복잡한 변수 수정하기

- 셸에서 다중행 지원은 다소 제한적이며 이전 행들을 편집할 수 없다. 따라서 코드나 객체 블록이 크면 에디터에서 편집하는게 좋다.

```
EDITOR="/usr/bin/emacs"

// edit {변수명} 형식으로 변수를 편집할 수 있다.
var wap = db.books.findOne({title: "War and Peace"});
edit wap
```



### 불편한 컬렉션명

- 컬렉션명이 예약어가 아니거나 유효하지 않은 자바스크립트 속셩명이 아니라면 db.collectionName으로 컬렉션을 항상 가져올 수 있다.
- 그럼에도 불구하고 특정 collectionName을 사용해야하는 경우 아래와 같이 사용하면 된다.

```
db.getCollection("version")
```

- 자바스크립트의 배열 접근 구문을 사용해서 컬렉션에 접근할 수도 있다
  - 자바스크립트에서 x.y는 x['y']와 동일하다.

```
var collections = ["posts", "comments", "authors"];

for (var i in collections) {
  print(db.blog[collections[i]]);
}
```

- 접근하려는 컬렉션의 이름이 다루기 힘든 경우에는 다음과 같은 방법을 사용한다.

```
var name = "@#$%"
db[name].find()
```



# #3 도큐먼트 생성, 갱신, 삭제

## 도큐먼트 삽입

- 삽입은 몽고DB에 데이터를 추가하는 기본 방법이다.

```
db.movies.insertOne({"title" : "Stand by Me"})
```

- _id 키는 자동으로 저장된다.

### insertMany

- 여러 도큐먼트를 컬렉션에 삽입하려면 insertMany로 도큐먼트 배열을 데이터베이스에 전달한다.

```
> db.movies.drop()
true
> db.movies.insertMany([
... {"title" : "Ghostbusters"},
... {"title" : "E.T."},
... {"title" : "Blade Runner"}]);
{
    "acknowledged" : true,
    "insertedIds" : [
        ObjectId("62e486759bac1dcffb872982"),
        ObjectId("62e486759bac1dcffb872983"),
        ObjectId("62e486759bac1dcffb872984")
    ]
}
> db.movies.find()
{ "_id" : ObjectId("62e486759bac1dcffb872982"), "title" : "Ghostbusters" }
{ "_id" : ObjectId("62e486759bac1dcffb872983"), "title" : "E.T." }
{ "_id" : ObjectId("62e486759bac1dcffb872984"), "title" : "Blade Runner" }
```

- insertMany는 여러 도큐먼트를 단일 컬렉션에 삽입할 때 유용하다.
- insertMany의 두번째 파라미터인 옵션 도큐먼트
  - 도큐먼트가 제공된 순서대로 삽입되도록 옵션 도큐먼트에 "ordered"키에 true를 지정한다.
  - false를 지정하면 몽고DB가 성능을 개선하려고 삽입을 재배열할 수 있다.
  - 정렬된 삽입
    - 순서가 지정되 않았다면 정렬된 삽입이 기본 값이다. 
    - 정렬된 삽입의 경우 삽입에 전달된 배열이 삽입 순서를 정의한다.
    - 도큐먼트가 삽입 오류를 생성하면, 배열에서 해당 지점을 벗어난 도큐먼트는 삽입되지 않는다.
  - 정렬되지 않은 삽입
    - 몽고DB는 일부 삽입이 오류를 발생시키는지 여부에 관계 없이 모든 도큐먼트 삽입을 시도한다.

### 삽입 유효성 검사

- 몽고DB의 삽입 전 검사
  - _id 필드가 존재하지 않으면 새로 추가
  - 모든 도큐먼트가 16메가 바이트보다 작아야하므로 크기를 검사

### 삽입

- 몽고 3.0이전에서는 insert를 사용했다.
- 몽고 3.2이후로 insertOne과 insertMany가 들어왔다. insert를 지양하자.

## 도큐먼트 삭제

- 도큐먼트 삭제는 deleteOne과 deleteMany를 사용한다.

```
> db.movies.find()
{ "_id" : ObjectId("62e486759bac1dcffb872982"), "title" : "Ghostbusters" }
{ "_id" : ObjectId("62e486759bac1dcffb872983"), "title" : "E.T." }
{ "_id" : ObjectId("62e486759bac1dcffb872984"), "title" : "Blade Runner" }
> db.movies.deleteOne({"_id" : ObjectId("62e486759bac1dcffb872982")})
{ "acknowledged" : true, "deletedCount" : 1 }
> db.movies.find()
{ "_id" : ObjectId("62e486759bac1dcffb872983"), "title" : "E.T." }
{ "_id" : ObjectId("62e486759bac1dcffb872984"), "title" : "Blade Runner" }
db.movies.deleteMany({"year" : 1984})
db.movies.find()
```

- deleteOne은 필터와 일리하는 첫번째 도큐먼트를 삭제한다.
  - 어떤 도큐먼트가 먼저 발견되는지는 도큐먼트가 삽입된 순서, 도큐먼트에 어떤 갱신이 이뤄졌는지, 어떤 인덱스를 지정하는지 등 몇가지 요인에 따라 달라진다.
- deleteMany는 필터와 일치하는 모든 도큐먼트를 삭제한다.

```
db.mailing.list.delteMany({"opt-out" : true})
```

### drop

- deleteMany로 전체 컬렉션을 삭제할 수도 있지만 drop을 사용하는 편이 더 빠르다.

```
db.movies.deleteMany({})

db.movies.drop()
```

## 도큐먼트 갱신

- 도큐먼트를 데이터베이스에 저장한 후에는 updateOne, updateMany, replaceOne과 같은 갱신 메서드를 사용해 변경한다.
- 최후의 승리자
  - 갱신은 원자적으로 이뤄진다. 
  - 갱신 요청 두 개가 동시에 발생하면 서버에 먼저 도착한 요청이 적용된 후 다음 요청이 적용된다.
  - 결국 마지막 요청이 최후의 승리자가 되므로 도큐먼트는 변질 없이 안전하게 처리된다.

### 도큐먼트 치환

- replaceOne은 도큐먼트를 새로운것으로 완전히 치환한다.
- 대대적인 스키마 마이그레이션에 유용하다.
- replaceOne 주의할점
  - 조건절에 2개 이상의 도큐먼트가 일치되는 조건을 입력했을 경우 이때 데이터베이스는 오류를 반환하고 아무것도 변경하지 않는다.
  - 가능하다면 _id키로 일치하는 코유한 도큐먼트를 찾아 갱신 대상으로 지정하는 것이 좋다.

```
db.people.replaceOne({"_id" : "ObjectId(1234....)"}, joe)
```

### 갱신 연산자

- 부분 갱신에는 원자적 갱신연산자를 사용한다.

- $inc 제한자
  
  - 누군가가 페에지를 방문할 때마다 URL로 페이지를 찾고 "pageviews" 키의 값을 증가시키려면 $inc 제한자를 사용한다
  
  - ```
    db.analytics.updateOne({"url": "www.example.com"}, {"$inc": {"pageviews": 1}})
    ```

- 연산자를 사용할때 _id값은 변경할 수 없다. 변경하려면 도큐먼트 전체를 치환한다.

`"$set" 제한자 사용하기`

- $set 은 필드값을 설정한다. 필드가 존재하지 않으면 새 필드가 생성된다.
- 이 기능은 스키마를 갱신하거나 사용자 정의 키를 추가할 때 편리하다.

```
db.analytics.updateOne({"url": "www.example.com"}, {"$set": {"favorite book": "AAA"}})
```

- 배열로도 입력할 수 있다.

```
db.analytics.updateOne({"url": "www.example.com"}, {"$set": {"favorite book": ["AAA", "BBB"]}})
```

- 필드를 제거하려면 $unset으로 키와 값을 모두 제거할 수 있다.

```
db.analytics.updateOne({"url": "www.example.com"}, {"$unset": {"favorite book": 1})
```

- $set은 nested 도큐먼트를 변경할 때도 사용한다.
- 키를 추가, 변경, 삭제할 때는 항상 $ 제한자를 사용해야 한다.

`증가와 감소`

- $inc 연산자는 이미 존재하는 키의 값을 변경하거나 새 키를 생성하는 데 사용한다.
- $inc는 $set과 비슷하지만 숫자를 증감하기 위해 설계됐다
  - int, long, double, dcimal 타입에만 사용할 수 있다.
  - 값은 반드시 숫자여야 한다.

`배열 연산자`

- $push는 배열이 이미 존재하면 배열 끝에 요소를 추가하고 존재하지 않으면 새로운 배열을 생성한다. 

```
db.blog.posts.findOne()
db.blog.posts.updateOne({"title": "A blog post", 
  {"$push" : {"commonts" : {"name" : "joe" ...}}}
})
```

- $push와 함께 사용할 수 있는 제한자
  - $each
  - $slice
  - $sort

`배열을 집합으로 사용하기`

- 배열을 집합처럼 처리하려면 쿼리 도큐먼트에 $ne를 사용한다.

```
db.papers.updateOne({"authors cited" : {"$ne" : "Richie"}}, {"$push": {"authors cited": "Richie"}})
```

- $addToSet을 사용할 수도 있다. $addToSet을 사용하면 무슨일이 일어났는지 더 잘 알 수 있다.
- 고유한 값을 여러개 추가하려면 $addToSet과 $each를 결합해서 사용한다.

`요소 제거하기`

- $pop
  
  - 배열을 큐나 스택처럼 사용하려면 배열의 양쪽 끝에서 요소를 제거하는 $pop을 사용한다.
  
  - ```
    {"$pop" : {"key" : 1}}
    ```
    
    - 배열의 마지막부터 요소를 제거
  
  - ```
    {"$pop" : {"key" : -1}}
    ```
    
    - 배열의 처음부터 요소를 제거

- $pull
  
  - 지정된 조건에 따라 요소를 제거할때는  $pull을 사용한다.
  
  - ```
    db.lists.updateOne({}, {"$pull" : {"todo": "laundry"}})
    ```
  
  - 지정된 조건이 일치하는 모든 요소를 제거한다.

`배열의 위치 기반 변경`

- 배열 내 여러 값을 다루는 방법
  - 위치를 이용하기
  - 위치 연산자 $를 사용하기
- 배열 인덱스는 기준이 0이고 배열 요소는 인덱스를 도큐먼트의 키처럼 사용한다.
- 따라서 첫번째 댓글의 투표수를 증가시키려면 다음과 같이 사용하는데 이 쿼리는 문제가 있다.

```
// 인덱스를 기반으로 접근하면 배열의 몇 번째 요소를 변경할지 알 수 없다.
db.blog.updateOne({"post"} : post_id), {"$inc" : {"comments.0.votes" : 1}}
```

- 따라서 위 쿼리는 아래와 같이 $ 연산자를 사용하면 된다.

```
db.blog.update({"comments.author": "John"}),
{"$set" : {"comments.$.author" : "Jim"}}
```

- John이라는 사용자가 이름을 Jim으로 갱신하려고할 때, 위치 연산자를 사용해서 댓글 내 author를 갱신한다.
- 위치연산자는 첫 번째로 일치하는 요소만 갱신한다. 따라서 John이 댓글을 2개 이상 남겼다면 처음 남긴 댓글의 작성자 명만 변경된다.

`배열 필터를 이용한 갱신`

- arrayFilters
  
  - 몽고 3.6 에서는 개별 배열 요소를 갱신하는 배열 필터인 arrayFilters를 도입해 특정 조건에 맞는 배열 요소를 갱신할 수 있다.
  
  - ```
    db.blog.updateOne(
      {"post" : post_id},
      { $set: { "comments.$[elem].hidden " : true} },
      {
        arrayFilters: [ { "elem.votes": { $lte: -5 } } ]
      }
    )
    ```
  
  - comments 배열의 각 일치 요소에 대한 식별자로 elem을 정의한다. elem이 식별한 댓글의 투표값이 -5 이하면 comments 도큐먼트에 hidden 필드를 추가하고 값을 true로 설정한다.

### 갱신 입력

- 갱신입력
  
  - 갱신 조건에 맞는 도큐먼트가 존재하지 않을 때는 쿼리 도큐먼트와 갱신 도큐먼트를 합쳐서 새로운 도큐먼트를 생성한다.
  - 조건에 맞는 도큐먼트가 발견되면 일반적인 갱신을 수행한다.

- 갱신입력 == upsert
  
  - upsert에서 얻는 장점들을 다 가져올 수 있다.
    - 특정 조건을 확인하기위해 데이터베이스에 쿼리하고 쿼리의 결과를 기반으로 update 또는 insert하는 과정이 없어서 원자적이고 데이터베이스 왕복도 피할 수 있다.

- ```
  db.analytics.updateOne({"url" : "/blog"} , {"$inc" : {"pageviews" : 1}},
    {"upsert" : true})
  ```

### 다중 도큐먼트 갱신

### 갱신한 도큐먼트 반환

# #4 쿼리

## find 소개

- 몽고db 에서 find 함수는 쿼리에 사용한다.
- find의 첫 매개변수에 따라 어떤 도큐먼트를 가져올지 결정된다.
- 빈 쿼리 도큐먼트 ( {} )는 컬렉션 내 모든 것과 일치한다. 매개변수에 쿼리 도큐먼트가 없으면 find 함수는 빈 쿼리 도큐먼트를 {} 로 인식한다. 따라서 `db.c.find()` 형태의 명령은 모든 도큐먼트와 일치하므로 컬렉션 c 내 모든 도큐먼트를 반환한다.
- 쿼리 도큐먼트에 여러 키/값 쌍을 추가해 검색을 제한할 수 있다.

```
// age가 27인 모든 도큐먼트를 찾는 쿼리
db.users.find({"age" : 27})

// username이 joe인 모든 도큐먼트를 찾는 쿼리
db.users.find({"username" : "joe"})

// 여러 조건을 추가할 수 있다.
// ,는 AND 조건으로 동작한다. 
// username, age가 일치하는 모든 사용자를 찾는 쿼리
db.users.find({"username" : "joe", "age" : 27})
```

### 반환받을 키 지정

- 때떄로 반환받은 도큐먼트 내 키/값 정보가 모두 필요하지 않을 수 있는데 그럴 때는 find의 두 번째 매개변수에 원하는 키를 지정하면 된다.
- 이는 네트워크상의 데이터 전송량과 클라이언트 측에서 도큐먼트를 디코딩 하는 데 드는 시간과 메모리를 줄여준다.

```
// "username"과 "email"만 리턴받는 쿼리, _id는 지정하지 않아도 항상 반환된다.
db.users.find({}, {"username": 1, "email": 1})

// 특정 키/값 쌍을 제외한 결과를 얻고 싶을때는 값을 0으로 매칭시켜서 제외시킬 수 있다.
db.users.find({}, {"username": 0})
```

### 제약 사항

- 데이터베이스에서 쿼리 도큐먼트 값은 반드시 상수여야 한다.
- 하나의 쿼리에서 도큐먼트 내 다른 키의 값을 참조할 수 없다.

```
// 재고 도큐먼트에 재고 수량 키 "in_stock"과 판매 수량 키 "num_sold"가 있으면 키 값을 다음과 같은 쿼리로 비교할 수 없다.
db.stock.find({"in_stock": "this.num_sold"}) // 작동하지 않음
```

## 쿼리 조건

- 쿼리는 완전 일치, 범위, OR절, 부정 조건 등 더 복잡한 조건으로 검색할 수 있다.

### 쿼리 조건절

- <, <=, >, >= 에 해당하는 비교연산자
  - $lt, $lte, $gt, $gte

```
// 18세 이상 30세 이하의 사용자를 찾는 쿼리
db.users.find({"age" : {"$gte": 18, "$lte" : 30}})


// 2007년 1월 1일 이전에 등록한 사람을 찾는 쿼리
start = new Date("01/01/2007")
db.users.find({"registered" : {"$lt": start}})
```

- 키 값이 특정 값과 일치하지 않는 도큐먼트를 찾는 연산자
  - $ne (not equal)
  - $ne는 모든 데이터형에 사용할 수 있다.

```
db.users.find({"username" : {"$ne", "joe"}})
```

### OR 쿼리

- 몽고DB의 OR쿼리를 날리는 두가지 방법
  - $in
  - $or

`$in`

- 하나의 키 값에 여러개 값이 있을때 $in에 조건 배열을 사용한다.

```
// 당첨자 번호를 뽑는 쿼리
db.raffle.find({"ticket_no" : {"$in" : [725, 542, 390]}})
```

- $in은 서로 다른 데이터형도 쓸 수 있다.

```
// user_id가 12345이거나 joe인 유저를 찾는 쿼리
db.users.find({"user_id": {"$in" : [12345, "joe"]}})
```

- $in의 조건 배열에 값이 하나만 주어지면 바로 일치하는 것을 찾는다 (일반 find와 동일하게 동작)
- $nin은 $in과 반대로 배열 내 조건과 일치하지 않는 도큐먼트를 반환한다.

```
// 당첨되지 않은 사람들을 모두 반환하는 쿼리
db.raffle.find({"ticket_no" : {"$nin" : [725, 542, 390]}})
```

`$or`

- $in은 하나의 키에 대해 OR쿼리를 하고 $or는 여러개의 키에 대한 조건을 지정할 수 있다.

```
db.raffle.find({"$or": [{"ticket_no" : 725} , {"winner" : true}]})
```

- $or는 다른 조건절도 포함할 수 있다.

```
// 당첨번호가 세 번호 중 적어도 하나와 일치하거나 winner가 true인 경우를 찾는 쿼리 
db.raffle.find({"$or": [{"ticket_no" : {"$in" : [725, 542, 390]}, {"winner" : true}]})
```

- AND 쿼리에서는 최소한의 인수로 최적의 결과를 추리는게 좋지만 OR 쿼리는 반대다. 첫 번째 인수가 일치하는 도큐먼트가 많을수록 효율적이다.
- $in을 사용할 수 있다면 $in을 사용하자. 쿼리 옵티마이저는 $in을 더 효율적으로 다룬다.

### $not

- $not은 메타 조건절이며 어떤 조건에도 적용할 수 있다.

```
// $mod는 키의 값을 첫 번째 인수로 나눈 후 그 값의 조건을 두 번째 값으로 기술하는 연산자다.
// 아래 쿼리는 id_num이 1,6,11,16 등인 사용자를 반환한다.
db.users.find({"id_num" : {"$mod" : [5, 1]}})

// 아래 쿼리는 $not이 적용되어 id_num이 2,3,4,5,7,8 인 사용자를 반환한다.
db.users.find({"id_num" : {"$not" : {"$mod" : [5, 1]}}})
```

## 형 특정 쿼리

- 일부 데이터형은 쿼리 시 형에 특정하게 작동한다.

### null

- null은 스스로와 일치하는 것을 찾는다.
- 아래와 같이 도큐먼트 컬렉션이 있다고 가정했을때 null 쿼리는 아래와 같다.

```
db.c.find()
{"_id" : ... "y" : null}
{"_id" : ... "y" : 1}
{"_id" : ... "y" : 2}

db.c.find({"y" : null})
{"_id" : ... "y" : null}
```

- null은 '존재하지 않음' 과도 일치하기 떄문에 키가 null인 값을 쿼리하면 해당 키를 갖지 않는 도큐먼트도 반환한다.

```
db.c.find({"x" : null})
{"_id" : ... "y" : null}
{"_id" : ... "y" : 1}
{"_id" : ... "y" : 2}
```

- 값이 null인 키만 찾고싶다면 $exists 조건절을 사용해서 null 존재 여부를 확인해야 한다.

```
db.c.find({"z" : {"$eq" : null, "$exists" : true}})
```

### 정규 표현식

- $regex는 쿼리에서 패턴 일치 문자열을 위한 정규식 기능을 제공한다.

```
// name이 Joe나 joe인 사용자를 모두 찾는 쿼리
db.users.find({"name": {"$regex" : /joe/i}})
```

- 몽고DB는 정규 표현식 일치에 펄 호환 정규 표현식 (Perl Compatible Regular Expression  PCRE) 라이브러리를 사용하며 PCRE에서 쓸 수 있는 모든 문법은 몽고 DB에서 쓸 수 있다. 쿼리하기 전에 먼저 자브스크립트 셸로 해당 정규표현식이 의도한대로 동작하는지 확인해보는 것이 좋다.
- 몽고DB는 접두사 정규 표현식 쿼리 (ex: /^joey/ ) 에 인덱스를 활용할 수 있어 쿼리를 더 빠르게 실행할 수 있다. (대소문자 구별하는 검색 제외)
- 정규표현식 또한 스스로와 일치하는 도큐먼트를 찾을 수 있다.

### 배열에 쿼리하기

- 배열 요소 쿼리는 스칼라 쿼리와 같은 방식으로 동작하도록 설계됐다.

```
db.food.insertOne({"fruit" : ["apple", "banana, "peach"]})

// 아래와 같이 쿼리해도 일치하는 도큐먼트를 성공적으로 찾을 수 있다.
db.food.find({"fruit" : "banana"})

// 이때 배열을 {"fruit" : "apple", "fruit" : "banana", "fruit" : "peach"} 와 같은 도큐먼트로 가정하면 이해가 조금 더 쉽다.
```

`$all 연산자`

- 2개 이상의 배열 요소가 일치하는 배열을 찾으려면 $all을 사용한다.

```
db.food.insertOne({"_id" : 1, "fruit" : ["apple", "banana", "peach"]})
db.food.insertOne({"_id" : 2, "fruit" : ["apple", "kumqaut", "orange"]})
db.food.insertOne({"_id" : 3, "fruit" : ["cherry", "banana", "apple"]})

db.food.find({"fruit" : {"$all" : ["apple", "banana"]}})
{"_id" : 1, "fruit" : ["apple", "banana", "peach"]}
{"_id" : 3, "fruit" : ["cherry", "banana", "apple"]}
```

- 전체 배열과 정확하게 일치해야 하는 도큐먼트를 쿼리할 수도 있다.

```
db.food.find({"fruit" : ["apple", "banana", "peach"]})
```

- 배열 내 특정 요소를 쿼리하려면 key.index 구몬을 이용해 순서를 지정한다.

```
db.food.find({"fruit.2" : "peach"})
```

- 배열의 인덱스는 항상 0부터 시작함을 참고하자.

`$size 연산자`

- $size는 특정 크기의 배열을 쿼리하는 유용한 조건절이다.

```
db.food.find({"fruit" : {"$size" : 3}})
```

- $size는 다른 $ 조건절과 결합해서 사용할 수 없다. 필요하다면 size 필드를 추가해서 도큐먼트가 업데이트될 때마다 size값을 증가시키는 방법을 사용해야 한다.

```
db.food.update(criteria, {"$push" : ... }, "$inc" : {"size" : 1})

db.food.find({"size" : {"$gt" : 3}})
```

`$slice 연산자`

- find는 두 번째 매개변수에 반환받을 특정 기를 지정할 수 있는데 $slice 연산자를 사용해서 배열 요소의 부분집합을 반환받을 수 있다.

```
// 먼저 달린 댓글 10개를 반환하는 쿼리
db.blog.posts.findOne(criteria, {"comments" : {"$slice" : 10}})

// 나중에 달린 댓글 10개를 반환하는 쿼리
db.blog.posts.findOne(criteria, {"comments" : {"$slice" : -10}})

// 처음 23개는 건너 뛰고 24번째 요소부터 10개(24 ~ 33)를 가져오는 쿼리. 요소 개수가 없을때는 length만큼만 반환한다.
db.blog.posts.findOne(criteria, {"comments" : {"$slice" : [23, 10]}})
```

- $slice 연산자는 도큐먼트 내 모든 키를 반환한다.

`일치하는 배열 요소의 반환`

- 배열 요소의 인덱스를 알고 있다면 $slice가 유용하지만 때로는 특정 기준과 일치하는 배열 요소를 원할 수도 있다. $연산자를 사용하면일치하는 요소를 반환받을 수 있다.

```
db.blog.posts.find({"comments.name" : "bob"}, {"comments.$" : 1})
```

`배열 및 범위 쿼리의 상호작용`

- 도큐먼트 내 스칼라는 쿼리 기준의 각 절과 일치해야 한다. 하지만 도큐먼트의 필드가 배열이라면 각 절의 조건을 충족하는 도큐먼트가 일치된다.

```
{"x" : 5}
{"x" : 15}
{"x" : 25}
{"x" : [5, 25]}


// x 값이 10 ~ 20 사이인 도큐먼트를 찾는 쿼리
db.test.find({"x" : {"$gt" : 10, "$lt" : 20}})
{"x" : 15}
{"x" : [5, 25]} // 5는 20보다 작고, 25는 10보다 작기 때문에
```

- 이 방법을 사용하면 배열에 대한 범위 쿼리가 본질적으로 쓸모업성진다. 범위가 모든 다중 요소 배열과 일치하기 때문이다. 따라서 원하는 결과를 위해 몇가지 방법을 사용해야 한다.

`$elemMatch`

- $elemMatch 연산자를 사용하면 몽고DB는 두 절을 하나의 배열 요소와 비교한다. 하지만 $elemMatch는 배열만 평가한다.

```
db.test.find({"x" : {"$elemMatch" : {"$gt" : 10, "$lt" : 20}}})
// 결과 없음
```

- 보통의 경우 한 필드에 배열값과 스칼라값을 함께 저장하지 않는다. 필드에 배열만 있는 경우 $elemMatch는 배열 범위 쿼리에 유용하다.

`min, max 사용하기`

- 쿼리하는 필드에 인덱스가 있다면 min, max 함수를 사용해 $gt, $lt값 사이로 인덱스 범위를 제한해 쿼리할 수 있다.

```
db.test.find({"x" : {"$gt" : 10, "$lt" : 20}}).min({"x" : 10}).max({"x" : 20})
{"x" : 15}
```

- min과 max에 인덱스의 모든 필드를 전달해야 한다.
- 하나의 필드에 배열, 스칼라값을 함께 저장하는 경우 범위쿼리를 할 때 min, max를 사용하면 좋다.
- 배열에 대한 $gt, $lt 쿼리의 인덱스는 비효율적이다. (5장에서 자세히)

### 내장 도큐먼트에 쿼리하기

- 내장 도큐먼트쿼리 쿼리 방식
  - 도큐먼트 전체를 대상으로 하는 방식
  - 도큐먼트 내 키/값 쌍 각각을 대상으로 하는 방식

```
{
  "name" : {
    "first" : "Joe",
    "last" : "Schmoe"
  },
  "age" : 45
}

db.people.find({"name" : {"first" : "Joe", "last" : "Schmoe"}})
```

- `db.people.find({"name" : {"first" : "Joe", "last" : "Schmoe"}})` 처럼 서브도큐먼트 전체에 쿼리하려면 서브도큐먼트와 정확하게 일치해야 한다. 필드가 추가되면 전체 도큐먼트가 일치하지 않으므로 더는 작동하지 않는다.
- 내장 도큐먼트에 쿼리할 때는 가능하면 특정 키로 쿼리하는 방법이 좋다.

```
db.people.find({"name.first" : "Joe", "name.last" : "Schmoe"})
```

- 내장 도큐먼트 구조가 복잡해질수록 일치하는 내장 도큐먼트를 찾기가 어려워진다.

```
db.blog.find()
{
  "content" : "...",
  "comments" : [
    {
      "author" : "joe",
      "score" : 3,
      "comment" : "nice post"
    },
    {
      "author" : "mary",
      "score" : 6,
      "comment" : "nice post"
    }
  ]
}

// 게시물에서 5점 이상을 받는 Joe의 댓글을 찾는 예제

// comment 필드가 쿼리에 없기 때문에 일치하는 도큐먼트가 없다.
db.blog.find({"comments" : {"author" : "joe", "score" : {"$gte" : 5}}})

// 쿼리 도큐먼트에서 댓글의 score 조건과 author 조건이 댓글 배열 내의 각기 다른 도큐먼트와 일치하므로 전체 도큐먼트를 그대로 반환한다.
db.blog.find({"comments.author" : "joe", "comments.score" : {"$gte" : 5}})
```

- 이와 같은 상황에서 모든 키를 지정하지 않고도 조건을 정확하게 묶으려면  $elemMatch를 사용해야 한다.

```
// 이 조건절은 조건을 부분적으로 지정해 배열 내에서 하나의 내장 도큐먼트를 찾게 해준다.
db.blog.find({"comments" : {"$elemMatch" : {"author" : "joe", "score" : {"$gte" : 5}}}})
```

- elemMatch를 사용해 조건을 그룹화할 수 있다. 내장 도큐먼트에서 2개 이사의 키의 조건 일치 여뷰를 확인할 때만 필요하다.

## $where 쿼리

- $where 절을 사용해 임의의 자바스크립트를 쿼리의 일부분으로 실행하면 거의 모든 쿼리를 표현할 수 있다.
- 사용자가 임의의 $where절을 실행하지 못하도록 $where 절을 보안적관점에서 제한해야 한다.
- $where 절은 도큐먼트 내 두 키의 값을 비교하는 쿼리에 가장 자주 쓰인다.

```
db.foo.insertOne({"apple" : 1, "banana" : 6, "peach" : 3})
db.foo.insertOne({"apple" : 8, "spinach" : 4, "watermelon" : 4})
```

- spinach와 watermelon값이 같은값을 가질때 몽고DB에서는 이 경우 $ 조건절을 사용할 수 없으므로 $where 절 내 자바스크립트로 처리한다.

```
db.foo.find({"$where" : function () { 
    for (var current in this) { 
        for (var other in this) {
            if (current != other && this[current] == this[other]) 
                { return true; } 
        }
    }
    return false;
}});
// 함수가 true를 반환하면 해당 도큐먼트는 결과 셋에 포함되고 false를 반환하면 포함되지 않는다.
```

- $where 쿼리는 일반 쿼리보다 훨씬 느리니 반드시 필요한 경우가 아니라면 사용하지 말자.
- 가능한한 $where 절이 아닌 조건은 인덱스로 거르고 $where 절은 결과를 세부적으로 조정할 떄 사용하자.
- 몽고 3.6에서는 쿼리 언어로 집계 표현식을 사용할 수 있도록  $expr 연산자가 추가됐다. $expr을 사용하면 자바스크립트를 실행하지 않아 더 빨리 쿼리할 수 있으므로 가능한 한 $where 대신 $expr을 사용하자.

## 커서

- 데이터베이스는 커서를 사용해 find의 결과를 반환한다.
- 일반적으로 클라이언트 측의 커서 구현체는 쿼리의 최종 결과를 강력히 제한해준다.
  - 결과 개수 제한
  - 결과 중 몇 개 스킵하기
  - 정렬 등등 ..

```
for(i = 0; i < 100, i ++) {
  db.collection.insertOne({x : i});
}

// 셸에서 커서를 생성하는 방법
var cursor = db.collection.find()
```

- 셸은 결과를 한 번에 하나씩 볼 수 있다는 장점이 있다.
- 결과를 얻으러면 커서의 next 메서드를 사용하고 다른 결과가 있는지 확인하려면 hasNext를 사용한다.

```
while (cursor.hasNext()) {
  obj = cursor.next();
  // 사용자 정의 작업 수행 
}
```

- cursor는 자바스크립트의 iterator를 구현했으므로  foreach 반복문도 가능하다.
- 커서는 find를 호출할 때 셸이 데이터베이스를 즉시 쿼리하지 않고 결과를 요청하는 쿼리를 보낼때까지 기다린다. 따라서 쿼리하기 전에 옵션을 추가할 수 있다.

```
// cursor는 거의 모든 함수가 자기 자신을 리턴하므로 옵션을 어떤 순서로든 이어쓸 수 있다.
// 아래 함수는 모두 같은 역할을 한다.
var cursor = db.foo.find().sort({"x" : 1}).limit(1).skip(10);
var cursor = db.foo.find().limit(1).sort({"x" : 1}).skip(10);
var cursor = db.foo.find().skip(10).limit(1).sort({"x" : 1});


// 실제 이 시점에 쿼리가 서버로 전송된다.
cursor.hasNext()
```

- 셸은 next나 hasNext 메서드 호출 시 서버 왕복 횟수를 줄이기 위해 한 번에 처음 100개 또는 4메가바이트 크기의 결과를 가져온다.
- 클라이언트는 첫 번째 결과 셋을 살펴본 후에 셸이 데이터베이스에 다시 접근해 더 많은 결과를 요청하게 한다.

### 제한, 건너뛰기, 정렬

- 결과 개수를 제한하려면 find호출에 limit 함수를 연결한다.
- limit은 상한만 설정하고 하한은 설정하지 않는다.

```
db.foo.find().limit(100)
```

- skip은 조건에 맞는 결과 중 처음 n개를 건너뛴 나머지를 반환한다.

```
db.foo.find().skip(100)
```

- sort는 객체를 매개변수로 받는다. 정렬 방향은 1 또는 -1이다.

```
db.foo.find().sort({"date" : -1}).limit(100)
```

- skip, limit, sort를 활요해서 페이징을 사용할 수 있다.

```
// 1 page
db.foo.find().sort({"date" : -1}).limit(100).skip(0)

// 2 page
db.foo.find().sort({"date" : -1}).limit(100).skip(100)

// x page
db.foo.find().sort({"date" : -1}).limit(n).skip(x * n)
```

`비교 순서`

- 몽고DB에는 데이터형을 비교하는 위계구조가 있다. 
- 모든 형을 하나의 키에 저장할 수 있기때문에 데이터형이 섞여 있는 키는 미리정의된 순서에 따라 정렬한다.
- 데이터형 정렬 순서
  1. 최솟값
  2. null
  3. 숫자(int, long, double, decimal)
  4. 문자열
  5. 객체/도큐먼트
  6. 배열
  7. 이진 데이터
  8. 객체 ID
  9. 불리언
  10. 날짜
  11. 타임스탬프
  12. 정규표현식
  13. 최댓값

### 많은 수의 건너뛰기 피하기

- 대부분의 데이터베이스는 skip을 위해 인덱스 안에 메타데이터를 저장하지만 몽고DB는 아직 해당 기능을 지원하지 않는다.
- 따라서 많은 수의 skip은 피해야 한다.

`skip을 사용하지 않고 페이지 나누기`

```
// 내림차순 정렬로 페이지 가져오기
var page1 = db.foo.find().sort({"date" : -1}).limit(100)

// display first page
while (page1.hasNext()) {
 latest = page1.next(); // 최신 도큐먼트 포인터 저장
 display(latest);
}

// 최신 도큐먼트 포인터를 기반으로 lt 쿼리
var page2 = db.foo.find({"date" : {"$lt" : latest.date}});
page2.sort({"date" : -1}).limit(100);
```

`랜덤으로 도큐먼트 찾기`

```
// 전체 페이지 사이즈를 가져오고 0~size 의 랜덤수를 만들어서 쿼리하는 예제
// 이렇게 쓰지 말자.
var total = db.foo.count()
var random = Math.floor(Math.random()*total)
db.foo.find().skip(random).limit(1)
```

- 도큐먼트를 입력할 때 랜덤 키를 별도로 추가하는 방법으로 해결할 수 있다.

```
db.people.insertOne({"name" : "joe", "random" : Math.random()})
db.people.insertOne({"name" : "john", "random" : Math.random()})
db.people.insertOne({"name" : "jim", "random" : Math.random()})

var random = Math.random()
result = db.people.findOne({"random" : {"$gt" : random}})
if (result == null) {
  result = db.people.findOne({"random" : {"$lte" : random}})
}
```

### 종료되지 않는 커서

- 커서 종류
  - 클라이언트 커서
  - 데이터베이스 커서 (서버 커서)
- 서버 측에서 보면 커서는 메모리와 리소스를 점유한다. 커서가 더는 가져올 결과가 없거나 클라이언트로부터 종료 요청을 받으면 데이터베이스는 점유하고 있던 리소스를 해제한다.
- 서버 커서를 종료하는 조건
  1. 커서는 조건에 일치하는 결과를 모두 살펴본 후에는 스스로 정리한다.
  2. 클라이언트측에서 유효 영역을 벗어나면 드라이버는 데이터베이스에 메시지를 보내 커서를 종료해도 된다고 알린다.
  3. 사용자가 아직 결과를 다 살펴보지 않았고 커서가 여전히 유효 영역 내에 있더라도 10분 동안 활동이 없으면 데이터베이스 커스는 자동으로 죽는다.
- 3번처럼 타임아웃에 의한 종료는 바람직한 동작이지만 때떄로 커서를 오래 남겨두고 싶을 때가 있다. 따라서 많은 드라이는 데이터베이스가 커서를 타임아웃시키지 못하게 하는 immortal이라는 함수를 제공한다. 커서의 타임아웃을 비활성화했다면 결과를 모두살펴보거나 커서를 명확히 종료해야 한다. 그렇지 않으면 커서는 데이터베이스에 남아 서버가 재시작할때까지 리소스를차지한다.

# #5 인덱싱

- 이 장에서 알 수 있는 것들
  
  - 인덱싱의 정의와 사용하는 이유
  
  - 인덱싱할 필드를 선정하는 방법
  
  - 인덱스 사용을 평가하고 적용하는 방법
  
  - 인덱스 생성 및 제거에 대한 관리 정보



> 몽고DB에서 인덱스는 전형적인 RDB의 인덱스와 거의 동일하게 작동한다. 이러한 인덱스에 익숙하다면 구문 명세에 대한 내용은 가볍게 읽고 넘겨도 된다.



## 인덱싱 소개

- 데이터베이스 인덱스는 책의 인덱스와 유사하다.

- 컬렉션 스캔
  
  - 인덱스를 사용하지 않는 쿼리를 컬렉션 스캔이라고 한다.
  
  - 큰 컬렉션을 스캔할 때는 컬렉션 스캔이 매우 느려지니 이런 방식은 피해야 한다.

- explain
  
  - 쿼리를 실행할때 explain 함수를 이용해 몽고DB가 무엇을 하는지 확인할 수 있다.
  
  - explain은 명령을 감싸는 커서 보조자 메서드와 사용하면 좋다.
  
  - explain 커서 메서드는 다양한 CRUD 작업의 실행 정보를 제공한다.
  
  - explain은 여러가지 모드에서 실행될 수 있는데 executionStats 모드는 인덱스를 이용한 쿼리의 효과를 이해하는데 도움이 된다.

```
 
 // 100만건의 users 데이터 셋팅  

 for (i=0; i<1000000; i++) {  
   db.users.insertOne(  
     {  
       "i" : i,  
       "username" : "user" + i,  
       "age" : Math.floor(Math.random()*120),  
       "created" : new Date()  
     }  
   )  
 }  

 db.users.find({"username" : "user10"}).explain("executionStats")  
 {  
   "explainVersion" : "1",  
   "queryPlanner" : {  
     "namespace" : "test.users",  
     "indexFilterSet" : false,  
     "parsedQuery" : {  
       "username" : {  
         "$eq" : "user10"  
       }  
     },  
     "maxIndexedOrSolutionsReached" : false,  
     "maxIndexedAndSolutionsReached" : false,  
     "maxScansToExplodeReached" : false,  
     "winningPlan" : {  
       "stage" : "COLLSCAN",  
       "filter" : {  
         "username" : {  
           "$eq" : "user10"  
         }  
       },  
       "direction" : "forward"  
     },  
     "rejectedPlans" : [ ]  
   },  
   "executionStats" : {  
     "executionSuccess" : true,  
     "nReturned" : 2,  
     "executionTimeMillis" : 50,  
     "totalKeysExamined" : 0,  
     "totalDocsExamined" : 85188,  
     "executionStages" : {  
       "stage" : "COLLSCAN",  
       "filter" : {  
         "username" : {  
           "$eq" : "user10"  
         }  
       },  
       "nReturned" : 2,  
       "executionTimeMillisEstimate" : 2,  
       "works" : 85190,  
       "advanced" : 2,  
       "needTime" : 85187,  
       "needYield" : 0,  
       "saveState" : 85,  
       "restoreState" : 85,  
       "isEOF" : 1,  
       "direction" : "forward",  
       "docsExamined" : 85188  
     }  
   },  
   "command" : {  
     "find" : "users",  
     "filter" : {  
       "username" : "user10"  
     },  
     "$db" : "test"  
   },  
   "serverInfo" : {  
     "host" : "8630ee4ab21c",  
     "port" : 27017,  
     "version" : "5.0.9",  
     "gitVersion" : "6f7dae919422dcd7f4892c10ff20cdc721ad00e6"  
   },  
   "serverParameters" : {  
     "internalQueryFacetBufferSizeBytes" : 104857600,  
     "internalQueryFacetMaxOutputDocSizeBytes" : 104857600,  
     "internalLookupStageIntermediateDocumentMaxSizeBytes" : 104857600,  
     "internalDocumentSourceGroupMaxMemoryBytes" : 104857600,  
     "internalQueryMaxBlockingSortMemoryUsageBytes" : 104857600,  
     "internalQueryProhibitBlockingMergeOnMongoS" : 0,  
     "internalQueryMaxAddToSetBytes" : 104857600,  
     "internalDocumentSourceSetWindowFieldsMaxMemoryBytes" : 104857600  
   },  
   "ok" : 1  
 }  

```

- executionStats.totalDocsExamined
  
  - 몽고DB가 쿼리를 실행하면서 살펴본 도큐먼트 개수이다. 이는 컬렉션에 들어있는 모든 도큐먼트 개수와 같다. 몽고 DB는 모든 도큐먼트 안에 있는 모든 필드를 살펴본다.

- executionStats.nReturned
  
  - 반환받은 결과의 개수



```
//인덱스 적용하기
db.users.createIndex({"username" : 1})
{
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"createdCollectionAutomatically" : false,
	"ok" : 1
}
```



```
db.users.find({"username" : "user10"}).explain("executionStats")  
{
	"explainVersion" : "1",
	"queryPlanner" : {
		"namespace" : "test.users",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"username" : {
				"$eq" : "user10"
			}
		},
		"maxIndexedOrSolutionsReached" : false,
		"maxIndexedAndSolutionsReached" : false,
		"maxScansToExplodeReached" : false,
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"username" : 1
				},
				"indexName" : "username_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"username" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"username" : [
						"[\"user10\", \"user10\"]"
					]
				}
			}
		},
		"rejectedPlans" : [ ]
	},
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 2,
		"executionTimeMillis" : 9,
		"totalKeysExamined" : 2,
		"totalDocsExamined" : 2,
		"executionStages" : {
			"stage" : "FETCH",
			"nReturned" : 2,
			"executionTimeMillisEstimate" : 0,
			"works" : 3,
			"advanced" : 2,
			"needTime" : 0,
			"needYield" : 0,
			"saveState" : 0,
			"restoreState" : 0,
			"isEOF" : 1,
			"docsExamined" : 2,
			"alreadyHasObj" : 0,
			"inputStage" : {
				"stage" : "IXSCAN",
				"nReturned" : 2,
				"executionTimeMillisEstimate" : 0,
				"works" : 3,
				"advanced" : 2,
				"needTime" : 0,
				"needYield" : 0,
				"saveState" : 0,
				"restoreState" : 0,
				"isEOF" : 1,
				"keyPattern" : {
					"username" : 1
				},
				"indexName" : "username_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"username" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"username" : [
						"[\"user10\", \"user10\"]"
					]
				},
				"keysExamined" : 2,
				"seeks" : 1,
				"dupsTested" : 0,
				"dupsDropped" : 0
			}
		}
	},
	"command" : {
		"find" : "users",
		"filter" : {
			"username" : "user10"
		},
		"$db" : "test"
	},
	"serverInfo" : {
		"host" : "8630ee4ab21c",
		"port" : 27017,
		"version" : "5.0.9",
		"gitVersion" : "6f7dae919422dcd7f4892c10ff20cdc721ad00e6"
	},
	"serverParameters" : {
		"internalQueryFacetBufferSizeBytes" : 104857600,
		"internalQueryFacetMaxOutputDocSizeBytes" : 104857600,
		"internalLookupStageIntermediateDocumentMaxSizeBytes" : 104857600,
		"internalDocumentSourceGroupMaxMemoryBytes" : 104857600,
		"internalQueryMaxBlockingSortMemoryUsageBytes" : 104857600,
		"internalQueryProhibitBlockingMergeOnMongoS" : 0,
		"internalQueryMaxAddToSetBytes" : 104857600,
		"internalDocumentSourceSetWindowFieldsMaxMemoryBytes" : 104857600
	},
	"ok" : 1
}

```



- executionStats.totalDocsExamined, executionStats.nReturned 의 수가 많이 줄어들고 쿼리는 이제 거의 즉각적으로 반환되는것을 확인할 수 있다.

- 단점은 인덱싱된 필드를 변경하는 쓰기 작업은 더 오래걸린다. 데이터가 변경될 때마다 해당 필드의 모든 인덱스를 갱신해야 하기 때문이다. 따라서 특정 상황에 따라 어떤 필드를 인덱싱하는게 적합한지 신중히 파악해야 한다.

- 인덱스를 생성할 대상 필드를 선택하려면, 자주 쓰는 쿼리와 빨리 수행해야 하는 쿼리를 조사해 공통적인 키 셋을 찾아보자. 자주 쓰이는 읽기 쿼리는 인덱싱하는게 일반적으로 좋다.



### 복합 인덱스 소개

- 상당수의 쿼리 패턴은 두 개 이상의 키를 기반으로 인덱스를 작성하는데 인덱스는 모든 값을 정렬된 순서로 보관하므로 정렬시에 필드 선언 순서가 중요하다.

```
// username이 정렬된 상황에서 아래와 같은 쿼리를 날리면 age가 먼저 정렬되고 
// username으로 정렬하기 때문에 인덱스를 사용하는 효과가 거의 없다.
db.users.find().sort({"age" : 1, "username" : 1})

// 따라서 age, username에 인덱스를 만들어줘야 한다.
db.users.createIndex({"age" : 1, "username" : 1})
```

- 필드가 2개 이상인 인덱스를 복합 인덱스라고 한다. 복합 인덱스는 쿼리에서 정렬 방향이 여러 개이거나 검색 조건에 여러 개의 키가 있을 때 유용하다.



`db.users.find({"age" : 21}).sort({"username" : -1})`

- 단일 값을 찾는 동등쿼리이다.
- age, username에 인덱스가 잡혀있으므로 age와 일치하는 도큐먼트에서 username 마지막 항목부터 역순으로 인덱스를 탐색한다.
- 이런 쿼리는 username을 따로 정렬할 필요가 없기 때문에 매우 효율적이다.
- 몽고DB는 인덱스를 어느 방향으로도 쉽게 탐색하므로 정렬 방향은 문제가 되지 않는다.

```
> db.users.find({"age" : 21}).sort({"username" : -1}).explain()
{
	"explainVersion" : "1",
	"queryPlanner" : {
		"namespace" : "test.users",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"age" : {
				"$eq" : 21
			}
		},
		"queryHash" : "50DD14CF",
		"planCacheKey" : "05FCC829",
		"maxIndexedOrSolutionsReached" : false,
		"maxIndexedAndSolutionsReached" : false,
		"maxScansToExplodeReached" : false,
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"age" : 1,
					"username" : 1
				},
				"indexName" : "age_1_username_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"age" : [ ],
					"username" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "backward",
				"indexBounds" : {
					"age" : [
						"[21.0, 21.0]"
					],
					"username" : [
						"[MaxKey, MinKey]"
					]
				}
			}
		},
		"rejectedPlans" : [ ]
	},
	"command" : {
		"find" : "users",
		"filter" : {
			"age" : 21
		},
		"sort" : {
			"username" : -1
		},
		"$db" : "test"
	},
	"serverInfo" : {
		"host" : "8630ee4ab21c",
		"port" : 27017,
		"version" : "5.0.9",
		"gitVersion" : "6f7dae919422dcd7f4892c10ff20cdc721ad00e6"
	},
	"serverParameters" : {
		"internalQueryFacetBufferSizeBytes" : 104857600,
		"internalQueryFacetMaxOutputDocSizeBytes" : 104857600,
		"internalLookupStageIntermediateDocumentMaxSizeBytes" : 104857600,
		"internalDocumentSourceGroupMaxMemoryBytes" : 104857600,
		"internalQueryMaxBlockingSortMemoryUsageBytes" : 104857600,
		"internalQueryProhibitBlockingMergeOnMongoS" : 0,
		"internalQueryMaxAddToSetBytes" : 104857600,
		"internalDocumentSourceSetWindowFieldsMaxMemoryBytes" : 104857600
	},
	"ok" : 1
}

```

- winningPlan 도큐먼트를 확인해보면 plan을 대락적으로 확인할 수 있다.



`db.users.find({"age" : {"$gte" : 21, "$lte" : 30}})`

```
> db.users.find({"age" : {"$gte" : 21, "$lte" : 30}}).explain()
{
	"explainVersion" : "1",
	"queryPlanner" : {
		"namespace" : "test.users",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"$and" : [
				{
					"age" : {
						"$lte" : 30
					}
				},
				{
					"age" : {
						"$gte" : 21
					}
				}
			]
		},
		"queryHash" : "0ED8ACDE",
		"planCacheKey" : "982CCEC6",
		"maxIndexedOrSolutionsReached" : false,
		"maxIndexedAndSolutionsReached" : false,
		"maxScansToExplodeReached" : false,
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"age" : 1,
					"username" : 1
				},
				"indexName" : "age_1_username_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"age" : [ ],
					"username" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"age" : [
						"[21.0, 30.0]"
					],
					"username" : [
						"[MinKey, MaxKey]"
					]
				}
			}
		},
		"rejectedPlans" : [ ]
	},
	"command" : {
		"find" : "users",
		"filter" : {
			"age" : {
				"$gte" : 21,
				"$lte" : 30
			}
		},
		"$db" : "test"
	},
	"serverInfo" : {
		"host" : "8630ee4ab21c",
		"port" : 27017,
		"version" : "5.0.9",
		"gitVersion" : "6f7dae919422dcd7f4892c10ff20cdc721ad00e6"
	},
	"serverParameters" : {
		"internalQueryFacetBufferSizeBytes" : 104857600,
		"internalQueryFacetMaxOutputDocSizeBytes" : 104857600,
		"internalLookupStageIntermediateDocumentMaxSizeBytes" : 104857600,
		"internalDocumentSourceGroupMaxMemoryBytes" : 104857600,
		"internalQueryMaxBlockingSortMemoryUsageBytes" : 104857600,
		"internalQueryProhibitBlockingMergeOnMongoS" : 0,
		"internalQueryMaxAddToSetBytes" : 104857600,
		"internalDocumentSourceSetWindowFieldsMaxMemoryBytes" : 104857600
	},
	"ok" : 1
}
```



`db.users.find({"age" : {"$gte" : 21, "$lte" : 30}}).sort({"username" : 1}).explain()`

- 이 쿼리는 username을 정렬된 순서로 반환하지 않으며 쿼리는 username에 따라 정렬된 결과를 요청한다.
- 몽고DB가 이미 원하는 순서대로 도큐먼트가 정렬된 인덱스를 단순히 통과하지 않고 결과를 반환하기 전에 메모리에서 정렬해야 함을 의미한다. 따라서 일반적으로 이전 쿼리보다 비효율적이다.
- 얼마나 많은 도큐먼트를 정렬해야 하는지에 따라 쿼리의 속도가 달라진다. 결과가 32메가바이트 이상이면 오류가 발생하므로 sort에 limit을 함께 사용해 결과를 32메가바이트 이하로 줄여야한다.

```
db.users.find({"age" : {"$gte" : 21, "$lte" : 30}}).sort({"username" : 1}).explain()
{
	"explainVersion" : "1",
	"queryPlanner" : {
		"namespace" : "test.users",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"$and" : [
				{
					"age" : {
						"$lte" : 30
					}
				},
				{
					"age" : {
						"$gte" : 21
					}
				}
			]
		},
		"queryHash" : "939A6D68",
		"planCacheKey" : "FE894A2C",
		"maxIndexedOrSolutionsReached" : false,
		"maxIndexedAndSolutionsReached" : false,
		"maxScansToExplodeReached" : false,
		"winningPlan" : {
			"stage" : "FETCH",
			"filter" : {
				"$and" : [
					{
						"age" : {
							"$lte" : 30
						}
					},
					{
						"age" : {
							"$gte" : 21
						}
					}
				]
			},
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"username" : 1
				},
				"indexName" : "username_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"username" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"username" : [
						"[MinKey, MaxKey]"
					]
				}
			}
		},
		"rejectedPlans" : [
			{
				"stage" : "FETCH",
				"inputStage" : {
					"stage" : "SORT",
					"sortPattern" : {
						"username" : 1
					},
					"memLimit" : 104857600,
					"type" : "default",
					"inputStage" : {
						"stage" : "IXSCAN",
						"keyPattern" : {
							"age" : 1,
							"username" : 1
						},
						"indexName" : "age_1_username_1",
						"isMultiKey" : false,
						"multiKeyPaths" : {
							"age" : [ ],
							"username" : [ ]
						},
						"isUnique" : false,
						"isSparse" : false,
						"isPartial" : false,
						"indexVersion" : 2,
						"direction" : "forward",
						"indexBounds" : {
							"age" : [
								"[21.0, 30.0]"
							],
							"username" : [
								"[MinKey, MaxKey]"
							]
						}
					}
				}
			}
		]
	},
	"command" : {
		"find" : "users",
		"filter" : {
			"age" : {
				"$gte" : 21,
				"$lte" : 30
			}
		},
		"sort" : {
			"username" : 1
		},
		"$db" : "test"
	},
	"serverInfo" : {
		"host" : "8630ee4ab21c",
		"port" : 27017,
		"version" : "5.0.9",
		"gitVersion" : "6f7dae919422dcd7f4892c10ff20cdc721ad00e6"
	},
	"serverParameters" : {
		"internalQueryFacetBufferSizeBytes" : 104857600,
		"internalQueryFacetMaxOutputDocSizeBytes" : 104857600,
		"internalLookupStageIntermediateDocumentMaxSizeBytes" : 104857600,
		"internalDocumentSourceGroupMaxMemoryBytes" : 104857600,
		"internalQueryMaxBlockingSortMemoryUsageBytes" : 104857600,
		"internalQueryProhibitBlockingMergeOnMongoS" : 0,
		"internalQueryMaxAddToSetBytes" : 104857600,
		"internalDocumentSourceSetWindowFieldsMaxMemoryBytes" : 104857600
	},
	"ok" : 1
}

```

- 같은 키를 역순으로 한 {"username" : 1, "age" : 1} 인덱스 또한 사용할 수 있다.
- 이때 몽고DB는 모든 인덱스 항목을 탐색하지만 도큐먼트를 재정렬하지 않는다. 이는 거대한 이메모리 정렬이 필요하지 않다는 장점이 있다.
- 복합 인덱스를 구성할 떄는 정렬 키를 첫번째에 놓으면 좋다.



### 몽고 DB가 인덱스를 선택하는 방법

- 5개의 인덱스가 있다고 가정해보자.

1. 쿼리가 들어오면 몽고DB는 쿼리 모양을 확인한다. 모양은 검색할 필드와 정렬 여부 등 추가 정보와 관련 있다. 시스템은 이 정보를 기반으로 쿼리를 충족하는 데 사용할 인덱스 후보 집합을 식별한다.
2. 1번에서 5개중 3개가 쿼리 후보로 식별됐다고 가정하면 몽고DB는 각 인덱스 후보에 하나씩 총 3개의 쿼리 플랜을 만들고 각각 다른 인덱스를 사용하는 3개의 병렬 스레드에서 쿼리를 실행한다. 
3. 가장 먼저 목표 상태에 도달하는 쿼리 플랜이 승자가 된다.

- 가장 먼저 목표상태에 도달한 쿼리 플랜은 동일한 모양을 가진 쿼리에 사용될 인덱스로 선택된다는 점이 중요하다.
- 플랜은 일정 기간동안 서로 경쟁하며 각 레이스의 결과로 전체 승리 플랜을 산출한다.
- 쿼리 스레드가 레이스에서 이기는 방법
  - 모든 쿼리 결과를 가장 먼저 반환하기
  - 결과에 대한 시범 횟수를 정렬 순서로 가장 먼저 반환하기



사진

- 여러 쿼리 플랜이 서로 경쟁함으로써 모양이 동일한 후속 쿼리가 있을 때 몽고DB 서버에서 어떤 인덱스를 선택할지 알 수 있다.
- 서버는 승리한 플랜을 차후 모양이 같은 쿼리에 사용하기 위해 캐시에 저장된다. 시간이 지나 컬렉션과 인덱스가 변경되면 쿼리 플랜이 캐시에서 제거되고, 몽고DB는 다시 가능한 쿼리 플랜을 실험해 해당 컬렉션 및 인덱스 집합에 가장 적합한 플랜을 찾는다.
- 쿼리 플랜이 삭제되는 case
  - 인덱스를 삭제, 추가하는 경우
  - mongod 프로세스를 다시 시작할때
- 쿼리 플랜 캐시는 명시적으로 지울 수 있다.



### 복합 인덱스 사용

- 인덱스를 올바르게 설계하려면 실제 워크로드에서 인덱스를 테스트하고 조정해야 하지만 몇 가지 모범 사례를 적용해볼 수 있다.

```
// 100만건의 students 데이터 셋 생성하기
for (i=0; i<1000000; i++) {  
    db.students.insertOne(  
       {  
          "student_id" : i,  
          "scores" : [
            {
              "type" : "exam",
              "score" : Math.floor(Math.random()*100)
            },
            {
              "type" : "quiz",
              "score" : Math.floor(Math.random()*100)
            },
            {
              "type" : "homework",
              "score" : Math.floor(Math.random()*100)
            },
            {
              "type" : "homework",
              "score" : Math.floor(Math.random()*100)
            },
          ],
          "class_id" : Math.floor(Math.random()*120),  
          "created" : new Date()  
       }  
    )  
}  

// 인덱스 생성
db.students.createIndex({"class_id": 1})
{
	"numIndexesBefore" : 1,
	"numIndexesAfter" : 2,
	"createdCollectionAutomatically" : false,
	"ok" : 1
}
db.students.createIndex({"student_id": 1, "class_id": 1})
{
	"numIndexesBefore" : 2,
	"numIndexesAfter" : 3,
	"createdCollectionAutomatically" : false,
	"ok" : 1
}

```

- 잘못된 쿼리 사용하기

```
db.students.find({"student_id" : {"$gt": 500000}, "class_id": 54}).sort({"student_id": 1}).explain("executionStats")
{
	"explainVersion" : "1",
	"queryPlanner" : {
		"namespace" : "test.students",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"$and" : [
				{
					"class_id" : {
						"$eq" : 54
					}
				},
				{
					"student_id" : {
						"$gt" : 500000
					}
				}
			]
		},
		"maxIndexedOrSolutionsReached" : false,
		"maxIndexedAndSolutionsReached" : false,
		"maxScansToExplodeReached" : false,
		"winningPlan" : {
			"stage" : "SORT",
			"sortPattern" : {
				"student_id" : 1
			},
			"memLimit" : 104857600,
			"type" : "simple",
			"inputStage" : {
				"stage" : "FETCH",
				"filter" : {
					"student_id" : {
						"$gt" : 500000
					}
				},
				"inputStage" : {
					"stage" : "IXSCAN",
					"keyPattern" : {
						"class_id" : 1
					},
					"indexName" : "class_id_1",
					"isMultiKey" : false,
					"multiKeyPaths" : {
						"class_id" : [ ]
					},
					"isUnique" : false,
					"isSparse" : false,
					"isPartial" : false,
					"indexVersion" : 2,
					"direction" : "forward",
					"indexBounds" : {
						"class_id" : [
							"[54.0, 54.0]"
						]
					}
				}
			}
		},
		"rejectedPlans" : [
			{
				"stage" : "FETCH",
				"inputStage" : {
					"stage" : "IXSCAN",
					"keyPattern" : {
						"student_id" : 1,
						"class_id" : 1
					},
					"indexName" : "student_id_1_class_id_1",
					"isMultiKey" : false,
					"multiKeyPaths" : {
						"student_id" : [ ],
						"class_id" : [ ]
					},
					"isUnique" : false,
					"isSparse" : false,
					"isPartial" : false,
					"indexVersion" : 2,
					"direction" : "forward",
					"indexBounds" : {
						"student_id" : [
							"(500000.0, inf.0]"
						],
						"class_id" : [
							"[54.0, 54.0]"
						]
					}
				}
			}
		]
	},
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 4022,
		"executionTimeMillis" : 38,
		"totalKeysExamined" : 8237,
		"totalDocsExamined" : 8237,
		"executionStages" : {
			"stage" : "SORT",
			"nReturned" : 4022,
			"executionTimeMillisEstimate" : 4,
			"works" : 12261,
			"advanced" : 4022,
			"needTime" : 8238,
			"needYield" : 0,
			"saveState" : 20,
			"restoreState" : 20,
			"isEOF" : 1,
			"sortPattern" : {
				"student_id" : 1
			},
			"memLimit" : 104857600,
			"type" : "simple",
			"totalDataSizeSorted" : 1065830,
			"usedDisk" : false,
			"inputStage" : {
				"stage" : "FETCH",
				"filter" : {
					"student_id" : {
						"$gt" : 500000
					}
				},
				"nReturned" : 4022,
				"executionTimeMillisEstimate" : 4,
				"works" : 8238,
				"advanced" : 4022,
				"needTime" : 4215,
				"needYield" : 0,
				"saveState" : 20,
				"restoreState" : 20,
				"isEOF" : 1,
				"docsExamined" : 8237,
				"alreadyHasObj" : 0,
				"inputStage" : {
					"stage" : "IXSCAN",
					"nReturned" : 8237,
					"executionTimeMillisEstimate" : 2,
					"works" : 8238,
					"advanced" : 8237,
					"needTime" : 0,
					"needYield" : 0,
					"saveState" : 20,
					"restoreState" : 20,
					"isEOF" : 1,
						"keyPattern" : {
						"class_id" : 1
					},
					"indexName" : "class_id_1",
					"isMultiKey" : false,
					"multiKeyPaths" : {
						"class_id" : [ ]
					},
					"isUnique" : false,
					"isSparse" : false,
					"isPartial" : false,
					"indexVersion" : 2,
					"direction" : "forward",
					"indexBounds" : {
						"class_id" : [
							"[54.0, 54.0]"
						]
					},
					"keysExamined" : 8237,
					"seeks" : 1,
					"dupsTested" : 0,
					"dupsDropped" : 0
				}
			}
		}
	},
	"command" : {
		"find" : "students",
		"filter" : {
			"student_id" : {
				"$gt" : 500000
			},
			"class_id" : 54
		},
		"sort" : {
			"student_id" : 1
		},
		"$db" : "test"
	},
	"serverInfo" : {
		"host" : "8630ee4ab21c",
		"port" : 27017,
		"version" : "5.0.9",
		"gitVersion" : "6f7dae919422dcd7f4892c10ff20cdc721ad00e6"
	},
	"serverParameters" : {
		"internalQueryFacetBufferSizeBytes" : 104857600,
		"internalQueryFacetMaxOutputDocSizeBytes" : 104857600,
		"internalLookupStageIntermediateDocumentMaxSizeBytes" : 104857600,
		"internalDocumentSourceGroupMaxMemoryBytes" : 104857600,
		"internalQueryMaxBlockingSortMemoryUsageBytes" : 104857600,
		"internalQueryProhibitBlockingMergeOnMongoS" : 0,
		"internalQueryMaxAddToSetBytes" : 104857600,
		"internalDocumentSourceSetWindowFieldsMaxMemoryBytes" : 104857600
	},
	"ok" : 1
}

```

- executionStats.totalKeysExamined를 확인해보면 executionStats.totalKeysExamined와 executionStats.nReturned와 비교하면 몽고DB가 결과 셋을 생성하기 위해 인덱스 내에서 몇 개의 키를 통과했는지 비교할 수 있다.
  - 책에 있는 예제에서는 도큐먼트 9903개를 찾으려고 인덱스키 85만 477개를 검사했고 executionTimeMillis가 4.3초걸렸다.
  - 책이랑 똑같은 데이터셋에 같은 인덱스, 같은 쿼리였는데 다른걸보니 몽고DB 옵티마이저가 많이 좋아졌나보다 .. 아니면 데이터 셋이 문제가 있거나?
- explain 출력에는 선정된 쿼리 플랜(winningPlan)이 있다. 이 선정된 쿼리 플랜은 몽고DB가 쿼리를 충족하는데 사용한 단계를 설명한다.
- 선정된 쿼리 플랜 아래에는 거부된 플랜(rejectedPlans)이 있다.
- hint 지정하기
  - 몽고DB는 데이터베이스가 특정 인덱스를 사용하도록 강제하는 두 가지 방법을 제공한다.
  - 그러나 쿼리 플래너의 결과를 재정의하는 방법은 매우 신중히 사용해야 한다. 이런 기술은 운영환경에서 사용해서는 안된다.
  - 커서 hint 메서드를 사용하면 모양이나 이름을 지정함으로써 사용할 인덱스를 지정할 수 있다.

```
db.students.find({"student_id" : {"$gt": 500000}, "class_id": 54}).sort({"student_id": 1}).hint("student_id_1_class_id_1").explain("executionStats")

db.students.find({"student_id" : {"$gt": 500000}, "class_id": 54}).sort({"student_id": 1}).explain("executionStats")
{
	"explainVersion" : "1",
	"queryPlanner" : {
		"namespace" : "test.students",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"$and" : [
				{
					"class_id" : {
						"$eq" : 54
					}
				},
				{
					"student_id" : {
						"$gt" : 500000
					}
				}
			]
		},
		"maxIndexedOrSolutionsReached" : false,
		"maxIndexedAndSolutionsReached" : false,
		"maxScansToExplodeReached" : false,
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"student_id" : 1,
					"class_id" : 1
				},
				"indexName" : "student_id_1_class_id_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"student_id" : [ ],
					"class_id" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"student_id" : [
						"(500000.0, inf.0]"
					],
					"class_id" : [
						"[54.0, 54.0]"
					]
				}
			}
		},
		"rejectedPlans" : [ ]
	},
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 4022,
		"executionTimeMillis" : 665,
		"totalKeysExamined" : 499999,
		"totalDocsExamined" : 4022,
		"executionStages" : {
			"stage" : "FETCH",
			"nReturned" : 4022,
			"executionTimeMillisEstimate" : 70,
			"works" : 500000,
			"advanced" : 4022,
			"needTime" : 495977,
			"needYield" : 0,
			"saveState" : 500,
			"restoreState" : 500,
			"isEOF" : 1,
			"docsExamined" : 4022,
			"alreadyHasObj" : 0,
			"inputStage" : {
				"stage" : "IXSCAN",
				"nReturned" : 4022,
				"executionTimeMillisEstimate" : 63,
				"works" : 500000,
				"advanced" : 4022,
				"needTime" : 495977,
				"needYield" : 0,
				"saveState" : 500,
				"restoreState" : 500,
				"isEOF" : 1,
				"keyPattern" : {
					"student_id" : 1,
					"class_id" : 1
				},
				"indexName" : "student_id_1_class_id_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"student_id" : [ ],
					"class_id" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"student_id" : [
						"(500000.0, inf.0]"
					],
					"class_id" : [
						"[54.0, 54.0]"
					]
				},
				"keysExamined" : 499999,
				"seeks" : 495978,
				"dupsTested" : 0,
				"dupsDropped" : 0
			}
		}
	},
	"command" : {
		"find" : "students",
		"filter" : {
			"student_id" : {
				"$gt" : 500000
			},
			"class_id" : 54
		},
		"sort" : {
			"student_id" : 1
		},
		"$db" : "test"
	},
	"serverInfo" : {
		"host" : "8630ee4ab21c",
		"port" : 27017,
		"version" : "5.0.9",
		"gitVersion" : "6f7dae919422dcd7f4892c10ff20cdc721ad00e6"
	},
	"serverParameters" : {
		"internalQueryFacetBufferSizeBytes" : 104857600,
		"internalQueryFacetMaxOutputDocSizeBytes" : 104857600,
		"internalLookupStageIntermediateDocumentMaxSizeBytes" : 104857600,
		"internalDocumentSourceGroupMaxMemoryBytes" : 104857600,
		"internalQueryMaxBlockingSortMemoryUsageBytes" : 104857600,
		"internalQueryProhibitBlockingMergeOnMongoS" : 0,
		"internalQueryMaxAddToSetBytes" : 104857600,
		"internalDocumentSourceSetWindowFieldsMaxMemoryBytes" : 104857600
	},
	"ok" : 1
}

```

- hint를 사용해서 안좋은 인덱스를 실행하니 이전보다 더 많은 인덱스를 검사하고 조금 더 오래 걸린것을 확인할 수 있다.
- 이 쿼리에서 좋은 인덱스는 "class_id" 로 인덱스 내에서 고려되는 키를 먼저 제한하는게 좋다. 모든 데이터셋에서 해당되지 않지만, 일반적으로 동등 필드를 사용할 필드가 다중값 필터를 사용할 필드보다 앞에 오도록 복합 인덱스를 설계해야 한다.

```

db.students.createIndex({"class_id": 1, "student_id": 1})
{
	"numIndexesBefore" : 3,
	"numIndexesAfter" : 3,
	"note" : "all indexes already exist",
	"ok" : 1
}

db.students.find({"student_id" : {"$gt": 500000}, "class_id": 54}).sort({"student_id": 1}).explain("executionStats")
{
	"explainVersion" : "1",
	"queryPlanner" : {
		"namespace" : "test.students",
		"indexFilterSet" : false,
		"parsedQuery" : {
			"$and" : [
				{
					"class_id" : {
						"$eq" : 54
					}
				},
				{
					"student_id" : {
						"$gt" : 500000
					}
				}
			]
		},
		"maxIndexedOrSolutionsReached" : false,
		"maxIndexedAndSolutionsReached" : false,
		"maxScansToExplodeReached" : false,
		"winningPlan" : {
			"stage" : "FETCH",
			"inputStage" : {
				"stage" : "IXSCAN",
				"keyPattern" : {
					"class_id" : 1,
					"student_id" : 1
				},
				"indexName" : "class_id_1_student_id_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"class_id" : [ ],
					"student_id" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"class_id" : [
						"[54.0, 54.0]"
					],
					"student_id" : [
						"(500000.0, inf.0]"
					]
				}
			}
		},
		"rejectedPlans" : [
			{
				"stage" : "FETCH",
				"inputStage" : {
					"stage" : "IXSCAN",
					"keyPattern" : {
						"student_id" : 1,
						"class_id" : 1
					},
					"indexName" : "student_id_1_class_id_1",
					"isMultiKey" : false,
					"multiKeyPaths" : {
						"student_id" : [ ],
						"class_id" : [ ]
					},
					"isUnique" : false,
					"isSparse" : false,
					"isPartial" : false,
					"indexVersion" : 2,
					"direction" : "forward",
					"indexBounds" : {
						"student_id" : [
							"(500000.0, inf.0]"
						],
						"class_id" : [
							"[54.0, 54.0]"
						]
					}
				}
			}
		]
	},
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 4022,
		"executionTimeMillis" : 50,
		"totalKeysExamined" : 4022,
		"totalDocsExamined" : 4022,
		"executionStages" : {
			"stage" : "FETCH",
			"nReturned" : 4022,
			"executionTimeMillisEstimate" : 12,
			"works" : 4023,
			"advanced" : 4022,
			"needTime" : 0,
			"needYield" : 0,
			"saveState" : 4,
			"restoreState" : 4,
			"isEOF" : 1,
			"docsExamined" : 4022,
			"alreadyHasObj" : 0,
			"inputStage" : {
				"stage" : "IXSCAN",
				"nReturned" : 4022,
				"executionTimeMillisEstimate" : 12,
				"works" : 4023,
				"advanced" : 4022,
				"needTime" : 0,
				"needYield" : 0,
				"saveState" : 4,
				"restoreState" : 4,
				"isEOF" : 1,
				"keyPattern" : {
					"class_id" : 1,
					"student_id" : 1
				},
				"indexName" : "class_id_1_student_id_1",
				"isMultiKey" : false,
				"multiKeyPaths" : {
					"class_id" : [ ],
					"student_id" : [ ]
				},
				"isUnique" : false,
				"isSparse" : false,
				"isPartial" : false,
				"indexVersion" : 2,
				"direction" : "forward",
				"indexBounds" : {
					"class_id" : [
						"[54.0, 54.0]"
					],
					"student_id" : [
						"(500000.0, inf.0]"
					]
				},
				"keysExamined" : 4022,
				"seeks" : 1,
				"dupsTested" : 0,
				"dupsDropped" : 0
			}
		}
	},
	"command" : {
		"find" : "students",
		"filter" : {
			"student_id" : {
				"$gt" : 500000
			},
			"class_id" : 54
		},
		"sort" : {
			"student_id" : 1
		},
		"$db" : "test"
	},
	"serverInfo" : {
		"host" : "8630ee4ab21c",
		"port" : 27017,
		"version" : "5.0.9",
		"gitVersion" : "6f7dae919422dcd7f4892c10ff20cdc721ad00e6"
	},
	"serverParameters" : {
		"internalQueryFacetBufferSizeBytes" : 104857600,
		"internalQueryFacetMaxOutputDocSizeBytes" : 104857600,
		"internalLookupStageIntermediateDocumentMaxSizeBytes" : 104857600,
		"internalDocumentSourceGroupMaxMemoryBytes" : 104857600,
		"internalQueryMaxBlockingSortMemoryUsageBytes" : 104857600,
		"internalQueryProhibitBlockingMergeOnMongoS" : 0,
		"internalQueryMaxAddToSetBytes" : 104857600,
		"internalDocumentSourceSetWindowFieldsMaxMemoryBytes" : 104857600
	},
	"ok" : 1
}

```





- student_id는 이미 정렬된 상태고 class_id 54의 첫번째 키부터 시작해 모든 키 쌍을 따라가면 된다.
- 복합 인덱스를 설계할 때는 인덱스를 사용할 공통 쿼리 패턴의 동등 필터, 다중값 필터, 정렬 구송 요소를 처리하는 방법을 알아야 한다.
- 복합 인덱스의 트레이드 오프
  - 복합 인덱스를 설계할 때 트레이드 오프는 자주 발생한다.
  - 인메모리 정렬을 피하려면 반환하는 도큐먼트 개수보다 더 많은 키를 검사해야 한다.
  - 인덱스를 사용해 정렬하려면 몽고DB가 인덱스 키를 순서대로 살펴볼 수 있어야 한다.
- 복합 인덱스 설계시 고려 사항
  - 동등 필터에 대한 키를 맨 앞에 표시해야 한다.
  - 정렬에 사용되는 키는 다중값 필드 앞에 표시해야 한다.
  - 다중값 필터에 대한 키는 마지막에 표시해야 한다.

`키 방향 선택하기`

- 두 개의 검색조건으로 정렬할 때는 인덱스 키의 방향이 서로 달라야하는 경우가 있다.
- 복합 정렬을 서로 다른 방향으로 최적화하려면 방향이 맞는 인덱스를 사용해야 한다.

```
// age asc, username desc
db.students.createIndex({"age": 1, "username": -1})
```

- 만약 애플리케이션이 `{"age": 1, "username": 1}` 을 이용해 정렬을 최적화해야 한다면 해당 방향으로 두 번째 인덱스를 생성한다.
- 역방향 인덱스는 서로 동등하다
  -  `{"age": -1, "username": 1}`  ==  `{"age": 1, "username": -1}` 
- 인덱스 방향은 다중 조건에 따라 정렬할 때만 문제가 된다. 단일 키로 정렬하면 몽고DB는 인덱스를 쉽게 역순으로 읽을 수 있다.



`커버드 쿼리 사용하기`

- 인덱스가 쿼리가 요구하는 값을 모두 포함하면, 쿼리가 커버드된다고 한다.
- 실무에서는 도큐먼트로 되돌아가지 말고 커버드 쿼리를 사용하자.
- 쿼리가 확실히 인덱스만 사용하게 하려면 모든 키를 반환받지 않도록 반환 키를 지정해야 한다.
- 커버드 쿼리에 explain을 실행하면 결과에 "IXSCAN"이 되고 executionStats.totalDocsExamined의 값이 0이 된다.



`암시적 인덱스`

- 복합 인덱스는 쿼리마다 다른 인덱스처럼 동작할 수 있다.
- `{"a": 1, "b": 1, "c": 1 .... "z": 1}`
  - `{"a": 1}`, `{"b": 1}`, `{"c": 1}` ... `{"z": 1}`
  - `{"a": 1, "b": 1}`, `{"a": 1, "b": 1, "c": 1}`
  - ...
- 인덱스의 첫번째 요소를 사용하지 않거나 건너뛴 서브셋은 불가능하다.
  - `{"a": 1, "c": 1}`
  - `{"b": 1}`



### $ 연산자의 인덱스 사용법

`비효율적인 연산자`

- 일반적으로 부정 조건은 비효율적이다. $ne
- $ne로 지정된 항목을 제외한 모든 인덱스 항목을 살펴봐야 하므로 기본적으로 전체 인덱스를 살펴봐야 한다.
- $not은 범위와 정규표현식을 반대로 뒤비을 수 있지만 $not을 사용하는 쿼리 대부분은 테이블 스캔을 수행한다.
- $nin은 항상 테이블 스캔을 수행한다.
- 이런 종류의 쿼리를 신속하게 실행해야 한다면 결과 셋이 적은 수의 도큐먼트를 반환하게끔 필터링하는 인덱스를 만드는게 더 좋다.

`범위`

- 복합 인덱스를 설계할 때는 완전 일치가 사용될 필드를 첫 번째에, 범위가 사용될 필드를 마지막에 놓자.
- 쿼리가 첫 번째 인덱스 키와 정확히 일치하는 값을 찾고 두 번째 인덱스 범위 안에서 검색하게 해준다.

```
// {"age" : 1, "username" : 1} 인덱스에서 아래 쿼리는 효율이 좋다.
db.users.find({"age" : 47, "username" : {"$gt" : "user5", "$lt" : "user8"}})

// 반대로 {"username" : 1, "age" : 1} 인덱스인 경우 쿼리가 user5~user8 사이의 모든 사용자를 살펴보고 age가 47인 사용자를 뽑아야 하므로 쿼리 플랜이 좋지 않다.
```





`OR 쿼리`

- 현재 몽고DB는 쿼리당 하나의 인덱스만 사용할 수 있다.
  - {"x" : 1}와 {"y" : 1} 인덱스가 있을 때 {"x" : 123, "y" : 456} 쿼리를 사용하면 인덱스 두개중 하나만 사용한다.
- 유일한 예외는 $or다. $or는 두 개의 쿼리를 수행하고 결과를 합치므로 $or절마다 하나씩 인덱스를 사용할 수 있다.

```
db.foo.find({"$or" : [{"x": 123}, {"y" : 456}]})
```

- 일반적으로 두 번 쿼리해서 결과를 병합하면 한 번 쿼리할 때보다 훨씬 비효율적이니 가능하면 $or보다는 $in을 사용하자.
- $or를 사용해야 한다면 몽고DB가 두 쿼리의 결과를 조사하고 중복을 모두 제거해야 한다.
- $in을 사용할때 정렬을 제외한다면 반환되는 도큐먼트 순서를 제어하는 방법은 없다
  - {"x": {"$in" : [1, 2, 3]}}, {"x": {"$in" : [3, 2, 1]}} 의 결과는 같다.



### 객체 및 배열 인덱싱

- 몽고DB는 도큐먼트 내부에 도달해서 내장 필드와 배열에 인덱스를 생성하도록 허용한다.
- 내장 개겣와 배열 필드는 복합 인덱스에서 최상위 필드와 결합될 수 있으며, 다소 특수한 경우를 제외하면 대부분 일반적인 인덱스 필드와 같은 방식으로 동작한다.



`내장 도큐먼트 인덱싱하기`

- 인덱스는 일반적인 키에 생성될 때와 동일한 방식으로 내장 도큐먼트 키에 생성될 수 있다.

```
{
  "username" : "sid",
  "loc" : {
    "ip" : "1.2.3.4"
    "city" : "Springfield",
    "state" : "NY"
  }
}

db.users.createIndex({"loc.city" : 1})
```

- 내장 도큐먼트 전체에 인덱싱을 적용할 수 있는데 이 경우엔 내장 도큐먼트의 모든 필드를 쿼리할 때만 도움이 되므로 주의해야 한다.



`배열 인덱싱하기`

- 배열에도 인덱스를 생성할 수 있다.

```
// 블로그 게시물에 comments라는 댓글 배열이 있고 가장 최근에 댓글이 달린 블로그를 찾기 위해 생성한 인덱스
db.blog.createIndex({"comments.date" : 1})
```

- 배열을 인덱싱하면 배열의 각 요소에 인덱스 항목을 생성하므로, 한 게시물에 20개의 댓글이 달렸다면 도큐먼트는 20개의 인덱스 항목을 가진다. 따라서 배열 내 모든 쓰기 작업은 모든 배열 요소를 갱신시킨다.

- 배열 요소에 대한 인덱스에는 위치 개념이 없어서 comments.4와 같이 특정 배열 요소를 찾는 쿼리에는 인덱스를 사용할 수 없다.

- db.blog.createIndex({"comments.10.votes" : 1}) 형태로 특정 항목에 인덱스를 생성할 수는 있지만 특정 항목에 쿼리할 때만 유용하다.

- 인덱스 항목의 한 필드만 배열로 가져올 수 있다.

  - 이는 여러 다중키 인덱스에 의해 인덱스 항목이 폭발적으로 늘어나는 것을 피하기 위함이다.

  - ```
    // {"x": 1, "y": 1} 로 인덱스가 만들어져 있을 때
    
    // 정상
    db.multi.insert({"x": [1, 2, 3], "y": 1})
    
    // 정상
    db.multi.insert({"x": 1, "y": [1, 2, 3]})
    
    // 불가능
    db.multi.insert({"x": [1, 2, 3], "y": [1, 2, 3]})
    cannot index parallel array [y][x]
    ```



`다중키 인덱스가 미치는 영향`

- 어떤 도큐먼트가 배열 필드를 인덱스 키로 가지면 인덱스는 즉시 다중키 인덱스로 표시된다.
- 다중키 인덱스는 explain() 출력의 isMultiKey 필드가 true다.
- 일단 다중키 인덱스로 표시되면 배열을 포함하는 도큐먼트가 모두 제거되더라도 비다중키가 될 수 없다.
- 비다중키가 되게 하려면 인덱스를 삭제하고 다시 생성해야만 한다.
- 다중키 인덱스는 비다중키 인덱스보다 약간 느릴 수 있고 하나의 도큐먼트를 여러 개의 인덱스 항목이 가리킬 수 있으므로 몽고DB는 결과를 반환하기 전에 중복을 제거해야 한다.



### 인덱스 카디널리티

- 카디널리티
  - 컬렉션의 한 필드에 대해 고윳값이 얼마나 많은지를 나타내는 값
  - 성별은 남 or 여이므로 매우 낮은 카디널리티를 갖는다.
  - username, email은 각 도큐먼트마다 거의 유일한 값을 갖고 이는 높은 카디널리티다.
  - age, zip code는 카디널리티가 중간쯤 된다.
- 일반적으로 필드의 카디널리티가 높을수록 인덱싱이 더욱 도움이된다.
- 복합인덱스에서도 높은 카디널리티 키를 낮은 카디널리티 키보다 앞에 놓는게 좋다.



## explain 출력

- explain은 쿼리에 대한 많은 정보를 제공하며, 느린 쿼리를 위한 중요한 진단 도구다.
- 어떤 쿼리든 마지막에 explain 호출을 추가할 수 있다.
- 다양한 explain 관련 필드들

`nReturned`

- 쿼리에 의해 반환된 도큐먼트 개수

`totalKeysExamined`

- 인덱스가 사용됐다면 살펴본 인덱스 항목 개수
- 테이블 스캔을 했다면 조사한 도큐먼트 개수

`totalDocsExamined`

- 몽고DB가 디스크내 실제 도큐먼트를 가리키는 인덱스 포인터를 따라간 횟수
- 쿼리가 인덱스의 일부가 아닌 검색 조건을 포함하거나, 인덱스에 포함되지 않은 필드를 반환하도록 요청한다면 몽고DB는 각 인덱스 항목이 가리키는 도큐먼트를 살펴봐야 한다.

`executionTimeMillis`

- 데이터베이스가 쿼리하는 데 걸린 시간.
- 만약 여러 개의 쿼리 플랜을 시도했다면 이 값은 최고로 뽑힌 플랜이 아니라 모든 플랜이 실행되기까지 걸린 시간을 반영한다.

`isMultiKey`

- 다중키 인덱스 사용 여부

`stage`

- 몽고DB가 인덱스를 사용해 쿼리할 수 있었는지 여부
  - IXSCAN: 인덱스를 사용해서 쿼리
  - COLSCAN: 테이블 스캔

`needYield`

- 쓰기 요청을 처리하도록 쿼리가 양보한 회수

`indexBounds`

- 인덱스가 어떻게 사용됐는지 설명하며 탐색한 인덱스의 범위



## 인덱스를 생성하지 않는 경우

- 인덱스는 데이터의 일부를 조회할 때가 가장 효율적이며 어떤 쿼리는 인덱스가 없는게 더 빠르다.
- 인덱스는 컬렉션에서 가져와야 하는 부분이 많을수록 비효율적인데 인덱스를 하나 사용하려면 두 번의 조회를 해야 하기 때문이다.
  - 한번은 인덱스를 살펴보고 또 한번은 실제 도큐먼트를 가리키는 인덱스의 포인터를 따라간다.
  - 컬렉션 전체를 반환할때는 인덱스보다 컬렉션 스캔이 더 빠르다.
  - 대체로 쿼리가 컬렉션의 30%이상을 반환하는 경우 인덱스는 종종 쿼리 속도를 높인다.



| 인덱스가 적합한 경우 | 컬렉션 스캔이 적합한 경우 |
| -------------------- | ------------------------- |
| 큰 컬렉션            | 작은 컬렉션               |
| 큰 도큐먼트          | 작은 도큐먼트             |
| 선택적 쿼리          | 비선택적 쿼리             |





## 인덱스 종류

- 여기서는 일반적인 인덱스만 다루고 6장에서 특수인덱스를 다룬다.

### 고유 인덱스

- 고유 인덱스는 각 값이 인덱스에 최대 한번 나타나도록 보장한다.

```
db.users2.createIndex({"firstname" : 1}, {unique : true, "partialFilterExpression" : {"firstname" : {$exists : true}}})

db.users2.insert({"firstname" : "bob"})
WriteResult({ "nInserted" : 1 })

db.users2.insert({"firstname" : "bob"})
WriteResult({
	"nInserted" : 0,
	"writeError" : {
		"code" : 11000,
		"errmsg" : "E11000 duplicate key error collection: test.users2 index: firstname_1 dup key: { firstname: \"bob\" }"
	}
})
```

- 중복 키 예외를 발생시키면 매우 비효율적이기 때문에 수많은 중복을 필터링하기보다는 가끔씩 발생하는 중복에 고유 제약 조건을 사용한다.
- _id 인덱스는 컬렉션을 생성하면 항상 자동으로 생성되는데 다른 고유 인덱스와 달리 삭제할 수 없다는 점을 제외하면 일반적인 고유 인덱스와 같다.
- 도큐먼트에 키가 존쟇지 않으면 고유 인덱스는 그 도큐먼트에 대해 값을 null로 저장한다. 이미 null 값이 있는 경우 insert에 실패한다.
- 인덱스 버킷
  - 인덱스 버킷은 크기 제한이 있으며 제한을 넘는 인덱스 항목은 인덱스에 포함되지 않는다.
  - 예를 들어 8킬로바이트보다 긴 키에는 고유 인덱스 제약 조건이 적용되지 않아서 똑같은 8킬로바이트 문자열을 입력할 수 있다.
  - 몽고DB 4.2 이전 버전에서 필드는 인덱스에 포함되려면 1024바이트보다 작아야 했다. 몽고 4.2 이후로 이런 제약이 사라졌다.



`복합 고유 인덱스`

- 복합 고유 인덱스를 만들수도 있다.
- 인덱스 항목의 모든 키에 걸친 값의 조합은 인덱스에서 최대 한 번만 나타난다.

```
db.users3.createIndex({"username" : 1, "age": 1}, {unique : true})

> db.users3.insert({"username" : "choi", "age" : 29})
WriteResult({ "nInserted" : 1 })
> db.users3.insert({"username" : "someone", "age" : 29})
WriteResult({ "nInserted" : 1 })
> db.users3.insert({"username" : "choi", "age" : 29})
WriteResult({
	"nInserted" : 0,
	"writeError" : {
		"code" : 11000,
		"errmsg" : "E11000 duplicate key error collection: test.users3 index: username_1_age_1 dup key: { username: \"choi\", age: 29.0 }"
	}
})

```





`중복 제거하기`

- 기존 컬렉션에 고유 인덱스를 구축할 때 중복된 값이 있으면 실패한다.

```
> db.users3.createIndex({"username" : 1, "age": 1}, {unique : true})
{
	"ok" : 0,
	"errmsg" : "Index build failed: aec09234-d41c-49ed-b29a-22277fee8e46: Collection test.users3 ( d376a675-33a4-4f0d-9526-9cadf20a1c1f ) :: caused by :: E11000 duplicate key error collection: test.users3 index: username_1_age_1 dup key: { username: \"choi\", age: 29.0 }",
	"code" : 11000,
	"codeName" : "DuplicateKey",
	"keyPattern" : {
		"username" : 1,
		"age" : 1
	},
	"keyValue" : {
		"username" : "choi",
		"age" : 29
	}
}
```



### 부분 인덱스

- 고유 인덱스는 null을 값으로 취급하므로 키가 없는 도큐먼트가 여러 개인 고유인덱스를 만들 수 없다. 하지만 오직 키가 존재할 때만 고유 인덱스가 적용되도록 할 때가 많은데 이때 부분인덱스를 사용하면 된다.

```
// 이메일 주소는 선택 항목이지만 입력할 경우 고유해야한다면 아래와 같이 인덱스를 생성할 수 있다.
db.users2.createIndex({"email" : 1}, {unique : true, "partialFilterExpression" : {"email" : {$exists : true}}})
```

- 부분 인덱스는 반드시 고유할 필요는 없고 고유하지 않은 부분 인덱스를 생성하려면 unique 옵션만 제거하면 된다.



## 인덱스 관리

- 인덱스는 컬렉션당 한 번만 만들어야 하고 다시 생성하면 아무 일도 일어나지 않는다.
- 데이터베이스의 인덱스 정보는 모두 system.indexes 컬렉션에 저장된다. 이는 예약된 컬렉션이므로 안에 있는 도큐먼트를 수정하거나 제거할 수 없고 명령어로만 조작할 수 있다.
- 특정 컬렉션의 모든 인덱스 정보를 확인하려면 db.collectioname.getIndexes()를 사용하자.

```
db.students.getIndexes()
[
	{
		"v" : 2,
		"key" : {
			"_id" : 1
		},
		"name" : "_id_"
	},
	{
		"v" : 2,
		"key" : {
			"student_id" : 1,
			"class_id" : 1
		},
		"name" : "student_id_1_class_id_1"
	},
	{
		"v" : 2,
		"key" : {
			"class_id" : 1,
			"student_id" : 1
		},
		"name" : "class_id_1_student_id_1"
	}
]
```

- key와 name 필드는 힌트에 사용하거나 인덱스가 명시돼야 하는 위치에 사용할 수 있다.
- v 필드는 내부적으로 인덱스 버저닝에 사용된다. 만약 v 필드가 매우 오래된 인덱스이고 비효율적인 형식으로 저장되었으니 몽고 DB2.0 이후 버전을 사용하여 인덱스를 삭제 및 재구축해 인덱스 업그레이드를 진행하자.



### 인덱스 식별

- 컬렉션 내 각 인덱스는 고유하게 식별하는 이름이 있다.
- 이 인덱스 이름은 기본적으로 키: 방향(1 or -1)_키2: 방향(1 or -1) 형태(ex: class_id_1_student_id_1) 이기 때문에 인덱스 키가 두 개 이상이면 인덱스명이 길어질 수 있으므로 createIndex의 옵션으로 원하는 이름을 지정할 수 있다.

```
db.soup.createIndex({"a" : 1, "b" : 1 ... "z" : 1}, {"name" : "alphabet"})
```



### 인덱스 변경

- 불필요해진 인덱스는 dropIndex 명령어를 통해 제거할 수 있다.

```
db.people.dropIndex("x_1_y_1")
```

- 새로운 인덱스를 구축하려면 시간이 오래 걸리고 리소스가 많이 필요하다.
- 몽고DB 4.2 이후 버전에서는 인덱스를 최대한 빨리 구축하기 위해, 구축이 완료될 때까지 데이터베이스의 모든 읽기와 쓰기를 중단한다.
- 데이터베이스가 읽기와 쓰기에 어느정도 응답하게 하려면 인덱스를 구축할 때 "background" 옵션을 사용하자. (그래도 애플리케이션에 큰 영향을 준다.)



# #6 특수 인덱스와 컬렉션 유형

- 이 장에서 설명하는 것들
  - 큐 같은 데이터를 위한 제한 컬렉션
  - 캐시르 위한 TTL 인덱스
  - 단순 문자열 검색을 위한 전문 인덱스
  - 2d 구현 및 구현 기하학을 위한 공간 정보 인덱스
  - 대용량 파일 저장을 위한 GridFS



## 공간 정보 인덱스

- 몽고DB는 2dsphere와 2d라는 공간 정보 인덱스를 가진다.
- 2dsphere
  - WGS84 좌표계를 기반으로 지표면을 모델링하는 구면 기하학으로 동작
    - WGS84 좌표계는 지표면을 주상절벽으로 모델링하며, 이는 극지방에 약간의 평탄화가 존재함을 의미한다.
  - 2dsphere 인덱스를 사용하면 지구의 형태를 고려하므로 2d 인덱스를 사용할 떄보다 더 정확한 거리를 계산할 수 있다. 
    - ex: 두 도시간의 거리
  - GeoJSON 형식으로 점, 선, 다각형의 기하 구조를 지정할 수 있다. http://www.geojson.org

```
// 점은 경도 좌표와 위도 좌표를 소로 갖는 배열로 표현된다.
{
  "name" : "New York City",
  "loc" : {
    "type" : "Point",
    "coordinates" : [50, 2]
  }
}


// 선은 점의 배열로 표현된다.
{
  "name" : "LineString",
  "loc" : {
    "type" : "Point",
    "coordinates" : [[0,1], [0, 2], [0, 3]]
  }
}

// 다각형은 선과 같은 방식으로 표현된다.
{
  "name" : "New England",
  "loc" : {
    "type" : "Polygon",
    "coordinates" : [[0,1], [0, 2], [0, 3]]
  }
}

// loc라는 필드명은 변경 가능하지만 type과 corordinates는 GeoJSON에서 지정되므로 변경이 불가하다.
```

- createIndex와 함께 2dsphere를 사용해 공간 정보 인덱스를 만들 수 있다.

```
db.openStreetMap.createIndex({"loc" : "2dsphere"})
```



### 공간 정보 쿼리 유형

- 공간정보 쿼리는 교차, 포함, 근접이라는 세가지 유형이 있다.
- 찾고싶은 항목은 {"$geometry" : geoJson} 와 같은 형태로 지정한다.
  - 예를 들어 "$geoIntersects" 연산자를 사용해 쿼리 위치와 교차하는 도큐먼트를 찾을 수 있다.

```
var eastVillage = { "type" : "Polygon", "coordinates" : [ [ [ -73.9732566, 40.7187272 ], [ -73.9724573, 40.7217745 ],
[ -73.9717144, 40.7250025 ], [ -73.9714435, 40.7266002 ], [ -73.975735, 40.7284702 ], [ -73.9803565, 40.7304255 ],
[ -73.9825505, 40.7313605 ], [ -73.9887732, 40.7339641 ], [ -73.9907554, 40.7348137 ], [ -73.9914581, 40.7317345 ],
[ -73.9919248, 40.7311674 ], [ -73.9904979, 40.7305556 ], [ -73.9907017, 40.7298849 ], [ -73.9908171, 40.7297751 ],
[ -73.9911416, 40.7286592 ], [ -73.9911943, 40.728492 ], [ -73.9914313, 40.7277405 ], [ -73.9914635, 40.7275759 ], 
[ -73.9916003, 40.7271124 ], [ -73.9915386, 40.727088 ], [ -73.991788, 40.7263908 ], [ -73.9920616, 40.7256489 ], 
[ -73.9923298, 40.7248907 ], [ -73.9925954, 40.7241427 ], [ -73.9863029, 40.7222237 ], [ -73.9787659, 40.719947 ],
[ -73.9772317, 40.7193229 ], [ -73.9750886, 40.7188838 ], [ -73.9732566, 40.7187272 ] ] ]}

db.openStreetMap.find({"loc" : {"$geoIntersects" : {"$geometry" : eastVillage}}})

// $geoWithin을 사용하면 지역에 완전히 포함된 항목을 쿼리할 수 있다.
db.openStreetMap.find({"loc" : {"$geoWithin" : {"$geometry" : eastVillage}}})

// $near를 사용해 주변 위치에 쿼리할 수 있다.
db.openStreetMap.find({"loc" : {"$near" : {"$geometry" : eastVillage}}})

```

- $near는 정렬을 포함하는 유일한 공간 정보 연산자다. 항상 가까운 곳부터 가장 먼 곳 순으로 반환된다.



### 공간 정보 인덱스 사용

- 몽고DB의 공간 정보 인덱스를 사용하면 특정 지역과 관련된 모양과 점이 포함된 컬렉션에서 공간 쿼리를 효율적으로 실행할 수 있다.
- 사용자가 뉴욕에서 레스토랑을 찾도록 돕는 모바일 애플리케이션 설계
  - 사용자가 현재 위치한 지역을 찾는다.
  - 해당 지역 내 레스토랑 수를 보여준다.
  - 지정된 거리 내에 있는 레스토랑을 찾는다.

`쿼리에서의 2d vs. 구면 기하학`

- 공간 정보 쿼리는 쿼리와 사용 중인 인덱스 유형에 따라 구면 또는 2d 구조를 사용한다.
- 각 공간 정보 연산자가 사용하는 기하 구조 유형

| 쿼리 유형                                    | 기하 구조 유형 |
| -------------------------------------------- | -------------- |
| $near (GeoJSON point, 2dsphere 인덱스)       | 구면           |
| $near (레거시 좌표, 2d 인덱스)               | 평면           |
| $geoNear (GeoJSON point, 2dsphere 인덱스)    | 구면           |
| $geoNear (레거시 좌표, 2d 인덱스)            | 평면           |
| $nearSphere (GeoJSON point, 2dsphere 인덱스) | 구면           |
| $nearSphere (레거시 좌표, 2d 인덱스)         | 구면           |
| $geoWithin : { $geometry : ... }             | 구면           |
| $geoWithin : { $box : ... }                  | 평면           |
| $geoWithin : { $polygon : ... }              | 평면           |
| $geoWithin : { $center : ... }               | 평면           |
| $geoWithin : { $centerSphere : ... }         | 구면           |
| $geoIntersects                               | 구면           |

- 2d 인덱스는 구에서 평면 기하학과 거리 계산을 모두 지원한다는 점에서 유의하자. 하지만 구면 기하학을 사용하는 쿼리는 2dsphere 인덱스를 사용할 때 성능과 정확성이 향상된다.
- $geoNear 쿼리와 특수명령어 geoNear 
  - $geoNear 연산자는 집계 연산자라는 점에서 유의하자.
  - $near 쿼리 외에도 $geoNear나 특수 명령어인 geoNear를 사용해 주변 위치를 쿼리할 수 있다.
  - geoNear 명령어와 $geoNear 집계 연산자를 사용하려면 컬렉션에 2dsphere 인덱스와 2d 인덱스가 최대 1개만 있어야 한다. 반면에 $near나 $geoWithin과 같은 공간 정보 쿼리 연산자는 컬렉션이 여러 개의 공간 정보 인덱스를 갖도록 허용한다.
  - geoNear 명령과 $geoNear 구문은 둘 다 위치 필드를 포함하지 않으므로 공간 정보 인덱스 제한이 존재한다. 따라서 여러 2d인덱스나 2dsphere 인덱스 간의 선택이 모호해진다.
- $near 쿼리 연산자는 몽고DB의 샤딩을 사용해 배포된 컬렉션에서는 작동하지 않는다.
- 공간 정보 쿼리 연산자에는 이러한 제한이 없으며, 위치 필드를 사용해 모호성을 없앤다.



`왜곡`

`레스토랑 검색`
