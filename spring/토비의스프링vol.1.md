



# 토비의 스프링 vol.1





# #1 오브젝트와 의존관계

## 초난감 DAO

## DAO의 분리

## DAO의 확장

## 제어의 역전

## 스프링의 IoC

## 싱글톤 레지스트리와 오브젝트 스코프

## 의존관계 주입

## XML을 이용한 설정

# #2 테스트

- 애플리케이션은 계속 변하고 복잡해져감 그 변화에 대응하는 두가지 전략
  - 확장과 변화를 고려한 객체지향적 설계와 그것을 효과적으로 담아낼 수 있는 IoC/DI 같은 기술
  - 만들어진 코드를 확신할 수 있게 해주고 변화에 유연하게 대처할 수 있는 자신감을 주는 테스트 기술
- 테스트는 스프링을 학습하는 데 있어 가장 효과적인 방법의 하나

## USERDAOTEST 다시 보기

### 테스트의 유용성

- 테스트란 결국 내가 예상하고 의도했던 대로 코드가 정확히 동작하는지를 확인해서 만든 코드를 확신할 수 있게 해주는 작업
- 테스트의 결과가 원하는 대로 나오지 않는 경우에는 코드나 설계에 결함이 있음을 알 수 있음 이를 통해 코드의 결함을 제거해가는 작업을 하고 최종적으로 테스트가 성공하면 모든 결함이 제거됐다는 확신을 얻을 수 있게 해줌

### UserDaoTest의 특징

```java
public class UserDaoTest {
	public static void main(String[] args) throws SQLException {
    ApplicationContext context = new GenericXmlApplicationContext("applicationContext.xml");
    
    UserDao dao = context.getBean("userDao", UserDao.class);
    User user = new User();
    user.setId("user");
    user.setName("백기선");
    user.setPassword("married");
    
    dao.add(user);
    
    System.out.println(user.getId() + " 등록 성공");
    
    User user2 = dao.get(user.getId());
    
    System.out.println(user2.getName());
    System.out.println(user2.getPassword());
    
    System.out.println(user2.getId() + " 조회 성공");
  }
}
```



#### 웹을 통한 DAO 테스트 방법의 문제점

- 웹을 사용해서 테스트하는 경우 DAO뿐만 아니라 서비스, 컨트롤러 ,jsp 등등 모든 레이어의 기능을 다 만들고 나서야 테스트가 가능하다는 점이 가장 큰 문제임
- 테스트를 수행하는데 참여하는 코드와 클래스가 많아지기 때문에 디버깅하기 어려움

#### 작은 단위의 테스트

- 테스트하고자 하는 대상이 명확하다면 그 대상에만 집중해서 테스트하는 것이 바람직함
- 한꺼번에 너무 많은 것을 몰아서 테스트하면 테스트 수행 과정도 복잡해지고 오류가 발생했을 때 정확한 원인을 찾기가 힘들어짐
- 테스트는 가능하면 작은 단위로 쪼개고 집중해서 할 수 있어야 함
- 테스트의 관심사가 다르다면 테스트할 대상을 분리하고 집중해서 접근해야함
- 작은 단위의 코드에 대해 테스트를 수행한 것을 단위 테스트(unit test)라고 함
- 여기서 말하는 단위는 하나의 관심에 집중해서 효율적으로 테스트할 만한 범위의 단위라고 보면 됨
- 일반적으로 단위는 작을수록 좋음
- 단위를 넘어서는 다른 코드들은 신경 쓰지 않고 참여하지도 않고 테스트가 동작할 수 있어야함
- 길고 많은 단위가 참여하는 통합테스트도 언젠가는 필요함 하지만 단위테스트보다 통합테스트가 먼저 우선으로 되는 경우엔 문제의 원인을 찾기 어려울 수 있음
- 단위 테스트를 하는 이유는 개발자가 설계하고 만든 코드가 원래 의도한 대로 동작하는지를 개발자 스스로 빨리 확인받기 위해서임

#### 자동수행 테스트 코드

