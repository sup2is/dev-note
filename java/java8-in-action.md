









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



# #4 스트림 소개

## 스트림이란 무엇인가?

- 스트림을 이용하면 선언형으로 컬렉션 데이터를 처리할 수 있음
- 멀티스레드 코드를 이용하지 않아도 병렬 스트림 으로 데이터를 병렬 처리할 수 있음
- 자바 8의 스트림 api의 특징
  - 선언형: 더 간결하고 가독성이 좋아짐
  - 조립 가능: 유연성이 좋아짐
  - 병렬화: 성능이 좋아짐

## 스트림 시작하기

- java 8의 컬렉션에서는 스트림을 반환하는 stream이라는 메서드가 추가됨(java.util.stream.Strema 참고)
- 스트림이란 데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소로 정의 가능

**연속된 요소**

- 컬렉션과 마찬가지로 스트림은 특정 요소 형식으로 이루어진 연소고딘 값 집합의 인터페이스를 제공함
- 컬렉션은 자료구조이므로 시간,공간의 복잡성과 관련된 요소 저장 및 접근 연산이 주를 이루지만 스트림은 filter,sorted,map 처럼 표현 계산식이 주를 이룸
- 즉 컬렉션의 주제는 데이터고 스트림의 주제는 계산임

**소스**

- 스트림은 컬렉션, 배열, I/O 자원 등의 데이터 제공 소스로부터 데이터를 소비함
- 정렬된 컬렉션으로 스트림을 만들면 정렬을 유지한다.

**데이터 처리 연산**

- 스트림은 함수형 프로그래밍 언어에서 일반적으로 지원하는 연산과 데이터베이스와 비슷한 연산을 지원함
- filter, map, reduce, find, match, sort 등
- 순차적 또는 병렬로 실행 가능

**파이프 라이닝**

- 대부분의 스트림 연산은 스트림 연산끼리 연결해서 커다란 파이프라인을 구성할 수 있도록스트림 자신을 반환함 그 덕분에 레이지, 쇼트서킷 같은 최적화도 얻을 수 있음
- 데이터 질의와 비슷함 from -> on -> join -> where  ...

**내부 반복**

- 반복자를 이용해서 명시적으로 반복하는 컬렉션과 달리 스트림은 내부 반복을 지원함



## 스트림과 컬렉션

- 자바의 컬렉션과 스트림 모두 시퀀셜한 요소 형식의 값을 저장하는 자료구조의 인터페이스를 제공
- 데이터를 언제 계산하느냐가 스트림과 컬렉션의 가장 큰 차이
- 컬렉션은 현재 자료구조가 포함하는 모든 값을 메모리에 저장함 즉 컬렉션의 모든 요소는 컬렉션에 추가하기 전에 계산되어야함
- 스트림은 이론적으로 요청할 때만 요소를 계산하는 고정된 자료구조
- 스트림과 컬렉션으로 무한대의 소수를 가진다고 가정했을때 컬렉션은 영원히 무한대의 소수를 계산하기때문에 무한루프에 빠지게된다.

### 딱 한번만 탐색 가능

- 반복자와 마찬가지로 스트림도 단 한번만 탐색 가능

### 외부 반복과 내부 반복

- 컬렉션을 사용하면 사용자가 직접 요소를 반복하는데 이를 외부 반복이라고 함
- 반면 스트림 라이브러리는 반복을 알아서 처리하고 결과 스트림 값을 어딘가에 저장해주는 내부 반복을 사용함
- 내부 반복을 이용하면 작업을 투명하게 병렬로 처리하거나 더 최적화된 다양한 순서로 처리할 수 있음
- 스트림 라이브러리의 내부 반복은 데이터 표현과 하드웨어를 활용한 병렬성 구현을 자동으로 선택함

## 스트림 연산

- 연결할 수 있는 스트림 연산을 중간 연산이라고 하며 스트림을 닫는 연산을 최종 연산이라고 함

### 중간 연산

- filter , sorted 같은 중간 연산은 다른 스트림을 반환함
- 중간연산의 중요한 특징은 단말연 산을 스트림 파이프라인에 실행하기 전까지는 아무 연산도 수행하지 않는다는 것임 lazy 함

### 최종 연산

### 스트림 이용하기

- 스트림 이용 과정은 다음의 세가지로 요약됨
  1. 질의를 수행할 데이터 소스
  2. 스트림 파이프라인을 구성할 중간 연산 연결
  3. 스트림 파이프라인을 실행하고 결과를 만들 최종 연산

