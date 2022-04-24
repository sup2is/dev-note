









# #1 개요

- 전문가란 아주 좁은 분야에서 할 수 있는 실수를 모두 해본 사람이다. - 닐스 보어



## 대상 독자

## 이 책에 있는 내용

### 이 책의 구성

`논리적 데이터베이스 설계 안티 패턴`

`물리적 데이터베이스 설계 안티 패턴`

`쿼리 안티 패턴`

`애플리케이션 개발 안티 패턴`

### 안티패턴의 구조

## 이 책에 없는 내용

## 일러두기

## 예제 데이터베이스

![1-1](./images/sql-antipatterns/1-1.jpeg)





# #2 무단횡단

- 이름을 밝힐 수 없는 넷스케이프 엔지니어가 한 번은 포인터를 Javascript로 넘겼다가 문자열로 저장한 다음 나중에 다시 C로 돌려받아, 서른 명 사망함...ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ

## 목표: 다중 값 속성 저장

- 테이블의 칼럼이 하나의 값을 가질 땐 설계가 쉽다.



## 안티패턴: 쉼표로 구분된 목록에 저장

- DB 구조 변경을 최소화하기위해 ...

```sql
CREATE TABLE Products (
	product_id SERIAL PRIMARY KEY,
  product_name VARCHAR(1000),
  account_id VARCHAR(100), -- 쉼표로 구분된 목록
);
```



**특정 계정에 대한 제품 조회**

- MsSQL에서는 아래와 같이 ''사용자12' 를 찾을 수 있지만 휴먼에러, 인덱스를 사용 못하고, SQL 벤더에도 중립적이지 못하다.

```sql
SELECT * FROM Products WHERE account_id REGEXP '[[:<:]]12[[:>:]]';
```



**주어진 제품에 대한 계정 정보 조회**

```sql
SELECT * FROM Products AS p JOIN Accounts AS a
	ON p.account_id REGEXP '[[:<:]]' || a.account_id || '[[:<:]]'
WHERE p.proudct_id = 123;
```

- 이런식의 표현은 인덱스도 사용 못하고 두 테이블을 모두 읽어 카테시안 곱을 생성한 다음 모든 행의 조합에 대해 정규 표현식을 평가해야 한다.



**집계 쿼리 만들기**

- 이상한 기교

```sql
SELECT product_id, LENGTH(account_id) - LENGTH(REPLACE(account_id, ',', '')) + 1
	AS contracts_per_product
FROM Products;
```



**특정 제품에 대한 계정 갱신**

- 목록의 마지막에 문자열 연결을 통해 새로운 아이디를 추가할 수 있지만 목록이 정렬된 상태로 유지되지는 못한다.
- 삭제 역시 이상하게 한다.



**제품 아이디 유효성 검증**

- 검증도 안되고 db는 쓰레기 더미가 된다.

**구분자 문자 선택**

- 구분자 선택도 애매하다.

**목록 길이 제한**

- n개의 계정에 대한 칼럼의 크기는 예측이 불가능하다.



## 안티패턴 인식 방법

- 프로젝트에서 아래와 같은 말이 나온다면 무단횡단 안티패턴이 사용되고 있는 것이다.
  - 이 목록이 지원해야 하는 최대 항목 수는 얼마나 될까?
  - SQL에서 단어의 경계를 어떻게 알아내는지 알아?
  - 이 목록에서 절대 나오지 않을 문자가 어떤 게 있을까?



## 안티패턴 사용이 합당한 경우

- 어떤 종류의 쿼리는 데이터베이스 반정규화를 적용해 성능을 향상시킬 수 있다. 
- 반정규화를 사용하기로 결정할 때는 보수적이어야 한다. 데이터베이스를 정규화하는 것이 먼저다.
- 정규화는 애플리케이션 코드를 좀더 융툥성 있게 하고 데이터베이스의 정합성을 유지할 수 있게 해준다.



## 해법: 교차 테이블 생성

- 별도의 테이블에 저장해 account_id가 별도의 행을 차지하도록 하는 것이 좋다.
- 새로만드는 Contracts 테이블은 Products와 Accounts 사이의 다대다 관계를 구현한다.

```sql
CREATE TABLE Contacts (
  product_id  BIGINT UNSIGNED NOT NULL,
  account_id  BIGINT UNSIGNED NOT NULL,
  PRIMARY KEY (product_id, account_id),
  FOREIGN KEY (product_id) REFERENCES Products(product_id),
  FOREIGN KEY (account_id) REFERENCES Accounts(account_id)
);

INSERT INTO Contacts (product_id, account_id)
VALUES (123, 12), (123, 34), (345, 23), (567, 12), (567, 34);

```



**계정으로 제품 조회하기와 제품으로 계정 조회하기**

- 주어진 계정에 대한 모든 제품의 속성을 조회하려면 Products 테이블과 Contracts 테이블을 조인하면 된다.

```sql
SELECT p.*
FROM  Products AS p JOIN Contacts AS c ON (p.product_id = c.product_id)
WHERE c.account_id = 34;
```

- 이쿼리는 효율적으로 인덱스를 사용할 수 있다.



**집계 쿼리 만들기**

- 제품당 계정 수를 리턴하는 쿼리

```sql
SELECT product_id, COUNT(*) AS accounts_per_product
FROM Contacts
GROUP BY product_id;
```

- 계정 당 제품 수를 구하는 쿼리

```sql
SELECT account_id, COUNT(*) AS products_per_account
FROM Contacts
GROUP BY account_id;
```

- 가장 많은 담당자를 할당 받은 제품을 구하는 쿼리

```sql
SELECT c.product_id, c.contacts_per_product
FROM (
  SELECT product_id, COUNT(*) AS accounts_per_product
  FROM Contacts
  GROUP BY product_id
) AS c
ORDER BY c.contacts_per_product DESC LIMIT 1
```



**특정 제품에 대한 계정 갱신**

```sql
INSERT INTO Contacts (product_id, account_id) VALUES (456, 34);

DELETE FROM Contacts WHERE product_id = 456 AND account_id = 34;
```



**제품 아이디 유효성 검증**

- FK를 사용해서 참조 정합성을 데이터베이스가 강제하도록 할 수 있다.



**구분자 문자 선택**

- 각 항목은 별도의 행으로 저장하므로 구분자를 사용하지 않는다.



**목록 길이 제한**

- 각 항목이 별도의 행으로 존재하기 때문에 한 컬럼에 물리적으로 저장할 수 있는 데이터 타입의 크기만 제한을 받는다.



**교차 테이블의 다른 장점**

- 쉼표로 구분된 목록보다 인덱스를 더 효율적으로 활용할 수 있기 때문에 성능이 좋아진다.
- 칼럼에 fk를 선언하면 많은 데이터베이스가 내부적으로 해당 칼럼에 대한 인덱스를 생성한다 
  - MySQL의 InnoDB
  - DB by DB
- 교차 테이블에 칼럼을 추가해 추가 속성을 넣을 수 있다.



**SQL Antipatterns Tip**

- 각 값은 자신의 칼럼과 행에 저장하라.



# #3 순진한 트리

- 각 답글에 답을 달 수 있는 구조로 아래와 같이 테이블을 간단하게 설계했다.

```sql
CREATE TABLE Comments (
  comment_id   SERIAL PRIMARY KEY,
  parent_id    BIGINT UNSIGNED,
  comment      TEXT NOT NULL,
  FOREIGN KEY (parent_id) REFERENCES Comments(comment_id)
);

```



## 계층구조 저장 및 조회하기

- 데이터가 재귀적 관계를 가지는 것은 흔한 일이다. 데이터는 트리나 계층적 구조가 될 수 있다.
- 트리 데이터 구조에서 각 항목은 노드라 불리고 노드는 여러 개의 자식을 가질 수 있고 부모를 하나 가진다. 부모가 없는 최상위 노드를 root라고 하고 가장 아래에 있는 자식이 없는 노드를 leaf라고한다. 중간에 있는 노드는 그냥 노드다.
- 트리 데이터 구조를 가지는 예
  - 조직도
  - 글타래



## 안티패턴: 항상 부모에 의존하기

- 초보적 방법은 parent_id 칼럼을 추가하는 것이다.

```sql
CREATE TABLE Comments (
  comment_id   SERIAL PRIMARY KEY,
  parent_id    BIGINT UNSIGNED,
  bug_id       BIGINT UNSIGNED NOT NULL,
  author       BIGINT UNSIGNED NOT NULL,
  comment_date DATETIME NOT NULL,
  comment      TEXT NOT NULL,
  FOREIGN KEY (parent_id) REFERENCES Comments(comment_id),
  FOREIGN KEY (bug_id) REFERENCES Bugs(bug_id),
  FOREIGN KEY (author) REFERENCES Accounts(account_id)
);

```





**인접 목록에서 트리 조회하기**

- 답글과 그 답글의 바로 아래 자식은 비교적 간단한 쿼리로 얻을 수 있지만 단 두 단계만 조회할 수 있다.

```sql
SELECT c1.*, c2.*
FROM Comments c1 LEFT OUTER JOIN Comments c2
  ON c2.parent_id = c1.comment_id;

```

- 트리의 특징 중 하나가 어느 깊이까지든 확장될 수 있다는 것이므로 단계에 상관없이 후손들을 조회할 수 있어야 한다.
- `COUNT()` 를 통해 답글 수를 계산하거나 `SUM()` 을 이용해 기계 조립에서 부품의 비용 합계를 구할 수 있어야한다.
- 또 다른 방법으로 글타래의 모든 행을 가져와서 애플리케이션에서 계층 구조를 만드는 방법인데 이 방법은 대량의 데이터를 애플리케이션으로 가져오는 것이기 때문에 비효율적이다.





**인접 목록에서 트리 유지하기**

- 새로운 노드를 추가하는 것과 같은 일부 연산은 간단하고 노드 하나 또는 서브트리를 이동하는 것 역시 쉽게 할 수 있다.

```sql
INSERT INTO Comments (bug_id, parent_id, author, comment)
  VALUES (1234, 7, 'Kukla', 'Thanks!');

UPDATE Comments SET parent_id = 3 WHERE comment_id = 6;
```

- 하지만 트레이서 노드를 삭제하는 작업은 복잡하고 비효율적이다.

```sql
SELECT comment_id FROM Comments WHERE parent_id = 4; -- returns 5 and 6
SELECT comment_id FROM Comments WHERE parent_id = 5; -- returns none
SELECT comment_id FROM Comments WHERE parent_id = 6; -- returns 7
SELECT comment_id FROM Comments WHERE parent_id = 7; -- returns none

DELETE FROM Comments WHERE comment_id IN ( 7 );
DELETE FROM Comments WHERE comment_id IN ( 5, 6 );
DELETE FROM Comments WHERE comment_id = 4;

```



