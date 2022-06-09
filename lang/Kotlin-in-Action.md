

# Kotlin in Action





# #1 코틀린이란 무엇이며 왜 필요한가?



## 코틀린 맛보기

```kotlin
data class Person(val name: String, val age: Int? = null)

fun main(args:Array<String>) {
    val persons = listOf(Person("영희"), Person("영희", age = 29))

    val oldest = persons.maxBy { it.age ?: 0 }
    println("나이가 가장 많은 사람: $oldest")
}
```



## 코틀린 주요 특성

- 코틀린의 주 목적은 현재 자바가 사용되고 있는 모든 용도에 적합하면서도 더 간결하고 생산적이며 안전한 대체 언어를 제공하는 것이다.

  

  

### 대상 플랫폼: 서버, 안드로이드 등 자바가 실행되는 모든 곳

- 일반적으로 코틀린을 활용할 수 있는 곳
  - 서버상의 코드
  - 안드로이드 디바이스에서 실행되는 모바일 애플리케이션

### 정적 타입 지정 언어

- 코틀린은 정적 타입 지정 언어다.
- 정적 타입 지정이라는 말은 모든 프로그램 구성 요소의 타입을 컴파일 시점에 알 수 있고 프로그램 안에서 객체의 필드나 메서드를 사용할 때마다 컴팡일러가 타입을 검증해준다는 뜻이다.
- 동적 타입 언어는 유연하지만 컴파일시점에 오류를 거르지 못하고 실행 시점에 오류가 발생한다.
- 코틀린에서는 모든 변수의 타입을 프로그래머가 직접 명시할 필요가 없다.

```kotlin
var x = 1
```

- 컴파일러가 문맥을 고려해 변수 타입을 결정하는 이런 기능을 타입 추론이라고 부른다.
- 정적 타입 지정의 장점
  - 성능: 실행 시점에 어떤 메서드를 호출할지 알아느낸 과정이 필요 없으므로 메서드 호출이 더 빠르다.
  - 신뢰성: 컴파일러가 프로그램의 정확성을 검증하기 때문에 실행 시 프로그램이 오류로 중단될 가능성이 더 적어진다.
  - 유지 보수성: 코드에서 다루는 객체가 어떤 타입에 속하는지 알 수 있기 때문에 처음 보는 코드를 다룰 때도 더 쉽다.
  - 도구 지원: 정적 타입 지정을 활용하면 더 안전하게 리팩토링 할 수 있고, 도구는 더 정확한 코드 완성 기능을 제공할 수 있으며, IDE의 다른 지원 기능도 더 잘 만들 수 있다.
- 코틀린은 널이 될 수 있는 타입을 지원한다. 널이 될 수 있는 타입을 지원함에 따라 컴파일 시점에 널 포인터 예외가 발생할 수 있는지 여부를 검사할 수 있어서 좀 더 프로그램의 신뢰성을 높일 수 있다.



### 함수형 프로그래밍과 객체지향 프로그래밍

- 함수형 프로그래밍의 핵심 개념
  - 일급 시민인 함수: 함수를 일반 값처럼 다룰 수 있다. 함수를 변수에 저장할 수 있고, 함수를 인자로 다른 함수에 전달할 수 있고 함수에서 새로운 함수를 만들어서 반환할 수 있다.
  - 불변성: 함수형 프로그래밍에서는 일단 만들어지고 나면 내부 상태가 절대로 바뀌지 않는 불변 객체를 사용해 프로그램을 작성한다.
  - 부수 효과 없음: 함수형 프로그래밍에서는 입력이 같으면 항상 같은 출력을 내놓고 다른 객체의 상태를 변경하지 않으며 함수 외부나 다른 바깥 환경과 상호작용하지 않는 순수 함수를 사용한다.
- 함수형 프로그래밍은 간결하고 멀티 스레드에도 안전하다. 테스트하기도 쉽다.
- 일반적으로 언어에 함수형을 사용하는 제약은 없지만 모든 언어가 함수형 프로그래밍을 편하게 사용하기에 충분한 라이브러리와 문법을 지원하지 않는다. 코틀린은 함수형 프로그래밍을 풍부하게 지원한다.
  - 함수 타입을 지원함에 따라 어떤 함수가 다른 함수를 파라미터로 받거나 함수가 새로운 함수를 반환할 수 있다.
  - 람다 식을 지원함에 따라 번거로운 준비 코드를 작성하지 않아도 코드 블록을 쉽게 정의하고 여기저기 전달할 수 있다.
  - 데이터 클래스는 불변적인 값 객체를 간편하게 만들 수 있는 구문을 제공한다.
  - 코틀린 표준 라이브러리는 객체와 컬렉션을 함수형 스타일로 다룰 수 있는 API를 제공한다.
- 코틀린으로 코드를 작성할 때는 객체지향과 함수형 접근 방법을 함께 조합해서 문제에 가장 적합한 도구를 사용하면 된다.



### 무료 오픈소스

## 코틀린 응용

### 코틀린 서버 프로그래밍

### 코틀린 안드로이드 프로그래밍

## 코틀린의 철학

### 실용성

- 코틀린은 실제 문제를 해결하기 위해 만들어진 실용적인 언어다.

### 간결성

- 코드가 더 간단하고 간결할수록 내용을 파악하기 쉽다.
- 코틀린은 프로그래머가 작성하는 코드에서 의미가 없는 부분을 줄이고 언어가 요구하는 구조를 만족시키기 위해 별 뜻은 없지만 프로그램에 꼭 넣어야하는 부수적인 요소를 줄이기 위해 많은 노력을 기울였다. ex getter, setter ... 

### 안전성

- 어떤 언어도 발생할 수 있는 모든 오류를 막을 수 없다.
- 더 큰 안전성을 얻기 위해서는 프로그램에 더 많은 정보를 덧붙여야 하므로 생산성이 하락한다. 안정성과 생산성 사이에는 트레이드 오프가 존재한다.
- 코틀린을 만들면서 자바보다 더 높은 수준의 안전성을 달성하되 전체 비용은 더 적게 지불하도록 설계했다.

### 상호운용성

- 자바의 라이브러리, 애너테이션, 인터페이스 등등 모두 코틀린 코드에 적용하는 일이 가능하다.
- 자바와 코틀린 소스 파일을 자유롭게 내비게이션 할 수 있고 자바 메서드를 리팩토링해도 그 메서드와 관련 있는 코틀린 코드까지 변경된다.



## 코틀린 도구 사용

### 코틀린 코드 컴파일