## 요약

- 스트림은 소스에서 추출된 연속 요소로, 데이터 처리 연산을 지원함
- 스트림은 내부 반복을 지원함, 내부 반복은 filter, map, sorted 등의 연산으로 반복을 추상화함
- 스트림에는 중간 연산과 최종 연산이 있음
- filter와 map처럼 스트림을 반환하면서 다른 연산과 연결될 수 있는 연산을 중간 연산이라고 함. 중간 연산을 이용해서 파이프라인을 구성할 수 있지만 중간 연산으로는 어떤 결과도 생성 불가
- forEach난 conut 처럼 스트림 파이프라인을 처리해서 스트림이 아닌 결과를 반환하는 연산을 최종 연산이라고함
- 스트림의 요소는 요청할 때만 계산됨

# #5 스트림 활용

## 필터와 슬라이싱

### Predicate로 필터링

```
List<Dish> vegetarianMenu = menu.stream().filter(Dish::isVegetarian).collect(toList())
```

### 고유 요소 필터링

- distinct로 중복 제거

```
List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4)
numbers.stream().filter(i -> i % 2 == 0).distinct().forEach(System.out::println)
```

### 스트림 축소

- limit으로 사이즈 지정

### 요소 건너뛰기

- n개 이하로 지정할때는 skip으로 지정

## 매핑

### 스트림의 각 요소에 함수 적용하기

- map 메서드로 컬렉션 데이터를 변경 가능

```
List<String> dishNames = menue.stream().map(Dish::getName).collect(toList())
```



### 스트림 평면화

- ["Hello", "World"] 라는 리스트의 각 고유 문자를 뽑는 스트림을 작성한다고 가정함

```
words.stream().map(word -> word.split("")).distinct().collect(toList())
```

- 위 스트림의 변환 타입은 Stream(String[])임
- flatmap으로 해결 가능

**flatMap**

- flatmap을 사용하면 각 배열을 스트림이 아니라 스트림 콘텐츠로 매핑함 간단하게 설명하면 flatMap 메서드는 스트림의 각 값을 다른 스트림으로 만든 다음에 모든 스트림을 하나의 스트림으로 연결하는 기능을 수행함

```
List<String> nuiqueCharacters = words.stream()
    .map(w -> w.split(""))
    .flatMap(Arrays::stream)
    .distinct()
    .collect(Collectors.toList());
```



## 검색과 매칭

- 특정 속성이 데이터 집합에 있는지 여부를 판단할 수 있음

### Predicate가 적어도 한 요소와 일치하는지  확인

- anyMatch()를 사용해서 최소 한 요소와 일치하는지 확인함

### Predicate가 모든 요소와 일치하는지 확인

- allMatch()
- 반대되는 개념으로 noneMatch() 있음

### 쇼트 서킷

- 전체 스트림을 처리하지 않았더라도 결과를 반환할 수 있고 이러한 상황을 쇼트 서킷이라고 함
- 예를들어 allMatch() 메서드에서 하나라도 일치하지않으면 false를 바로 반환함

### 요소 검색

- findAny()로 현재 스트림에서 임의의 요소를 반환함

### 첫번째 요소 찾기

- findFirst()로 현재 스트림에서 첫번째 요소를 반환함

**findAny와 findFirst는 스트림에서 첫번째 요소찾기가 어려울 수 있음 따라서 findFirst의 사용은 병렬성에 있음**

## 리듀싱

- 스트림 요소를 조합해서 더 복잡한 질의를 표현하는 방법
- 리듀싱 연산은 모든 스트림 요소를 처리해서 값으로 도출하는 연산임

```
int sum = 0;
for (int x : numbers) {
  sum += x;
}
```

- 위 연산은 아래처럼 변할 수 있음

```
int sum = numbers.stream.reduce(0, (a, b) -> a + b)
```

- reduce는 두개의 인수를 가짐
  1. 초깃값 0
  2. 두 요소를 조합해서 새로운 값을 만드는 BinaryOperator\<T\> 
- 초기값없이 작성할 수 있는데 리턴 타입이 Optional이 됨

```
Optional<Integer> = numbers.stream.reduce((a, b) -> a + b)
```

### 최댓값과 최솟값

- reduce로 최댓값과 최솟값을 얻을수도 있음

```
Optinal<Integer> max = numbers.stream().reduce(Integer::max)
```



## 숫자형 스트림

### 기본형 특화 스트림

