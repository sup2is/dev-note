

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
- 자바와 같이 public, protected, private 변경자가 있지만 package-default는 코틀린에 없다. 대신 internal이라는 새로운 가시성 변경자를 도입했다.
- internal은 모듈 내부에서만 볼 수 있다는 의미를 갖는다. 
- 코틀린에서 기본 가시성은 public이다.

| 변경자    | 클래스 멤버                      | 최상위 선언                    |
| --------- | -------------------------------- | ------------------------------ |
| public    | 모든 곳에서 볼 수 있다.          | 모든 곳에서 볼 수 있다.        |
| internal  | 같은 모듈 안에서만 볼 수 있다.   | 같은 모듈 안에서만 볼 수 있다. |
| protected | 하위 클래스 안에서만 볼 수 있다. | (최상위 선언에 적용할 수 없음) |
| private   | 같은 클래스 안에서만 볼 수 있다. | 같은 파일 안에서만 볼 수 있다. |

- 어떤 클래스의 기반 타입 목록에 들어있는 타입이나 제네릭 클래스의 타입 파라미터에 들어잇는 타입의 가시성은 그 클래스 자신의 가시성과 같거나 더 높아야 하고, 메서드의 시그니처에 사용된 모든 타입의 가시성은 그 메서드의 가시성과 같거나 더 높아야 한다는 더 일반적인 규칙에 해당한다.
- 코틀린에서 protected는 오직 어떤 클래스나 그 클래스를 상속한 클래스 안에서만 보인다.
- 또 다른 차이는 코틀린에서 외부 클래스가 내부 클래스, 중첩된 클래스의 private 멤버에 접근할 수 없다.

### 내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스

- 코틀린에도 클래스 안에 다른 클래스를 선언할 수 있다.
- 코틀린의 중첩 클래스는 명시적으로 요청하지 않는 한 바깥쪽 클래스 인스턴스에 접근 권한이 없다.
- 코틀린의 중첩 클래스에 아무런 변경자가 붙지 않으면 기본적으로 자바 static 중첩 클래스와 같다.
- 이를 내부 클래스로 변경해서 바깥쪽 클래스에 대한 참조를 포함하게 만들고 싶으면 inner 키워드를 붙여야 한다.
- 내부 클래스에 Inner에서 바깥쪽 클래스 Outer의 참조에 접근하려면 this@Outer라고 써야 한다.

```kotlin
class Outer {
    inner class Inner {
        fun getOuterReference(): Outer = this@Outer
    }
}

```

- 코틀린에서 클래스 계층을 만들되 그 계층에 속한 클래스의 수를 제한하고 싶은 경우 중첩 클래스를 쓰면 편리하다.

### 봉인된 클래스: 클래스 계층 정의 시 계층 확장 제한

```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
        else ->
            throw IllegalArgumentException("Unknown expression")
    }

fun main(args: Array<String>) {
    println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
}

```

- 코틀린 컴파일러는 when을 사용해 Expr 타입의 값을 검사할 때 꼭 디폴트 분기인 else 분기를 덧붙이게 강제한다.
- 항상 디폴트 분기를 추가하는게 편하지 않기 때문에 코틀린은 이런 문제에 대한 해법을 제공한다. sealed 클래스가 그 답이다.
- 상위 클래스에 sealed 변경자를 붙이면 그 상위 클래스를 상속한 하위클래스 정의를 제한할 수 있다.
- sealed 클래스 하위 클래스를 정의할 때는 반드시 상위 클래스 안에 중첩시켜야 한다.

```kotlin
sealed class Expr {
    class Num(val value: Int) : Expr()
    class Sum(val left: Expr, val right: Expr) : Expr()
}

fun eval(e: Expr): Int =
    when (e) {
        is Expr.Num -> e.value
        is Expr.Sum -> eval(e.right) + eval(e.left)
    }

fun main(args: Array<String>) {
    println(eval(Expr.Sum(Expr.Sum(Expr.Num(1), Expr.Num(2)), Expr.Num(4))))
}

```

- when식에서 sealed 클래스의 모든 하위 클래스를 처리한다면 디폴트 분기가 필요 없다.
- sealed 클래스는 자동으로 open이다.
- sealed 클래스에 새로운 하위 클래스가 추가되면 when절에서 컴파일에러가 발생해 쉽게 알아챌 수 있다.
- 코틀린 1.5부터 sealed 인터페이스가 추가됐다. 참고

## 뻔하지 않은 생성자와 프로퍼티를 갖는 선언

- 코틀린은 주 생성자와 부 생성자를 구분한다.
- 코틀린은 초기화블록을 통해 초기화로직을 추가할 수 있다.

### 클래스 초기화: 주 생성자와 초기화 블록

```kotlin
class User(val nickname: String,
           val isSubscribed: Boolean = true)
```

- 위와 같이 클래스 선언에 중괄호가 없고 괄호사이 val만 존재한다면 이는 주 생성자라고 부른다. 디폴트 값도 사용 가능하다.
- 주 생성자는 생성자 파라미터를 지정하고 그 생성자 파라미터에 의해 초기화되는 프로퍼티를 정의하는 두가지 목적에 쓰인다. 

```kotlin
class User constructor(_nickname: String) {
  val nickname: String
  init {
    nickname = _nickname
  }
}
```

- constructor는 주 생성자나 부 생성자 정의를 시작할 때 사용한다.
- init 키워드는 초기화 블록을 시작한다.
- 초기화 블록에는 클래스가 만들어질 때 실행될 초기화 코드가 들어간다.
- 주 생성자는 제한적이라 별도의 코드를 포함할 수 없으므로 초기화 블록을 사용한다. 여러개의 초기화 블록도 선언할 수 있다.
- 클래스의 인스턴스를 만들려면 new 키워드 없이 생성자를 직접 호출하면 된다.

```kotlin
val hyun = User("현석")
```

- 클래스에 기반 클래스가 있다면 주 생성자에서 기반 클래스의 생성자를 호출해야 할 필요가 있다.
- 기반 클래스를 초기화하려면 기반 클래스 이름 뒤에 괄호를 치고 생성자 인자를 넘긴다.

```kotlin
open class User(val nickname: String) {...}
class TwitterUser(nickname: String) : User(nickname) {...}
```

- 클래스를 정의할 때 별도로 생성자를 정의하지 않으면 컴파일러가 아무 일도 하지 않는 인자가 없는 디폴트 생성자를 만들어준다.
- 하위 클래스라면 반드시 상위클래스의 생성자를 호출해야 하기 때문에 반드시 괄호가 들어간다.

```kotlin
class RadioButton: Button()
```

- 반면 인터페이스는 생성자가 없기 때문에 괄호가 없다.
- 어떤 클래스를 클래스 외부에서 인스턴스화하지 못하게 막고 싶다면 모든 생성자를 private으로 만들면 된다.

```kotlin
class Secretive private constructor() {}
```

- 대부분의 경우 간단한 생성자로 끝난다.

### 부 생성자: 상위 클래스를 다른 방식으로 초기화

- 일반적으로 코틀린에서는 생성자가 여럿 있는 경우가 자바보다 훨씬 적다.
- 생성자 오버로딩도 가능하면 디폴트 파라미터로 해결할 수 있다.
- 하지만 부 생성자도 만들 수 있다 필요에 따라 얼마든지 부 생성자를 많이 선언해도 된다.

```kotlin
class MyButton : View {
	constructor(ctx: Context)
  	: super(ctx) {
      //..
  }
  
  constructor(ctx: Context, attr: AttributeSet)
  	: super(ctx, attr) {
      //..
  }
}
```

- 자바와 마찬가지로 this를 통해 자신의 다른 생성자를 호출할 수도 있다.

```kotlin
class MyButton : View {
	constructor(ctx: Context) : this(ctx, MY_STYLE) {
      //..
  }
```

- 클래스에 주 생성자가 없다면 모든 부 생성자는 반드시 상위 클래스를 초기화하거나 다른 생성자에게 생성을 위임해야 한다.

### 인터페이스에 선언된 프로퍼티 구현

- 코틀린에서는 인터페이스에 추상 프로퍼티 선언을 넣을 수 있다.

```kotlin
interface User {
	val nickname: String
}
```

- 이는 User 인터페이스를 구현하는 클래스가 nickname의 값을 얻을 수 있는 방법을 제공해야 한다는 뜻이다.

```kotlin
fun getFacebookName(accountId: Int) = "fb:$accountId"

interface User {
    val nickname: String
}
class PrivateUser(override val nickname: String) : User

class SubscribingUser(val email: String) : User {
    override val nickname: String
        get() = email.substringBefore('@')
}

class FacebookUser(val accountId: Int) : User {
    override val nickname = getFacebookName(accountId)
}

fun main(args: Array<String>) {
    println(PrivateUser("test@kotlinlang.org").nickname)
    println(SubscribingUser("test@kotlinlang.org").nickname)
}

```

- PrivateUser는 주 생성자 안에서 프로퍼티를 직접 선언하는 간결한 구문을 사용한다.
- SubscribingUser는 커스텀 게터로 nickname 프로퍼티를 설정한다.
- FacebookUser는 초기화식으로 nickname 프로퍼티를 설정한다.
- SubscribingUser은 호출될 때마다 계산하고 FacebookUser은 생성될 때 한번만 호출하는 방식이다.
- 인터페이스에는 추상 프로퍼티뿐 아니라 게터와 세터가 있는 프로퍼티를 선언할 수 있다. 

```kotlin
interface User {
    val email: String
    val nickname: String
        get() = email.substringBefore('@')
}

```



### 게터와 세터에서 뒷받침하는 필드에 접근

```kotlin
class User(val name: String) {
    var address: String = "unspecified"
        set(value: String) {
            println("""
                Address was changed for $name:
                "$field" -> "$value".""".trimIndent())
            field = value
        }
}

fun main(args: Array<String>) {
    val user = User("Alice")
    user.address = "Elsenheimerstrasse 47, 80687 Muenchen"
}

```

- 코틀린에서는 프로퍼티의 값을 바꿀 때 user.address = "new value" 처럼 필드 설정 구문을 사용ㅎㄴ다.