- 코틀린 소스코드는 보통 .kt 라는 확장자를 파일에 붙인다.
- 컴파일되면 .class 파일을 만들어낸다.
- 코틀린 컴파일러로 컴파일한 코드는 코틀린 런타임 라이브러리에 의존한다.
- 런타임 라이브러리는 코틀린 자체 표준 라이브러리 클래스와 코틀린에서 자바 API의 기능을 확장한 내용이 들어 있다. 
- 코틀린으로 컴파일한 애플리케이션을 배포할때는 런타임 라이브러리도 함께 배포해야 한다.
- 코틀린은 maven, gradle, ant 등의 빌드 시스템과 호환된다.
- maven과 gradle은 애플리케이션을 패키지할 때 알아서 코틀린 런타임을 포함시켜준다.



### 인텔리J IDEA와 안드로이드 스튜디오의 코틀린 플러그인

### 대화형 셸

### 이클립스 플러그인

### 온라인 놀이터

### 자바-코틀린 변환기

## 요약

- 코틀린은 타입 추론을 지원하는 정적 타입 지정 언어다. 따라서 소스코드의 정확성과 성능을 보장하면서도 소스코드를 간결하게 유지할 수 있다.
- 코틀린은 객체지향과 함수형 프로그래밍 스타일을 모두 지원한다. 코틀린에서는 일급 시민 함수를 사용해 수준 높은 추상화가 가능하고 불변 값 지원을 통해 멀티 스레드 애플리케이션 개발과 테스트를 더 쉽게 할 수 있다.
- 코틀린을 서버 애플리케이션 개발에 잘 활용할 수 있다. 코틀린은 기존 자바 프레임워크를 완벽하게 지원하는 한편, HTML 생성기나 영속화등의 일반적인 작업을 위한 새로운 도구를 제공한다.
- 코틀린을 안드로이드에도 활용할 수 있다.
- 코틀린은 오픈소스다. 또한 주요 IDE와 빌드 시스템을 완전히 지원한다.
- 코틀린은 실용적이며 안전하고 간결하며 상호운용성이 좋다. 이는 코틀린을 설계하면서 일반적인 작업에 대해 이미 잘 알려진 해법을 채택하고 NullPointerException과 같이 흔히 발생하는 오류를 방지하며 읽기 쉽고 간결한 코드를 지원하면서 자바와 아무런 제약 없이 통합될 수 있는 언어를 만드는 데 초점을 맞췄다는 뜻이다.





# #2 코틀린 기초

## 기본 요소: 함수와 변수

### Hello, World!

```kotlin
fun main(args: Array<String>) {
    println("Hello, world!")
}

```

- 함수를 선언할 때 fun 키워드를 사용한다.
- 파라미터의 이름 뒤에 그 파라미터의 타입을 쓴다.
- 함수를 최상위 수준에 정의할 수 있다. 자바와 달리 꼭 클래스 안에 함수를 넣어야 할 필요가 없다.
- 배열도 일반적인 클래스와 마찬가지다. 코틀린에는 자바와 달리 배열 처리를 위한 문법이 따로 존재하지 않는다.
- 코틀린 표준 라이브러리는 여러 가지 표준 자바 라이브러리 함수를 간결하게 사용할 수 있게 감싼 래퍼를 제공한다. println도 그런 함수 중 하나다.
- 줄 끝에 세미콜론을 붙이지 않아도 된다.

### 함수

```kotlin
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}

fun main(args: Array<String>) {
    println(max(1, 2))
}

```

- 함수 이름 뒤에는 괄호 안에 파라미터 목록이 온다.
- 함수의 반환 타입은 파라미터 목록의 닫는 괄호 다음에 오는데 괄호와 반환 타입 사이를 콜론으로 구분해야 한다.
- 코틀린의 if는 결과를 만드는 식이다.
- 자바에서 모든 제어 구조는 문(statement)이지만 코틀린에서는 루프를 제외한 대부분의 제어 구조가 식(expression)이다.



**식이 본문인 함수**

- 앞의 함수 본문은 if 식 하나로만 이뤄져 있다. 이런 경우 다음과 같이 중괄호를 없애고 return을 제거하면서 등호를 식 앞에 붙이면 더 간결하게 함수를 표현할 수 있다.

```kotlin
fun max(a: Int, b: Int) : Int = if (a > b) a else b
```

- 코틀린에서는 식이 본문인 함수가 자주 쓰인다.
- 반환타입까지 생략할 수 있다. (식이 본문인 함수만 가능!!)

```kotlin
fun max(a: Int, b: Int) = if (a > b) a else b
```

- 식이 본문인 함수의 경우 사용자가 반환타입을 적지 않아도 컴파일러가 함수 본문 식을 분석해 식의 결과 타입을 함수 반환 타입으로 정해준다. (타입 추론)

### 변수

- 코틀린에서는 타입 지정을 생략하는 경우가 흔하다.

```kotlin
val question = "어쩌구" // String
val answer = 42 // Int
val answer:Int = 42 // Int

val yearsToCompute = 7.5 // Double
```

- 초기화식을 사용하지 않고 변수를 선언하려면 변수 타입을 반드시 명시해야한다.

```kotlin
val answer: Int
answer = 42
```

**변경 가능한 변수와 변경 불가능한 변수**

- val: 변경 불가능한 참조를 저장하는 변수.
- var: 변경 가능한 참조를 저장하는 변수.
- 기본적으로 모든 변수를 val로 선언해서 불변으로 사용하고 나중에 꼭 필요할때만 var로 변경하자. (함수형 스타일)
- val 참조 자체는 불변이지만 그 참조가 가리키는 객체 내부 값은 변경될 수 있다.
- var 키워드를 사용하면 변수의 값을 변경할 수 있지만 변수의 타입은 고정돼 바뀌지 않는다.

```kotlin
var answer = 42
answer = "no answer" //불가능. 컴파일에러 발생
```



### 더 쉽게 문자열 형식 지정: 문자열 템플릿

```kotlin
fun main(args: Array<String>) {
    val name = if (args.size > 0) args[0] else "Kotlin"
    println("Hello, $name!")
}

```

- 코틀린에서도 문자열 리터럴 안에서 변수를 사용할 수 있다.
- 문자열 리터럴의 필요한 곳에 변수를 넣되 변수 앞에 $를 추가해야 한다.
- $ 문자를 문자열에 넣고 싶으면 이스케이프 시켜야 한다.
- 아래와 같이 중괄호를 묶어서 사용하는 것을 추천한다.

```kotlin
    println("Hello, ${name}!")
```



## 클래스와 프로퍼티

```kotlin
class Person(
    val name: String,
    var isMarried: Boolean
)
```

- 이런 유형의 코드가 없이 데이터만 저장하는 클래스를 값 객체라고 부른다.
- 코틀린의 기본 가시성은 public이므로 이런 경우 변경자를 생략해도 된다.