- map 메서드의 리턴타입은 Stream\<T\> 타입이여서 sum()같은 메서드로 int 타입의 데이터를 받을 수 없음
- 이때는 mapToInt같은 숫타 스트림 매핑 메서드를 사용해야함 mapToInt의 리턴타입은 IntStream 같은 기본형 특화 스트림임

```
int calories = mune.stream()
            .mapToInt(Dish::getCalories)
            .sum();
```

- boxed로 기본형 특화 스트림에서 일반 스트림으로 변경 가능

```
IntStream intstream = mune.stream()
            .mapToInt(Dish::getCalories);
Stream<Integer> stream = inteStream.boxed();
```



### 숫자 범위

- IntStream과 LongStream은 range와 rangeClosed라는 두가지 정적 메서드를 제공함
- range는 시작과 끝을 포함하지 않음



## 스트림 만들기

### 값으로 스트림 만들기

- 임의의 수를 인수로 받는 정적 메서드 Stream.of를 이용해서 스트림을 만들 수 있음

```
Strean<String> stream = Stream.of("java 8", "stream")
stream.map(String::toUpperCase).forEach(...)
```

- Stream.empty() 로 비울 수 있음

### 배열로 스트림 만들기

- Arrays.stream을 이용해서 스트림을 만들 수 있음

```
int[] numbers = {2, 3, 5, 7}
int sum = Arrays.stream(numbers).sum()
```



### 파일로 스트림 만들기

- java.nio.file.Files의 많은 메서드가 스트림을 반환함
- Files.lines는 주어진 파일의 행 스트림을 문자열로 반환함



### 함수로 무한 스트림 만들기

- Stream.interate와 Stream.generate를 제공함 두 연산으로 무한 스트림 생성 가능
- 보통 limit이랑 섞어서 사용함



## 요약

- 스트림 api를 이용하면 복잡한 데이터 처리 질의를 표현할 수 있음 
- iflter, distinct, skip, limit 메서드로 스트림을 필터링하거나 자를 수 있음
- map, flatMap 메서드로 스트림의 요소를 추출하거나 변환한 수 있음 
- findFirst, findAny로 스트림의 요소를 검색할 수 있음 이런 메서드들을 쇼트서킷이라고하고 결과를 찾는 즉시 리턴함
- reduce 메서드로 스트림의 모든 요소를 반복 조합하여 값을 도출할 수 있음
- filter, map 등은 상태를 저장하지 않는 상태 없는 연산임 reduce, sorted 같은 연산은 소트름의 모든 요소를 버퍼에 저장하기때문에 상태 있는 연산이라고함
- IntStream, DoubleStream, LongStream은 기본형 특화 스트림임
- 컬렉션뿐 아니라 값, 배열, 파일로도 스트림 생성 가능
- 크기가 정해지지 않은 스트림을 무한스트림이라고함



# #6 스트림으로 데이터 수집

## 컬렉터란 무엇인가?

### 고급 리듀싱 기능을 수행하는 컬렉터

- 스트림에 collect를 호출하면 스트림의 요소에 리듀싱 연산이 수행됨. 
- 직접 정의도 가능함 가장 많이 사용하는게 toList()

### 미리 정의된 컬렉터

- Collectors에서 제공하는 메서드의 기능은 크게 세가지임
  1. 스트림 요소를 하나의 값으로 리듀스하고 요약
  2. 요소 그룹화
  3. 요소 분할

## 리듀싱과 요약

- 컬렉터로 스트림의 모든 항목을 하나의 결과로 합칠 수 있음

### 스트림값에서 최댓값과 최솟값 검색

- Collectors.maxBy, Collectors.minBy로 찾을 수 있음

```
Comparator<Dish> dishCaloriesComparator = Comparator.comparingInt(Dish::getCalories);

Optinal<Dish> mostCalorieDish = menu.stream().collect(maxBy(dishCaloriesComparator));
```



### 요약 연산

- summingInt 같은 특별한 요약 팩터리 메서드를 제공함



### 문자열 연결

- joining 팩토리 메서드를 이요하면 스트림의 각 객체에 toString 메서드를 호출해서 추출한 모든 문자열을 하나의 문자열로 연결해서 반환함
- joining()은 내부적으로 StringBuilder를 이용해서 문자열을 하나로 만듦

```
menu.stream().collect(joining());
```



### 범용 리듀싱 요약 연산

