









# #2 동적 파라미터화 코드 전달하기



- 동작 파라미터화를 이용하면 자주 바뀌는 요구사항에 효과적으로 대응할 수 있음
- 동작 파라미터화란 아직은 어떻게 시행할 것인지 결정하지 않은 코드 블록을 의미함

## 변화하는 요구사항에 대응하기

- 변화하는 요구사항에 대응하기 위해서는 파라미터를 직접 추가하는 방식 보다는 동작파라미터화를 이용해서 유연성을 얻어야함

## 동작 파라미터화

- predicate interface를 작성해서 동작여부에 대한 인터페이스를 작성하고 메서드에 predicate interface 타입의 파라미터를 통해 인터페이스로 해결하는 방법 이것을 strategy design pattern 이라고 부름
- 사용자는 변화하는 요구사항에 따라 미리 작성한 인터페이스를 구현한 클래스타입을 전달함으로써 의존성을 낮출 수 있음

```java
interface MyPredicate {
	boolean test();
}


class MyPredicateImpl implements MyPredicate {
	@Override
	boolean test ...
}




...
    
   
    
public void test(MyPredicate predicate) {
    if(predicate.test()) {
        ...
    }
}

```

- predicate interface 한개의 파라미터로 다양한 동작을 구성할 수 있다.



## 복잡한 과정 간소화 

- interface를 작성하고 그 interface를 구현하는 클래스를 만드는 일은 상당히 번거로운 작업임
- 익명클래스로 구현할 수 있음

### 익명 클래스

- interface를 직접 new 연산자로 구현했을때 익명클래스로 구현할 수 있는데 여전히 코드가 장황함
- 익명클래스는 자바 8의 람다로 해결 가능

### 람다 표현식 사용



```java
interface MyPredicate {
	boolean test(int value);
}


class MyPredicateImpl implements MyPredicate {
	@Override
	boolean test (int value) {...}
}


public void some(int value, MyPredicate predicate) {
    if(predicate.test(value)) {
        ...
    }
}


//실제 사용(익명클래스)
Object someObj = test(value, new MyPredicate() {
    	//익명클래스로 구현
    	@Override
		boolean test (int value) {...}
	}
);

//실제 사용(람다 표현식)
Object someObj = test(value, (value) -> ... )


```

- 기존에 Comparator, Runnable 역시 람다로 사용할 수 있다.

## 요약

- 동작 파라미터화에서는 메서드 내부적으로 다양한 동작을 수행할 수 있도록 코드를 메서드 인수로 전달함
- 동작 파라미터화를 이용하면 벼노하하는 요구사항에 더 잘 대응할 수 있는 코드를 구현할 수 이고 나중에 엔지니어링 비용을 줄일 수 있음
- 코드 전달 기법을 이용하면 동작을 메서드의 인수로 전달할 수 있음 자바 8에서는 람다를 사용함



# #3 람다 표현식

## 람다란 무엇인가?

- 람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화 한 것
- 람다의 특징
  - 익명: 보토으이 메서드와 달리 이름이 없으므로 익명이라 표현함. 구현해야 할 코드에 대한 걱정거리가 줄어듬
  - 함수: 람다는 메서드처럼 특정 클래스에 종속되지 않으므로 함수라고 함 하지만 메서드의 기능을 포함했음
  - 전달: 람다 표현식을 메서드 인수로 전달하거나 변수로 저장 가능
  - 간결성: 익명 클래스처럼 많은 자질구레한 코드를 구현할 필요가 없음
- 람다는 세가지로 구분됨
  - 람다 파라미터: 실제 interface 타입의 파라미터
  - 화살표: 파라미터와 바디를 구분자
  - 람다의 바디: 실제 메서드의 몸체
- 함수형 인터페이스에서 사용가능



### 함수형 인터페이스

- 함수형 인터페이스는 오직 하나의 추상메서드만 갖고 있음
- java에서는 @FunctionalInterface 어노테이션을 사용해서 이 인터페이스가 한개의 추상메서드만 있을것을 보장할 수 있음
- 대표적으로 Comparator, Runnable 등이 있음
- 디폴스메서드가 있더라도 추상메서드가 한개라면 함수형 인터페이스임

```java
Runaable r1 = () -> System.out.println("hello world")	
```

## 람다 활용: 실행 어라운드 패턴

- 자원처리에 사용하는 순환패턴은 자원을 열고 처리한다음에 자원을 닫는 순서로 이루어짐
- 실제 자원을 처리하는 코드를설정과 정리 두 과정이 둘러싸는 형태를 갖는것을 실행 어라운드 패턴이라고함
- 람다를 사용해서 실행어라운드패턴을 조금 더 효율적으로 작성할 수 있음







```

```



## 함수형 인터페이스 사용

- 함수형 인터페이스의  메서드 시그니처를 함수 디스크럽터라고함
- 우리가 작성한 MyPredicate 같은것을 java.util.function 패키지에서 지원하고 있음

### Predicate

- boolean test(T t);

### Consumer

- void accept(T t);

### Function

- R apply(T t);
- T타입의 인수를 받아서 R타입으로 변환시킴



- 굉장히 많은 타입의 함수형 인터페이스를 java.util.function 에서 제공하고 있음



## 형식 검사, 형식 추론, 제약

### 형식 검사

- 람다가 사용되는 컨텍스트를 이용해서 람다의 형식 추론이 가능함

### 같은 람다, 다른 함수형 인터페이스

- 같은 람다 표현식이라도 다른 함수형 인터페이스에 할당될 수 있음

### 형식 추론



## 메서드 레퍼런스

### 요약

- 메서드명 앞에 :: 구분자를 사용해서 메서드 참조를 사용할 수 있음
- 람다에게 메서드 명을 직접적으로 전달시킴



**메서드 레퍼런스를 만드는 방법**

1. 정적 메서드 레퍼런스
   - 예를들어 Integer의 parseInt는 Integer:parseInt 로 표현 가능
2. 다양한 형식의 인스턴스 메서드 레퍼런스
   - 예를들으 String의 length 메서드는 String::length로 표현 가능
3. 기존 객체의 인스턴스 메서드 레퍼런스
   - 예를 들어 Transaction 객체를 할당받은 expensiveTransaction 지역 변수가 있고 Transaction 객체에는 getValue 메서드가 있다면 이를 expensiveTrasaction::getValue로 사용가능



## 생성자 레퍼런스

- ClassName::new 처럼 new 를 통해서 생성자의 레퍼런스를 만들 수 있음



## 람다 표현식을 조합할 수 있는 유용한 메서드

- 함수형인터페이스를 조합해서 더 커다란 함수형 인터페이스를 작성할 수 있음

### Prediacate 조합

- negate(), and(), or() 세가지 메서드를 통해서 or, and, 반전에 대해서 확장 가능

### Function 조합

- andThen(), compose() 를 사용해서 확장 가능





