### 프로퍼티

- 클래스라는 개념의 목적은 데이터를 캡슐화하고 캡슐화한 데이터를 다루는 코드를 한 주체 아래 가두는 것이다.
- 자바에서는 필드와 접근자를 한데 묶어 프로퍼티라고 하며 코틀린은 프로퍼티를 언어 기본 기능으로 제공한다.
- 코틀린 프로퍼티는 자바의 필드와 접근자 메서드를 완전히 대신한다.
- 클래스에서 프로퍼티를 선언할 때는 앞에서 살펴본 변수를 선언하는 방법과 마찬가지로 val 또는 var를 사용한다.
- val로 선언한 프로퍼티는 읽기전용, var로 선언한 프로퍼티는 변경 가능

```kotlin
class Person(
    val name: String, // private, getter
    var isMarried: Boolean // private, getter, setter
)
```

- 코틀린에서는 프로퍼티이름을 직접 사용해도 코틀린이 자동으로 getter를 호출해준다.
- 대부분의 프로퍼티에는 그 프로퍼티의 값을 저장하기 위한 필드가 있다. 이를 프로퍼티를 뒷받침하는 필드 backing field라고 한다.
- 원한다면 프로퍼티 값을 그때그때 계산하는 커스텀 게터를 사용할 수 있다.



### 커스텀 접근자

```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
        get() {
            return height == width
        }
}

fun main(args: Array<String>) {
    val rectangle = Rectangle(41, 43)
    println(rectangle.isSquare)
}

```

- isSquare 프로퍼티에는 자체 값을 저장하는 필드가 필요 없다. 이 프로퍼티에는 자체 구현을 제공하는 게터만 존재한다. 클라이언트가 이 프로퍼티에 접근할 때마다 게터가 프로퍼티 값을 매번 다시 계산한다.



### 코틀린 소스 구조: 디렉터리와 패키지

- 코틀린도 자바와 비슷한 개념의 패키지가 있다.
- 같은 패키지에 속해 있다면 다른 파일에서 정의한 선언일지라도 직접 사용할 수 있다.
- 다른 패키지에 정의한 선언을 사용하려면 임포트를 통해 선언을 불러와야 한다.
- 코틀린에서는 여러 클래스를 한 파일에 넣을 수 있고 파일의 이름도 마음대로 정할 수 있다.
- 코틀린에서는 디스크상의 어느 디렉터리에 소스코드 파일을 위치시키든 관계 없다.
- 하지만 대부분의 경우 자바와 같이 패키지별로 디렉터리를 구성하는 편이 낫다.
- 여러 클래스를 한 파일에 넣는 것을 주저하지 말자.



## 선택 표현과 처리: enum과 when

- when은 자바의 switch를 대치하되 훨씬 강력하다.

### enum 클래스 정의

```kotlin
enum class Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}

```

- enum은 자바 선언보다 코틀린 선언에 더 많은 키워드를 써야 하는 흔치 않은 예다.
- 코틀린에서 enum은 소프트 키워드라 부르는 존재다.
- 자바와 마찬가지로 enum안에 프로퍼티나 메서드를 정의할 수 있다.

```kotlin
enum class Color(
        val r: Int, val g: Int, val b: Int
) {
    RED(255, 0, 0), ORANGE(255, 165, 0),
    YELLOW(255, 255, 0), GREEN(0, 255, 0), BLUE(0, 0, 255),
    INDIGO(75, 0, 130), VIOLET(238, 130, 238);

    fun rgb() = (r * 256 + g) * 256 + b
}

fun main(args: Array<String>) {
    println(Color.BLUE.rgb())
}

```

- enum 안에 메서드를 정의하는 경우 반드시 enum 상수 목록과 메서드 정의 사이에 세미콜론을 넣어야 한다 



### when으로 enum 클래스 다루기

```kotlin
enum class Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}

fun getMnemonic(color: Color) =
    when (color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
    }

fun main(args: Array<String>) {
    println(getMnemonic(Color.BLUE))
}

```

- if와 마찬가지로 when도 expression이기 때문에 함수에 when을 바로 사용할 수 있다.
- 자바와 달리 각 분기의 끝에 break를 넣지 않아도 된다.
- 한 분기 안에서 여러 값을 매치 패턴으로 사용하려면 콤마로 분리한다.

```kotlin
enum class Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}

fun getWarmth(color: Color) = when(color) {
    Color.RED, Color.ORANGE, Color.YELLOW -> "warm"
    Color.GREEN -> "neutral"
    Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold"
}

fun main(args: Array<String>) {
    println(getWarmth(Color.ORANGE))
}

```



### when과 임의의 객체를 함께 사용

- 코틀린의 when 분기 조건은 임의의 객체를 허용한다.

```kotlin
fun mix(c1: Color, c2: Color) =
        when (setOf(c1, c2)) {
            setOf(RED, YELLOW) -> ORANGE
            setOf(YELLOW, BLUE) -> GREEN
            setOf(BLUE, VIOLET) -> INDIGO
            else -> throw Exception("Dirty color")
        }

fun main(args: Array<String>) {
    println(mix(BLUE, YELLOW))
}

```

- when 식의 인자로 아무 객체나 사용할 수 있다. when은 이렇게 인자로 받은 객체가 각 분기 조건에 있는 객체와 같은지 테스트한다.
- setOf는 Set으로 만들고 순서는 중요하지 않기 때문에 예상하는대로 잘 동작한다.
- when에서는 동등성을 사용한다. 맨 처음부터 끝까지 순차적으로 동등성을 검사하고 조건을 찾을 수 없다면 else 분기로 빠진다.

### 인자 없는 when 사용

- 인자가 없는 when 식을 사용하면 불필요한 객체 생성을 막을 수 있다.

```kotlin

fun mixOptimized(c1: Color, c2: Color) =
    when {
        (c1 == RED && c2 == YELLOW) ||
        (c1 == YELLOW && c2 == RED) ->
            ORANGE

        (c1 == YELLOW && c2 == BLUE) ||
        (c1 == BLUE && c2 == YELLOW) ->
            GREEN

        (c1 == BLUE && c2 == VIOLET) ||
        (c1 == VIOLET && c2 == BLUE) ->
            INDIGO

        else -> throw Exception("Dirty color")
    }

fun main(args: Array<String>) {
    println(mixOptimized(BLUE, YELLOW))
}
```

- when에 아무 인자도 없으려면 각 분기의 조건이 불리언 결과를 계산하는 식이어야 한다.
- 이런 방식은 추가 객체를 만들지는 않지만 가독성은 더 떨어진다.



### 스마트 캐스트: 타입 검사와 타입 캐스트를 조합