## 안티패턴 인식 방법

- 다음과 같은 말을 듣는다면 순진한 트리 안티패턴이 사용되고 있음을 눈치챌 수 있다.
  - "트리에서 얼마나 깊은 단계를 지원해야 하지?"
  - "트리 데이터 구조를 관리하는 코드는 건드리는게 겁나"
  - "트리에서 고아 노드를 정리하기 위해 주기적으로 스크립트를 돌려야 해"



## 안티패턴 사용이 합당한 경우

- 인접 목록의 강점은 주어진 노드의 부모나 자식을 바로 얻어올 수 있고 새로운 노드를 추가하기도 쉽기 때문에 계층적 데이터로 작업하는 데 이 정도로만으로도 충분하다면 인접 목록은 적절한 방법이다.
- WITH 키워드, CTE(Common Table Expression)을 사용한 재귀적 쿼리 문법을 사용할 수 있는 DBMS라면 순진한 트리 구조를 사용해도 제약은 없어진다.

```sql
-- WITH절
WITH CommentTree
    (comment_id, bug_id, parent_id, author, comment, depth)
AS (
    SELECT *, 0 AS depth FROM Comments
    WHERE parent_id IS NULL
  UNION ALL
    SELECT c.*, ct.depth+1 AS depth FROM CommentTree ct
    JOIN Comments c ON (ct.comment_id = c.parent_id)
)
SELECT * FROM CommentTree WHERE bug_id = 1234;

-- START WITH ... CONNECT BY PRIOR
SELECT * FROM Comments
START WITH comment_id = 9876
CONNECT BY PRIOR parent_id = comment_id;
```

- 초판이 2011년 인걸 감안하면 이해가 가능하지만 2022년인 지금 재귀적 쿼리를 지원하지 않는 dbms는 거의 없다는게 함정



## 해법: 대안 트리 모델 사용

- 계층적 데이터를 저장하는 데는 인접 목록 모델(순진한 트리) 외에도 경로 열거(Path Enumeration), 중첩 집합(Nested Sets), 클로저 테이블(Closure Table)과 같은 몇가지 대안이 있다.



**경로 열거**

- 경로 열거 방법에서는 일련의 조상을 각 노드의 속성으로 저장해 이를 해결한다.
- 디렉토리 구조에서도 경로 열거 형태를 볼 수 있다.
  - /usr/local/lib 
    - /usr는 local의 부모
    - /local은 lib의 부모
- 기존 parent_id 칼럼 대신 path란 칼럼을 정의한다.

```sql
CREATE TABLE Comments (
  comment_id   SERIAL PRIMARY KEY,
  path         VARCHAR(1000),
  bug_id       BIGINT UNSIGNED NOT NULL,
  author       BIGINT UNSIGNED NOT NULL,
  comment_date DATETIME NOT NULL,
  comment      TEXT NOT NULL,
  FOREIGN KEY (bug_id) REFERENCES Bugs(bug_id),
  FOREIGN KEY (author) REFERENCES Accounts(account_id)
);

```



| comment_id | path    | author | comment |
| ---------- | ------- | ------ | ------- |
| 1          | 1/      | A      | A       |
| 2          | 1/2/    | B      | B       |
| 3          | 1/2/3/  | C      | C       |
| 4          | 1/4/    | D      | D       |
| 5          | 1/4/5/  | E      | E       |
| 6          | 1/4/6/  | F      | F       |
| 7          | 1/4/6/7 | G      | G       |

- 위와 같은 형태에서 경로가 /1/4/6/7/ 인 답글 #7의 조상을 찾으려면 다음과 같이 할 수 있다.

```sql
SELECT *
FROM Comments AS c
WHERE '1/4/6/7/' LIKE c.path || '%';

-- 1/4/6/%, 1/4/%, 1/% 로 매칭
```

- 반대로 경로가 1/4/인 답글 #4의 후손을 찾으려면 다음과 같이 할 수 있다.

```sql
SELECT *
FROM Comments AS c
WHERE c.path LIKE '1/4/' || '%';

-- /1/4/5, 1/4/6, 1/4/6/7 매칭
```

- 특정 노드의 서브트리에서 글쓴이당 답글 수를 세는 집계함수역시 쉽게 사용할 수 있다.

```sql
SELECT COUNT(*)
FROM Comments AS c
WHERE c.path LIKE '1/4/' || '%'
GROUP BY c.author;

```

- 새로운 노드를 추가하는 방법은 인접 목록 모델에서와 비슷하게 부모 경로 + 새로운 노드의 아이디를 덧붙이는 방법으로 추가할 수 있다.

```sql
-- MySQL의 LAST_INSERT_ID()를 사용해서 path 수정
INSERT INTO Comments (author, comment) VALUES ('Ollie', 'Good job!');
UPDATE Comments 
  SET path = (SELECT path FROM Comments WHERE comment_id = 7)
    || LAST_INSERT_ID() || '/'
WHERE comment_id = LAST_INSERT_ID();
```

- 경로 열거 방법은 경로가 올바르게 형성되도록 하거나 값이 실제 노드에 대응되도록 강제하는 방법은 없다. 경로 문자열을 유지하는 것은 애플리케이션 코드에 종속되며 이를 검증하는데는 비용이 많이 든다.
- 경로 열거 방법은 VARCHAR 칼럼의 길이만큼 제한이 있기 때문에 무제한 확장은 불가능하다.



**중첩 집합**

- 중첩 집합은 각 노드가 자신의 부모를 저장하는 대신 자기 자손의 집합에 대한 정보를 저장한다.

```sql
CREATE TABLE Comments (
  comment_id   SERIAL PRIMARY KEY,
  nsleft       INTEGER NOT NULL,
  nsright      INTEGER NOT NULL,
  bug_id       BIGINT UNSIGNED NOT NULL,
  author       BIGINT UNSIGNED NOT NULL,
  comment_date DATETIME NOT NULL,
  comment      TEXT NOT NULL,
  FOREIGN KEY (bug_id) REFERENCES Bugs (bug_id),
  FOREIGN KEY (author) REFERENCES Accounts(account_id)
);

```

- 이 정보는 트리의 각 노드를 두 개의 수로 부호화해 나타낼 수 있는데 여기에선 nsleft와 nsright로 구분한다.
- 각 노드의 nsleft와 nsright 수는 다음과 같이 주어진다.
  - nsleft 는 모든 자식 노드의 nsleft 보다 작아야 한다.
  - nsright 는 모든 자식의 노드의 nsright 보다 커야 한다.
  - 이 수들은 comment_id 값과는 아무런 상관이 없다.



![3-1](./images/sql-antipatterns/3-1.jpeg)



| comment_id | nsleft | nsright | author | comment |
| ---------- | ------ | ------- | ------ | ------- |
| 1          | 1      | 14      | A      | A       |
| 2          | 2      | 5       | B      | B       |
| 3          | 3      | 4       | C      | C       |
| 4          | 6      | 13      | D      | D       |
| 5          | 7      | 8       | E      | E       |
| 6          | 9      | 12      | F      | F       |
| 7          | 10     | 11      | G      | G       |

- nsleft 값이 현재 노드의 nsleft와 nsright 사이에 있는 노드를 검색하면 답글 #4 와 그 자손을 얻을 수 있다.

```sql
SELECT c2.*
FROM Comments AS c1
  JOIN Comments as c2
    ON c2.nsleft BETWEEN c1.nsleft AND c1.nsright
WHERE c1.comment_id = 4;

```

- 답글 #6과 그 조상은 nsright 값이 현재 노드의 숫자 사이에 있는 노드를 검색해 얻을 수 있다.

```sql
SELECT c2.*
FROM Comments AS c1
  JOIN Comment AS c2
    ON c1.nsleft BETWEEN c2.nsleft AND c2.nsright
WHERE c1.comment_id = 6;

```

- 중첩 집합 모델의 주요 강점 중 하나는 자식을 가진 노드를 삭제했을 때 그 자손이 자동으로 삭제된 노드 부모의 자손이 된다. 노드를 삭제해도 트리 구조에서는 아무런 문제가 없다.

```sql
-- Reports depth = 3
SELECT c1.comment_id, COUNT(c2.comment_id) AS depth
FROM Comment AS c1
  JOIN Comment AS c2
    ON c1.nsleft BETWEEN c2.nsleft AND c2.nsright
WHERE c1.comment_id = 7
GROUP BY c1.comment_id;

DELETE FROM Comment WHERE comment_id = 6;

-- Reports depth = 2
SELECT c1.comment_id, COUNT(c2.comment_id) AS depth
FROM Comment AS c1
  JOIN Comment AS c2
    ON c1.nsleft BETWEEN c2.nsleft AND c2.nsright
WHERE c1.comment_id = 7
GROUP BY c1.comment_id;

```

- 중첩 집합 모델의 단점은 부모 노드를 얻는 과정이 아래와 같이 복잡해진다.

```sql
SELECT parent.*
FROM Comment AS c
  JOIN Comment AS parent
    ON c.nsleft BETWEEN parent.nsleft AND parent.nsright
  LEFT OUTER JOIN Comment AS in_between
    ON c.nsleft BETWEEN in_between.nsleft AND in_between.nsright
    AND in_between.nsleft BETWEEN parent.nsleft AND parent.nsright
WHERE c.comment_id = 6
  AND in_between.comment_id IS NULL;

```

- 중첩 집합 모델에서는 노드를 추가, 이동하는 것과 같은 트리 조작도 다른 모델을 사용할 때보다 복잡하다. 새로운 노드를 추가할 때마다 새 노드의 왼쪽 값보다 큰 모든 노드의 왼쪽, 오른쪽 값을 다시 계산해야 한다.

```sql
-- make space for NS values 8 and 9
UPDATE Comment
  SET nsleft = CASE WHEN nsleft >= 8 THEN nsleft+2 ELSE nsleft END,
      nsright = nsright+2
WHERE nsright >= 7;

-- create new child of comment #5, occupying NS values 8 and 9
INSERT INTO Comment (nsleft, nsright, author, comment)
  VALUES (8, 9, 'Fran', 'Me too!');

```

- 중첩 집합 모델은 각 노드에 대해 조작하는 것 보다는 서브트리를 쉽고 빠르게 조회하는 것이 중요할 때 가장 잘 맞는다.
- 트리에 노드를 삽입하는 과정이 복잡하기 때문에 쓰기가 빈번하다면 중첩 집합은 좋은 선택이 아니다.



**클로저 테이블**

- 클로저 테이블은 부모-자식 관계에 대한 경로만을 저장하는 것이 아니라 트리의 모든 경로를 저장한다.