- 모든 컬렉터는 reducing 팩토리 메서드로도 정의할 수 있음
- reducing을 사용하지않은 범용 팩터리 메서드는 프로그래머의 편의를 위함이였음
- reducing은 세개의 변수를 얻음
  1. 첫 번째 인수는 리듀싱 연산의 시작값이거나 스트림에 인수가 없을때는 반환값임
  2. 두 번째 인수는 변환 함수
  3. 두항목을 하나의 값으로 더하는 BinaryOperator임

```
int totalCalories = menu.stream().collect(reducing(0, Dish:getCalories, (i,j) -> i + j));
```



## 그룹화

- 그룹화 연산의 결과로 그룹화 함수가 반환하는 키 그리고 각 키에 대응하는 스트림의 모든 항목 리스트를 값으로 갖는 맵이 반환됨

```
Map<Dish.Type, List<Dish>> dishesByType = menu.stream().collect(groupingBy(dish::getType))
```

### 다수준 그룹화

- 두 인수를 받는 팩토리 메서드 Collectors.groupingBy를 이용해서 항목을 다수준으로 그룹화 할 수 있음

```
Map<Dish.Type, Map<CaloricLevel, List<Dish>>> dishesByTypeCaloricLevel = menu.stream()
.collect(
	groupingBy(Dish:getType,
    	groupingBy(dish -> {
    		if(dish.getCalories() <= 400) {return CaloricLevel.Diet} 
    		else if (dish.getCalories() <= 700) {return CaloricLevel.NORMAL}
    		else {return CaloricLevel.FAT}
    		})))
```

- 다수준 그룹화 연산은 다양한 수준으로 확장됨 즉 n 수준 그룹화의 결과는 n수준 트리 구조로 표현되는 n수준 맵이됨
- 보통 groupingBy의 연산을 버킷 개념으로 생각하면 쉬움

### 서브그룹으로 데이터 수집

```
Map<Dish.Type, Long> typesCount = menu.stream().collect(groupingBy(Dish::getType, counting()));
```

**컬렉터 결과를 다른 형식에 적용하기**

- Collectors.collectingandThen으로 컬렉터가 반환한 결과를 다른 형식으로 활용 가능

```
Map<Dish.Type, Dish> mostCaloricByType = 
	menu.stream()
		.collect(groupingBy(Dish::getType,
			collectiongAndThen(
				maxBy(comparingInt(dish::getCalories)), Optional::get)))
```

- 리듀싱 컬럭테너는 절대 Optional.empty를 반환하지 않음!

- 가장 외부 계층에서 안쪽으로 다음과 같은 작업이 수행됨
  1. 컬렉터는 점선으로 표시되어 있으며 groupingBy는 가장 바깥쪽에 위치하면서 요리의 종류에 따라 메뉴 스트림을 세 개의 서브스트림으로 그룹호함
  2. groupingBy 컬렉터는 collectiongAndThen 컬렉터를 감싼다. 따라서 두 번째 컬렉터는 그룹화된 새 개의 서브스트림에 적용됨
  3. collectiongAndThen  컬렉터는 세번째 컬렉터 maxBy를 감싼다.
  4. 리듀싱 컬렉터가 서브스트림에 연산을 수행한 결과에 collectingAndThen의 Optional::get 변환 함수가 젹옹됨
  5. groupingBy 컬렉터가 반환하는 맵의 분류 키에 대응하는 세 값이 각각의 요리 형식에서 가장 높은 칼로리임



## 분할

- 분할은 분할 함수라 불리는 predicate를 분류 함스로 사용하는 특수한 그룹화 기능임
- true or false의 그룹으로 분류됨

```
Map<Boolean, List<Dish>> partitionedMenu = 
	menu.stream().collect(partitionBy(Dish::isVegeterian))
```



### 분할 함수의 장점

- 분할 함수의 장점은 true, false 두가지 요소의 스트림 리스트를 모두 유지한다는 것임



### 숫자를 소수와 비소수로 분할하기



## Collector 인터페이스

- Collector 인터페이스는 리듀싱 연산을 어떻게 구현할지 제공하는 메서드 집합으로 구성됨 toList, gorupingBy 등

```java
public interface Collector<T, A, R> {
    Supplier<A> supplier();
    BiConsumer<A, T> accumulator();
    BinaryOperator<A> combiner();
    Function<A, R> finisher();
    Set<Characteristics> characteristics();
}
```



- Collector 는 스킵







# #7 병렬 데이터 처리와 성능

- 스트림을 사용하면 순차 스트림을 병렬 스트림으로 쉽게 바꿀 수 있음

## 병렬 스트림