```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr): Int {
    if (e is Num) {
        val n = e as Num
        return n.value
    }
    if (e is Sum) {
        return eval(e.right) + eval(e.left)
    }
    throw IllegalArgumentException("Unknown expression")
}

fun main(args: Array<String>) {
    println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
}

```

- 코틀린에서는 is를 사용해 변수 타입을 검사한다
- 자바에서는 instanceof로 확인한 다음에 그 타입에 속한 멤버에 접근하기 위해 명시적으로 변수 타입을 캐시팅해야 한다.
- 코틀린에서는 프로그래머 대신 컴파일러가 캐스팅을 해준다.
- 어떤 변수가 원하는 타입인지 일단 is로 검사하고 나면 굳이 변수를 원하는 타입으로 캐스팅하지 않아도 마치 처음부터 그 변수가 원하는 타입으로 선언된 것처럼 사용할 수 있다. 하지만 실제로는 컴파일러가 캐스팅을 수행해준다. 이를 스마트캐스트라고 한다.

```kotlin

interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr): Int {
    if (e is Num) {
        val n = e as Num // 없어도 됨 e.value로 가능
        return n.value
    }
    if (e is Sum) {
        return eval(e.right) + eval(e.left)
    }
    throw IllegalArgumentException("Unknown expression")
}

fun main(args: Array<String>) {
    println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
}

```

- IDE에서 스마트 캐스트 부분의 배경색을 달리 표현해준다.
- 스마트 캐스트는 is로 변수에 든 값의 타입을 검사한 다음에 그값이 바뀔 수 없는 경우에만 작동한다.
- 스마트 캐스트를 사용한다면 그 프로퍼티는 반드시 val이어야 하며 커스텀 접근자를 사용한 것이어도 안된다.



### 리팩토링: if를 when으로 변경

- 코틀린에는 if가 값을 만들어내는 expression이기 때문에 삼항연산자가 따로 없다.

```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr): Int =
    if (e is Num) {
        e.value
    } else if (e is Sum) {
        eval(e.right) + eval(e.left)
    } else {
        throw IllegalArgumentException("Unknown expression")
    }

fun main(args: Array<String>) {
    println(eval(Sum(Num(1), Num(2))))
}

```

- when절을 사용하면 더 깔끔하게 쓸 수 있다.

```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr): Int =
    when (e) {
        is Num ->
            e.value
        is Sum ->
            eval(e.right) + eval(e.left)
        else ->
            throw IllegalArgumentException("Unknown expression")
    }

fun main(args: Array<String>) {
    println(eval(Sum(Num(1), Num(2))))
}

```



### if와 when의 분기에서 블록 사용

- if나 when 모두 분기에 블록을 사용할 수 있다.
- 블록을 사용하는 경우 마지막 문장이 블록 전체의 결과가 된다.

```kotlin
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun evalWithLogging(e: Expr): Int =
    when (e) {
        is Num -> {
            println("num: ${e.value}")
            e.value // return
        }
        is Sum -> {
            val left = evalWithLogging(e.left)
            val right = evalWithLogging(e.right)
            println("sum: $left + $right")
            left + right // return
        }
        else -> throw IllegalArgumentException("Unknown expression")
    }

fun main(args: Array<String>) {
    println(evalWithLogging(Sum(Sum(Num(1), Num(2)), Num(4))))
}

```





## 대상을 이터레이션: while과 for 루프

- 코틀린과 자바에서 이터레이션은 거의 비슷하다.

### while루프

### 수에 대한 이터레이션: 범위와 수열

- 코틀린에는 자바의 for 루프에 해당하는 요소가 없다. (어떤 변수를 초기화하고 그 변수를 사용하는 방법)
- 코틀린은 범위를 사용한다.

```kotlin
val oneToTen = 1..0
```

- 코틀린의 범위는 폐구간 또는 양끝을 포함하는 구간이다. 이는 두 번째 값이 항상 범위에 포함된다는 뜻이다.
- 정수 범위로 수행할 수 있는 가장 단순한 작업은 범위에 속한 모든 값에 대한 이터레이션이다. 이런식으로 어떤 범위에 속한 값을 일정한 순서로 이터레이션하는 경우를 수열이라고 부른다.

```kotlin
fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz "
    i % 3 == 0 -> "Fizz "
    i % 5 == 0 -> "Buzz "
    else -> "$i "
}

fun main(args: Array<String>) {
    for (i in 1..100) {
        print(fizzBuzz(i))
    }
}

```

```kotlin
fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz "
    i % 3 == 0 -> "Fizz "
    i % 5 == 0 -> "Buzz "
    else -> "$i "
}

fun main(args: Array<String>) {
    for (i in 100 downTo 1 step 2) {
        print(fizzBuzz(i))
    }
}

```

- 100부터 거꾸로 -2개씩 순열을 생성하는 예제
- 위에서 언급한대로 ..은 마지막 값을 포함한다 마지막 값을 포함하고싶지 않다면 until을 사용하면 된다. 나중에 자세히

### 맵에 대한 이터레이션

```kotlin
fun main(args: Array<String>) {
    val binaryReps = TreeMap<Char, String>()

    for (c in 'A'..'F') {
        val binary = Integer.toBinaryString(c.toInt())
        binaryReps[c] = binary
    }

    for ((letter, binary) in binaryReps) {
        println("$letter = $binary")
    }
}

```

- get과 put을 사용하는 대신 map[key]나 map[key] = value를 사용해 값을 가져오고 설정할 수 있다.
- 이런 문법은 컬렉션에도 활용할 수 있다.

```kotlin
val list = arrayListOf("10", "11", "1001")
for ((index, element) in list.withIndex()) { // index와 함께 컬렉션을 이터레이션한다.
	println("$index: $element")
}
```



### in으로 컬렉션이나 범위의 원소 검사

- in 연산자를 사용해 어떤 값이 범위에 속하는지 검사할 수 있다.
- 반대로 !in을 사용하면 어떤 값이 범위에 속하지 않는지 검사할 수 있다.

```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
fun isNotDigit(c: Char) = c !in '0'..'9'

fun main(args: Array<String>) {
    println(isLetter('q'))
    println(isNotDigit('x'))
}

```

```kotlin
fun recognize(c: Char) = when (c) {
    in '0'..'9' -> "It's a digit!"
    in 'a'..'z', in 'A'..'Z' -> "It's a letter!"
    else -> "I don't know…"
}

fun main(args: Array<String>) {
    println(recognize('8'))
}

```

- java.lang.Comparable을 구현한 클래스라면 그 클래스의 인스턴스 객체를 사용해 범위를 만들 수 있다.
- 자세한 내용은 뒤에 ..

## 코틀린의 예외 처리