- 접근자의 본문에서는 field라는 특별한 식별자를 통해 뒷받침하는 필드에 접근할 수 있다.

- 게터에서는 field의 값을 읽을 수만 있고 세터에서는 읽거나 쓸 수 있다.

  

### 접근자의 가시성 변경

- 접근자의 가시성은 기본적으로 프로퍼티의 가시성과 같다.
- 원한다면 변경할 수 있다.

```kotlin
class LengthCounter {
    var counter: Int = 0
        private set

    fun addWord(word: String) {
        counter += word.length
    }
}

fun main(args: Array<String>) {
    val lengthCounter = LengthCounter()
    lengthCounter.addWord("Hi!")
    println(lengthCounter.counter)
}
```



## 컴파일러가 생성한 메서드: 데이터 클래스와 클래스 위임

- 코틀린은 equals, hashcode, toString을 기계적으로 생성하는 작업을 보이지 않는 곳에서 해준다. 따라서 소스코드를 깔끔하게 유지할 수 있다.

### 모든 클래스가 정의해야 하는 메서드

**문자열 표현 : toString()**

```kotlin
class Client(val name: String, val postalCode: Int) {
    override fun toString() = "Client(name=$name, postalCode=$postalCode)"
}

fun main(args: Array<String>) {
    val client1 = Client("Alice", 342562)
    println(client1)
}

```

- 이런식으로 toString을 구현할 수 있다.

**객체의 동등성: equals()**

```kotlin
class Client(val name: String, val postalCode: Int)

fun main(args: Array<String>) {
    val client1 = Client("Alice", 342562)
    val client2 = Client("Alice", 342562)
    println(client1 == client2)
}
```

- 코틀린에서 ==는 참조 동일성을 검사하지 않고 객체의 동등성을 검사한다. == 은 equals다. 참조 비교를 하고 싶다면 ===를 쓰면 된다.
- 위 결과는 false다. eqauls를 재정의해야 한다.

```kotlin
class Client(val name: String, val postalCode: Int) {
    override fun equals(other: Any?): Boolean {
        if (other == null || other !is Client)
            return false
        return name == other.name &&
               postalCode == other.postalCode
    }
    override fun toString() = "Client(name=$name, postalCode=$postalCode)"
}

fun main(args: Array<String>) {
    val processed = hashSetOf(Client("Alice", 342562))
    println(processed.contains(Client("Alice", 342562)))
}

```

- Any 는 java.lang.Object를 대응하는 클래스로 코틀린의 모든 클래스의 최상위 클래스다. Any?는 널이 될 수 있는 타입이므로 other는 null일 수 있다.

**해시 컨테이너 : hasCode()**

- HashSet은 원소를 비교할 때 비용을 줄이기 위해 먼저 객체의해시 코드를 비교하고 해시 코드가 같은 경우에만 실제 값을 비교한다.
- 원소들이 해시 코드에 대한 규칙을 지키지 않는 경우 HashSet은 제대로 작동할 수 없다.
- 이 문제를 해결하려면 hashCode를 재정의해야 한다.

```kotlin
override fun hashCode(): Int =
      firstName.hashCode() * 37 + lastName.hashCode()
```



### 데이터 클래스: 모든 클래스가 정의해야 하는 메서드 자동 생성

- 어떤 클래스가 데이터를 저장하는 역할만을 수행한다면 toString, eqauls, hashCode를 반드시 오버라이드해야 한다.
- 코틀린은 data라는 변경자를 클래스 앞에 붙이면 컴파일러가 자동으로 만들어준다.
- data가 붙은 클래스를 데이터 클래스라고 한다.

```kotlin
data class Client(val name: String, val postalCode: Int)

fun main(args: Array<String>) {
    val bob = Client("Bob", 973293)
    println(bob.copy(postalCode = 382555))
}
```

- eqauls와 hashCode는 주 생성자에 나열된 모든 프로퍼티를 고려해 만들어진다.
- 주 생성자 밖에 나열된 프로퍼티는 eqauls와 hashCode의 대상이 아니다.
- 그 외에도 몇가지 유용한 메서드를 더해준다.

**데이터 클래스와 불변성: copy() 메서드**

- 데이터 클래스의 프로퍼티가 반드시 val일 필요는 없지만 권장사항이다.
- 데이터 클래스 인스턴스를 불변 객체로 더 쉽게 활용할 수 있게 코틀린 컴파일러는 copy() 메서드를 제공해준다.
- 객체를 메모리상에서 직접 바꾸는 대신 복사본을 만드는 편이 더 낫다.

### 클래스 위임: by 키워드 사용

- 구현 상속에 의해 많은 문제가 발생해서 코틀린은 기본적으로 클래스가 final이다.
- 종종 상속을 허용하지 않는 클래스에 새로운 동작을 추가해야할 때 일반적으로 데코레이터 패턴을 사용하지만 데코레이터 패턴을 준비하는 코드는 너무 많다.
- 인터페이스를 구현할 때 by 키워드를 통해 그 인터페이스에 대한 구현을 다른 객체에 위임중이라는 사실을 명시할 수 있다.

```kotlin
class CountingSet<T>(
        val innerSet: MutableCollection<T> = HashSet<T>()
) : MutableCollection<T> by innerSet {

    var objectsAdded = 0

    override fun add(element: T): Boolean {
        objectsAdded++
        return innerSet.add(element)
    }

    override fun addAll(c: Collection<T>): Boolean {
        objectsAdded += c.size
        return innerSet.addAll(c)
    }
}

fun main(args: Array<String>) {
    val cset = CountingSet<Int>()
    cset.addAll(listOf(1, 1, 2))
    println("${cset.objectsAdded} objects were added, ${cset.size} remain")
}
```

- size() 메서드를 위임하지 않아도 컴파일러가 자동으로 위임한 메서드를 만들어준다.
- 새롭게 정의하고싶은 메서드는 새롭게 정의하면 된다.

## object 키워드: 클래스 선언과 인스턴스 생성

- 코틀린에서 object 키워드를 다양한 상황에서 사용하지만 모든 경우 클래스를 정의하면서 동시에 인스턴스를 생성한다는 공통점이 있다.
- object를 사용하는 상황
  - 객체 선언은 싱글턴을 정의 하는 방법중 하나다.
  - 동반 객체는 인스턴스 메서드는 아니지만 어떤 클래스와 관련있는 메서드와 팩토리 메서드를 담을 때 쓰인다. 동반 객체 메서드에 접근할 때는 동반 객체가 포함된 클래스의 이름을 사용할 수 있다.
  - 객체 식은 자바의 무명 내부 클래스 대신 쓰인다.



### 객체 선언: 싱글턴을 쉽게 만들기

- 코틀린은 객체 선언 기능을 통해 싱글턴을 언어에서 기본 지원한다.
- 객체 선언은 클래스 선언과 그 클래스에 속한 단일 인스턴스의 선언을 합친 선언이다.
- 클래스와 마찬가지로 객체 선언 안에 프로퍼티, 메서드, 초기화블록을 만들 수 있지만 생성자는 객체 선언에 쓸 수 없다.
- 객체 선언도 클래스나 인터페이스를 상속할 수 있다.

```kotlin
object CaseInsensitiveFileComparator : Comparator<File> {
    override fun compare(file1: File, file2: File): Int {
        return file1.path.compareTo(file2.path,
                ignoreCase = true)
    }
}

fun main(args: Array<String>) {
    println(CaseInsensitiveFileComparator.compare(
        File("/User"), File("/user")))
    val files = listOf(File("/Z"), File("/a"))
    println(files.sortedWith(CaseInsensitiveFileComparator))
}
```

- 클래스 안에서 객체를 선언할 수도 있다. 그런 객체도 인스턴스는 단 하나뿐이다. 바깥 클래스의 인스턴스가 여러개가 되어도 한개다.

```kotlin
data class Person(val name: String) {
    object NameComparator : Comparator<Person> {
        override fun compare(p1: Person, p2: Person): Int =
            p1.name.compareTo(p2.name)
    }
}

fun main(args: Array<String>) {
    val persons = listOf(Person("Bob"), Person("Alice"))
    println(persons.sortedWith(Person.NameComparator))
}
```



### 동반 객체: 팩토리 메서드와 정적 멤버가 들어갈 장소

- 코틀린 클래스 안에는 정적인 멤버가 없다. 코틀린은 static을 지원하지 않는다.
- 그 대신 코틀린에서는 패키지 수준의 최상위 함수와 객체선언을 홀용한다.
- 대부분의 경우 최상위 함수를 활용하는 편을 더 권장한다. 하지만 최상위 함수는 private으로 표시된 클래스 비공개 멤버에 접근할 수 없다.
- 클래스 안에 정의된 객체 중 하나에 companion 이라는 특별한 표시를 붙이면 그 클래스의 동반 객체로 만들 수 있다.
- 이 때 객체의 이름을 따로 지정할 필요가 없어서 동반 객체의 멤버를 사용하는 구문은 자바의 정적 메서드 호출이나 정적 필드 사용 구문과 같아진다.

```kotlin
class A {
    companion object {
        fun bar() {
            println("Companion object called")
        }
    }
}

fun main(args: Array<String>) {
    A.bar()
}
```

- 동반 객체는 private 생성자를 호출하기 좋은 위치다.
- 동반 객체는 자신을 둘러싼 클래스의 모든 private 멤버에 접근할 수 있다. 따라서 동반 객체는 바깥쪽 클래스의  private 생성자도 호출할 수 있다.

```kotlin
fun getFacebookName(accountId: Int) = "fb:$accountId"

class User private constructor(val nickname: String) {
    companion object {
        fun newSubscribingUser(email: String) =
            User(email.substringBefore('@'))

        fun newFacebookUser(accountId: Int) =
            User(getFacebookName(accountId))
    }
}

fun main(args: Array<String>) {
    val subscribingUser = User.newSubscribingUser("bob@gmail.com")
    val facebookUser = User.newFacebookUser(4)
    println(subscribingUser.nickname)
}
```

- 팩토리 스타일로 사용할 수 있다.

### 동반 객체를 일반 객체처럼 사용

