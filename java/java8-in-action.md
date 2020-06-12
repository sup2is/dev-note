









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

