```sql
CREATE TABLE Comments (
  comment_id   SERIAL PRIMARY KEY,
  bug_id       BIGINT UNSIGNED NOT NULL,
  author       BIGINT UNSIGNED NOT NULL,
  comment_date DATETIME NOT NULL,
  comment      TEXT NOT NULL,
  FOREIGN KEY (bug_id) REFERENCES Bugs(bug_id),
  FOREIGN KEY (author) REFERENCES Accounts(account_id)
);

CREATE TABLE TreePaths (
  ancestor    BIGINT UNSIGNED NOT NULL,
  descendant  BIGINT UNSIGNED NOT NULL,
  PRIMARY KEY(ancestor, descendant),
  FOREIGN KEY (ancestor) REFERENCES Comments(comment_id),
  FOREIGN KEY (descendant) REFERENCES Comments(comment_id)
);

```

- 트리 구조에 대한 정보를 Comments 테이블에 저장하는 대신 TreePaths를 사용한다.
- 이 테이블에는 트리에서 조상/자손 관계를 가진 모든 노드 쌍을 한 행으로 저장한다. 또한 각 노드에 대해 자기 자신을 참조하는 행도 추가한다.



![3-2](./images/sql-antipatterns/3-2.jpeg)



| ancestor | descendant |
| -------- | :--------- |
| 1        | 1          |
| 1        | 2          |
| 1        | 3          |
| 1        | 4          |
| 1        | 5          |
| 1        | 6          |
| 1        | 7          |
| 2        | 2          |
| 2        | 3          |
| 3        | 3          |
| 4        | 4          |
| 4        | 5          |
| 4        | 6          |
| 4        | 7          |
| 5        | 5          |
| 6        | 6          |
| 6        | 7          |
| 7        | 7          |



- 답글 #4의 자손을 얻으려면 TreePaths에서 ancestor가 4인 행을 가져오면 된다.

```sql
SELECT c.*
FROM Comments AS c
  JOIN TreePaths AS t ON c.comment_id = t.descendant
WHERE t.ancestor = 4;

```

- 답글 #6의 조상을 얻으려면 TreePath에서 descendant가 6인 행을 가져오면 된다.

```sql
SELECT c.*
FROM Comments AS c
  JOIN TreePaths AS t ON c.comment_id = t.ancestor
WHERE t.descendant = 6;

```

- 새로운 종말 노드, 예를 들어 답글 #5에 새로운 자식을 추가하려면 먼저 자기 자신을 참조하는 행을 추가하고 TreePaths에서 답글 #5를  descendant로 참조하는 모든 행을 복사해 descendant를 새로운 답글 아이디로 바꿔 넣는다.

```sql
INSERT INTO TreePaths (ancestor, descendant)
  SELECT t.ancestor, 8
  FROM TreePaths AS t
  WHERE t.descendant = 5
 UNION ALL
  SELECT 8, 8;

```

- 종말 노드를 삭제할 때, 예를 들어 #7을 삭제할 때는 TreePaths에서 descendant로 참조하는 모든 행을 삭제한다.

```sql
DELETE FROM TreePaths WHERE descendant = 7;

```

- 서브트리, 예를들어 답글#4와 그 자손을 삭제하려면 TreePaths에서 답글 #4를 descendant로 참조하는 모든 행과  답글 #4의 자손을 descendant로 참조하는 모든 행을 삭제한다.

```sql
DELETE FROM TreePaths
WHERE descendant IN (SELECT descendant
		     FROM TreePaths
		     WHERE ancestor = 4);

```

- 서브트리를 트리 내 다른 위치로 이동하고자 할 때는, 먼저 서브트리의 최상위 노드와 그 노드의 자손들을 참조하는 행을 삭제해 서브트리와 그 조상의 연결을 끊는다.
- 예를 들어 답글 #6을 답글 #4의 자식에서 #3의 자식으로 옮기려면 아래와 같이 한다.

```sql
-- START:delete
DELETE FROM TreePaths
WHERE descendant IN (SELECT descendant
		     FROM TreePaths
		     WHERE ancestor = 6)
  AND ancestor IN (SELECT ancestor
		   FROM TreePaths
		   WHERE descendant = 6
		     AND ancestor != descendant);
-- END:delete
-- 위 쿼리는 (1, 6), (1, 7), (4, 6), (4, 7) 을 삭제한다. 즉 자기 자신과 자기 자신이 갖고 있는 서브트리 외에 모든 참조를 제거한다.

-- START:reinsert
INSERT INTO TreePaths (ancestor, descendant)
  SELECT supertree.ancestor, subtree.descendant
  FROM TreePaths AS supertree
    CROSS JOIN TreePaths AS subtree
  WHERE supertree.descendant = 3
    AND subtree.ancestor = 6;
-- END:reinsert
-- 새로운 위치의 조상들과 서브트리의 자손들에 대응하는 행을 추가해서 고아가 된 서브트리를 붙인다.
-- 위 쿼리는 (1, 6), (2, 6), (3, 6), (1, 7), (2, 7), (3, 7) 경로를 새로 생성한다.

```

- 클로저 테이블 모델은 중첩 집합 모델보다 직관적이다. 조상과 자손을 조회하는 것은 두 방법 모두 빠르고 쉽지만 클로저 테이블이 계층구조 정보를 유지하기가 쉽다. 
- 두 방법 모두 인접 목록이나 경록 열거 방법보다 자식이나 부모를 조회하기 편리하다.
- 부모나 자식 노드를 더 쉽게 조회할 수 있도록 TreePaths에 path_length 속성을 추가하면 클로저 테이블을 개선할 수 있다. 
- 자기자신의 path_length는 0, 자식의 path_length는 1 ... 2 형태로 구성하면 아래와 같이 자식을 쉽게 조회할 수 있다.

```sql
SELECT *
FROM TreePaths
WHERE ancestor = 4 AND path_length = 1;

```



**어떤 모델을 사용해야 하는가?**

- 인접 목록은 가장 흔히 사용되는 모델로 많은 소프트웨어 개발자가 알고 있다.
- WITH나 CONNET BY PRIOR를 이용한 재귀적 쿼리는 인접 목록 모델을 좀 더 효율적으로 만들지만 이 문법을 지원하는 데이터베이스를 써야 한다.
- 경로 열거는 브레드크럼을 사용자 인터페이스에 보여줄 때는 좋지만 참조 정합성을 강제하지 못하고 정보를 중복 저장하기 때문에 깨지기 쉬운 구조다.
- 중첩 집합은 트리를 수정하는 일은 거의 없고 조회를 많이 하는 경우 적합하다. 역시 참조 정합성을 지원하지는 못한다.
- 클로저 테이블은 가장 융통성 있는 모델이고 한 노드가 여러 트리에 속하는 것을 허용하는 유일한 모델이다. 클로저 테이블은 별도의 저장공간을 사용하기 때문에 계산을 줄이는 대신 저장공간을 많이 사용하는 트레이드오프가 발생한다.

<br>

| 모델          | 테이블 | 자식 조회 | 트리 조회 | 삽입   | 삭제   |
| ------------- | ------ | --------- | --------- | ------ | ------ |
| 인접 목록     | 1      | 쉽다      | 어렵다    | 쉽다   | 쉽다   |
| 재귀적 쿼리   | 1      | 쉽다      | 쉽다      | 쉽다   | 쉽다   |
| 경로 열거     | 1      | 쉽다      | 쉽다      | 쉽다   | 쉽다   |
| 중첩 집합     | 1      | 어렵다    | 쉽다      | 어렵다 | 어렵다 |
| 클로저 테이블 | 2      | 쉽다      | 쉽다      | 쉽다   | 쉽다   |



**SQL Antipatterns Tip**

- 계층구조에는 항목과 관계가 있다. 작업에 맞도록 이 둘을 모두 모델링해야 한다.



# #4 아이디가 필요해



## 목표: PK 관례 확립

- 목표는 모든 테이블이 PK를 갖도록 하는 것이지만 PK의 본질을 혼동하면 안된다.
- PK는 테이블 내의 모든 행이 유일함을 보장하기 때문에, 각 행에 접근하는 논리적 메커니즘이 되고 중복 행이 저장되는 것을 방지한다.
- 까다로운 부분은  PK로 사용할 칼럼을 선정하는 일이다.
- 아무런 의미도 갖지 않는 인위적인 값을 PK로 잡을때는 이를 가상키 또는 대체키라 한다.
- 여러 클라이언트가 동시에 새로운 행을 삽입하는 경우에도 각 행의 가상키 값이 유일하게 할당되는 것을 보장하기 위해, 대부분의 DBMS는 트랜잭션 격리 범위 밖에서 유일한 정수값을 생성하는 메커니즘을 제공한다.

| 기능           | 지원 데이터베이스                                   |
| -------------- | --------------------------------------------------- |
| AUTO_INCREMENT | MySQL                                               |
| GENERATOR      | Firebird, InterBase                                 |
| IDENTITY       | DB2, Derby, Microsoft SQL Server, Sybase            |
| ROWID          | SQLite                                              |
| SEQUENCE       | DB2, Firebird, Informix, Ingres, Oracle, PostgreSQL |
| SERIAL         | MySQL, PostgreSQL                                   |



## 안티 패턴: 만능키

- 모든 테이블에 id란 이름의 칼럼이 있는 것은 너무도 흔해져 이게 PK와 동의어가 되어 버렸다.
- 모든 테이블에 id 칼럼을 추가하는 것은, 그 사용을 이상하게 만드는 몇 가지 효과를 초개한다.

```sql
CREATE TABLE Bugs (
  id          SERIAL PRIMARY KEY,
  description VARCHAR(1000),
  -- . . .
);

```



**중복 키 생성**

- 테이블 안에 다른 칼럼이 자연키로 사용될 수 있는 상황에서조차 단지 통념에 따라 id 칼럼을 PK로 정의하고 bug_id는 UNIQUE를 사용하는 경우

```sql
CREATE TABLE Bugs (
  id          SERIAL PRIMARY KEY,
  bug_id      VARCHAR(10) UNIQUE,
  description VARCHAR(1000),
  -- . . .
);

INSERT INTO Bugs (bug_id, description, ...)
  VALUES ('VIS-078', 'crashes on save', ...);

```

- 사실 bug_id칼럼은 각 행을 유일하게 식별할 수 있도록 해준다는 면에서 id와 사용 목적이 동일하다.

**중복 행 허용**

- 복합 키는 여러 칼럼을 포함한다. PK는 특정한 bug_id와 product_id 값의 조합이 테이블 안에서 한 번만 나타난다는 것을 보장해야 하지만 id 칼럼을 PK로 사용하는 경우 두 칼럼에 제약조건이 적용되지 않는다.