- 동반 객체는 클래스 안에 정의된 일반 객체다.
- 동반 객체가 인터페이스를 상속하거나 동반 객체 안에 확장 함수와 프로퍼티를 정의할 수 있다.
- 대부분의 경우 클래스 이름을 통해 동반 객체에 속한 멤버를 참조할 수 있으므로 객체 이름을 짓느라 고심할 필요가 없다. 하지만 필요하다면 변경 가능하다.



**동반 객체에서 인터페이스 구현**

- 동반객체도 인터페이스를 구현할 수 있다.

**동반 객체 확장**

- 동반객체에도 확장 함수를 적용할 수 있다.

### 객체 식: 무명 내부 클래스를 다른 방식으로 작성

- 무명 객체를 정의할 때도 object 키워드를 쓴다. 무명 객체는 자바의 무명 내부 클래스를 대신한다.
- 코틀린의 무명 클래스는 여러 인터페이스를 구현하거나 클래스를 확장하면서 인터페이스를 구현할 수 있다.
- 자바의 무명 클래스와 같이 객체 식 안의 코드는 그 식이 포함된 함수의 변수에 접근할 수 있다. 하지만 자바와 달리 final이아닌 변수도 객체 식 안에서 사용할 수 있다.

## 요약

- 코틀린의 인터페이스는 자바 인터페이스와 비슷하지만 디폴트 구현을 포함할 수 있고 프로퍼티도 포함할 수 있다.
- 모든 코틀린 선언은 기본적으로 final이며  public이다.
- 선언이 final이 되지 않게 만들려면 앞에 open을 붙여야 한다.
- internal 선언은 같은 모듈 안에서만 볼 수 있다.
- 중첩 클래스는 기본적으로 내부 클래스가 아니다. 바깥쪽 클래스에 대한 참조를 중첩 클래스 안에 포함시키려면 inner 키워드를 중첩 클래스 선언 앞에 붙여서 내부 클래스로 만들어야 한다.
- sealed 클래스를 상속하는 클래스를 정의하려면 반드시 부모 클래스 정의 안에 중첩 클래스로 정의해야 한다.
- 초기화블록과 부 생정자를 활용해 클래스 인스턴스를 더 유연하고 초기화할 수 있다.
- field 식별자를 통해 프로퍼티 접근자안에서 프로퍼티의 데이터를 저장하는 데 쓰이는 뒷받침하는 필드를 참조할 수 있다.
- 데이터 클래스를 사용하면 컴파일러가 eqauls, hashCode, toString, copy 등의 메서드를 자동으로 생성해준다.
- 클래스 위임을 사용하면 위임 패턴을 구현할 때 필요한 수많은 성가신 준비 코드를 줄일 수 있다.
- 객체 선언을 사용하면 코틀린답게 싱글턴 클래스를 정의할 수 있다.
- 동반 객체는 자바의 정적메서드와 필드 정의를 대신한다.
- 동반 객체도 다른 객체와 마찬가지로 인터페이스를 구현할 수 있다. 외부에서 동반 객체에 대한 확장 함수와 프로퍼티를 정의할 수 있다.
- 코틀린의 객체 식은 자바의 무명 내부 클래스를 대신한다. 하지만 코틀린 객체 식은 여러 인스턴스를 구현하거나 객체가 포함된 영역에 있는 변수의 값을 변경할 수 있는 등 자바 무명 내부 클래스보다 더 많은 기능을 제공한다.





# #5 람다로 프로그래밍

## 람다 식과 멤버 참조

### 람다 소개: 코드 블록을 함수 인자로 넘기기

- 함수형 언어에서는 함수를 값처럼 다루고 함수를 직접 다른 함수에 전달할 수 있다.
- 코틀린에서도 자바 8과 마찬가지로 람다를 쓸 수 있다.

### 람다와 컬렉션

```kotlin
data class Person(val name: String, val age: Int)

fun findTheOldest(people: List<Person>) {
    var maxAge = 0
    var theOldest: Person? = null
    for (person in people) {
        if (person.age > maxAge) {
            maxAge = person.age
            theOldest = person
        }
    }
    println(theOldest)
}

fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 29), Person("Bob", 31))
    findTheOldest(people)
}
```

- 이 코드는 복잡하다. 아래와 같이 maxBy를 사용하면 가장 큰 원소를 쉽게 찾을 수 있다.

```kotlin
    val people = listOf(Person("Alice", 29), Person("Bob", 31))
    println(people.maxBy{it.age})
```

- 모든 컬렉션에 maxBy를 사용할 수 있다.

### 람다 식의 문법

- 람다는 값처럼 여기저기 전달할 수 있는 동작의 모음이다.
- 코틀린 람다 식은 항상 중괄호로 둘러싸여 있다.
- 화살표가 인자 목록과 람다 본문을 구분해준다.

```kotlin
fun main(args: Array<String>) {
    val sum = { x: Int, y: Int -> x + y }
    println(sum(1, 2))
}

```

- 람다식을 변수에 저장할 수도 있다.
- 코드 일부분을 블록으로 둘러싸 실행할 필요가 있다면 run을 사용한다. run은 인자로 받은 람다를 실행해 주는 라이브러리 함수다.

```kotlin
fun main(args: Array<String>) {
    run { println(42) }
}

```

- 코틀린에는 함수 호출 시 맨 뒤에 있는 인자가 람다라면 그 람다를 괄호 밖을 빼낼 수 있는 문법 관습이 있다.

```kotlin
people.maxBy({p: Person -> p.age})
people.maxBy() {p: Person -> p.age}
```

- 람다가 함수의 유일한 인자라면 왼쪽 괄호도 빼도 된다.

```kotlin
people.maxBy {p: Person -> p.age}
```

- 로컬 변수처럼 컴파일러는 람다 파라미터의 타입도 추론할 수 있다. 따라서 파라미터 타입을 굳이 명시하지 않아도 된다.

```
people.maxBy() {p -> p.age}
```

- 마지막으로 람다의 파라미터 이름을 디폴트 이름인 it로 바꾸면 람다식이 더 간단해진다.
- 람다의 파라미터가 하나뿐이고 그 타입을 컴파일러가 추론할 수 있는 경우 it를 바로 쓸 수 있다.

```kotlin
people.maxBy {it.age}
```

- 람다를 변수에 저장할 때는 파라미터의 타입을 추론할 문맥이 존재하지 않아서 파라미터 타입을 명시해야 한다.

```kotlin
val getAge = {p: Person -> p.age}
people.maxBy(getAge)
```

- 본문이 여러 줄로 있는 경우 맨 마지막에 있는 식이 람다의 결과 값이 된다.

```kotlin
fun main(args: Array<String>) {
    val sum = { x: Int, y: Int ->
       println("Computing the sum of $x and $y...")
       x + y
    }
    println(sum(1, 2))
}

```



### 현재 영역에 있는 변수에 접근

- 람다를 함수 안에서 정의하면 함수의 파라미터뿐 아니라 람다 정의의 앞에선언된 로컬 변수까지 람다에서 모두 사용할 수 있다.

```kotlin
fun printMessagesWithPrefix(messages: Collection<String>, prefix: String) {
    messages.forEach {
        println("$prefix $it")
    }
}

fun main(args: Array<String>) {
    val errors = listOf("403 Forbidden", "404 Not Found")
    printMessagesWithPrefix(errors, "Error:")
}
```

- 자바와 다른 점은 코틀린 람다 안에서는 파이널 변수가 아닌 변수에 접근할 수 있다는 점이다. 또한 람다 안에서 바깥 변수를 변경해도 된다.

```kotlin
fun printProblemCounts(responses: Collection<String>) {
    var clientErrors = 0
    var serverErrors = 0
    responses.forEach {
        if (it.startsWith("4")) {
            clientErrors++
        } else if (it.startsWith("5")) {
            serverErrors++
        }
    }
    println("$clientErrors client errors, $serverErrors server errors")
}

fun main(args: Array<String>) {
    val responses = listOf("200 OK", "418 I'm a teapot",
                           "500 Internal Server Error")
    printProblemCounts(responses)
}
```

- 람다가 사용하는 변수를 람다가 캡쳐한 변수라고 부른다.
- 코틀린에서 람다가 파이널인 변수를 포획한 경우 람다 코드를 변수 값과 함께 저장한다. 파이널이 아닌 변수를 포획한 경우엔 변수를 감싸서 나중에 변경하거나 읽을 수 있게 한 다음 래퍼에 대한 참조를 람다 코드와 함께 저장한다.
- 한가지 알아둬야할 함정은 람다를 이벤트 핸들러나 다른 비동기적으로 실행되는 코드로 활용하는 경우 함수 호출이 끝난 다음에 로컬 변수가 변경될 수도 있다.

### 멤버 참조

- 코틀린에서는 자바 8과 마찬가지로 함수를 값으로 바꿀 수 있다. 이때 이중콜론을 사용한다.

```kotlin
val getAge = Person::age
val getAge = {person: Person -> person.age} // 이 문장과 같다.
```

- ::를 사용하는 식을 멤버 참조라고 부른다. 멤버 참조는 프로퍼티나 메서드를 단 하나만 호출하는 함수 값을 만들어준다.
- 멤버 참조 뒤에는 괄호를 넣으면 안된다.
- 멤버 참조는 그 멤버를 호출하는 람다와같은 타입이다.
- 최상위에 선언된 함수나 프로퍼티, 생성자도 참조할 수도 있다. 확장 함수도 가능하다.

```kotlin
fun salute() = println("Salute!")

fun main(args: Array<String>) {
    run(::salute)
}

```

```kotlin
data class Person(val name: String, val age: Int)

fun main(args: Array<String>) {
    val createPerson = ::Person
    val p = createPerson("Alice", 29)
    println(p)
}
```



## 컬렉션 함수형 API

- 함수형 프로그래밍 스타일을 사용하면 컬렉션을 다룰 때 편리하다.

### 필수적인 함수: filter와 map

```kotlin
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4)
    println(list.filter { it % 2 == 0 })
}
```

```kotlin
data class Person(val name: String, val age: Int)

fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 29), Person("Bob", 31))
    println(people.filter { it.age > 30 })
}
```

- filter 함수는 컬렉션에서 원치 않는 원소를 제거한다.
- 원소를 변환하려면 map 함수를 사용해야 한다.