- UserDaoTest의 한가지 특징은 테스트할 데이터가 코드를 통해 제공되고, 테스트 작업 역시 코드를 통해 자동으로 실행한다는 점
- 테스트는 자동으로 수행되도록 코드로 만들어지는 것이 중요함
- 자동으로 수행되는 테스트의 장점은 자주 반족할 수 있다는 것
- 만들어둔 기능 테스트가 있다면 리팩토링, 수정을 안전하게 수행할 수 있음

#### 지속적인 개선과 점진적인 개발을 위한 테스트

- 테스트를 이용하면 새로운 기능도 기대한 대로 동작하는지 확인할 수 있을 뿐 아니라, 기존에 만들어뒀던 기능들이 새로운 기능을 추가하느라 수정한 코드에 영향을 받지 않고 여전히 잘 동작하는 지를 확인할 수 도 있음

### UserDaoTest의 문제점

**수동 확인 작업의 번거로움**

- 입력한 값과 가져온 값이 일치하는지 테스트에서 자동으로 확인해주지 않고 콘솔에 나온 값을 보고 등록 조회가 성공적으로 되고 있는지를 확인하는 건 사람의 책임
- 테스트의 결과를 확인하는 일은 사람의 책임이기 때문에 완전히 자동으로 테스트되는 방법이라고 말할 수 없음

**실행 작업의 번거로움**

- 테스트를 만들때마다 main()메서드를 생성할 수 없으므로 main()메서드를 이용하는 방법보다 좀 더 편리하고 체계적으로 테스트를 실행하고 그결과를 확인하는 방법이 필요함

## USERDAOTEST 개선

### 테스트 검증의 자동화

- 수정 전 테스트코드

```java
    System.out.println(user2.getName());
    System.out.println(user2.getPassword());
    System.out.println(user2.getId() + " 조회 성공");
```

- 수정 후 테스트코드

```java
		if (!user.getName().eqauls(user2.getName())) {
				System.out.println("테스트 실패 (name)")
		} else if (!user.getPassword().eqauls(user2.getPassword())) {
				System.out.println("테스트 실패 (password)")
		} else {
      	System.out.println("조회 테스트 성공")
    }
```

- add()는 별도로 검증하지 않고 get()을 검증함으로써 add()와 get()메서드를 동시에 검증
- 이 테스트는 추후 코드의 동작에 영향을 미칠 수 있는 어떤 변화라도 생기면 언제든 다시 실행시켜볼 수 있음
- "테스트란 개발자가 마음 편하게 잠자리에 들 수 있게 해주는 것" - 켄트백
- 개발과정, 유지보수를 하면서 기존 애플리케이션 코드에 수정을 할 때 마음의 평안을 얻고 자신이 만ㄷ지는 코드에 대해 항상 자신감을 가질 수 있으며, 새로 도입한 기술의 적용에 문제가 없는지 확인할 수 있는 가장 좋은 방법은 빠르게 실행 가능하고 스스로 테스트 수행과 기대하는 결과에 대한 확인까지 해주는 코드로된 자동화된 테스트를 만들어주는 것



### 테스트의 효율적인 수행과 결과 관리

- main() 메서드로는 한계가 있음
- 일정한 패턴을 가진 테스트를 만들 수 있고, 테스트가 실패한 곳을 빠르게 찾을 수 있는 기능을 갖춘 테스트 지원 도구와 그에 맞는 테스트 작성 방법이 필요함
- JUnit은 자바로 단위 테스트를 만들 때 유용하게 사용할 수 있음

#### JUnit 테스트로 전환

- 개발자가 만든 클래스의 오브젝트를 생성하고 실행하는 일은 프레임워크에 의해 진행됨

#### 테스트 메서드 전환