- 코틀린의 예외 처리도 자바랑 비슷하다.
- 자바와 다른 부분은 new를 사용해서 예외를 생성하지 않고 throw는 expression으로 동작한다.

```kotlin
val percentage = 
	if (number in 0..100)
		number
	else
		throw IllegalArgumentException("...")
```

- 이 떄 percentage 변수는 초기화되지 않는다
- throw에 대한 설명은 6장에서 ...



### try, catch, finally

```kotlin

fun readNumber(reader: BufferedReader): Int? {
    try {
        val line = reader.readLine()
        return Integer.parseInt(line)
    }
    catch (e: NumberFormatException) {
        return null
    }
    finally {
        reader.close()
    }
}

fun main(args: Array<String>) {
    val reader = BufferedReader(StringReader("239"))
    println(readNumber(reader))
}

```

- 코틀린에서는 체크예외와 언체크 예외를 구분하지 않는다.
- 코틀린에서는 함수가 던지는 예외를 지정하지 않고 발생한 예외를 잡아내도 되고 잡아내지 않아도 된다.
- 문법은 대부분 비슷하지만 코틀린에서 try-with-resource는 지원하지 않는다. 라이브러리 함수로 같은 기능을 구현했다. 8장에서 ..

### try를 식으로 사용

```kotlin
fun readNumber(reader: BufferedReader) {
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        return
    }

    println(number)
}

fun main(args: Array<String>) {
    val reader = BufferedReader(StringReader("not a number"))
    readNumber(reader)
}

```

- try 역시 expression이기 때문에 try절 마지막 블록의 문장이 식이 된다.
- try 문은 반드시 블록으로 감싸야 한다.
- 이 예제에서는 catch 블록 안에서 return 문을 사용했다 따라서 예외가 발생한 경우 catch 블록 다음 코드는 실행되지 않는다. 계속 진행하고 싶다면 catch 블록도 값을 만들어야 한다.

```kotlin
fun readNumber(reader: BufferedReader) {
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        null
    }

    println(number)
}

fun main(args: Array<String>) {
    val reader = BufferedReader(StringReader("not a number"))
    readNumber(reader)
}

```



## 요약

- 함수를 정의할 때 fun 키워드를 사용한다. val과 var는 각각 읽기 전용 변수와 변경 가능한 변수를 선언할 때 쓰인다.
- 문자열 템플릿을 사용하면 문자열을 연결하지 않아도 되므로 코드가 간결해진다. 변수 이름 앞에 $을 붙이거나 식을 ${식} 처럼 ${}로 둘러싸면 변수나 식의 값을 문자열 안에 넣을 수 있다.
- 코틀린에서는 값 객체 클래스를 아주 간결하게 표현할 수 있다.
- 다른 언어에도 있는 if는 코틀린에서 식이며 값을 만들어낸다.
- 코틀린 when은 자바의 switch와 비슷하지만 더 강력하다.
- 어떤 변수의 타입을 검사하고 나면 굳이 그 변수를 캐스팅하지 않아도 검사한 타입의 변수처럼 사용할 수 있다. 그런 경우 컴파일러가 스마트 캐스트를 활용해 자동으로 타입을 바꿔준다.
- for, while, do-while 루프는 자바가 제공하는 같은 키워드의 기능과 비슷하다.
- 1..5와 같은 식은 범위를 만들어낸다. 범위와 수열은 코틀린에서 같은 문법을 사용하며 for루프에 대해 같은 추상화를 제공한다. 어떤 값이 범위 안에 들어 있거나 들어있지 않은지 검사하기 위해 in이나 !in을 사용한다.
- 코틀린 예외 처리는 자바와 비슷하다. 다만 코틀린에서는 함수가 던질 수 있는 예외를 선언하지 않아도 된다.





# #3 함수 정의와 호출

## 코틀린에서 컬렉션 만들기

```kotlin
val set = hashSetOf(1, 7, 53)
val list = arrayListOf(1, 7, 53)
val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")

fun main(args: Array<String>) {
    println(set.javaClass) // javaClass는 java의 getClass()에 해당하는 코틀린 코드다.
    println(list.javaClass)
    println(map.javaClass)
}

```

- 코틀린 컬렉션은 기존 자바의 컬렉션과 똑같은 클래스다. 하지만 더 많은 기능을 사용할 수 있다.

```kotlin
fun main(args: Array<String>) {
    val strings = listOf("first", "second", "fourteenth")
    println(strings.last())
    val numbers = setOf(1, 14, 2)
    println(numbers.max())
}

```



## 함수를 호출하기 쉽게 만들기

- 자바 컬렉션에는 디폴트 toString 구현이 들어있다.

```kotlin
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3)
    println(list)
}

// [1, 2, 3]
```

- 직접 함수를 구현해서 사용할 수 있다.

```kotlin
fun <T> joinToString(
        collection: Collection<T>,
        separator: String,
        prefix: String,
        postfix: String
): String {

    val result = StringBuilder(prefix)

    for ((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

fun main(args: Array<String>) {
    val list = listOf(1, 2, 3)
    println(joinToString(list, "; ", "(", ")"))
}

```

- 이 함수는 제네릭해서 어떤 컬렉션이든 처리할 수 있다.



### 이름 붙인 인자

- 코틀린으로 작성한 함수를 호출할 때는 함수에 전달하는 인자 중 일부의 이름을 명시할 수 있다.
- 호출인자의 이름을 하나라도 명시하면 반드시 모든 인자의 이름을 명시해야한다.



### 디폴트 파라미터 값

- 코틀린에서는 함수 선언에서 파라미터의 디폴트 값을 지정할 수 있으므로 이런 오버로드 중 상당수를 피할 수 있다.

```kotlin
fun <T> Collection<T>.joinToString(
        separator: String = ", ",
        prefix: String = "",
        postfix: String = ""
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

fun main(args: Array<String>) {
    val list = listOf(1, 2, 3)
    println(list.joinToString(separator = "; ",
          prefix = "(", postfix = ")"))
}

```

- 이제 함수를 호출할 때 모든 인자를 쓸 수도 있고 일부를 생략할 수도 있다.

### 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티

- 코틀린에서는 반드시 클래스 안에 함수를 작성할 필요가 없다.
- 대신 함수를 직접 소스 파일의 최상위 수준, 모든 다른 클래스의 밖에 위치시키면 된다.
- 그런 함수들은 여전히 패키지의 멤버 함수이므로 다른 패키지에서 사용하고 싶을때는 그 함수가 정의된 패키지를 임포트해야 한다.
- 이런 함수들은 컴파일 시점에 {파일명kt}라는 이름의 클래스 파일 안에 있는 정적 함수의 형태로 컴파일된다.

**최상위 프로퍼티**