```kotlin
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4)
    println(list.map { it * it })
}
```

```kotlin
data class Person(val name: String, val age: Int)

fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 29), Person("Bob", 31))
    println(people.map { it.name })
}
```

```kotlin
fun main(args: Array<String>) {
    val numbers = mapOf(0 to "zero", 1 to "one")
    println(numbers.mapValues { it.value.toUpperCase() })
}

```



### all, any, count, find: 컬렉션에 술어 적용

- 컬렉션의 모든 원소가 어떤 조건을 만족하는지 판단하는 연산을 코틀린에서는 all과 any로 사용한다.
- count 함수는 조건을 만족하는 원수의 개수를 반환하면 find 함수는 조건을 만족하는 첫 번째 원소를 반환한다.

```kotlin
data class Person(val name: String, val age: Int)

val canBeInClub27 = { p: Person -> p.age <= 27 }

fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 27), Person("Bob", 31))
    println(people.all(canBeInClub27))
}

// return false
```

```kotlin
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3)
    println(!list.all { it == 3 })
    println(list.any { it != 3 })
}

// return true, true
```

```kotlin
data class Person(val name: String, val age: Int)

val canBeInClub27 = { p: Person -> p.age <= 27 }

fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 27), Person("Bob", 31))
    println(people.find(canBeInClub27))
}

// return Alice
```



### groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경

- 컬렉션의 모든 원소를 어떤 특성에 따라 여러 그룹으로 나누고싶을땐 groupBy를 사용하면 된다.

```kotlin
data class Person(val name: String, val age: Int)

fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 31),
            Person("Bob", 29), Person("Carol", 31))
    println(people.groupBy { it.age })
}

// {31=[Person(name=Alice, age=31), Person(name=Carol, age=31)], 29=[Person(name=Bob, age=29)]}
```

```kotlin
fun main(args: Array<String>) {
    val list = listOf("a", "ab", "b")
    println(list.groupBy(String::first))
}
```



### flatMap과 flatten: 중첩된 컬렉션 안의 원소 처리

- flatMap 함수는 먼저 인자로 주어진 람다를 컬렉션의 모든 객체에 적용하고 람다를 적용한 결과 얻어지는 여러 리스트를 한 리스트로 모은다.

```kotlin
fun main(args: Array<String>) {
    val strings = listOf("abc", "def")
    println(strings.flatMap { it.toList() })
}
```

```kotlin
class Book(val title: String, val authors: List<String>)

fun main(args: Array<String>) {
    val books = listOf(Book("Thursday Next", listOf("Jasper Fforde")),
                       Book("Mort", listOf("Terry Pratchett")),
                       Book("Good Omens", listOf("Terry Pratchett",
                                                 "Neil Gaiman")))
    println(books.flatMap { it.authors }.toSet())
}
```



## 지연 계산(lazy) 컬렉션 연산

- 앞 절에서 살펴본 map과 filter 같은 몇 가지 컬렉현 함수들은 컬렉션을 즉시 생성한다. 이는 컬렉션 함수를 연쇄하면 매 단계마다 계산 중간 결과를 새로운 컬렉션에 임시로 담는다는 말이다.
- 시퀀스를 사용하면 중간 임시 컬렉션을 사용하지 않고도 컬렉션 연산을 연쇄할 수 있다.
- 시퀀스를 사용하면 원소가 많은 경우 성능이 매우 좋아진다.
- 코틀린 지연 계산 시퀀스는 Sequence 인터페이스에서 시작한다. 이 인터페이스는 단지 한 번에 하나씩 열거될 수 있는 원소의 시퀀스를 표현할 뿐이다. Sequence안에는 iterator라는 단 하나의 메서드가 있다. 그 메서드를 통해 시퀀스로부터 원소 값을 얻을 수 있다.
- 큰 컬렉션은 무조건 시퀀스를 사용하자.
- 시퀀스에 대한 연산을 지연 계산하기 때문에 정말 계산을 실행하게 만들려면 최조 시퀀스의 원소를 하나씩 이터레이션 하거나 최종 시퀀스를 리스트로 변환해야 한다.



### 시퀀스 연산 실행: 중간 연산과 최종 연산

- 시퀀스에 대한 연산은 중간연산과 최종연산으로 나눈다.
- 중간 연산은 다른 시퀀스를 반환하고 최종연산은 결과를 반환한다.

```kotlin
fun main(args: Array<String>) {
    listOf(1, 2, 3, 4).asSequence()
            .map { print("map($it) "); it * it }
            .filter { print("filter($it) "); it % 2 == 0 }
}

// 이 연산은 최종연산자가 없기 때문에 실제로 print가 호출되지 않는다.


fun main(args: Array<String>) {
    listOf(1, 2, 3, 4).asSequence()
            .map { print("map($it) "); it * it }
            .filter { print("filter($it) "); it % 2 == 0 }
            .toList()
}

// 이제야 호출 된다.
```

- 시퀀스의 경우 모든 연산은 각 원소에 대해 순차적으로 적용된다. 따라서 원소에 연산을 차례대로 적용하다가 결과가 얻어지면 그 이후 원소에 대해서는 변환이 이뤄지지 않을 수도 있다. ex: find
- 시퀀스는 자바의 스트림과 개념이 같다. 스트림도 lazy하게 동작.



### 시퀀스 만들기

- asEquence() 함수 외에도 generateSequence 함수를 사용할 수 있다.

```kotlin
fun main(args: Array<String>) {
    val naturalNumbers = generateSequence(0) { it + 1 }
    val numbersTo100 = naturalNumbers.takeWhile { it <= 100 }
    println(numbersTo100.sum())
}
```

- 이 예제에서 naturalNumbers와 numbersTo100은 모두 시퀀스다. 최종연산 이전까지는 계산되지 않는다.
- 시퀀스를 사용하는 일반적인 용례중 하나는 객체의 조상으로 이뤄진 시퀀스를 만들어내는 것이다.

```kotlin
fun File.isInsideHiddenDirectory() =
        generateSequence(this) { it.parentFile }.any { it.isHidden }

fun main(args: Array<String>) {
    val file = File("/Users/svtk/.HiddenDir/a.txt")
    println(file.isInsideHiddenDirectory())
}
```

- 이렇게 시퀀스를 사용하면 만족하는 디렉토리를 찾고 더 이상 상위 디렉토리를 뒤지지 않는다.



## 자바 함수형 인터페이스 활용

- 코틀린 람다를 자바 API에 사용해도 아무 문제가 없다.
- 코틀린은 함수형 인터페이스를 인자로 취하는 자바 메서드를 호출할 때 람다를 넘길 수 있게 해준다.

```kotlin
fun createAllDoneRunnable(): Runnable {
    return Runnable { println("All done!") }
}

fun main(args: Array<String>) {
    createAllDoneRunnable().run()
}
```



### 자바 메서드에 람다를 인자로 전달

- 자바 메서드에 코틀린 람다를 전달할 수 있다.

```kotlin
// 자바
void postponeComputation(int delay, Runnable computation);

postponeComputation(1000) {println(42)}
```

- 객체식을 함수형 인터페이스 구현으로 넘기는 방법도 있지만 이 방법은 메서드를 호출할 때마다 새로운 객체가 생성된다.

```kotlin
postponeComputation(1000, object : Runnable {
	override fun run() {
		println(42)
	}
})

// 매번 Runnable 생성
```

- 정의가 들어있는 함수의 변수에 접근하지 않는 람다에 대응하는 무명 객체를 메서드를 호출할 때마다 반복 사용한다.
- 만약 람다가 주변 영역의 변수를 캡쳐한다면 매 호출마다 같은 인스턴스를 사용할 수 없다. 그런 경우라면 호출마다 새로운 인스턴스를 생성해준다.

```kotlin
fun hanleComputation(id: String) {
	postponeComputation(1000) {println(id)}
}
```





### SAM 생성자: 람다를 함수형 인터페이스로 명시적으로 변경

- SAM 생성자는 람다를 함수형 인터페이스의 인스턴스로 변환할 수 있게 컴파일러가 자동으로 생성한 함수다.
- 컴파일러가 자동으로 람다를 함수형 인터페이스 무명 클래스로 바꾸지 못하는 경우 SAM 생성자를 사용할 수 있다.
- 예를 들어 함수형 인터페이스의 인스턴스를 반환하는 메서드가 있다면 람다를 직접 반환할 수 없고, 반환하고픈 람다를  SAM 생성자로 감싸야 한다.

```kotlin
fun createAllDoneRunnable(): Runnable {
    return Runnable { println("All done!") }
}

fun main(args: Array<String>) {
    createAllDoneRunnable().run()
}
```

- SAM은 좀 어렵네요.. pass

## 수신 객체 지정 람다: with와 apply

- 코틀린에서는 수신 객체를 명시하지 않고 람다의 본문 안에서 다른 객체의 메서드를 호출할 수 있게 해준다. 그런 람다를 수신 객체 지정 람다라고 부른다.

### with 함수

```kotlin
fun alphabet(): String {
    val result = StringBuilder()
    for (letter in 'A'..'Z') {
         result.append(letter)
    }
    result.append("\nNow I know the alphabet!")
    return result.toString()
}

fun main(args: Array<String>) {
    println(alphabet())
}
```

- with를 사용하면 아래와 같다.

```kotlin
fun alphabet(): String {
    val stringBuilder = StringBuilder()
    return with(stringBuilder) { // 메서드를 호출하려는 수신 객체를 지정
        for (letter in 'A'..'Z') {
            this.append(letter) // this는 수신 객체
        }
        append("\nNow I know the alphabet!") // this 생략도 가능
        this.toString() // 람다에서 값을 반환
    }
}

fun main(args: Array<String>) {
    println(alphabet())
}
```

- with문은 실제로 파라미터가 2개 있는 함수다. 첫번째 파라미터는 stringbuilder 두번째 파라미터는 람다
- with 함수는 첫 번째 인자로 받은 객체를 두 번쨰 인자로 받은 람다의 수신 객체로 만들어준다.
- with가 반환하는 값은 람다 코드를 실행한 결과고 마지막 식의 값이다.
- 아래처럼 리팩토링할 수 있다.