- 기존에 만들었던 main() 메서드에 있던 테스트 코드를 일반 메서드로 옮기는 것
- 새로 만들 테스트 메서드는 JUnit 프레임워크가 요구하는 조건 두가지를 따라야함
  - ~~메서드가 public으로 선언되어야 함~~
  - 메서드가 private으로 선언되지 않아야함 [https://junit.org/junit5/docs/current/user-guide/#writing-tests-classes-and-methods](https://junit.org/junit5/docs/current/user-guide/#writing-tests-classes-and-methods)
  - 메서드에 @Test 애너테이션을 붙여줘야 함

```java
import org.junit.Test;

...

public class UserDaoTest {
  
	@Test //Junit에게 테스트용 메서드임을 알려줌
	public void addAndGet() throws SQLException {
    ...
  }
}

```



#### 검증 코드 전환

- `assertThat()` 메서드는 첫 번째 파라미터의 값을 뒤에 나오는 Matcher 라고 불리는 조건으로 비교해서 일치하면 다음으로 넘어가고 아니면 테스트가 실패하도록 만들어 줌
- `is()` 는 Matcher의 일종으로 `eqauls()` 로 비교해주는 기능을 가짐
- Junit은 예외가 발생하거나 `assertThat()` 에서 실패하지 않고 테스트 메서드의 실행이 완료되면 테스트가 성공했다고 인식함.
- Junit을 적용한 UserDaoTest

```java
import static org.hamcrest.CoreMatchers.is;
import static org.junit.Assert.assertThat;

...

public class UserDaoTest {

	@Test
	public void addAndGet() throws SQLException {
    
    ApplicationContext context = new GenericXmlApplicationContext("applicationContext.xml");
    
    UserDao dao = context.getBean("userDao", UserDao.class);
    User user = new User();
    user.setId("user");
    user.setName("백기선");
    user.setPassword("married");
    
    dao.add(user);

		User user2 = dao.get(user.getId());
    
		assertThat(user2.getName(), is(user.getName()));
		assertThat(user2.getPassword(), is(user.getPassword()));
	}
}
```



#### Junit 테스트 실행

- 생성한 JUnit 테스트코드도 어디선가는 실행시켜줘야하기 때문에 main() 메서드에 추가

```java
import org.junit.runner.JUnitCore;

...
  
public static void main(String[] args) {
  JUnitCore.main("springbook.user.dao.UserDaoTest");
}
```

- 성공할 경우 메시지

```
Jnit version 4.7
Time: 0.578
OK (1 test)
```

- 실패할 경우 메시지

```
Time: 1.094
Ther was 1 failure:
1) addAndGet(spring.dao.UserDaoTest)
java.lang.AssertionError:
Expected is "백기선"
	got: null
	...
		at springbook.dao.UserDaoTest.main(UserDaoTest.java:36)
FAILURES!!!
Tests run: 1, Failures: 1
```

- 함께 출력된 호출 스택을 살펴보면 실패한 원인이 무엇이고 테스트 코드에서 검증에 실패한 위치는 어디인지도 확인할 수 있음
- Junit은 `assertThat()` 을 이용해 검증을 했을 때 기대한 결과가 아니면 `AssertionError`를 던짐 따라서 `assertThat()`의 조건을 만족하지 못하면 테스트는 더이상 진행되지 않고 JUnit은 테스트가 실패했음을 알게됨
- 테스트 수행 중에 일반 예외가 발생한 경우에도 마찬가지로 테스트 수행은  중단되고 테스트는 실패함



## 개발자를 위한 테스팅 프레임워크 JUNIT

- 스프링을 학습하고 제대로 활용하려면 최소한의 JUnit 테스트 작성 방법과 실행 방법은 알고 있어야함
- 대부분의 자바 IDE는 JUnit 테스트를 손쉽게 실행할 수 있는 JUnit 테스트 지원 기능을 내장하고 있어서 더욱 편리하게 JUnit 테스트를 만들고 활용할 수 있게 해줌

### JUnit 테스트 실행 방법

- JUnitCore를 이용해 테스트를 실행하고 콘솔에 출력된 메시지를 보고 결과를 확인하는 방법은 간단하긴 하지만 테스트의 수가 많아지면 관리하기가 힘들어진다는 단점이 있음
- 가장 좋은 JUnit 테스트 실행 방법은 자바 IDE에 내장된 JUnit 테스트 지원 도구를 사용하는 것

#### IDE

- IDE에서 Run As > Jnit Test를 선택하면 테스트가 자동으로 실행됨
- main() 메서드 없이 테스트할 수 있음
- IDE에서 테스트의 총 수행시간, 실행한 테스트의 수, 테스트 에러의 수, 테스트 실패의 수를 확인할 수 있음
- JUnit은 한 번에 여러 테스트 클래스를 동시에 실행할 수도 있음
- JUnit 테스트의 실행과 그 결과를 확인하는 방법이 매우 간단하고 직관적이며 소스와 긴밀하게 연동된 결과를 볼 수 있기 때문에 IDE의 지원을 받는 것이 가장 편리함

#### 빌드 툴

- 빌드 툴에서 제공하는 JUnit 플로그인이나 태스크를 이용해 JUnit 테스트를 실행할 수 있음

### 테스트 결과의 일관성

- 테스트가 외부 상태에 따라 결과값이 달라지면 안됨. ex DB, network, etc ...
- 코드의 변경사항이 없다면 테스트는 항상 동일한 결과를 내야함

#### deleteAll()의 getCount() 추가

#### deleteAll()과 getCount()의 테스트

#### 동일한 결과를 보장하는 테스트

- 단위 테스트는 코드가 바뀌지 않는다면 매번 실행할 때마다 동일한 테스트 결과를 얻을 수 있어야함

### 포괄적인 테스트

- 테스트를 안만드는것도 위험한 일이지만 성의 없이 테스트를 만드는 바람에 문제가 있는 코드인데도 성공하게 만드는 건 더 위험함
- 다양한 케이스를 검증해야함

#### getCount()테스트

- ~~JUnit은 특정한 테스트 메서드의 실행 순서를 보장해주지 않음~~
  - [https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-execution-order-methods](https://junit.org/junit5/docs/current/user-guide/#writing-tests-test-execution-order-methods)
- 단위테스트는 실행 순서에 상관없이 독립적으로 항상 동일한 결과를 낼 수 있도록 해야 함

#### addAndGet() 테스트 보완

#### get() 예외조건에 대한 테스트

- JUnit은 `assertThat()` 검증 뿐만 아니라 테스트 에러. 즉 예외 조건에 대한 검증도 할 수 있음
- JUnit4 에러 검증 방법

```java
	@Test(expected=EmptyResultDataAccessException.class) // 테스트 중에 발생할 것으로 기대하는 예외 클래스를 지정해 준다.
	public void getUserFailure() throws Exception {
	
		dao.deleteAll();
		assertThat(dao.getCount(), is(0));
		
		dao.get("unkown_id"); // 이 메소드 실행 중에 예외가 발생해야 한다. 예외가 발생하지 않으면 테스트가 실패한다.
	}
```

- `@Test` 애너테이션의 expected 앨리먼트를 사용해서 테스트 메서드중에 발생하리라 기대하는 예외 클래스를 넣어주면 됨
- expected를 지정하면 지정한 예외가 던져져야 테스트가 성공함

- JUnit5 에러 검증 방법

```java
    @Test
    void exceptionTesting() {
        Exception exception = assertThrows(ArithmeticException.class, () ->
            calculator.divide(1, 0));
        assertEquals("/ by zero", exception.getMessage());
    }

```



#### 테스트를 성공시키기 위한 코드의 수정

#### 포괄적인 테스트

- 간단한 코드라도 포괄적인 테스트를 만들어두는 편이 훨씬 안전하고 유용함.
- 종종 단순하고 간단한 테스트가 치명적인 실수를 피할 수 있게 해주기도 함
- 개발자는 성공하는 테스트만 골라서 만드는 경향이 있으므로 QA나 인수담당자에 의해 꼼꼼하게 준비된 전문적인 테스트가 수행될 필요가 있음
- 하지만 개발자 레벨에서도 다양항 상황과 입력값을 고려하는 포괄적인 테스트를 만들수 있음
- "항상 네거티브 테스트를 먼저 만들라" - 로드존슨
- 테스트를 작성할 때 부정적인 케이스를 먼저 만드는 습관을 들이는게 좋음

### 테스트가 이끄는 개발

#### 기능설계를 위한 테스트

#### 테스트 주도 개발

- 만들고자 하는 기능의 내용을 담고 있으면서 만들어진 코드를 검증도 해줄 수 있도록 테스트 코드를 먼저 만들고 테스트를 성공하게 해주는 코드를 작성하는 방식의 개발 방법을 테스트 주도 개발이라고함
- 실패한 테스트를 성공시키기 위한 목적이 아닌 코드는 만들지 않는다는 것이 tdd의 기본 원칙. 기본 원칙을 따랐다면 모든 코드는 검증된 코드
- tdd에서는 테스트를 작성하고 이를 성공시키는 코드를 만드는 작업의 주기를 가능한 한 짧게 가져가도록 권장함. 개발한 코드의 오류는 빨리 발견할수록 좋음
- tdd를 하면 자연스럽게 단위 테스트를 만들 수 있음

### 테스트 코드 개선

- JUnit 프레임워크는 테스트 메서드를 실행때마다 반복되는 준비 작업을 별도의 메서드에 넣어 동작시키게 할 수 있음

#### @Before

```java
	
...
  
		@Before // JUnit이 제공하는 애노테이션 @Test 메소드가 실행되기 전에 먼저 실행되야 하는 메소드를 정의한다.
	public void setUp() {	
		DataSource dataSource = new SingleConnectionDataSource( // 테스트에서 UserDao가 사용할 DataSource 오브젝트를 직접생성한다.
				"jdbc:mysql://localhost/testdb", "spring", "book", true);
		this.user1 = new User("gyumee", "박성철", "springno1");
		this.user2 = new User("leegw700", "이길원", "springno2");
		this.user3 = new User("bumjin", "박범진", "springno3");
		
	} // 각 테스트 메소드에 반복적으로 나타났던 코드를 제거하고 별도의 메소드로 옮긴다.

...

```

- JUnit5에서는 `@BeforeEach`, `@BeforeAll`로 사용가능
  - [https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations](https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations)
- JUnit 프레임워크 테스트 메서드 실행 과정
  1. 테스트 클래스에서 `@Test`가 붙은 public이고 void형이며 파라미터가 없는 테스트 메서드를 모두 찾는다.
  2. 테스트 클래스의 오브젝트를 하나 만든다.
  3. `@Before`가 붙은 메서드가 있으면 실행한다.
  4. `@Test`가 붙은 메서드를 하나 호출하고 테스트 결과를 저장해둔다.
  5. `@After`가 붙은 메서드가 있으면 실행한다.
  6. 나머지 테스트 메서드에 대해 2~5번을 반복한다.
  7. 모든 테스트의 결과를 종합해서 돌려준다.
- JUnit4에서는 `@Test`가 붙은 메서드를 실행하기 전과 후에 각각 `@Before`, `@After`가 붙은 메서드를 자동으로 실행함
- JUnit4에서는 `@Test` 메서드의 수만큼 이 클래스의 오브젝트를 만듦. 각 테스트가 서로 영향을 주지 않고 독립적으로 실행됨을 확실히 보장해주기 위해서임
- JUnit5에서는 `@TestInstance(Lifecycle.PER_CLASS)` 라는 옵션을 제공해주기때문에 모든 테스트를 하나의 인스턴스에서 실행할 수 있음



#### 픽스처

- 테스트를 수행하는 데 필요한 정보나 오브젝트를 픽스처라함
- 픽스처는 여러 테스트에서 반복적으로 사용되기때문에 `@Before` 를 사용하면 편리함



## 스프링 테스트 적용

### 테스트를 위한 애플리케이션 컨텍스트 관리

### DI와 테스트

## 학습 테스트로 배우는 스프링

## 정리