- 함수와 마찬가지로 프로퍼티도 파일의 최상위 수준에 놓을 수 있다.
- 이런 프로퍼티는 정적 필드에 저장된다.
- 상수 타입의 경우 아래와 같이 const 키워드를 추가하면 자바에서 public static final 이 붙은 상수 타입으로 사용할 수 있다.

```kotlin
const val UNIX_LINE_SEPARATOR = "\n"
```





## 메서드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티

- 확장함수는 어떤 클래스의 멤버 메서드인 것처럼 호출할 수 있지만 그 클래스의 밖에 선언된 함수다.
- 확장 함수를 만들려면 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙이기만 하면 된다.
- 클래스의 이름을 수신 객체 타입이라 부르며 확장 함수가 호출되는 대상이 되는 값을 수신 객체라고 부른다.

```kotlin
fun String.lastChar(): Char = this.get(this.length - 1)

// String은 수신 객체 타입
// this는 수신 객체 

// 이 함수를 호출하는 구문은 다른 일반 클래스 멤버를 호출하는 구문과 똑같다.
println("Kotlin".lastChar())
```

- String 클래스가 직접 작성한 코드도 아니고 소스코드를 소유한 것도 아니지만 원하는 메서드를 String 클래스에 추가할 수 있다.
- 자바 클래스로 컴파일한 클래스 파일이 있는 한 그 클래스에 원하는 대로 확장을 추가할 수 있다.
- 일반 메서드의 본문에서 this를 사용할 떄와 마찬가지로 확장 함수 본문에도 this를 쓸 수 있다. 생략도 가능하다.
- 확장 함수 내부에서는 일반적인 인스턴스 메서드의 내부에서와 마찬가지로 수신 객체의 메서드나 프로퍼티를 바로 사용할 수 있다.
- 클래스 내부에서만 사용할 수 있는 private, protected 멤버는 사용할 수 없다.



### 임포트와 확장 함수

- 확장함수를 사용하기 위해서는 그 함수를 다른 클래스나 함수와 마찬가지로 임포트해야만 한다.
- 함수 이름이 충돌할 수도 있는데 그 땐 as를 사용해서 함수이름을 다른 이름으로 부를 수 있다.

```kotlin
import strings.lastChar as last
val c = "Kotlin".last()
```



### 자바에서 확장 함수 호출

- 내부적으로 확장 함수는 수신 객체를 첫 번째 인자로 받는 정적 메서드다.
- 확장 함수를 StringUtil.kt 파일에 정의했다면 자바에서 다음과 같이 호출할 수 있다.

```kotlin
char c = StringUtilKt.lastChar("Java")
```



### 확장 함수로 유틸리티 함수 정의



```kotlin
fun <T> Collection<T>.joinToString( // Collection<T>에 대해 확장
        separator: String = ", ",
        prefix: String = "",
        postfix: String = ""
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}


fun main(args: Array<String>) {
    val list = arrayListOf(1, 2, 3)
    println(list.joinToString(" "))
}

```

- 확장 함수는 단지 정적 메서드 호출에 대한 문법적인 편의이기 때문에 클래스가 아닌 더 구체적인 타입을 수신 객체 타입으로 지정할 수도 있다.

```kotlin
fun Collection<String>.join(
        separator: String = ", ",
        prefix: String = "",
        postfix: String = ""
) = joinToString(separator, prefix, postfix)
```



### 확장 함수는 오버라이드할 수 없다

- 확장 함수가 정적 메서드와 같은 특징을 가지므로 확장 함수를 하위 클래스에서 오버라이드할 수는 없다.
- 확장 함수는 클래스의 일부가 아니라 클래스 밖에 선언된다.

```kotlin
open class View {
    open fun click() = println("View clicked")
}

class Button: View() {
    override fun click() = println("Button clicked")
}

fun View.showOff() = println("I'm a view!")
fun Button.showOff() = println("I'm a button!")

fun main(args: Array<String>) {
    val view: View = Button()
    view.showOff()
}


// 인스턴스는 Button이지만 View 타입 변수이기 때문에
// I'm a view!가 호출된다.
```

- 어떤 클래스를 확장한 함수와 그 클래스의 멤버 함수의 이름과 시그니처가 같다면 확장함수가 아니라 멤버 함수가 호출된다. 



### 확장 프로퍼티

- 확장 프로퍼티를 사용하면 기존 클래스 객체에 대한 프로퍼티 형식의 구문으로 사용할 수 있는 API를 추가할 수 있다.
- 프로퍼티라고 불리지만 상태를 저장할 적절한 방법이 없기 때문에 아무 상태도 가질 수 없다. 하지만 프로퍼티 문법으로 더 짧게 코드를 작성할 수 있어서 편한 경우가 있다.

```kotlin
val String.lastChar: Char
    get() = get(length - 1)
var StringBuilder.lastChar: Char
    get() = get(length - 1)
    set(value: Char) {
        this.setCharAt(length - 1, value)
    }

fun main(args: Array<String>) {
    println("Kotlin".lastChar)
    val sb = StringBuilder("Kotlin?")
    sb.lastChar = '!'
    println(sb)
}

```



## 컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원

- vararg 키워드를 사용하면 호출 시 인자 개수가 달라질 수 있는 함수를 정의할 수 있다.
- 중위함수 호출 구문을 사용하면 인자가 하나뿐인 메서드를 간편하게 호출할 수 있다.
- 구조 분해 선언을 사용하면 복합적인 값을 분해해서 여러 변수에 나눠 담을 수 있다.

### 자바 컬렉션 API 확장

- 코틀린에서 자바 컬렉션을 그대로 사용하지만 추가적인 함수를 사용할 수 있는것도 확장 함수를 사용하는 것이다.

### 가변 인자 함수: 인자 개수가 달라질 수 있는 함수 정의

```kotlin
public fun <T> listOf(vararg elements: T): List<T> = if (elements.size > 0) elements.asList() else emptyList()
```

- 자바와 비슷한 가변 인자 함수는 ... 이 아니라 vararg 라는 키워드를 사용한다.
- 배열의 경우 자바는 그냥 던지면 되지만 코틀린에서는 배열을 명시적으로 풀어서 배열의 각 원소가 인자로 전달되게 해야 한다. 기술적으로는 스프레드 연산자가 그런 작업을 해주지만 실제로는 * 를 붙여서 사용한다.

```kotlin
fun main(args: Array<String>) {
    val list = listOf("args: ", *args)
    println(list)
}

```



### 값의 쌍 다루기: 중위 호출과 구조 분해 선언

```kotlin
val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```