```kotlin
fun alphabet() = with(StringBuilder()) {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
    toString()
}

fun main(args: Array<String>) {
    println(alphabet())
}

```



### apply 함수

- apply 함수는 거의 with와 같다. 유일한 차이는 apply는 항상 자신에게 전달된 객체를 반환한다는 점 뿐이다.

```kotlin
fun alphabet() = StringBuilder().apply {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
}.toString()

fun main(args: Array<String>) {
    println(alphabet())
}
```

- apply를 실행하면 그 수신 객체의 참조가 넘어간다. 그리고 .toString()으로 문자열을 반환한 것이다.
- 이런 apply 함수는 객체의 인스턴스를 만들면서 즉시 프로퍼티 중 일부를 초기화해야 하는 경우 유용하다.
- 코틀린에서는 어떤 클래스가 정의돼 있는 라이브러리의 특별한 지원 없이도 그 클래스 인스턴스에 대해 apply를 활용할 수 있다.

```kotlin
fun alphabet() = buildString {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
}

fun main(args: Array<String>) {
    println(alphabet())
}
```



## 요약

- 람다를 사용하면 코드 조각을 다른 함수에게 인자로 넘길 수 있다.
- 코틀린에서는 람다가 함수 인자인 경우 괄호 밖으로 람다를 빼낼 수 있고 람다의 인자가 단 하나뿐인 경우 인자 이름을 지정하지 않고  it이라는 디폴트 이름으로 부를 수 있다.
- 람다 안에 있는 코드는 그 람다가 들어있는 바깥 함수의 변수를 읽거나 쓸 수 있다.
- 메서드, 생성자, 프로퍼티의 이름 앞에 ::을 붙이면 각각에 대한 참조를 만들 수 있다. 그런 참조를 람다 대신 다른 함수에게 넘길 수 있다.
- filter, map, all, any 등의 함수를 활용하면 컬렉션에 대한 대부분의 연산을 직접 원소를 이터레이션하지 않고 수행할 수 있다.
- 시퀀스를 사용하면 중간 결과를 담는 컬렉션을 생성하지 않고도 컬렉션에 대한 여러 연산을 조합할 수 있다.
- 함수형 인터페이스를 인자로 받는 자바 함수를 호출할 경우 람다를 함수형 인터페이스 인자 대신 넘길 수 있다.
- 수신 객체 지정 람다를 사용하면 람다 안에서 미리 정해둔 수신 객체의 메서드를 직접 호출할 수 있다.
- 표준 라이브러리의 with 함수를 사용하면 어떤 객체에 대한 참조를 반복해서 언급하지 않으면서 그 객체의 메서드를 호출할 수 있다. apply를 사용하면 어떤 객체라도 빌더 스타일의 API를 사용해 생성하고 초기화할 수 있다.



# #6 코틀린 타입 시스템

## 널 가능성

- 널 가능성은 NPE 오류를 피할 수 있게 돕기 위한 코틀린 타입 시스템의 특성이다.
- 코틀린에서는 널이 될 수 있는지 여부를 타입 시스템에 축가함으로써 컴파일러가 여러 가지 오류를 컴파일 시 미리 감지해서 실행 시점에 발생할 수 있는 예외의 가능성을 줄일 수 있다.

### 널이 될 수 있는 타입

- 코틀린에서 널이 인자로 들어올 수 없다면 아래와 같이 함수를 정의할 수 있다.

```kotlin
fun strLen(s: String) = s.length

strLen(null) // <- null은 들어갈 수 없기 때문에 컴파일 오류 발생
```

- 이 함수가 널과 문자열을 인자로 받을 수 있게 하려면 타입 이름 뒤에 물음표를 달아줘야한다.

```kotlin
fun strLen(s: String?) = s.length
```

- 어떤 타입이든 ?를 달아주면 null 참조를 저장할 수 있다는 뜻이다.
- 널이 될 수 있는 타입의 변수가 있다면 그에 대해 수행할 수 있는 연산이 제한된다.
  - 널이 될 수 있는 타입인 변수에 대해 변수.메서드() 형태로 직접 호출 불가
  - 널이 될 수 있는 값을 널이 될 수 없는 타입의 변수에 대입 불가
  - 널이 될 수 있는 타입의 값을 널이 될 수 없는 타입의 파라미터를 받는 함수에 전달 불가
- null과 비교하고 나면 컴파일러는 그 사실을 기억하고 null이 아님이 확실한 영역에서는 해당 값을 널이 될 수 없는 타입의 값처럼 사용할 수 있다.

```kotlin
fun strLenSafe(s: String?): Int =
    if (s != null) s.length else 0

fun main(args: Array<String>) {
    val x: String? = null
    println(strLenSafe(x))
    println(strLenSafe("abc"))
}
```



### 타입의 의미

- "타입은 분류로 ... 타입은 어떤 값들이 가능한지와 그 타입에 대해 수행할 수 있는 연산의 종류를 결정한다" - 위키피디아
- 자바는 null을 제대로 다루지 못한다. 코틀린의 널이 될 수 있는 타입은 이런 문제에 대해 종합적인 해법을 제공한다.
- 널이 될 수 있는 타입과 널이 될 수 없는 타입을 구분하면 각 타입의 값에 어떤 연산이 가능할지 명확히 이해할 수 있고 실행 시점에 예외를 발생시킬 수 있는 연산을 판단할 수 있다.



### 안전한 호출 연산자: ?.

- ?.은 null 검사와 메서드 호출을 한 번의 연산으로 수행한다.

```kotlin
fun printAllCaps(s: String?) {
    val allCaps: String? = s?.toUpperCase()
    println(allCaps)
}

fun main(args: Array<String>) {
    printAllCaps("abc")
    printAllCaps(null)
}
```

- 호출하려는 값이 null 이 아니라면 일반 메서드처럼 호출되고 호출하려는 값이 null이면 이 호출은 무시되고 null이 결과값이 된다.
- 안전한 호출의 결과 타입도 널이 될 수 있는 사실에 유의할 것!
- 메서드 호출 뿐 아니라 프로퍼티를 읽거나 쓸 때에도 안전한 호출을 사용할 수 있다.

```kotlin
class Employee(val name: String, val manager: Employee?)

fun managerName(employee: Employee): String? = employee.manager?.name

fun main(args: Array<String>) {
    val ceo = Employee("Da Boss", null)
    val developer = Employee("Bob Smith", ceo)
    println(managerName(developer))
    println(managerName(ceo))
}
```

```kotlin
class Address(val streetAddress: String, val zipCode: Int,
              val city: String, val country: String)

class Company(val name: String, val address: Address?)

class Person(val name: String, val company: Company?)

fun Person.countryName(): String {
   val country = this.company?.address?.country
   return if (country != null) country else "Unknown"
}

fun main(args: Array<String>) {
    val person = Person("Dmitry", null)
    println(person.countryName())
}

```



### 엘비스 연산자: ?:

- 코틀린은 null 대신 사용할 디폴트 값을 지정할 때 편리하게 사용할 수 있는 연산자를 제공한다.
- 그 연산자를 엘비스 연산자라고 한다. ?: 귀엽
- 좌항 값이 널이면 우항 값을 결과로 한다.

```kotlin
fun strLenSafe(s: String?): Int = s?.length ?: 0

fun main(args: Array<String>) {
    println(strLenSafe("abc"))
    println(strLenSafe(null))
}
```

- 엘비스 연산자는 return, throw 등등에 넣을 수 있고 엘비스 연산자의 좌항이 널이면 함수가 즉시 어떤 값을 반환하거나 예외를 던지게할 수 있다.

```kotlin
class Address(val streetAddress: String, val zipCode: Int,
              val city: String, val country: String)

class Company(val name: String, val address: Address?)

class Person(val name: String, val company: Company?)

fun printShippingLabel(person: Person) {
    val address = person.company?.address
      ?: throw IllegalArgumentException("No address")
    with (address) {
        println(streetAddress)
        println("$zipCode $city, $country")
    }
}

fun main(args: Array<String>) {
    val address = Address("Elsestr. 47", 80687, "Munich", "Germany")
    val jetbrains = Company("JetBrains", address)
    val person = Person("Dmitry", jetbrains)
    printShippingLabel(person)
    printShippingLabel(Person("Alexey", null))
}
```



### 안전한 캐스트: as?

- as? 연산자는 어떤 값을 지정한 타입으로 캐스트한다.
- as?는 값을 대상 타입으로 변환할 수 없으면 null을 반환한다.
- 안전한 캐스트를 사용할 때 일반적인 패턴은 캐스트를 수행한 뒤에 엘비스 연산자를 사용하는 것이다.



```kotlin
class Person(val firstName: String, val lastName: String) {
   override fun equals(o: Any?): Boolean {
      val otherPerson = o as? Person ?: return false

      return otherPerson.firstName == firstName &&
             otherPerson.lastName == lastName
   }

   override fun hashCode(): Int =
      firstName.hashCode() * 37 + lastName.hashCode()
}

fun main(args: Array<String>) {
    val p1 = Person("Dmitry", "Jemerov")
    val p2 = Person("Dmitry", "Jemerov")
    println(p1 == p2)
    println(p1.equals(42))
}
```

- 이런 패턴을 사용하면 파라미터로 받은 값이 원하는 타입인지 쉽게검사하고 캐스트할 수 있고 타입이 맞지 않으면 쉽게 false를 반환할 수 있다. 스마트 캐스트도 동작한다.

### 널 아님 단언: !!

- 느낌표를 !! 으로 사용하면 어떤 값이든 널이 될 수 없는 타입으로 강제로 바꿀 수 있다.
- 널에 대해 !!를 적용하면 NPE가 발생한다.

```kotlin
fun ignoreNulls(s: String?) {
    val sNotNull: String = s!!
    println(sNotNull.length)
}

fun main(args: Array<String>) {
    ignoreNulls(null)
}

// NPE
```