```sql
-- START:typical
CREATE TABLE BugsProducts (
  id          SERIAL PRIMARY KEY,
  bug_id      BIGINT UNSIGNED NOT NULL,
  product_id  BIGINT UNSIGNED NOT NULL,
  FOREIGN KEY (bug_id) REFERENCES Bugs(bug_id),
  FOREIGN KEY (product_id) REFERENCES Products(product_id)
);

INSERT INTO BugsProducts (bug_id, product_id)
  VALUES (1234, 1), (1234, 1), (1234, 1); -- duplicates are permitted
-- END:typical
```



- 방법은 bug_id, product_id에 UNIQUE제약조건을 거는 것인데 이런 경우엔 id칼럼은 불필요한 것이다.

```sql
-- START:unique
CREATE TABLE BugsProducts (
  id          SERIAL PRIMARY KEY,
  bug_id      BIGINT UNSIGNED NOT NULL,
  product_id  BIGINT UNSIGNED NOT NULL,
  UNIQUE KEY (bug_id, product_id),
  FOREIGN KEY (bug_id) REFERENCES Bugs(bug_id),
  FOREIGN KEY (product_id) REFERENCES Products(product_id)
);
-- END:unique
```



**모호한 키의 의미**

- id란 이름이 너무 일반적이기 때문에 아루먼 의미도 갖지 못한다.
- 이는 PK칼럼 이름이 동일한 두 테이블을 조인할 때 특히 문제가 된다.



**USING 사용**

**어려운 복합키**



## 안티패턴 인식 방법

- 다음과 같은 말은 안티패턴의 증거가 될 수 있다.
  - 모든 테이블의 PK칼럼이 id라는 이름을 갖고 있다.
  - "이 테이블에는 PK가 없어도 될 것 같은데"
  - "다대다 연결에서 왜 중복이 발생하지?"
  - "나는 데이터베이스 이론에서 값은 색인 테이블로 옮기고  ID로 참조해야한다고 하는 걸 읽어서. 그러나 그렇게 하고 싶지 않아. 내가 원하는 실제 값을 얻기 위해 매번 조인을 해야 하기 때문이지."



## 안티패턴 사용이 합당한 경우

- 가상키는 지나치게 긴 자연키를 대체하기 위해 사용한다면 적절한 선택이다.
  - 파일 시스템의 파일 속성을 저장하는 테이블에서 파일 경로는 좋은 자연키이지만 인덱스로 잡기엔 비용이 많이 든다.



## 해법: 상황에 맞추기

- PK는 제약조건이지 데이터 타입이 아니다.
- 또한 테이블의 특정 칼럼을 PK로 잡지 않고도 자동 증가하는 정수값을 가지도록 정의할 수 있다.



**있는 그대로 말하기**

- PK에 의미 있는 이름을 선택해야 한다. 이 이름은 PK가 식별하는 엔터티의 타입을 나타내야 한다.
- FK에서도 가능하다면 같은 칼럼 이름을 사용하는게 기본이지만 연결의 본질을 더 잘 표현하는 경우라면 FK를 자신이 참조하는 PK이름과 다르게 하는 것도 괜찮다.

```sql
CREATE TABLE Bugs (
  -- . . .
  reported_by  BIGINT UNSIGNED NOT NULL,
  FOREIGN KEY (reported_by) REFERENCES Accounts(account_id)
);

```



**관례에서 벗어나기**

**자연키와 복합키 포용**

- 유일함이 보장되고, NULL 값을 가지는 경우가 없고, 행을 식별하는 용도로 사용할 수 있는 속성이 테이블에 있다면, 단지 통념을 따르기 위해 가상키를 추가해야 한다는 의무감을 느낌 필요는 없다.
- 실제로 테이블에 있는 각 속성은 변하기 마련이고 유일하지 않게 될 수도 있기 때문에 이런 경우에는 가상키를 사용할 수 있다.
- 여러 칼럼의 조합으로 행을 가장 잘 식별할 수 있다면 이 칼럼 조합을 복합키로 사용해야 한다.

```sql
-- START:table
CREATE TABLE BugsProducts (
  bug_id      BIGINT UNSIGNED NOT NULL,
  product_id  BIGINT UNSIGNED NOT NULL,
  PRIMARY KEY (bug_id, product_id),
  FOREIGN KEY (bug_id) REFERENCES Bugs(bug_id),
  FOREIGN KEY (product_id) REFERENCES Products(product_id)
);
-- END:table

-- START:insert
INSERT INTO BugsProducts (bug_id, product_id)
  VALUES (1234, 1), (1234, 2), (1234, 3);

INSERT INTO BugsProducts (bug_id, product_id)
  VALUES (1234, 1); -- error: duplicate entry
--END:insert

```

- 복합 PK를 참조하는 FK 또한 복합키가 되어야 함에 유의해야 한다.



**SQL Antipatterns Tip**

- 관례는 도움이 될 때만 좋은 것이다.



# #5 키가 없는 엔트리

## 목표: 데이터베이스 아키텍처 단순화

- 관계형 데이터베이스 설계는 각 테이블 자체에 대한 것이기도 하고 테이블간의 관계에 대한 것이기도 하다.
- 참조 정합성은 데이터베이스를 적절히 설계하고 운영하는 데 있어 중요한 부분이다.
- 어떤 칼럼 또는 칼럼 묶음에 FK 제약조건을 선언하면, 그 칼럼에 들어가는 값은 부모 테이블의 PK 또는 UNIQUE KEY에 의존해야 한다. 
- FK를 무시하라는 이유들
  - 데이터 업데이트 시 제약조건과 충돌할 수 있다.
  - 참조 정합성 제약조건을 지원할 수 없는 매우 융통성 있는 데이터베이스 설계를 사용하고 있다.
  - FK에 데이터베이스가 자동 생성하는 인덱스 때문에 성능에 영향을 받는다고 믿는다.
  - FK를 지원하지 않는 데이터베이스를 사용하고 있다.
  - FK 선언을 위해 문법을 찾아봐야 한다.



## 안티패턴: 제약조건 무시

- FK 제약조건을 생략하는 것이 처음에는 데이터베이스 설계를 단순하고 유연하고 빠르게 하는 것처럼 보이겠지만 다른 방식으로 대가를 치러야 한다. (참조정합성에 대한 대가)



**무결점 코드**

- 많은 사람들이 참조 정합성을 애플리케이션 코드에 녹인다.
- 

```sql
-- # 변경전 select로 참조 정합성 확인
-- START:select
SELECT account_id FROM Accounts WHERE account_id = 1;
-- END:select
-- START:insert
INSERT INTO Bugs (reported_by) VALUES (1);
-- END:insert


-- #변경전 select로 참조 정합성 확인
-- START:select
SELECT bug_id FROM Bugs WHERE reported_by = 1;
-- END:select
-- START:delete
DELETE FROM Accounts WHERE account_id = 1;
-- END:delete
```

- 위 방법은 동시성 문제가 생길 수 있고 테이블락을 사용할경우 병목이 될 수 있다.



**오류 확인**

- 손상된 데이터를 찾기 위해 개발자가 작성한 스크립트로 참조 정합성이 깨지는부분을 아래와 같이 수정해볼 수 있다.

```sql
SELECT b.bug_id, b.status
FROM Bugs b LEFT OUTER JOIN BugStatus s
  ON (b.status = s.status)
WHERE s.status IS NULL;

UPDATE Bugs SET status = DEFAULT WHERE status = 'BANANA';
```

- 그러나 위 스크립트를 얼마나 자주 해야하는지 정확한 타이밍이나 횟수를 계산하기 어렵고 디폴트값이 없는 칼럼에 대해서 좋은 방어책이 아니다.



**"내 잘못이 아냐!"**

- 데이터베이스는 일관성 있게 유지해야하지만 데이터베이스에 접근하는 모든 애플리케이션과 스크립트가 올바르게 변경을 가하는지 확신할 수 없다.



**진퇴양난 업데이트**

- 많은 개발자가 여러 테이블의 관련된 칼럼을 업데이트할 때 불편해지기 때문에 FK 제약조건 사용을 꺼린다.

```sql
DELETE FROM BugStatus WHERE status = 'BOGUS'; -- ERROR!
DELETE FROM Bugs WHERE status = 'BOGUS';
DELETE FROM BugStatus WHERE status = 'BOGUS'; -- retry succeeds


UPDATE BugStatus SET status = 'INVALID' WHERE status = 'BOGUS'; -- ERROR!
UPDATE Bugs SET status = 'INVALID' WHERE status = 'BOGUS'; -- ERROR!

```



## 안티패턴 인식 방법

- 사람들이 다음과 같은 말을 하는 걸 들으면 아마도 키가 없는 엔트리 안티패턴을 사용하고 있을 것이다.
  - "어떤 값이 한 테이블에는 있고 다른 테이블에는 없는지 확인하려면 쿼리를 어떻게 작성해야 하지?"
  - "테이블에 삽입하면서 다른 테이블에 어떤 값이 있는지를 확인하는 빠른 방법이 없을까?"
  - "FK라고? FK는 데이터베이스를 느리게 만들기 때문에 사용하지 말라고 들었는데?"



## 안티패턴 사용이 합당한 경우

- FK제약조건을 지원하지 않는 데이터베이스 (MySQL의  myisam엔진, 3.6.19 이전의 SQLite) 을 사용할 수밖에 없는 경우
- 관계를 모델링하는 데 FK를 사용할 수 없는 극단적으로 유연한 데이터베이스 설계인 경우



## 해법: 제약조건 선언하기

- 데이터베이스 설계에 참조 정합성을 강제해서 추가 공정 없이 처음부터 잘못된 데이터가 입력되지 않도록 할 수 있다.

```sql
CREATE TABLE Bugs (
  -- . . .
  reported_by       BIGINT UNSIGNED NOT NULL,
  status            VARCHAR(20) NOT NULL DEFAULT 'NEW',
  FOREIGN KEY (reported_by) REFERENCES Accounts(account_id),
  FOREIGN KEY (status) REFERENCES BugStatus(status)
);

```

- FK를 사용하면 불필요한 코드를 작성하지 않아도 되고, 데이터베이스를 변경할 때도 모든 코드가 동일한 제약조건을 따른다는 것을 확신할 수 있다.

**여러 테이블 변경 지원**

- FK는 애플리케이션 코드로 흉내낼 수 없는 단계적 업데이트(cascading update)기능이 있다.

