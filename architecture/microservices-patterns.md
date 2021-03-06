

github : https://github.com/gilbutITbook/007035





# #1 모놀리식 지옥에서 벗어나라



## 모놀리틱의 장점

- 개발이 간단하다: ide 등 개발 툴은 단일 애플리케이션 구축에 초점이 맞추어져 있음
- 애플리케이션을 쉽게 변경할 수 있다: 코드, db 스키마 변경해서 빌드/배포하기 용이함
- 테스트하기 쉽다
- 배포하기 쉽다
- 확장하기 쉽다.

## 모놀리틱의 단점

- 너무 복잡함, 분석이 힘듦
- 개발이 더딤
- 커밋부터 배포에 이르는 길고 험난한 여정
- 확장하기 어려움
- 모놀리스는 확실하게 전달하기 어려움
- 갈수록 한물간 기술 스택에 발목이 붙잡힘

## 확장 큐브와 마이크로 서비스

- X축 확장: 다중 인스턴스에 고루 요청 분산: 애플리케이션 인스턴스 확장, 수평적 확장
- Z축 확장:  요청 속성별 라우팅: 특정 값을 통해 데이터 하위 집합을 인스턴스별로 부여해서 처리할 수 있음
- Y축 확장: 기능에 따라 애플리케이션을 서비스로 분해
- 하나의 애플리케이션을 여러 서비스로 기능 분해하는 아키텍처 스타일, 각 서비스가 집중된/응집된 책임을 맡고 있음

## 마이크로 서비스는 모듈성을 갖고 있다

- 서비스를 모듈성의 단위로 사용함
- api라는 경계선을 갖고 있기 때문에 인터페이스를 유지할 수 있음

## 서비스마다 db가 따로 있다

- 개발 단계에서 다른 서비스 개발자와 일일이 협의하지 않고도 개발자 본인이 담당한 서비스 스키마를 변경할 수 있음



## 마이크로 서비스 아키텍처의 장단점

### 장점

- 크고 복잡한 애플리케이션을 지속적으로 전달/배포할 수 있음
- 서비스 규모가 작아 관리하기 쉬움
- 서비스를 독립적으로 배포/호가장할 수 있음
- 마이크로서비스 아키텍처 덕분에 팀이 자율적으로 움직임
- 결함 격리가 잘됨
- 새로운 기술을 실험하고 도입하기 쉬움

#### 크고 복잡한 애플리케이션을 지속적으로 전달/배포할 수 있음

- 테스트성: 자동화 테스트를 작성하기 쉽고 더 빨리 실행시킴
- 배포성: 배포때문에 다른팀과 협의할 필요가 없음 <- 과연?
- 자율성, 느슨한 결합

### 단점

- 딱 맞는 서비스를 찾기가 쉽지 않음
- 분산 시스템은 너무 복잡해서 개발 테스트 배포가 어려움
- 여러 서비스에 걸친 기능을 배포할 때에는 잘 조정해야 함
- 마이크로서비스 아키텍처 도입 시점을 결정하기가 어려움

## 마이크로서비스 아키텍처 패턴 언어

### 마이크로서비스 아키텍처도 만병통치약은 아니다.

### 패턴 및 패턴 언어

- 통신패턴
- 트랜잭션 관리를 위한 데이터 일관성 패턴
- 애플리케이션을 여러 서비스로 분해하는 패턴
- 데이터 쿼리 패턴

등등 .. 많은 패턴이 있음 ..

## 마이크로서비스 너머: 프로세스와 조직

- 규모가 큰 팀을 여러 팀으로 나누는것이 좋음

## 

# #2 분해전략

## 마이크로 서비스 아키텍처란 무엇인가?

- 마이크로 서비스 아키텍처는 관리성, 테스트성, 배포성이 높은 애플리케이션을 구축하는 아키텍처 스타일임

### 소프트웨어 아키테겇의 정의와 중요성

**소프트웨어 아키텍처의 정의**

- 컴퓨팅 시스템의 소프트웨어 아키텍처는 소프트웨어 엘리먼트와 그들 간의 관계 그리고 이 둘의 속성으로 구성된 시스템을 추론하는데 필요한 구조의 집합이다.

**헥사고날 아키텍처**

- 비지니스 로직에 있던 표현/데이터 접근 로직이 어댑터와 분리되었기 때문에 비지니스로직이 표현/데이터 접근 로직 어디에도 의존하지 않음

### 마이크로서비스 아키텍처는 일종의 아키텍처 스타일이다.

- 마이크로서비스 아키텍처의 핵심 제약 조건은 서비스를 느슨하게 결합한다는 것

**서비스란 무엇인가?**

- 서비스는 어떤 기능이 구현되어 단독 배포가 가능한 소프트웨어 컴포넌트임

**느슨한 결합**

- 느슨하게 결합된 서비스는 마이크로서비스 아키텍처의 주요 특성중 하나임

**공유 라이브러리의 역할**

- 서비스 코드 중복을 줄이는 것은 좋지만 의도치 않은 서비스간 결합도를 유발하지 않도록 조심해야함

**서비스 규모는 별로 중요하지 않다**



## 마이크로 서비스 아키텍처 정의