- 근본적으로 !!는 컴파일러에게 "나는 이 값이 null이 아님을 잘 알고 있다. 내가 잘못 생각했다면 예외가 발생해도 감수하겠다." 라는 의미로 사용한다.
- 어떤 함수가 값이 널인지 검사한 다음에 다른 함수를 호출한다고해도 컴파일러는 호출된 함수 안에서 안전하게 그 값을 사용할 수 있음을 인식할 수 없다. 이런 경우 !!를 사용할 수 있다.
- !!를 널에 대해 사용해서 발생하는 예외의 스택 트레이스에는 어떤 파일의 머 번째 줄인지에 대한 정보는 들어있지만 어떤 식에서 예외가 발생했는지에 대한 정보는 들어있지 않다. 어떤 값이 널이었는지 확실히 하기 위해 여러 !! 단언문을 한 줄에 함께 쓰는 일을 피하자!



### let 함수

- let 함수를 안전한 호출 연산자와 함께 사용하면 원하는 식을 평가해서 결과가 널인지 검사한 다음에 그 결과를 변수에 넣는 작업을 간단한 식을 사용해 한꺼번에 처리할 수 있다.

```kotlin
fun sendEmailTo(email: String) {
    println("Sending email to $email")
}

fun main(args: Array<String>) {
    var email: String? = "yole@example.com"
    email?.let { sendEmailTo(it) }
    email = null
    email?.let { sendEmailTo(it) }
}

```

- sendEmailTo가 null이 아닌 타입만 받지만 let함수를 사용하면 인자를 전달할 수도 있다.
- let을 통해 널이 될 수 있는 타입의 값을 널이 될 수 없는 타입의 값으로 바꿔서 람다에 전달한다.
- let으로 넘긴 타입이 실제로 널이라면 함수에서는 아무런 일도 발생하지 않는다.





### 나중에 초기화할 프로퍼티

- 코틀린에서는 클라스 안에 널이 될 수 없는 프로퍼티를 생성자 안에서 초기화하지 않고 특별한 메서드 안에서 초기화할 수는 없다.
- 이런 특별한 초기화가 필요한 경우 경우 널이 될 수 있는 타입을 사용할 수밖에 없다. 이 경우 모든 프로퍼티에는 !! 가 들어가야 한다.

```kotlin
import org.junit.Before
import org.junit.Test
import org.junit.Assert

class MyService {
    fun performAction(): String = "foo"
}

class MyTest {
    private var myService: MyService? = null

    @Before fun setUp() {
        myService = MyService()
    }

    @Test fun testAction() {
        Assert.assertEquals("foo",
            myService!!.performAction())
    }
}
```

- lateinit을 사용하면 프로퍼티를 나중에 초기화시킬 수 있다.

```kotlin
import org.junit.Before
import org.junit.Test
import org.junit.Assert

class MyService {
    fun performAction(): String = "foo"
}

class MyTest {
    private lateinit var myService: MyService

    @Before fun setUp() {
        myService = MyService()
    }

    @Test fun testAction() {
        Assert.assertEquals("foo",
            myService.performAction())
    }
}
```

- 나중에 초기화하는 프로퍼티는 항상 var 여야 한다.
- 나중에 초기화하는 프로퍼티는 널이 될 수 없는 타입이라 해도 더 이상 생성자 안에서 초기화할 필요가 없다.
- 그 프로퍼티를 초기화하기 전에 프로퍼티에 접근하면 특별한 예외가 발생한다.

### 널이 될 수 있는 타입 확장

- 널이 될 수 있는 타입에 대한 확장 함수를 정의하면 null 값을 다루는 강력한 도구로활용할 수 있다.
- 코틀린 String의 isNullOrEmpty이나 isNullOrBlack가 이런 함수다.

```kotlin
fun verifyUserInput(input: String?) {
    if (input.isNullOrBlank()) {
        println("Please fill in the required fields")
    }
}

fun main(args: Array<String>) {
    verifyUserInput(" ")
    verifyUserInput(null)
}
```

- 안전한 호출 없이도 널이 될 수 잇는 수신 객체 타입에 대해 선언된 확장 함수를 호출 가능하다.
- 지금부터 너무 헷갈려요



### 타입 파라미터의 널 가능성

- 코틀린에서 함수나 클래스의 모든 타입 파라미터는 기본적으로 널이 될 수 있다.
- 널이 될 수 있는 타입을 포함하넌 어떤 타입이라도 타입 파라미터를 대신할 수 있다.
- 타입 파라미터 T를 클래스나 함수 안에서 타입 이름으로 사용하면 이름 끝에 물음표가 없더라도 T는 널이 될 수 있는 타입이다.

```kotlin
fun <T> printHashCode(t: T) {
    println(t?.hashCode()) // T에 ?가 없지만 T는 타입파라미터, 즉 안전한 호출을 사용해야 한다.
}

fun main(args: Array<String>) {
    printHashCode(null) // T의 타입은 Any? 로 추론된다.
}
```

- 타입 파라미터가 널이 아님을 확실히 하려면 널이 될 수 없는 타입 상한을 지정해야 한다.

```kotlin
fun <T: Any> printHashCode(t: T) { // T는 null이 될 수 없는 타입이 된다.
	println(t.hashCode())
}
```



### 널 가능성과 자바

- 자바의 @Nullabe은 코틀린의 String? 과 같고 @NotNull은 코틀린의 String과 같다
- 코틀린은 여러 널 가능성 애너테이션을 알아본다.
- 이런 널 가능성이 소스코드에 없으면 자바의 타입은 코틀린의 플랫폼 타입이 된다.

**플랫폼 타입**

- 플랫폼 타입은 코틀린이 널 관련 정보를 알 수 없는 타입을 말한다.
- 플랫폼 타입에 대해 수행하는 모든 연산에 대한 책임은 사용자에 있다는 뜻이다.
- 플랫폼 타입일때 컴파일러는 모든 연산을 허용한다.
- 코틀린 컴파일러는 공개 가시성인 코틀린 함수의 널이 아닌 타입인 파라미터와 수신 객체에 대한 널 검사를 추가해준다. 따라서 공개 가시성 함수에 널 값을 사용하면 즉시 예외가 발생한다.
- 오류를 피하려면 자바 메서드의 문서를 자세히 살펴봐서 그 메서드가 널을 반환할지 알아내고 널을 반환하는 메서드에 대한 널 검사를 추가해야 한다.
- 코틀린에서는 플랫폼타입을 직접 선언할 수 없다.
- 에러 메시지에서 ! 표기는 타입의 널 가능성에 대해 아무 정보도 없다는 뜻이다.
- 플랫폼 타입은 널에 대한 정보가 없기 때문에 널 가능, 널 불가능 타입 두 곳 모두에 바인딩 될 수 있다.

**상속**

- 코틀린에서 자바 메서드를 오버라이드할 때 그 메서드의 파라미터와 반환 타입을 널이 될 수 있는 타입으로 선언할지 널이 될 수 없는 타입으로 선언할지 결정해야 한다.
- 자바 클래스나 인터페이스를 코틀린에서 구현할 경우 널 가능성을 제대로 처리하는 일이 중요하다.

## 코틀린의 원시 타입

- 코틀린은 원시 타입과 래퍼 타입을 구분하지 않는다.

### 원시 타입: Int, Boolean 등

- 코틀린은 원시 타입과 래퍼 타입을 구분하지 않기 때문에 항상 같은 타입을 사용한다.

```kotlin
fun showProgress(progress: Int) {
    val percent = progress.coerceIn(0, 100)
    println("We're ${percent}% done!")
}

fun main(args: Array<String>) {
    showProgress(146)
}
```

- 코틀린은 실행 시점에 숫자 타입은 가능한 한 가장 효율적인 방식으로 표현된다. 대부분 Int 타입은 자바의 int 타입으로 컴파일한다. 이런 컴파일이 불가능할 땐 컬렉션, 제네릭을 사용하는 경우다.

### 널이 될 수 있는 원시 타입: Int?, Boolean? 등

- 널이 될 수 있는 코틀린 타입은 자바 원시 타입으로 표현할 수 없다. 따라서 코틀린에서 널이 될 수 있는 원시 타입을 사용하면 그 타입은 자바의 wrapper 타입으로 컴파일된다.

```kotlin
data class Person(val name: String,
                  val age: Int? = null) {

    fun isOlderThan(other: Person): Boolean? {
        if (age == null || other.age == null)
            return null
        return age > other.age
    }
}

fun main(args: Array<String>) {
    println(Person("Sam", 35).isOlderThan(Person("Amy", 42)))
    println(Person("Sam", 35).isOlderThan(Person("Jane")))
}
```

- 제네릭의 경우 무조건 래퍼타입을 사용하기 때문에 대규모 컬렉션을 효율적으로 저장해야한다면 서드파티 라이브러리를 사용하거나 배열을 사용해야 한다.



### 숫자 변환

- 코틀린은 한 타입의 숫자를 다른 타입의 숫자로 자동 변환하지 않는다. 반드시 직접 변환 메서드를 호출해야 한다.
- 코틀린은 Boolean을 제외한 모든 원시타입에 대한 변환 함수를 제공한다. toChar, toByte ...

```kotlin
fun main(args: Array<String>) {
    val x = 1
    println(x.toLong() in listOf(1L, 2L, 3L)) //변환 안하면 컴파일 에러 발생
}

```

- 숫자 리터럴을 사용할 때는 보통 변환 함수를 호출할 필요가 없다. 42L 42.0f ..

```kotlin
fun foo(l: Long) = println(l)

fun main(args: Array<String>) {
    val b: Byte = 1
    val l = b + 1L
    foo(42)
}
```

- 문자열을 숫자로 변환할 수도 있다.

```kotlin

fun main(args: Array<String>) {
    println("42".toInt())
}

```



### Any, Any?: 최상위 타입

- 코틀린에서는 Any 타입이 모든 널이 될 수 없는 타입의 조상이다.
- 자바에서 원시타입은 Object 타입이 아니지만 코틀린에서 Int, Boolean 등 원시타입을 포함한 모든 타입의 조상이 Any다
- 널이 될 수 있는 타입은 Any?다.
- 내부에서 Any 타입은 java.lang.Object 에 대응한다. 
- Any에는 toString, equals, hashCode만 있고 wait, notify는 없기 때문에 필요하다면 java.lang.Object로 캐스팅해서 사용해야 한다.



### Unit 타입: 코틀린의 void