```sql
CREATE TABLE Bugs (
  -- . . .
  reported_by       BIGINT UNSIGNED NOT NULL,
  status            VARCHAR(20) NOT NULL DEFAULT 'NEW',
  FOREIGN KEY (reported_by) REFERENCES Accounts(account_id)
    ON UPDATE CASCADE
    ON DELETE RESTRICT,
  FOREIGN KEY (status) REFERENCES BugStatus(status)
    ON UPDATE CASCADE
    ON DELETE SET DEFAULT
);

```



**오버헤드? 그닥~**

- FK 제약조건이 약간의 오버헤드가 있는 것은 사실이지만 다른 대안과 비교했을 때 FK가 훨씬 효울적이라는 것은 입증되었다.
  - INSERT, UPDATE, DELETE 전에 데이터를 확인하기 위해 SELECT 쿼리를 실행할 필요가 없다.
  - 여러 테이블을 변경하기 위해 테이블 잠금을 사용할 필요가 없다.
  - 불가피하게 생기는 고아 데이터를 정정하기 위해 품질 제어 스크림트를 주기적으로 돌릴 필요가 없다.
- FK는 사용하기 쉽고, 성능을 향상시킬 뿐 아니라, 단순하든 복잡하든 데이터를 변경할 때 참조 정합성을 일관적으로 유지하는데 도움이 된다.

**SQL Antipatterns Tip**

- 제약조건을 사용해 데이터베이스에서 실수를 방지하라.







# #6 엔터티-속성-값

## 목표: 가변 속성 지원

- 소프트웨어 프로젝트에서 확장성은 자주 목표로 언급이 된다.
- 일반적인 테이블은 테이블에 있는 모든 행과 관계된 속성 칼럼으로 이루어져 있고, 각 행은 비슷한 객체의 인스턴스를 나타낸다. 속성 집합이 다르면 객체의 타입도 다르다는 뜻이며, 따라서 다른 테이블에 있어야 한다.
- 그러나 현대적인 객체지향 프로그래밍 모델에서는 동일한 데이터 타입을 확장하는 것과 같은 방법으로 객체의 타입도 관계를 가질 수 있다.

사진

- 위 이미지에서 Bug와 FeatureRequest는 Issue 타입이 갖는 속성을 기본으로 갖고 각각 자신만의 속성을 추가로 갖는다.

## 안티패턴: 범용 속성 테이블 사용

- 가변 속성을 지원해야 할 때 별도 테이블을 생성해 속성을 행으로 저장할 수 있다.

`엔터티`

- 보통 이 칼럼은 하나의 엔터티에 대해 하나의 행을 가지는 부모 테이블에 대한 FK다

`속성`

- 일반적인 테이블에서의 칼럼 이름을 나타내지만, 이 새로운 설계에서는 각 행마다 속성이 하나씩 들어간다.

`값`

- 모든 엔터티는 각 속성에 대한 값을 가진다. 예를 들어 PK 값이 1234인 버그가 주어졌을 때, status란 속성을 가지고, 그 속성 값은 NEW다



사진



- 이 설계는 엔터티-속성-값 또는 줄여서 EAV라 불린다.

```sql
CREATE TABLE Issues (
  issue_id    SERIAL PRIMARY KEY
);

INSERT INTO Issues (issue_id) VALUES (1234);

CREATE TABLE IssueAttributes (
  issue_id    BIGINT UNSIGNED NOT NULL,
  attr_name   VARCHAR(100) NOT NULL,
  attr_value  VARCHAR(100),
  PRIMARY KEY (issue_id, attr_name),
  FOREIGN KEY (issue_id) REFERENCES Issues(issue_id)
);

INSERT INTO IssueAttributes (issue_id, attr_name, attr_value)
  VALUES
    (1234, 'product',          '1'),
    (1234, 'date_reported',    '2009-06-01'),
    (1234, 'status',           'NEW'),
    (1234, 'description',      'Saving does not work'),
    (1234, 'reported_by',      'Bill'),
    (1234, 'version_affected', '1.0'),
    (1234, 'severity',         'loss of functionality'),
    (1234, 'priority',         'high');

```

- 별도 테이블을 추가해 다음과 같은 이득을 얻은 것 같아 보인다.
  - 두 테이블 모두 적은 칼럼을 갖고 있다.
  - 새로운 속성을 지원하기 위해 칼럼 수를 늘릴 필요가 없다.
  - 특정 속성이 해당 행에 적용되지 않을 때 NULL을 채워야하는 칼럼이 지저분하게 생기는 것을 피할 수 있다.
- 개선된 것처럼 보이지만 데이터베이스 설계가 단순하다고 해서 사용하기 어려운 것을 보상해주지는 않는다.



**속성 조회**

- 일반적으로 Issue 테이블에 date_reported라는 칼럼이 있으면 모든 버그와 조회일자는 아래와 같이 조회 가능하다.

```sql
SELECT issue_id, date_reported FROM Issues;

```

- EVA 설계를 사용할 때 위 쿼리와 동일한 정보를 얻으려면 IssueAttributes 테이블에서 문자열로 date_reported란 이름의 속성을 가진 행을 꺼내야 한다.

```sql
SELECT issue_id, attr_value AS "date_reported"
FROM IssueAttributes
WHERE attr_name = 'date_reported';

```

- 위 쿼리는 더 복잡하고 덜 명확하다.



**데이터 정합성 지원**

- EVA를 사용하면 일반적인 데이터베이스 설계를 사용할 때 얻을 수 있는 여러가지 장점을 희생해야 한다.

`필수 속성 사용 불가`

- 일반적인 데이터베이스 설계에서는 칼럼을 NOT NULL로 선언해 항상 값을 가지도록 강제할 수 있다.
- EVA 설계에서는 각 속성이 테이블의 칼럼이 아니라 행으로 대응된다. 하지만 이런 형태의 제약조건은 지원하지 않는다.
- EVA 설계에서는 이를 강제하는 애플리케이션 코드를 직접 작성해야 한다.

`SQL 데이터 타입 사용 불가`

- 일반적인 데이터베이스 설계에서는 칼럼의 데이터 타입을 강제해 아래와 같은 문제를 예방할 수 있다.

```sql
INSERT INTO Issues (date_reported) VALUES ('banana'); -- ERROR!

```

- EVA 설계에서는 모든 속성을 수용할 수 있는 문자열 타입을 사용하는 것이 보통이기 때문에 유효하지 않은 값을 거부할 방법이 없다.

```sql
INSERT INTO IssueAttributes (issue_id, attr_name, attr_value)
  VALUES (1234, 'date_reported', 'banana');  -- Not an error!

```



`참조 정합성 강제 불가`

- 일반적인 데이터베이스에서는 색인 테이블에 대한 FK를 정의해 특정 속성의 범위를 제한할 수 있다.

```sql
CREATE TABLE Issues (
  issue_id         SERIAL PRIMARY KEY,
  -- other columns
  status           VARCHAR(20) NOT NULL DEFAULT 'NEW',
  FOREIGN KEY (status) REFERENCES BugStatus(status)
);

```

- EVA 설계에서는 attr_value 칼럼에 이런 식의 제약조건을 적용할 수 없다.

```sql
CREATE TABLE IssueAttributes (
  issue_id         BIGINT UNSIGNED NOT NULL,
  attr_name        VARCHAR(100) NOT NULL,
  attr_value       VARCHAR(100),
  FOREIGN KEY (attr_value) REFERENCES BugStatus(status)
);

```



`속성 이름 강제 불가`

- EVA 설계에서 어떤 데이터는 date_reported 란 이름의 속성을 사용하고 어떤 데이터는 report_date란 이름의 속성을 사용한다.
- 이 방법을 강제하는 방법은 attr_name 칼럼에 들어갈 수 있는 속성을 저장하는 테이블에 대한 FK를 선언하면 해결할 수 있다.



**행을 재구성하기**

- 각 속성이 IssueAttributes 테이블의 별도 행으로 저장되어 있으므로 행 하나의 일부 속성을 꺼내기 위해서는 각 속성에 대한 조인이 필요하다. 

```sql
SELECT i.issue_id,
  i1.attr_value AS "date_reported",
  i2.attr_value AS "status",
  i3.attr_value AS "priority",
  i4.attr_value AS "description"
FROM Issues AS i
  LEFT OUTER JOIN IssueAttributes AS i1
    ON i.issue_id = i1.issue_id AND i1.attr_name = 'date_reported'
  LEFT OUTER JOIN IssueAttributes AS i2
    ON i.issue_id = i2.issue_id AND i2.attr_name = 'status'
  LEFT OUTER JOIN IssueAttributes AS i3
    ON i.issue_id = i3.issue_id AND i3.attr_name = 'priority';
  LEFT OUTER JOIN IssueAttributes AS i4
    ON i.issue_id = i4.issue_id AND i4.attr_name = 'description';
WHERE i.issue_id = 1234;

```

- 속성 개수가 늘어나면 조인 회수도 늘어나야하고 쿼리 비용은 지수적으로 증가한다.



## 안티패턴 인식 방법

- 프로젝트 팀에서 다음과 같은 말을 하는 사람이 있다면 누군가 EVA 안티패턴을 사용하고 있다는 뜻이다.
  - "이 데이터베이스는 메타데이터 변경 없이 확장이 가능하지. 런타임에 새로운 속성을 정의할 수 있어"
  - "하나의 쿼리에서 조인을 최대 몇 번이나 할 수 있지?"
  - "우리 전자상거래 플랫폼에서는 리포트를 어떻게 생성해야 할지 이해할 수가 없어. 아무래도 컨설턴트를 고용해야 할 것 같아."



## 안티패턴 사용이 합당한 경우

- 관계형 데이터베이스에서 EVA 안티패턴 사용을 합리화하기는 어렵다.
- 하지만 동적 속성을 지원하는 일부 애플리케이션의 기능에 적용할 수 있다.
- 프로젝트 계획 시 EVA 사용의 위험과 이에 따른 부가 작업을 충분히 고려하여 꼭 필요한 곳에만 사용한다면 나쁘다고만 할 수는 없지만 EVA를 사용하는 시스템은 다루기가 매우 까다롭다.
- 비관계형 데이터 관리가 필요하다면 가장 좋은 방법은 비관계형 기술을 사용하는 것이다.
  - CouchDB, MongoDB, Redis ...



## 해법: 서브타입 모델링

- EVA를 사용하지 않고 저장하는 몇가지 방법이 있다.
- 타입의 수에 제한이 있고 각 서브타입의 속성을 알고 있다면 대부분의 방법이 잘 들어 맞는다.
- 어떤 방법이 최적일지는 데이터를 어떻게 조회할지에 따라 다르기때문에 각 경우에 따라 올바른 설계를 해야 한다.

**단일 테이블 상속(Single Table Inheritance)**