- 1단계는 애플리케이션 요건을 핵심 요청으로 추출하는 것
- 2단계는 어떻게 여러 서비스로 분해할지 결정하는 것
- 서비스별로 api를 정의하는 일
- 분해과정에서는 네트워크 지연, 서비스간 동기 통신으로 인해 가용성이 떨어지는 문제, 여러 서비스에 걸쳐 데이터를 일관성 시키기, 애플리케이션 도처에 숨어있는 만능 클래스 걷어내기 등의 문제가 있음

### 시스템 작업 식별

- 애플리케이션 아키텍처를 정의하는 첫 단추는 시스템 작업을 정의하는 일
- 1단계는 시스템 작업을 기술하기 위해 필요한 보케블러리를 제공하는 핵심 클래스로 구성된 고수준의 도메인 모델을 생성하는것
- 2단계는 시스템 작업 식별 후 그 동작을 도메인 모델 관점에서 기술하는 것
- 하나의 큰 컨텍스트에서 시나리오를 정해서 작은 모델을 뽑아내는것이 핵심

**시스템 작업 정의**

- 애플리케이션이 어떤 요청을 처리할지 식별하는 단계
- 기술 개념이 아닌 비지니스 개념 중심으로 서비수가 구성된 아키텍쳐임



### 서비스 정의: 비지니스 능력 패턴별 분해

- 마이크로서비스 아키텍처를 구축하는 첫번째 전략은 비지니스 능력에 따라 분해하는 것임
- 비지니스 능력을 식별한 후 능력에 따라 또는 연관된 능력 그룹에 따라 서비스를 정의
- 서비스를 거의 변하지 않는 비지니스 능력에 따라 구성하면 비교적 안정적인 아키텍처를 구축할 수 있음
- 나중에 비지니스 요건이 달라져도 아키텍처를 구성하는 개별 컴포넌트는 아키텍처를 그대로 둔 채 발전 시킬수 있음



### 서비스정의: 하위 도메인 패턴별 분해

- ddd에는 마이크로서비스 아키텍처에 적용하면 정말 유용한 하위 도메인과 경계 컨텍스트 개념이 있음
- ddd는 범위가 분명한 도메인 모델을 여러 개 정의하여 기존 방식의 문제점을 해결하는 전혀 다른 방식의 모델링임
- 도메인 모델의 범위를 ddd 용어로는 경게 컨텍스트라고 함
- 경계 컨텍스트는 도메인 모델을 구현한 코드 아티팩트를 포함하여 마이크로 서비스 아키텍처에 ddd를 적용하면 각 서비스가 경계 컨텍스트가 됨
- 자체 도메인 모델을 가진 하위 도메인이라는 개념 덕분에 만능 클래스를 제거하고 서비스로 분해하기가 더 수월해짐



### 분해 지침

- 비지니스 능력에 따른 분해, 하위 도메인에 따른 분해는 마이크로서비스 아키텍처를 정의하는 주요 수단임

**단일 책임 원칙**

- 클래스는 오직 하나의 변경 사유를 가져야 함



**공동 폐쇄 원칙 Common Closure Principle**

- 패키지의 클래스들은 동일한 유형의 변경에 대해 닫혀있어야 함 패키지에 영향을 주는 변경은 그 패키지에 속한 모든 클래스에 영향을 끼침
- 어떤 두 클래스가 동일한 사유로 맞물려 변겨오디면 동일한 패키지에 있어야 함 <- 과연 패키지여야만 하는가? msa에서 서비스단위도 포함인가?
- ccp를 적용해서 마이크로서비스 아키텍처를 구축하면 동일한 사유로 변경되는 컴포넌트를 모두 같은서비스로 묶을 수 있음
- 가능하면 변경 영향도를 정확히 한 팀, 한 서비스에 국한시키는게 좋음



### 서비스 분해의 장애물

- 네트워크 지연
- 동기 통신으로 인한 가용성 저하
- 여러 서비스에 걸쳐 데이터 일관성 유지 <- saga 로 해결가능
- 데이터의 일관도니 뷰 확보
- 분해를 저해하는 만능 클래스 <- 각각 서비스별로 하위 도메인을 갖도록 구성할 것



### 서비스 api 정의

- 서비스 api를 정의하려면 우선 각각의 시스템 작업을 서비스로 매핑한 후 그 시스템 작업을 구현하려면 어느 서비스가 서로 협동해야 할지 파악해야함
- 제일 먼저 어느 서비스가 요청의 진입점인지 결정해야 함
- 마이크로서비스 아키텍처에서는 비동기 메시징이 중추적인 역할을 함





## 마치며

- 아키텍처는 애플리케이션 개발 속도에 직접 영향을 주는 갖가지 '~성' 을 좌우함 ex 고나리성, 테스트성, 배포성
- 마이크로서비스 아키텍처는 애플리케이션의 고나리성, 테스트성, 배포성을 높이는 아키텍처 스타일임
- 마이크로서비스 아키텍처는 기술적 고나심사보다 비지니스 능력, 하위 도메인 등 비지니스 관심사 위주로 구성됨
- 서비스를 분해하느 ㄴ패턴은 크게 두가지임
  - 비지니스 능력에 따른 분해: 비지니스 아키텍처 기반
  - 하위 도메인에 따른 분해: ddd 개념 기반
- ddd를 적용하고 서비스마다 도메인 모델을 따로 설계하면 의존 관계가 뒤엉켜 분해를 가로막는 만능 클래스를 제거할 수 있음



