- 컬렉션에 parallelStream을 호출하면 병렬 스트림이 생성됨

```
public static long parallelSum (long n) {
	return Stream.interate(1L, i -> i + 1)
		.limit(n)
		.parallel()
		.reduce(0L, Long::sum)
}
```



### 스트림 성능 측정

- 리듀싱 과정을 시작하는 시점에 전체 숫자 리스트가 준비되지 않았을 경우 스트림을 병렬로 추리할 수 있도록 청크로 분할할 수 없음
- 병렬로 지시했고 각각 다른 스레드에서 실행되지만 사실 이런 경우 순차처리방식과 별 다른점이 없고 스레드를 할당하는 비용만 나와서 일반적인 순차처리보다 늦어짐
- 병렬 프로그래밍은 상당히 까다로운 주제임
- 이 메서드가 언박싱 또는 박싱이 이루어지는지, 어떤 자료구조를 사용해야하는지에 대한 주제가 성능측정에 매우 중요함
- 병렬 처리는 매우 비싼 비용이기때문에 코어 간에 데이터 전송 시간보다 훨씬 오래 걸리는 작업만 병렬로 다른 코어에서 수행하는 것이 바람직함.
- 항상 병렬화를 올바르게 사옹하고 있는지를 확인해야함



### 병렬 스트림의 올바른 사용법

- 병렬 스트림을 잘못 사용하면서 발생하는 많은 문제는 공유된 상태를 바꾸는 알고리즘을 사용하기 때문임
- 병렬 스트림 계산에서는 공유된 가변 상태를 피해야함



### 병렬 스트림 효과적으로 사용하기

- 확신이 서지 않는다면 직접 측정할 것, 그리고 신중하게 고려할 것, 병렬 스트림이 순차스트림보다 빠른것이 아님, 반드시 직접 성능체크할것
- 박싱을 주의할 것, 자동 박싱과 언박싱은 성능을 크게 저하시킬 수 있는 요소임 IntStream같은 기본형 특화 스트림을 사용하는 것이 좋음
- 순차스트림보다 병렬 스트림에서 성능이 떨어지는 연산이 있는데 특히 limit이나 findFirst 처럼 요소의 순서에 의존하는 연산을 병렬스트림에서 수행하려면 비싼 비용을 치러야함
- 스트림에서 수행하는 전체 파이프라인 연산 비용을 고려할것 처리해야 할 요소 수가 N이고 하나의 요소를 처리하는데 드는 비용이 Q라 하면 전체 스트림 파이프라인 처리비용은 N*Q로 예쌍가능함 Q가 높아진다는건 병렬 스트림으로 성능을 개선할 수 있는 가능성이 있음을 의미
- 소량의 데이터에서는 병렬 스트림이 도움이 됮 ㅣ않음 소량의 데이터를 처리하느 ㄴ상황에서는 병렬화 고자ㅓㅇ에서 생기는 부가 비용을 상쇄할 수 있을 만큼의 이득을 얻지 못하기 때문
- 스트림을 구성하는 자료구조가 적절한지 확인할 것 예를들어 ArrayList가 LinkedList보다 분할하는것은 더 좋음 



## 포크/조인 프레임워크

은 .. 스킵



## Spliterator

도 스킵



## 요약

- 내부 반복을 이용하면 명시적으로 다른 스레드를 사용하지 않고도 스트림을 병렬로 처리할 수 있음
- 간단하게 스트림을 병렬로 처리할 수 있지만 항상 병렬 처리가 빠른 것은 아님. 병렬 소프트웨어 동작 방법과 성능은 직관적이지 않을 때가 많으므로 병렬 처리를 사용했을 때 선응을 직접 측정해봐야 함
- 병렬 스트림으로 데이터 집합을 병렬 실행할 때 특히 처리해야 할 데이터가 아주 많거나 각 요소를 처리하는데 오랜 시건이 걸릴 때 성능을 높일 수 있음
- 가능하면 기본형 특화 스트림을 사용하느 ㄴ등 올바른 자료구조 선택이 어떤 연산을 병렬로 처리하는 것 보다 성능적으로 더 큰 영향을 미칠 수 있음
- 포크/조인 프레임워크에서는 병렬화 할 수 있는 태스크를 작은 태스크로 분할한 다음에 분할된 태스크를 각각 스레드로 실행하며 서브태스크 각각의 결과를 합쳐서 최종 결과를 생성함
- Spliterator는 탐색하려는 데이터를 포함하는 스트림을 어떻게 병렬화할 것인지 정의함



















