- 코틀린 Unit 타입은 자바 void와 같은 기능을 한다.
- 코틀린의 함수 반환 타입이 Unit이고 그 함수가 제네릭 함수를 오버라이드하지 않는다면 그 함수는 내부에서 자바 void 함수로 컴파일된다.
- 자바와 다른점은 Unit은 모든 기능을 갖는 일반적인 타입이며 void와 달리 타입 인자로 쓸 수 있다.
- Unit 타입의 함수는 Unit 값을 묵시적으로 반환한다.

```kotlin
interface Processor<T> {
	fun process(): T
}

class NoResultProcessor : Processor<Unit> {
  override fun process() {
    //..
    // <- 마지막줄에 return을 명시할 필요가 없다.
  }
  
}
```

### Nothing 타입: 이 함수는 결코 정상적으로 끝나지 않는다.

- 테스트 라이브러리의 fail이나 정상적으로 함수가 끝나지 않는다는 사실을 알면 유용한데 이때 Nothing 타입을 사용한다.

```kotlin
fun fail(message: String): Nothing {
    throw IllegalStateException(message)
}

fun main(args: Array<String>) {
    fail("Error occurred")
}
```

- Nothing 타입은 아무 값도 반환하지 않는다. 따라서 Nothing은 함수의 반환 타입이나 반환 타입으로 쓰일 타입 파라미터로만 쓸 수 있다.



## 컬렉션과 배열

### 널 가능성과 컬렉션

- 변수 타입 뒤에 ? 를 붙이는 것 처럼 타입 인자에도 ? 를 사용할 수 있다.

```kotlin
import java.util.ArrayList
import java.io.BufferedReader
import java.io.StringReader

fun readNumbers(reader: BufferedReader): List<Int?> {
    val result = ArrayList<Int?>()
    for (line in reader.lineSequence()) {
        try {
            val number = line.toInt()
            result.add(number)
        }
        catch(e: NumberFormatException) {
            result.add(null)
        }
    }
    return result
}

fun addValidNumbers(numbers: List<Int?>) {
    var sumOfValidNumbers = 0
    var invalidNumbers = 0
    for (number in numbers) {
        if (number != null) {
            sumOfValidNumbers += number
        } else {
            invalidNumbers++
        }
    }
    println("Sum of valid numbers: $sumOfValidNumbers")
    println("Invalid numbers: $invalidNumbers")
}

fun main(args: Array<String>) {
    val reader = BufferedReader(StringReader("1\nabc\n42"))
    val numbers = readNumbers(reader)
    addValidNumbers(numbers)
}
```

- 특별한 내용은 없고 리스트 원소에 접근하면 Int? 타입의 값을 얻고 이 값을 사용하기 전에 널 여부를 검사해야 한다.



### 읽기 전용과 변경 가능한 컬렉션

- 코틀린에서는 컬렉션 안의 데이터에 접근하는 인터페이스와 컬렉션 안의 데이터를 변경하는 인터페이스를 분리했다는 점이다.
- 코틀린 컬렉션은 kotlin.collections.Collection 부터 시작한다. 이 Collection 인터페이스를 사용하면 컬렉션 안의 원소에 대해 이터레이션하고 컬렉션의 크기를 얻고, 어떤 값이 컬렉션 안에 들어있는지 검사하고, 컬렉션에서 데이터를 읽는 여러 다른 연산을 수행할 수 있다. but 쓰기 연산은 없다.
- 컬렉션의 데이터를 수정하려면 kotlin.collections.MutableCollection 인터페이스를 사용하라.
- MutableCollection은 Collection의 확장이다.
- 코드에서 가능하면 항상 읽기 전용 인터페이스를 사용하는 것을 권장한다.
- 이렇게 구분한 이유는 val과 var처럼 가변과 불변을 구분한 것과 비슷하다.

```kotlin
fun <T> copyElements(source: Collection<T>,
                     target: MutableCollection<T>) {
    for (item in source) {
        target.add(item)
    }
}

fun main(args: Array<String>) {
    val source: Collection<Int> = arrayListOf(3, 5, 7)
    val target: MutableCollection<Int> = arrayListOf(1)
    copyElements(source, target)
    println(target)
}
```

- 컬렉션 인터페이스를 사용할 때 항상 염두에 둬야 할 핵심은 읽기 전용 컬렉션이라고해서 꼭 변경 불가능한 컬렉션일 필요는 없다는 점이다. 같은 인스턴스를 읽기전용, 쓰기 모두 바라볼 때
- 읽기 전용이라고 항상 스레드 세이프하지 않다는 점을 명심해야 한다.

### 코틀린 컬렉션과 자바

- 모든 코틀린 컬렉션은 그에 상응하는 자바 컬렉션 인터페이스의 인스턴스라는 점은 사실이다.
- 코틀린은 모든 자바 컬렉션 인터페이스마다 읽기 전용, 쓰기 가능한 인터페이스라는 두 가지 표현을 제공한다.

| 컬렉션 타입 | 읽기 전용 타입 | 변경 가능 타입                                    |
| ----------- | -------------- | ------------------------------------------------- |
| List        | listOf         | mutableListOf, arrayListOf                        |
| Set         | setOf          | mutableSetOf, hashSetOf, linkedSetOf, sortedSetOf |
| Map         | mapOf          | mutableMapOf, hashMapOf, linkedMapOf, sortedMapOf |

- 자바에서는 읽기전용, 쓰기전용을 구분하지 않으므로 코틀린에서 Collection 타입으로 넘겨도 쓰기가 가능하다.
- 위 상황은 널이 아닌 타입의 컬렉션을 자바에 넘겼을때도 똑같은 문제가 생길 수 있다.
- 컬렉션을 자바로 넘길때는 특별히 주의해야 한다.

### 컬렉션을 플랫폼 타입으로 다루기

### 객체의 배열과 원시 타입의 배열

- 코틀린 배열은 타입 파라미터를 받는 클래스다.
- 코틀린에서 배열을 만드는 방법
  - arrayOf 함수에 원소를 넘기면 배열을 만들 수 있다.
  - arrayOfNulls 함수에 정수 값을 인자로 넘기면 모든 원소가 null이고 인자로 넘긴 값과 크기가 같은 배열을 만들 수 있다. 원소 타입이 널이될 수 있는 타입인 경우에만 이 함수를 쓸 수 있다.
  - Array 생성자는 배열 크기와 람다를 인자로 받아서 람다를 호출해서 각 배열 원소를 초기화해준다. arrayOf를 쓰지 않고 각 원소가 널이 아닌 배열을 만들어야 하는 경우 이 생성자를 사용한다.

```kotlin
fun main(args: Array<String>) {
    val letters = Array<String>(26) { i -> ('a' + i).toString() }
    println(letters.joinToString(""))
}
```

- 컬렉션을 vararg 인자에 넘기는 방법은 아래와 같다.

```kotlin
fun main(args: Array<String>) {
    val strings = listOf("a", "b", "c")
    println("%s/%s/%s".format(*strings.toTypedArray()))
}
```

- Array\<Int\> 같은 타입을 선언하면 그 배열은 박싱된 정수의 배열로 컴파일된다. 박싱하지 않은 배열이 필요하다면 특별한 배열 클래스를 사용해야 하낟.
- 코틀린은 원시 타입의 배열을 표현하는 별도 클래스를 가 원시 타입마다 하나씩 제공한다. IntArray, ByteArray ...



## 요약

- 코틀린은 널이 될 수 있는 타입을 지원해 NPE 오류를 컴파일 시점에 감지할 수 있다.
- 코틀린의 .?, ?:, !!, let 함수 등을 사용하면 널이 될 수 있는 타입을 간결한 코드로 다룰 수 있다.
- as? 연산자를 사용하면 값을 다른 타입으로 변환하는 것과 변환이 불가능한 경우를 처리하는 것을 한꺼번에 편리하게 처리할 수 있다.
- 자바에서 가져온 타입은 코틀린에서 플랫폼 타입으로 취급된다. 개발자는 플랫폼 타입을 널이 될 수 있는 타입으로도, 널이 될 수 없는 타입으로도 사용할 수 있다.
- 코틀린에서는 수를 표현하는 타입이 일반 클래스와 똑같이 생겼고 일반 클래스와 똑같이 동작한다. 하지만 대부분 컴파일러는 숫자 타입을 자바 원시 타입으로 컴파일한다.
- 널이 될 수 있는 원시 타입은 자바의 박싱한 원시 타입에 대응한다.
- Any 타입은 다른 모든 타입의 조상 타입이며, 자바의 Object에 해당한다. Unit은 자바의 void와 비슷하다.
- 정상적으로 끝나지 않는 함수의 반환 타입을 지정할 때 Nothing 타입을 사용한다.
- 코틀린 컬렉션은 표준 자바 컬렉션 클래스를 사용한다. 하지만 코틀린은 자바보다 컬렉션을 더 개선해서 읽기 전용 컬렉션과 변경 가능한 컬렉션을 구별해 제공한다.
- 자바 클래스를 코틀린에서 확장하거나 자바 인터페이스를 코틀린에서 구현하는 경우 메서드 파라미터의 널 가능성과 변경 가능성에 대해 깊이 생각해야 한다.
- 코틀린의 Array 클래스는 일반 제네릭 클래스처럼 보인다. 하지만 Array는 자바 배열로 컴파일된다.
- 원시 타입의 배열은 IntArray와 같이 각 타입에 대한 특별한 배열로 표현된다.









# #7 연산자 오버로딩과 기타 관례

- 어떤 기능과 미리 정해진 이름의 함수를 연결해주는 기법을 코틀린에서는 관례라고 부른다.
- 언어 기능을 타입에 의존하는 자바와 달리 코틀린은 관례에 의존한다.

## 산술 연산자 오버로딩

- 자바에서는 원시타입, String만 + 연산자를 사용할 수 있다.
- 코틀린에서는 BigInteger 같은 레퍼런스 타입에도 + 를 사용할 수 있다.

### 이항 산술 연산 오버로딩

```kotlin
data class Point(val x: Int, val y: Int) {
    operator fun plus(other: Point): Point {
        return Point(x + other.x, y + other.y)
    }
}

fun main(args: Array<String>) {
    val p1 = Point(10, 20)
    val p2 = Point(30, 40)
    println(p1 + p2)
}
```