- to는 코틀린 키워드가 아니라 중위 호출이라는 특별한 방식으로 to 라는 일반 메서드를 호출한 것이다.
- 중위 호출 시에는 수신 객체와 유일한 메서드 인자 사이에 메서드 이름을 넣는다.
- 인자가 하나뿐인 일반 메서드나 인자가 하나뿐인 확장 함수에 중위 호출을 사용할 수 있다.
- 함수를 중위 호출에 사용하게 허용하고싶으면 infix 변경자를 함수 선언 앞에 추가해야 한다.

```kotlin
public infix fun <A, B> A.to(that: B): Pair<A, B> = Pair(this, that)

val (number, name) = 1 to "one"
```

- 이런 기능을 구조 분해 선언이라고 부른다.
- Pair 뿐 아니라 다른 객체에도 구조 분해 선언을 활용할 수 있다.

```kotlin
for ((index, element) in collection.withIndex()) {
	println("$index: $element")
}
```



## 문자열과 정규식 다루기

- 코틀린의 문자열도 자바의 문자열과 같다.

### 문자열 나누기

- 자바에 split은 반드시 정규식으로 동작하지만 코틀린에서는 다양한 방식으로 사용할 수 있다.

```kotlin
fun main(args: Array<String>) {
    println("12.345-6.A".split("\\.|-".toRegex())) // 정규식을 명시적으로 만듦
}

fun main(args: Array<String>) {
    println("12.345-6.A".split(".", "-")) // 여러 구분 문자열을 지정
}

```



### 정규식과 3중 따옴표로 묶은 문자열

```kotlin
fun parsePath(path: String) {
    val directory = path.substringBeforeLast("/")
    val fullName = path.substringAfterLast("/")

    val fileName = fullName.substringBeforeLast(".")
    val extension = fullName.substringAfterLast(".")

    println("Dir: $directory, name: $fileName, ext: $extension")
}

fun main(args: Array<String>) {
    parsePath("/Users/yole/kotlin-book/chapter.adoc")
}

```

- 아래 방법으로 같은 기능을 만들어낼 수 있다.

```kotlin
fun parsePath(path: String) {
    val regex = """(.+)/(.+)\.(.+)""".toRegex()
    val matchResult = regex.matchEntire(path)
    if (matchResult != null) {
        val (directory, filename, extension) = matchResult.destructured
        println("Dir: $directory, name: $filename, ext: $extension")
    }
}

fun main(args: Array<String>) {
    parsePath("/Users/yole/kotlin-book/chapter.adoc")
}

```

- 3중 따옴표 문자열에는 역슬래시를 포함한 어떤 문자도 이스케이프할 필요가 없다.

### 여러 줄 3중 따옴표 문자열

- 3중 따옴표 문자열을 이스케이프에서만 사용하지 않고 아래와 같이 줄바꿈을 표현할때도 사용한다.

```kotlin
val kotlinLogo = """| //
                   .|//
                   .|/ \"""

fun main(args: Array<String>) {
    println(kotlinLogo.trimMargin("."))
}


//result
| //
|//
|/ \

```



## 코드 다듬기: 로컬 함수와 확장

- 코틀린에서는 함수에서 추출한 함수를 원 함수 내부에 중첩시킬 수 있다.

```kotlin
class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {
    if (user.name.isEmpty()) {
        throw IllegalArgumentException(
            "Can't save user ${user.id}: empty Name")
    }

    if (user.address.isEmpty()) {
        throw IllegalArgumentException(
            "Can't save user ${user.id}: empty Address")
    }

    // Save user to the database
}

fun main(args: Array<String>) {
    saveUser(User(1, "", ""))
}

```

- 검증이 늘어날 경우 중복이 생기게된다. 이 경우 로컬 함수로 분리하면 중복을 없애는 동시에 코드 구조를 깔끔하게 유지할 수 있다.

```kotlin

class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {

    fun validate(user: User,
                 value: String,
                 fieldName: String) {
        if (value.isEmpty()) {
            throw IllegalArgumentException(
                "Can't save user ${user.id}: empty $fieldName")
        }
    }

    validate(user, user.name, "Name")
    validate(user, user.address, "Address")

    // Save user to the database
}

fun main(args: Array<String>) {
    saveUser(User(1, "", ""))
}

```

- 로컬 함수는 자신이 속한 바깥 함수의 모든 파라미터와 변수를 사용할 수 있어서 다음과 같이 개선할 수 있다.

```kotlin

class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {
    fun validate(value: String, fieldName: String) {
        if (value.isEmpty()) {
            throw IllegalArgumentException(
                "Can't save user ${user.id}: " +
                    "empty $fieldName")
        }
    }

    validate(user.name, "Name")
    validate(user.address, "Address")

    // Save user to the database
}

fun main(args: Array<String>) {
    saveUser(User(1, "", ""))
}

```

- 좀 더 개선하고 싶다면 이 함수를 확장 함수로 만들 수도 있다. 

```kotlin

class User(val id: Int, val name: String, val address: String)

fun User.validateBeforeSave() {
    fun validate(value: String, fieldName: String) {
        if (value.isEmpty()) {
            throw IllegalArgumentException(
               "Can't save user $id: empty $fieldName")
        }
    }

    validate(name, "Name")
    validate(address, "Address")
}

fun saveUser(user: User) {
    user.validateBeforeSave()

    // Save user to the database
}

fun main(args: Array<String>) {
    saveUser(User(1, "", ""))
}

```

- 중첩함수의 깊이가 깊어지면 코드를 읽기가 상당히 어려워지기 때문에 일반적으로는 한 단계만 중첩시키는 것을 권장한다.

## 요약

- 코틀린은 자체 컬렉션 클래스를 정의하지 않지만 자바 클래스를 확장해서 더 풍부한  API를 제공한다.
- 함수 파라미터의 디폴트 값을 정의하면 오버로딩한 함수를 정의할 필요성이 줄어든다. 이름 붙인 인자를 사용하면 함수의 인자가 많을 때 함수 호출의 가독성을 더 향상시킬 수 있다.
- 코틀린 파일에서 클래스 멤버가 아닌 최상위 함수와 프로퍼티를 직접 선언할 수 있다. 이를 활용하면 코드 구조를 더 유연하게 만들 수 있다.
- 확장 함수와 프로퍼티를 사용하면 외부 라이브러리에 정의된 클래스를 포함해 모든 클래스의  API를 그 클래스의 소스코드를 바꿀 필요 없이 확장할 수 있다. 확장 함수를 사용해도 실행 시점에 부가 비용이 들지 않는다. 
- 중위 호출을 통해 인자가 하나 밖에 없는 메서드나 확장 함수를 더 깔끔한 구문으로 호출할 수 있다.
- 코틀린은 정규식과 일반 문자열을 처리할 때 유용한 다양한 문자열 처리 함수를 제공한다. 
- 자바 문자열로 표현하려면 수많은 이스케이프가 필요한 문자열의 경우 3중 따옴표 문자열을 사용하면 더 깔끔하게 표현할 수 있다.