- 가장 단순한 설계는 관련된 모든 타입을 하나의 테이블에 저장하고 각 타입에 있는 모든 속성을 별도의 칼럼으로 가지도록 하는 것이다.
- 예제에서 이 속성은 issue_type이다.

```sql
CREATE TABLE Issues (
  issue_id         SERIAL PRIMARY KEY,
  reported_by      BIGINT UNSIGNED NOT NULL,
  product_id       BIGINT UNSIGNED,
  priority         VARCHAR(20),
  version_resolved VARCHAR(20),
  status           VARCHAR(20),
  issue_type       VARCHAR(10),  -- BUG or FEATURE
  severity         VARCHAR(20),  -- only for bugs
  version_affected VARCHAR(20),  -- only for bugs
  sponsor          VARCHAR(50),  -- only for feature requests
  FOREIGN KEY (reported_by) REFERENCES Accounts(account_id)
  FOREIGN KEY (product_id) REFERENCES Products(product_id)
);

```

- 단일 테이블 상속의 한계는 새로운 객체 타입이 생기면 데이터베이스는 새로 생긴 객체 타입의 속성을 수용해야 한다. 이는 칼럼 수의 실질적인 한계에 직면할 수도 있다.
- 단일 테이블 상속의 또 다른 한계는 어떤 속성이 어느 서브타입에 속하는지를 정의하는 메타데이터가 없다.
- 단일 테이블 상속은 서브타입 개수가 적고 특정 서브타입에만 속하는 속성 개수가 적을 때, 그리고 액티브 레코드와 같은 단일 테이블 데이터베이스 접근 패턴을 사용해야 할 때가 가장 좋다.

**구체 테이블 상속(Concrete Table Inheritance)**

- 다른 방법은 서브타입별로 별도의 테이블을 만드는 것이다.
- 각 테이블에는 베이스타입에 있는 공통 속성뿐 아니라 특정 서브타입에만 필요한 속성도 포함된다.

```sql
CREATE TABLE Bugs (
  issue_id         SERIAL PRIMARY KEY,
  reported_by      BIGINT UNSIGNED NOT NULL,
  product_id       BIGINT UNSIGNED,
  priority         VARCHAR(20),
  version_resolved VARCHAR(20),
  status           VARCHAR(20),
  severity         VARCHAR(20), -- only for bugs
  version_affected VARCHAR(20), -- only for bugs
  FOREIGN KEY (reported_by) REFERENCES Accounts(account_id),
  FOREIGN KEY (product_id) REFERENCES Products(product_id)
);

CREATE TABLE FeatureRequests (
  issue_id         SERIAL PRIMARY KEY,
  reported_by      BIGINT UNSIGNED NOT NULL,
  product_id       BIGINT UNSIGNED,
  priority         VARCHAR(20),
  version_resolved VARCHAR(20),
  status           VARCHAR(20),
  sponsor          VARCHAR(50),  -- only for feature requests
  FOREIGN KEY (reported_by) REFERENCES Accounts(account_id),
  FOREIGN KEY (product_id) REFERENCES Products(product_id)
);

```

- 구체 테이블 상속이 단일 테이블 상속보다 좋은 점은, 특정 서브타입을 저장할 때 해당 서브타입에 적용되지 않는 속성은 저장할 수 없게 한다는 것이다.

```sql
INSERT INTO FeatureRequests (issue_id, severity) VALUES ( ... ); -- ERROR!

```

- 구체 테이블 상속의 다른 장점은 단일 테이블 상속 설계에 있어야 했던 각 행의 서브타입을 나타내는 부가적 속성이 필요하지 않다는 것이다. (issue_type)
- 그러나 서브타입 속성에서 어떤 속성이 공통 속성인지 알기 어렵고 새로운 공통속성이 추가되면 모든 서브타입 테이블을 변경해야 한다.
- 관련된 객체가 이런 서브타입 테이블에 저장되었다는 것을 알려주는 메타데이터 역시 없다. 프로젝트에 새로 투입된 개발자가 테이블 정의를 살펴보면 이 두 테이블 사이의 관계를 정확하게 파악하기 어렵다.
- 각 서브타입이 별도 테이블에 저장되어 있는 경우 서브타입에 상관없이 모든 객체를 보는 것이 복잡해진다 이 쿼리를 쉽게 하려면 각 서브타입 테이블에 공통 속성만을 선택한 다음 이를 UNION으로 묶은 뷰를 정의해야 한다.

```sql
CREATE VIEW Issues AS
  SELECT b.issue_id, b.reported_by, ... 'bug' AS issue_type
  FROM Bugs AS b
   UNION ALL
  SELECT f.issue_id, f.reported_by, ... 'feature' AS issue_type
  FROM FeatureRequests AS f;

```

- 구체 테이블 상속 설계는 모든 서브타입을 한꺼번에 조회할 필요가 거의 없는 경우에 가장 적합하다.



**클래스 테이블 상속(Class Table Inheritance)**

- 테이블이 객체지향 클래스인 것처럼 생각해 상속을 흉내내는 것이다.
- 서브타입에 공통인 속성을 포함하는 베이스타입을 위한 테이블을 하나 만들고 각 서브타입에 대해 테이블을 만든다.
- 서브타입 테이블의 PK는 베이스 테이블에 대한 FK역할도 한다.

```sql
CREATE TABLE Issues (
  issue_id         SERIAL PRIMARY KEY,
  reported_by      BIGINT UNSIGNED NOT NULL,
  product_id       BIGINT UNSIGNED,
  priority         VARCHAR(20),
  version_resolved VARCHAR(20),
  status           VARCHAR(20),
  FOREIGN KEY (reported_by) REFERENCES Accounts(account_id),
  FOREIGN KEY (product_id) REFERENCES Products(product_id)
);

CREATE TABLE Bugs (
  issue_id         BIGINT UNSIGNED PRIMARY KEY,
  severity         VARCHAR(20),
  version_affected VARCHAR(20),
  FOREIGN KEY (issue_id) REFERENCES Issues(issue_id)
);

CREATE TABLE FeatureRequests (
  issue_id         BIGINT UNSIGNED PRIMARY KEY,
  sponsor          VARCHAR(50),
  FOREIGN KEY (issue_id) REFERENCES Issues(issue_id)
);

```

- 이 설계는 메타데이터에 의해 일대일 관계가 강제된다.
- 이 설계는 검색에서 베이스 타입에 있는 속성만 참조하는 한, 모든 서브타입에 대한 검색을 하는 데 효율적인 방법을 제공한다.

```sql
SELECT i.*, b.*, f.*
FROM Issues AS i
  LEFT OUTER JOIN Bugs AS b USING (issue_id)
  LEFT OUTER JOIN FeatureRequests AS f USING (issue_id);

```

- 이 쿼리는 좋은 VIEW 후보이기도 하다.
- 모든 서브타입에 대한 조회가 많고 공통칼럼을 참조하는 경우가 많다면 이 설계가 가장 적합하다.

**반구조적 데이터(Semistructure Data)**

- 서브타입의 수가 많거나 또는 새로운 속성을 지원해야 하는 경우가 빈번하다면 데이터 속성 이름과 값을 XML 또는 JSON 형식으로 부호화해 TEXT 칼럼으로 저장할 수 있다.
- Martin Fowler는 이 패턴을 직렬화된 LOB(Serialized LOB)이라 부른다

```sql
CREATE TABLE Issues (
  issue_id         SERIAL PRIMARY KEY,
  reported_by      BIGINT UNSIGNED NOT NULL,
  product_id       BIGINT UNSIGNED,
  priority         VARCHAR(20),
  version_resolved VARCHAR(20),
  status           VARCHAR(20),
  issue_type       VARCHAR(10),   -- BUG or FEATURE
  attributes       TEXT NOT NULL, -- all dynamic attributes for the row
  FOREIGN KEY (reported_by) REFERENCES Accounts(account_id),
  FOREIGN KEY (product_id) REFERENCES Products(product_id)
);

```

- 이 설계으 장점은 확장이 쉽다는 것이다.
- 새로운 속성은 언제든 TEXT 칼럼에 저장할 수 있다.
- 단점은 이런 구조에서 SQL 특정 속성에 접근하는 것을 거의 지원하지 못한다는 점이다. (GROUP BY, SUM ...)
- 이 설계는 서브타입 개수를 제한할 필요가 없가 어느 때고 새로운 속성을 정의할 수 있는 완전한 유연성이 필요할 때 가장 적합하다.



**사후 처리**

- 프로젝트를 인계받았거나 어쩔 수 없이 EAV를 사용해야하는 경우 EAV를 사용할 때 수반되는 부가 작업을 예상하고 계획해야 한다.
- 무엇보다 일반적인 테이블에 데이터가 저장되어 있을 때처럼 엔터티를 단일 행으로 조회하는 쿼리를 작성하면 안되고 EAV 방식처럼 조회해야 한다.

```sql
SELECT issue_id, attr_name, attr_value
FROM IssueAttributes
WHERE issue_id = 1234;

```



**SQL Antipatterns Tip**

- 메타데이터를 위해서는 메타데이터를 사용하라.





# #7 다형성 연관

- 사용자는 버그에 댓글을 달 수 있는 요구사항이다. Bugs와 Comments 사이의 관계는 일대다 관계다.

```sql
CREATE TABLE Comments (
  comment_id   SERIAL PRIMARY KEY,
  bug_id       BIGINT UNSIGNED NOT NULL,
  author_id    BIGINT UNSIGNED NOT NULL,
  comment_date DATETIME NOT NULL,
  comment      TEXT NOT NULL,
  FOREIGN KEY (author_id) REFERENCES Accounts(account_id),
  FOREIGN KEY (bug_id) REFERENCES Bugs(bug_id)
);

```

- 그러나 댓글이 달릴 수 있는 테이블이 두개 일 수 있다. FeatureRequest는 별도 테이블에 저장하기는 하지만 Bugs와 비슷한 테이블이고 버그나 기능 요청 중 어느 이슈 타입과 관계되든 Comments를 하나의 테이블에 저장하고 싶은 상황이다.
- 그러나 여러개의 부모 테이블을 참조하는 FK를 만들 수는 없다.

```sql
-- START:constraint
  ...
  FOREIGN KEY (issue_id)
      REFERENCES Bugs(issue_id) OR FeatureRequests(issue_id)
);
-- END:constraint
```



## 목표: 여러 부모 참조

![7-1](./images/sql-antipatterns/7-1.jpeg)

## 안티 패턴: 이중 목적의 FK 사용

- 이런 경우에 대한 해법은 다형성 연관이란 이름이 붙을 정도로 널리 알려져 있다.
- 여러 테이블을 참조하기 때문에 난잡한 연관이라 불리기도 한다.

**다형성 연관 정의**