- 연산자를 오버로딩하는 함수에는 꼭 operator가 있어야 한다.
- operator키워드는 어떤 함수가 관례를 따르는 함수인지 명확히 알 수 있다.
- 위 코드에서 p1 + p2는 p1.plus(p2) 가 된다.

| 식    | 함수 이름        |
| ----- | ---------------- |
| a * b | times            |
| a / b | div              |
| a % b | mod(1.1부터 rem) |
| a + b | plus             |
| a - b | minus            |

- 연산자 우선순위는 기본 숫자형 연산자 우선순위와 같다. 곱셈이 덧셈보다 먼저 수행
- 코틀린 연산자가 자동으로 교환 법칙을 지원하지 않음에 유의할 것

```kotlin
data class Point(val x: Int, val y: Int)

operator fun Point.times(scale: Double): Point {
    return Point((x * scale).toInt(), (y * scale).toInt())
}

fun main(args: Array<String>) {
    val p = Point(10, 20)
    println(p * 1.5)
}
```

- 위 예제에서 p * 1.5는 가능하지만 1.5 * p는 컴파일이 안된다.
- 연산자 함수의 반환 타입이 꼭 두 피연산자 중 하나와 일치해야하는 조건은 없다.

```kotlin
operator fun Char.times(count: Int): String {
    return toString().repeat(count)
}

fun main(args: Array<String>) {
    println('a' * 3)
}
```

- 일반 함수와 마찬가지로 operator 함수도 오버로딩할 수 있다. 이름은 같지만 파라미터 타입이 서로 다른 연산자 함수를 여럿 만들 수 있다.
- 비트 연산자는 특별한 연산자 함수를 사용하지 않는다.
  - shl: 왼쪽 시프트
  - shr: 오른쪽 시프트
  - ushr: 오른쪽 시프트
  - and: qlxm rhq
  - or: 비트 합
  - xor: 비트 배타
  - inv: 비트 반전

```kotlin
fun main(args: Array<String>) {
    println(0x0F and 0xF0)
    println(0x0F or 0xF0)
    println(0x1 shl 4)
}
```

### 복합 대입 연산자 오버로딩

- 코틀린은 += 같은 복합 대입 연사자도 지원한다.
- point += Point(3, 4)는 point = point + Point(3, 4)라고 쓴 식과 같다.

```kotlin
operator fun Point.plus(other: Point): Point {
    return Point(x + other.x, y + other.y)
}

fun main(args: Array<String>) {
    var point = Point(1, 2)
    point += Point(3, 4)
    println(point)
}
```

- 반환 타입이 Unit인 plusAssign 함수를 정의하면 코틀린은 += 연산자에 그 함수를 사용한다. 그럼 컬렉션에 값을 넣을 수 있다.

```kotlin
import java.util.ArrayList

fun main(args: Array<String>) {
    val numbers = ArrayList<Int>()
    numbers += 42
    println(numbers[0])
}

@kotlin.internal.InlineOnly
public inline operator fun <T> MutableCollection<in T>.plusAssign(element: T) {
    this.add(element)
}

```

- 다른 복합 대입 연산자 함수도 비슷하게 minusAssign, timesAssign 등의 이름을 사용한다.
- plus와 plusAssign 연산을 동시에 정의하지 말자.
- 클래스가 불변이라면 plus 만 정의, 빌더와 비슷한건 plusAssign으로 정의
- 코틀린 표준 라이브러리는 +와 -에 새로운 컬렉션을 반환하며 +=, -=는 변경을 적용한 복사본을 반환한다.

```kotlin
fun main(args: Array<String>) {
    val list = arrayListOf(1, 2)
    list += 3
    val newList = list + listOf(4, 5)
    println(list)
    println(newList)
}
```



### 단항 연산자 오버로딩

```kotlin
data class Point(val x: Int, val y: Int)

operator fun Point.unaryMinus(): Point {
    return Point(-x, -y)
}

fun main(args: Array<String>) {
    val p = Point(10, 20)
    println(-p)
}

```

- 단항 연산자 오버로딩 함수는 인자를 취급하지 않는다.

| 식       | 함수 이름  |
| -------- | ---------- |
| +a       | unaryPlus  |
| -a       | unaryMinus |
| !a       | not        |
| ++a, a++ | inc        |
| --a, a-- | dec        |

```kotlin
import java.math.BigDecimal

operator fun BigDecimal.inc() = this + BigDecimal.ONE

fun main(args: Array<String>) {
    var bd = BigDecimal.ZERO
    println(bd++)
    println(++bd)
}
```

## 비교 연산자 오버로딩

### 동등성 연산자: eqauls

- == 가 equals를 호출하는 것 역시 관례를 적용한 것에 불과하다.
- 코틀린에서 동등성 검사는 equals 호출과 널 검사로 컴파일된다.
- 식별자 비교 연산자는 === 를 사용한다. (같은 참조인지) ===를 오버로딩할 수는 없다.
- Any의 eqauls에는 operator가 붙어있어서 오버라이드할때 operator 변경자를 붙이지 않아도 적용된다. 

### 순서 연산자: compareTo

- 코틀린도 자바의 Comparable 인터페이스를 지원한다. compareTo 메서드를 호출하는 관례도 지원한다.
- p1 < p2 는 p1.compareTo(p2) < 0 과 같다.

```kotlin
import kotlin.comparisons.compareValuesBy

class Person(
        val firstName: String, val lastName: String
) : Comparable<Person> {

    override fun compareTo(other: Person): Int {
        return compareValuesBy(this, other,
            Person::lastName, Person::firstName)
    }
}

fun main(args: Array<String>) {
    val p1 = Person("Alice", "Smith")
    val p2 = Person("Bob", "Johnson")
    println(p1 < p2)
}

```

- 처음에는 성능에 신경쓰지말고 이해하기 쉽고 간결하게 코드를 작성하고 나중에 그 코드가 자주 호출됨에 따라 성능이 문제가 되면 성능을 개선하라.
- Comprable 인터페이스를 구현하는 모든 자바 클래스를 코틀린에서는 간결한 연산자 구문으로 비교할 수 있다.



## 컬렉션과 범위에 대해 쓸 수 있는 관례

### 인덱스로 원소에 접근: get과 set

- 코틀린에서 맵의 원소나 배열 원소에 접근할때 각괄호를 사용할 수 있다.
- Map과 MutableMap에서 각괄호를 사용해서 get, set에 대한 operator는 이미 정의되어 있다.

```kotlin
data class Point(val x: Int, val y: Int)

operator fun Point.get(index: Int): Int {
    return when(index) {
        0 -> x
        1 -> y
        else ->
            throw IndexOutOfBoundsException("Invalid coordinate $index")
    }
}

fun main(args: Array<String>) {
    val p = Point(10, 20)
    println(p[1])
}
```

- 파라미터가 n개도 가능하고 반드시 Int일 필요도 없다.
- 쓰기는 아래와 같은 형태로 하면 된다.

```kotlin
data class MutablePoint(var x: Int, var y: Int)

operator fun MutablePoint.set(index: Int, value: Int) {
    when(index) {
        0 -> x = value
        1 -> y = value
        else ->
            throw IndexOutOfBoundsException("Invalid coordinate $index")
    }
}

fun main(args: Array<String>) {
    val p = MutablePoint(10, 20)
    p[1] = 42
    println(p)
}

```



### in 관례

- in은 객체가 컬렉션에 들어있는지 검사한다.
- in 연산자는 contains연산자와 대응한다.

```kotlin
data class Point(val x: Int, val y: Int)

data class Rectangle(val upperLeft: Point, val lowerRight: Point)

operator fun Rectangle.contains(p: Point): Boolean {
    return p.x in upperLeft.x until lowerRight.x &&
           p.y in upperLeft.y until lowerRight.y
}

fun main(args: Array<String>) {
    val rect = Rectangle(Point(10, 20), Point(50, 50))
    println(Point(20, 30) in rect)
    println(Point(5, 5) in rect)
}
```

- 10..20은 20을 포함하지만 10 until 20은 20을 포함하지 않는다. until은 열린 범위를 표현할때 사용한다.

### rangeTo 관례

- .. 연산자는 rangeTo 함수를 간략하게 표현하는 방법이다.
- 어떤 클래스가 Comparable 인터페이스를 구현하면 rangeTo를 정의할 필요가 없다.
- rangeTo 연산자는 다른 산술연산자보다 우선순위가 낮아서 괄호로 인자를 감싸주면 더 좋다.

### for 루프를 위한 iterator 관례

- 코틀린의 for 루프는 범위 검사와 똑같이 in 연산자를 사용한다.

```kotlin
import java.time.LocalDate

operator fun ClosedRange<LocalDate>.iterator(): Iterator<LocalDate> =
        object : Iterator<LocalDate> {
            var current = start

            override fun hasNext() =
                current <= endInclusive

            override fun next() = current.apply {
                current = plusDays(1)
            }
        }

fun main(args: Array<String>) {
    val newYear = LocalDate.ofYearDay(2017, 1)
    val daysOff = newYear.minusDays(1)..newYear
    for (dayOff in daysOff) { println(dayOff) }
}
```

- iterator 메서드를 확장하면 확장 함수로 정의하는 관례를 사용할 수 있다.

## 구조 분해 선언과 component 함수

- 구조 분해를 사용하면 복합적인 값을 분해해서 여러 다른 변수를 한꺼번에 초기화할 수 있다.

```kotlin
data class Point(val x: Int, val y: Int)

fun main(args: Array<String>) {
    val p = Point(10, 20)
    val (x, y) = p
    println(x)
    println(y)
}
```





### 구조 분해 선언과 루프

## 프로퍼티 접근자 로직 재활용: 위임 프로퍼티

### 위임 프로퍼티 소개

### 위임 프로퍼티 사용: by lazy()를 사용한 프로퍼티 초기화 지연

### 위임 프로퍼티 구현

### 위임 프로퍼티 컴파일 규칙

### 프로퍼티 값을 맵에 저장

### 프레임워크에서 위임 프로퍼티 활용

## 요약