# #4 클래스, 객체, 인터페이스

## 클래스 계층 정의

### 코틀린 인터페이스

- 코틀린 인터페이스는 자바 8 인터페이스와 비슷하다.
- 인터페이스에는 아무런 상태도 들어갈 수 없다.

```kotlin
interface Clickable {
    fun click()
}
class Button : Clickable {
    override fun click() = println("I was clicked")
}

fun main(args: Array<String>) {
    Button().click()
}

```

- 자바에서는 extends, implements를 사용하지만 코틀린에서는 클래스 이름 뒤에 : 콜론을 붙이고 인터페이스와 클래스 이름을 적는 형태로 구현과 확장을 모두 처리한다.
- 자바와 마찬가지로 인터페이스는 n개, 호가장은 1개만 가능하다.
- 코틀린에서는 override 키워드를 반드시 사용해야 한다.
- 인터페이스에 메서드도 디폴트 구현을 제공할 수 있다.

```kotlin
interface Clickable {
    fun click()
    fun showOff() = println("I'm clickable!")
}

```

- click은 반드시 구현해야하지만 showOff는 optional이다.
- 아래와 같이 다른 인터페이스에 같은 default 시그니처 함수가 있다면 이 두 개를 동시에 구현하는 클래스는 컴파일에러가 발생한다.

```kotlin
interface Focusable {
    fun setFocus(b: Boolean) =
        println("I ${if (b) "got" else "lost"} focus.")

    fun showOff() = println("I'm focusable!")
}

```

- 따라서 이 경우 직접 구현해야한다.

```kotlin
class Button : Clickable, Focusable {
    override fun click() = println("I was clicked")

    override fun showOff() { // 상위 타입의 이름을 꺽쇠 괄호 사이에 넣어서 "super"를 지정하면 어떤 상위 타입의 멤버 메서드를 호출할지 지정할 수 있다.
        super<Clickable>.showOff() 
        super<Focusable>.showOff()
    }
}
```

### open, final, abstarct 변경자: 기본적으로 final

- 취약한 기반 클래스라는 문제는 하위 클래스가 기반 클래스에 대해 가졌던 가정이 기반 클래스를 변경함으로써 깨져버린 경우에 생긴다.
- 어떤 클래스가 자신을 상속하는 방법에 대해 정확한 규칙을 제공하지 않는다면 그 클래스의 클라이언트는 기반 클래스를 작성한 사람의 의도와 다른 방식으로 메서드를 오버라이드할 위험이 있다.
- 이 문제를 해결하기 위해 조슈아 블로크는 "상속을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 상속을 금지하라" 라는 조언을 한다.
- 코틀린도 마찬가지의 철학을 따른다. 코틀린의 클래스와 메서드는 기본적으로 final이다.
- 어떤 클래스의 상속을 허용하려면 클래스 앞에 open 변경자를 붙여야 한다. 메서드와 프로퍼티도 마찬가지다.

```kotlin
interface Clickable {
    fun click()
    fun showOff() = println("I'm clickable!")
}

open class RichButton : Clickable { // 이 클래스는 열려 있다.

    fun disable() {} // 이 함수는 파이널이다. 하위 클래스가 이 메서드를 오버라이드 할 수 없다.

    open fun animate() {} // 이 함수는 열려 있다.

    override fun click() {} // 이 함수는 열려 있는 메서드를 오버라이드 한다. 오버라이드한 메서드는 기본적으로 열려 있다.
}

```

- override 함수를 닫으려면 아래와 같이 final을 붙여야한다.

```kotlin
interface Clickable {
    fun click()
    fun showOff() = println("I'm clickable!")
}

open class RichButton : Clickable {
    final override fun click() {}
}

```

- 자바처럼 코틀린에서도 클래스를 abstract로 선언할 수 있다. abstract로 선언한 추상 클래스는 인스턴스화 할 수 없다.
- 추상 멤버는 항상 열려있어서 open 변경자를 명시할 필요가 없다.

```kotlin
abstarct class Animated {
	abstract fun animate() // 반드시 구현해야 한다.
  
  open fun stopAnimating() {} // 비추상함수는 기본적으로 final이라 open으로 열어줄 수 있다.
  
  fun animateTwice() {}
}
```

- 클래스 내에서 상속 제어 변경자의 의미

| 변경자   | 이 변경자가 붙은 멤버는...                              | 설명                                                         |
| -------- | ------------------------------------------------------- | ------------------------------------------------------------ |
| final    | 오버라이드할 수 없음                                    | 클래스 멤버의 기본 변경자다.                                 |
| open     | 오버라이드할 수 있음                                    | 반드시 open을 명시해야 오버라이드할 수 있다.                 |
| abstarct | 반드시 오버라이드 해야 함                               | 추상 클래스의 멤버에만 이 변경자를 붙일 수 있다.             |
| override | 상위 클래스나 상위 인스턴스의 멤버를 오버라이드 하는 중 | 오버라이드하는 멤버는 기본적으로 열려있다. 하위 클래스의 오버라이드를 금지하려면 final을 명시해야 한다. |



### 가시성 변경자: 기본적으로 공개

- 가시성 변경자는 코드 기반에 있는 선언에 대한 클래스 외부 접근을 제어한다.
- 자바와 같이 public, protected, private 변경자가 있지만 package-default는 코틀린에 없다.
- 코틀린에서 기본 가시성은 public이다.

### 내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스

### 봉인된 클래스: 클래스 계층 정의 시 계층 확장 제한

## 뻔하지 않은 생성자와 프로퍼티를 갖는 선언

### 클래스 초기화: 주 생성자와 초기화 블록

### 부 생성자: 상위 클래스를 다른 방식으로 초기화

### 인터페이스에 선언된 프로퍼티 구현

### 게터와 세터에서 뒷받침하는 필드에 접근

### 접근자의 가시성 변경

## 컴파일러가 생성한 메서드: 데이터 클래스와 클래스 위임

### 모든 클래스가 정의해야 하는 메서드

### 데이터 클래스: 모든 클래스가 정의해야 하는 메서드 자동 생성

### 클래스 위임: by 키워드 사용

## object 키워드: 클래스 선언과 인스턴스 생성

### 객체 선언: 싱글턴을 쉽게 만들기

### 동반 객체: 팩토리 메서드와 정적 멤버가 들어갈 장소

### 동반 객체를 일반 객체처럼 사용

### 객체 식: 무명 내부 클래스를 다른 방식으로 작성

## 요약