- 다형성 연관을 작동하게 하려면 참조하는 부모 테이블이름을 뜻하는 issue_type이라는 칼럼을 추가해야한다.

```sql
CREATE TABLE Comments (
  comment_id   SERIAL PRIMARY KEY,
  issue_type   VARCHAR(20),     -- "Bugs" or "FeatureRequests"
  issue_id     BIGINT UNSIGNED NOT NULL,
  author       BIGINT UNSIGNED NOT NULL,
  comment_date DATETIME,
  comment      TEXT,
  FOREIGN KEY (author) REFERENCES Accounts(account_id)
);

```

- 이런 설계에서는 FK를 지정할 수 없다. FK는 하나의 테이블만 참조할 수 있기 때문에 다형성 연관을 사용할 경우에는 이 연관을 메타데이터에 선언할 수 없다.
- 그러므로 데이터 정합성을 강제하는 것이 불가능하다.
- 이 패턴은 이전에 봤던 EAV 안티 패턴과 비슷한점이 많다. (EAV도 참조정합성 강제 불가능)



**다형성 연관에서의 조회**

- 자식 테이블을 부모 테이블과 조인할 때 issue_type을 정확하게 사용해서 조회해야 한다.

```sql
-- START:issue
SELECT *
FROM Bugs AS b JOIN Comments AS c
  ON (b.issue_id = c.issue_id AND c.issue_type = 'Bugs')
WHERE b.issue_id = 1234;
-- END:issue
```

- 댓글을 조회할때는 아래와 같이 OUTER JOIN을 사용해서 조회해야 한다.

```sql
-- START:comment
SELECT *
FROM Comments AS c
  LEFT OUTER JOIN Bugs AS b
    ON (b.issue_id = c.issue_id AND c.issue_type = 'Bugs')
  LEFT OUTER JOIN FeatureRequests AS f
    ON (f.issue_id = c.issue_id AND c.issue_type = 'FeatureRequests');
-- END:comment

```



**비 객체지향 예제**

- 다형성 연관은 부모 테이블이 서로 아무런 관계가 없을 때도 사용할 수 있다.
- Orders 테이블, Users 테이블 모두 Address와 연관될 수 있다.
- 이런 설계는 한 주소를 사용자와 주문 둘 다에 연관시킬 수 없다.

```sql
-- START:table
CREATE TABLE Addresses (
  address_id   SERIAL PRIMARY KEY,
  parent       VARCHAR(20),     -- "Users" or "Orders"
  parent_id    BIGINT UNSIGNED NOT NULL,
  address      TEXT
);
-- END:table

```

- 또한 사용자가 배송지 주소 뿐 아니라 청구지 주소도 갖고 있다면 이를 구별하기 위한 방법이 Address  테이블에 있어야 하고 이런 표시는 잡초처럼 퍼져 나간다.

```sql
-- START:weeds
CREATE TABLE Addresses (
  address_id   SERIAL PRIMARY KEY,
  parent       VARCHAR(20),     -- "Users" or "Orders"
  parent_id    BIGINT UNSIGNED NOT NULL,
  users_usage  VARCHAR(20),     -- "billing" or "shipping"
  orders_usage VARCHAR(20),     -- "billing" or "shipping"
  address      TEXT
);
-- END:weeds
```



## 안티패턴 인식 방법

- 다음과 같은 말이 들린다면 다형성 연관 안티패턴이 사용되고 있음을 나타낸다.
  - "이 태깅 스키마는 데이터베이스 내의 어떤 리소스에도 태그(또는 다른 속성)를 달 수 있다.
  - "우리 데이터베이스 설계에서는 FK를 선언할 수 없어."
  - "entity_type 칼럼의 용도가 뭐지?"
- Ruby on Rails 에서는 액티브 레코드 클래스에 :polymorphic 속성을 선언하면 다형성 연관을 사용할 수 있다.

```sql
class Comment < ActiveRecord::Base
  belongs_to :commentable, :polymorphic => true
end

class Bug < ActiveRecord::Base
  has_many :comments, :as => :commentable
end

class FeatureRequest < ActiveRecord::Base
  has_many :comments, :as => :commentable
end

```

- Java를 위한 Hibernate 프레임워크도 다양한 스키마 선언을 통해 다형성 연관을 지원한다.
  - [https://javabeat.net/polymorphic-association-mapping-relationship-hibernate/](https://javabeat.net/polymorphic-association-mapping-relationship-hibernate/)
  - [https://thorben-janssen.com/polymorphic-association-mappings-of-independent-classes/](https://thorben-janssen.com/polymorphic-association-mappings-of-independent-classes/)
  - [https://www.concretepage.com/hibernate/hibernate-any-manytoany-and-anymetadef-annotation-example](https://www.concretepage.com/hibernate/hibernate-any-manytoany-and-anymetadef-annotation-example)



## 안티패턴 사용이 합당한 경우

- 다형성 연관 안티패턴은 사용을 피하고 FK와 같은 제약조건을 사용해 참조 정합성을 보장해야 한다.
- 다형성 연관은 메타데이터 대신 애플리케이션 코드에 지나치게 의존하게 만드는 경우가 많다.
- Hibernate와 같은 객체-관계 프로그래밍 프레임워크를 사용하는 경우 이 안티패턴 사용이 불가피할 수 있다. 이런 프레임워크는 참조 정합성 유지를 위한 애플리케이션 로직을 캡슐화해 다형성 연관으로 인해 생기는 위험을 완화해줄 수 있다.



## 해법: 관계 단순화

- 다형성 연관의 단점을 피하면서 필요한 데이터 모델을 지원하기 위해서는 데이터베이스를 다시 설계하는 게 낫다.



**역 참조**

`교차 테이블 생성`

- 자식 테이블 Comments에 있는 FK는 여러 부모 테이블을 참조할 수 없으므로, 대신 Comments 테이블을 참조하는 여러 개의 FK를 사용하도록 한다. 각 부모에 대해 별도의 교차 테이블을 생성하고 교차 테이블에는 각 부모 테이블에 대한 FK 뿐 아니라 Comments에 대한 FK도 포함시킨다.

```sql
CREATE TABLE BugsComments (
  issue_id    BIGINT UNSIGNED NOT NULL,
  comment_id  BIGINT UNSIGNED NOT NULL,
  PRIMARY KEY (issue_id, comment_id),
  FOREIGN KEY (issue_id) REFERENCES Bugs(issue_id),
  FOREIGN KEY (comment_id) REFERENCES Comments(comment_id)
);


CREATE TABLE FeaturesComments (
  issue_id    BIGINT UNSIGNED NOT NULL,
  comment_id  BIGINT UNSIGNED NOT NULL,
  PRIMARY KEY (issue_id, comment_id),
  FOREIGN KEY (issue_id) REFERENCES FeatureRequests(issue_id),
  FOREIGN KEY (comment_id) REFERENCES Comments(comment_id)
);

```

- 이 방법을 사용하면 issue_type 같은 칼럼이 불필요해지고 FK를 통해 데이터 정합성을 강제할 수 있다.



![7-2](./images/sql-antipatterns/7-2.jpeg)



`신호등 설치`

- 각 댓글은 하나의 버그 또는 하나의 기능요청에만 연결되어야 한다. 이 규칙을 부분적으로 강제할 수 있는데 각 교차테이블의 comment_id 칼럼에 UNIQUE 제약조건을 선언하면 된다.

```sql
CREATE TABLE BugsComments (
  issue_id    BIGINT UNSIGNED NOT NULL,
  comment_id  BIGINT UNSIGNED NOT NULL,
  UNIQUE KEY (comment_id),
  PRIMARY KEY (issue_id, comment_id),
  FOREIGN KEY (issue_id) REFERENCES Bugs(issue_id),
  FOREIGN KEY (comment_id) REFERENCES Comments(comment_id)
);

```

- 위 방법은 하나의 댓글이 하나의 버그에만 매핑되게 강제할 수 있지만 버그와 기능요청 양쪽에 동시에 연관되는 것은 방지하지 못한다. 이를 방지하는 것은 애플리케이션 코드의 책임으로 남는다.

`양쪽 다 보기`

- 특정 버그 또는 기능 요청에 대한 댓글은 교차테이블을 이용해 간단히 조회할 수 있다.

```sql
-- START:issue
SELECT *
FROM BugsComments AS b
  JOIN Comments AS c USING (comment_id)
WHERE b.issue_id = 1234;
-- END:issue
-- START:comment
SELECT *
FROM Comments AS c
  LEFT OUTER JOIN (BugsComments JOIN Bugs AS b USING (issue_id))
    USING (comment_id)
  LEFT OUTER JOIN (FeaturesComments JOIN FeatureRequests AS f USING (issue_id))
    USING (comment_id)
WHERE c.comment_id = 9876;
-- END:comment

```



`차선 통합`

- 때론 여러 부모 테이블에 대해 조회한 결과를 하나의 테이블에서 조회한 것처럼 보이게 할 필요가 있을때 아래와 같은 방법을 사용할 수 있다.

```sql
SELECT b.issue_id, b.description, b.reporter, b.priority, b.status,
    b.severity, b.version_affected,
    NULL AS sponsor
  FROM Comments AS c
    JOIN (BugsComments JOIN Bugs AS b USING (issue_id))
      USING (comment_id)
  WHERE c.comment_id = 9876;
  
UNION
  SELECT f.issue_id, f.description, f.reporter, f.priority, f.status,
    NULL AS severity, NULL AS version_affected,
    f.sponsor
  FROM Comments AS c
    JOIN (FeaturesComments JOIN FeatureRequests AS f USING (issue_id))
      USING (comment_id)
  WHERE c.comment_id = 9876;

```

- 한쪽 테이블에만 존재하는 칼럼에 대해서는 그에 대응해 NULL로 대응시켜준다.
- 다른 방법은 COALESCE() 함수를 사용하는 방법이다.
- COALESCE()은 파라미터의 순서대로 NULL이 아닌 값을 가진 인자를 리턴한다.

```sql
SELECT c.*,
  COALESCE(b.issue_id,    f.issue_id   ) AS issue_id,
  COALESCE(b.description, f.description) AS description,
  COALESCE(b.reporter,    f.reporter   ) AS reporter,
  COALESCE(b.priority,    f.priority   ) AS priority,
  COALESCE(b.status,      f.status     ) AS status,
  b.severity,
  b.version_affected,
  f.sponsor
  
FROM Comments AS c
  LEFT OUTER JOIN (BugsComments JOIN Bugs AS b USING (issue_id))
    USING (comment_id)
  LEFT OUTER JOIN (FeaturesComments JOIN FeatureRequests AS f USING (issue_id))
    USING (comment_id)
WHERE c.comment_id = 9876;

```

- 위 방법을 사용해서 뷰로 만들어두면 애플리케이션에서 간단하게 사용할 수 있다.



**공통 수퍼테이블 생성**

- 객체지향 다형성에서는 서브타입이 공통의 수퍼타입을 공유하기 때문에 두 서브타입을 비슷하게 참조할 수 있다.
- 모든 부모 테이블이 상속할 베이스테이블을 생성해 문제를 해결할 수 있다.

```sql
CREATE TABLE Issues (
  issue_id     SERIAL PRIMARY KEY
);

CREATE TABLE Bugs (
  issue_id     BIGINT UNSIGNED PRIMARY KEY,
  FOREIGN KEY (issue_id) REFERENCES Issues(issue_id),
  . . .
);

CREATE TABLE FeatureRequests (
  issue_id     BIGINT UNSIGNED PRIMARY KEY,
  FOREIGN KEY (issue_id) REFERENCES Issues(issue_id),
  . . .
);

CREATE TABLE Comments (
  comment_id   SERIAL PRIMARY KEY,
  issue_id     BIGINT UNSIGNED NOT NULL,
  author       BIGINT UNSIGNED NOT NULL,
  comment_date DATETIME,
  comment      TEXT,
  FOREIGN KEY (issue_id) REFERENCES Issues(issue_id),
  FOREIGN KEY (author) REFERENCES Accounts(account_id),
);

```

![7-3](./images/sql-antipatterns/7-3.jpeg)



- 특정 댓글이 참조하는 버그 또는 기능요청을 비교적 간단한 쿼리를 통해 조회할 수 있다.
- Issue 테이블에 속성을 정의하지 않았다면 쿼리에 이 테이블을 포함시킬 필요가 없다. Bugs 테이블과 Issues 테이블의 PK 값이 같기 때문에 Bugs를 Comments와 직접 조인할 수 있다. 
- 데이터베이스에서 칼럼이 서로 유사한 정보를 표현하는 경우에는 FK 제약조건으로 직접 연결되어 있지 않더라도 두 테이블을 조인할 수 있다.

```sql
-- START:comment
SELECT *
FROM Comments AS c
  LEFT OUTER JOIN Bugs AS b USING (issue_id)
  LEFT OUTER JOIN FeatureRequests AS f USING (issue_id)
WHERE c.comment_id = 9876;
-- END:comment
-- START:issue
SELECT *
FROM Bugs AS b
  JOIN Comments AS c USING (issue_id)
WHERE b.issue_id = 1234;
-- END:issue

```



**SQL Antipatterns Tip**

- 모든 테이블 관계에는 참조하는 테이블 하나, 참조되는 테이블 하나가 있다.





# #8 다중 칼럼 속성

- 사용자 연락처 테이블에서 전화번호 설계는 약간 까다롭다. 사람들은 여러 개의 전화번호를 사용한다.
- 칼럼이 얼마나 많아야 충분할까?



## 목표: 다중 값 속성 저장

- 2장 무단횡단과 비슷하다.
- 버그에 태그를 다는 요구사항이 있다. 해당 버그에는 N개의 태그가 달릴 수 있다.



## 안티패턴: 여러 개의 칼럼 생성

- 속성에 여러 값이 들어가는 것을 고려해야 하지만, 각 칼럼에는 하나의 값만을 저장해야 한다는 것을 안다.
- 테이블에 여러 칼럼을 만들고 각 칼럼에 하나의 태그를 저장하게 하는 것이 자연스러워 보인다.

```sql
CREATE TABLE Bugs (
  bug_id      SERIAL PRIMARY KEY,
  description VARCHAR(1000),
  tag1        VARCHAR(20),
  tag2        VARCHAR(20),
  tag3        VARCHAR(20)
);

```

- 주어진 버그에 태그를 달 때, 이 세 칼럼 중 하나에 값을 넣는다. 사용되지 않는 칼럼은 NULL로 남는다.

| bug_id | description | tag1     | tag2        | tag3 |
| ------ | ----------- | -------- | ----------- | ---- |
| 1      | blabla      | crash    | NULL        | NULL |
| 2      | blabla      | printing | performance | NULL |
| 3      | blabla      | NULL     | NULL        | NULL |



**값 검색**

- 주어진 태그를 가진 버그를 찾으려면 세 칼럼을 모두 확인해야 한다.

```sql
SELECT * FROM Bugs
WHERE tag1 = 'performance'
   OR tag2 = 'performance'
   OR tag3 = 'performance';

-- START:style1
SELECT * FROM Bugs
WHERE (tag1 = 'performance' OR tag2 = 'performance' OR tag3 = 'performance')
  AND (tag1 = 'printing' OR tag2 = 'printing' OR tag3 = 'printing');
-- END:style1
-- START:style2
SELECT * FROM Bugs
WHERE 'performance' IN (tag1, tag2, tag3)
  AND 'printing'    IN (tag1, tag2, tag3);
-- END:style2

```



**값 추가와 삭제**

- 값을 추가하기 전에 먼저 해당 칼럼에 값이 있는지 확인하고 업데이트해야한다.

```sql
-- START:select
SELECT * FROM Bugs WHERE bug_id = 3456;
-- END:select
-- START:update
UPDATE Bugs SET tag2 = 'performance' WHERE bug_id = 3456;
-- END:update

```

- 위 방법은 동시쓰기 충돌이 일어날 수 있다.
- 값 삭제는 아래와 같이 NULLIF() 함수를 사용해서 태그를 삭제할 수 있다.

```sql
UPDATE Bugs
SET tag1 = NULLIF(tag1, 'performance'),
    tag2 = NULLIF(tag2, 'performance'),
    tag3 = NULLIF(tag3, 'performance')
WHERE bug_id = 3456;

```

- NULLIF()는 두 인수 값이 같을 때 NULL을 리턴한다.
- 아래 문장은 performance란 새로운 태그를 저장할 때 세 칼럼이 모두 NULL이 아니면 아무런 변경도 가하지 않는다.

```sql
UPDATE Bugs
SET tag1 = CASE
      WHEN 'performance' IN (tag2, tag3) THEN tag1
      ELSE COALESCE(tag1, 'performance') END,
    tag2 = CASE
      WHEN 'performance' IN (tag1, tag3) THEN tag2
      ELSE COALESCE(tag2, 'performance') END,
    tag3 = CASE
      WHEN 'performance' IN (tag1, tag2) THEN tag3
      ELSE COALESCE(tag3, 'performance') END
WHERE bug_id = 3456;

```



**유일성 보장**

- 여러 칼럼에 동일한 값이 나타나지 않게 하고 싶겠지만 다중 칼럼 속성 안티패턴을 사용하는 경우에는 데이터베이스에서 이를 예방하지 못한다.

```sql
INSERT INTO Bugs (description, tag1, tag2, tag3)
  VALUES ('printing is slow', 'printing', 'performance', 'performance');

```



**값의 수 증가 처리**

- 이 설계의 또 다른 단점은 칼럼 세 개가 모자랄 수도 있다는 것이다.
- 테이블을 정의하는 시점에 태그의 최대 개수가 얼마나 될지 예측할 수 없다.

```sql
ALTER TABLE Bugs ADD COLUMN tag4 VARCHAR(20);

```

- 이런 변경은 다음과 같은 이유로 비용이 많이 든다.
  - 이미 데이터를 포함하고 있는 데이터베이스 테이블 구조를 변경하려면 테이블 전체를 잠금 설정하고 다른 클라이언트의 접근을 차단하는 과정이 필요하다.
  - 어떤 데이터베이스는 희망하는 구조의 새로운 테이블을 정의해 예전 테이블에서 모든 데이터를 복사한 다음 예전 테이블을 삭제하는 식으로 테이블 변경을 구현한다. 테이블에 많은 데이터가 쌓여 있다면 작업에 많은 시간이 걸린다.
  - 다중 칼럼 속성의 집합에 칼럼을 추가한 경우 모든 애플리케이션에서 이 테이블을 참조하는 모든 SQL문이 변경되어야 한다.





## 안티패턴 인식 방법

- 사용자 인터페이스나 문서에 여러 개의 값을 할당할 수 있지만 최대 개수가 제한되어 있는 속성이 기술되어 있다면 다중 칼럼 속성 안티패턴이 사용되고 있음을 나타내는 것으로 볼 수 있다.
- 이 안티패턴이 사용되고 있음을 나타내는 징조
  - "태그를 최대 몇 개까지 붙일 수 있도록 지원해야 하지?"
  - "SQL에서 여러 칼럼을 한꺼번에 검색하려면 어떻게 해야 하지?"



## 안티패턴 사용이 합당한 경우

- 어떤 경우에는 속성의 개수가 고정되고 선택의 위치나 순서가 중요할 수 있다.
- 각 칼럼에 들어가는 값은 같은 종류지만 그 의미와 사용처가 달라 논리적으로 다른 속성이 되는 경우다.



## 해법: 종속 테이블 생성

- 가장 좋은 해법은 다중 값 속성을 위한 칼럼을 하나 가지는 종속 테이블을 만드는 것이다.
- 여러 개의 값을 여러 개의 칼럼 대신 여러 개의 행에 저장하는 것이다.

```sql
CREATE TABLE Tags (
  bug_id       BIGINT UNSIGNED NOT NULL
  tag          VARCHAR(20),
  PRIMARY KEY (bug_id, tag),
  FOREIGN KEY (bug_id) REFERENCES Bugs(bug_id)
);

INSERT INTO Tags (bug_id, tag)
  VALUES (1234, 'crash'), (3456, 'printing'), (3456, 'performance');

```

- 하나의 버그에 연관된 모든 태그가 한 컬럼에 있으면 주어진 태그에 대한 버그를 검색하는 작업이 좀더 직관적이다.

```sql
SELECT * FROM Bugs JOIN Tags USING (bug_id)
WHERE tag = 'performance';

SELECT * FROM Bugs
  JOIN Tags AS t1 USING (bug_id)
  JOIN Tags AS t2 USING (bug_id)
WHERE t1.tag = 'printing' AND t2.tag = 'performance';

```

- 추가 삭제 역시 다중 칼럼 안티패턴을 사용할 때보다 훨씬 쉬워진다.

```sql
INSERT INTO Tags (bug_id, tag) VALUES (1234, 'save');

DELETE FROM Tags WHERE bug_id = 1234 AND tag = 'crash';

```

- PK 제약조건을 통해 중복을 허용하지 않게 하고 버그에 달릴 수 있는 태그의 수가 제한되지 않는다.



**SQL Antipatterns Tip**

- 같은 의미를 가지는 각각의 값은 하나의 칼럼에 저장하라.





