



# Effective Kotlin

코드 참고: [https://github.com/f-lab-edu/kts-effective-kotlin/](https://github.com/f-lab-edu/kts-effective-kotlin/)

코드 참고: [https://github.com/MaxzMeng/Effective-Kotlin-zh-CN](https://github.com/MaxzMeng/Effective-Kotlin-zh-CN)

# #1 안정성

- 코틀린의 가장 큰 장점은 안전하다는 것!!!

## 아이템 1: 가변성을 제한하라

- 프로그램이 상태를 가지면 어려운 점들 ..
  - 프로그램을 이해하고 디버깅하기 힘들어진다. 추적 & 사이드이펙트 등등 ..
  - 가변성이 있으면 코드의 실행을 추론하기 어려워진다.
  - 멀티스레드 프로그램일 때 적절한 동기화가 필요하다.
  - 모든 상태를 테스트하기 어렵다.
  - 상태변경이 일어날 때 다른 부분에 알려줘야 하는 경우가 있다.
- **변할 수 있는 지점은 줄일 수록 좋다.**
- 순수 함수형 언어
  - 하스켈같은 순수 함수형언어는 가변성을 완전히 차단한다.
  - 실제로 프로그램을 작성하기 매우 어렵기 때문에 주류로 사용하는 언어가 되지 못한다.
- 가변성
  - 시스템의 상태를 나타내기 위한 중요한 방법
  - 하지만 변경이 일어나는 부분을 신중하고 확실하게 결정해야 한다.



### 코틀린에서 가변성 제어하기

- 코틀린에서 가변성 제어하기
  - 읽기 전용 프로퍼티 (val)
  - 가변 컬렉션과 읽기 전용 컬렉션 구분하기
  - 데이터 클래스의 copy



**읽기 전용 프로퍼티 (val)**

- val로 선언한 프로퍼티는 마치 값처럼 동작하고 일반적인 방법으로 값이 변하지 않는다.

```kotlin
val a = 10
a = 20 // 오류
```

- 읽기 전용 프로퍼티가 mutable 객체를 담고 있다면 내부적으로는 변할 수 있다.

```kotlin
val list = mutableListOf(1,2,3)
list.add(4)
print(list) // [1, 2, 3, 4]
```

- 사용자 정의 게터로 정의된 val 프로퍼티도 변할 수 있다.

```kotlin
var name: String = "GilDong"
var surname: String = "Hong"
val fullName
	get() = "$name $surname"

fun main() {
	println(fullName) // Hong GilDong
	name = "Kim"
	println(fullName) // Kim GilDong
```

- var는 게터와 세터를 모두 제공하지만 val은 변경이 불가능하므로 getter만 제공한다. 따라서 val을 var로 오버라이드할 수 있다.
- val은 읽기 전용이지 불변이 아니라는 점을 기억해야 한다. 정말 불변은 final을 사용하면 된다.

```kotlin
var name: String? = "GilDong"
var surname: String = "Hong"
val fullName: String?
	get() = name?.let {"$it $surname"}

val fullName2: String? = name?.let {"$it $surname"}

fun main() {
	if (fullName != null) {
		println(funllName.length) // 오류
	}

	if (fullName2 != null) {
		println(fullName2.length) // 12(Kim GilDong)
	}
}
```

- fullName은 게터로 정의했으므로 스마트 캐스트할 수 없다.
- fullName2처럼 지역 변수가 아닌 프로퍼티가 final이고 사용자 정의 게터를 갖지 않을 경우 스마트 캐스트할 수 있다.



**가변 컬렉션과 읽기 전용 컬렉션 구분하기**

- 프로퍼티처럼 컬렉션도 읽고 쓰기 전용, 읽기 전용 컬렉션이 있다.

- 읽고 쓰기 전용 컬렉션

  - MutableIterable
  - MutableCollection
  - MutableSet
  - MutableList

- 읽기 전용 컬렉션

  - Iterable
  - Collection
  - Set
  - List

- 코틀린은 컬렉션을 읽기 전용으로 설계해서 내부적으로 immutable하지 않은 컬렉션을 외부적으로 immutable하게 보이게 만들어서 안정성을 얻는다.

- 읽기전용 -> 읽기 쓰기 전용

  - 코틀린에서 읽기 전용은 읽기 전용으로만 사용해야하고 다운캐스팅을 하면안된다.

  - 만약 필요하다면 읽기 전용에서 copy를 통해 새로운 mutable 컬렉션을 만드는 list.toMutableList를 활용해야 한다.

  - ```kotlin
    val list = listOf(1,2,3)
    
    // 이렇게 하지 마세요! 아래의 코드처럼 사용필요.
    if (list is MutableList) {
    	list.add(4)
    }
    val list = listOf(1,2,3)
    
    val mutableList = list.toMutableList()
    
    mutable.add(4)
    ```



**데이터 클래스의 copy**

- immutable의 장점

  - 한 번 정의된 상태가 유지되므로 코드를 이해하기 쉽다.
  - immutable 객체는 공유했을 때도 충돌이 따로 이루어지지 않으므로, 병렬 처리를 안전하게 할 수 있다.
  - immutable 객체에 대한 참조는 변경되지 않으므로 쉽게 캐시할 수 있다.
  - immutable 객체는 방어적 복사본을 만들 필요가 없다. 또한 다른 객체를 복사할 때 깊은 복사를 따로 하지 않아도 된다.
  - immutable 객체는 다른 객체를 만들 때 활용하기 좋다.
  - immutable 객체는 set, map의 key로 사용할 수 있다.

- immutable의 단점

  - 객체의 상태를 변경할 수 없다.

  - 자신의 일부를 수정한 새로운 객체를 만들어내는 메서드를 가져야 한다. <- 이게 지키기 어려움

  - ```kotlin
    class User (val name:String, val surname: String) {
    	fun withSurname(surname: String) = User(name, usrname)
    }
    
    var user = User("AAA", "BBB")
    user = user.withSurname("CCC")
    print(user) // User(name="AAA", surname="CCC")
    ```

- data 클래스와 copy

  - 위와 같은 단점은 코틀린의 data 한정자와 copy 메서드로 커버가 가능하다.

  - copy 메서드를 활용하면 모든 기본생성자 프로퍼티가 같은 새로운 객체를 만들어낼 수 있다.

  - ```kotlin
    data class User (val name:String, val surname: String)
    
    var user = User("AAA", "BBB")
    user = user.copy("CCC")
    print(user) // User(name="AAA", surname="CCC")
    ```

### 다른 종류의 변경 가능 지점

- 변경할 수 있는 리스트를 만드는 두 가지 방법

  - ```kotlin
    val list1: MutableList<Int> = mutableListOf()
    var list2: List<Int> = listOf()
    
    lsit1.add(1)
    list2 = list2 + 1
    
    list1 += 1 // list1.plusAssign(1)로 변경
    list2 += 1 // list2 = list2.plus(1)로 변경
    ```

  - 첫번째는 구체적인 리스트 구현 내부에 변경 가능 지점이 있고 두번째는 프로퍼티 자체가 변경 가능 지점이 된다.

- mutable 프로퍼티로 변경사항 추적하기 (by Delegates)

  - mutable 리스트 대신 mutable 프로퍼티를 사용하는 형태는 사용자 정의 세터를 활용해서 변경을 추적할 수 있다.

  - ```kotlin
    var names by Delegates.observable(listOf<String>()) { _, old, new -> 
    	println("Names changed from $old to $new")
    }
    
    names += "Fabio" // Names changed from [] to [Fabio]
    names += "Bill" // Names changed from [Fabio] to [Fabio, Bill]
    ```

  - (이러한 기능이 필요하다면) mutable 프로퍼티(var)에 읽기 전용 컬렉션을 넣어 사용하는 것이 쉽다.

- 최악의 방식

  - mutable 프로퍼티 & mutable 컬렉션은 최악이다.

  - ```kotlin
    // 이렇게 하지 마세요.
    var list3 = mutableListOf<Int>()
    ```

  - 가변성은 제한하는 것이 좋다.

### 변경 가능 지점 노출하지 말기

- 상태를 나타내는 mutable 객체를 외부에 노출하는 것은 굉장히 위험하다.

```kotlin
data class User(val name: String) 

class UserRepository {
	private val storedUsers: MuatableMap<Int, String> = mutableMapOf()

	fun loadAll(): MutableMap<Int,String> {
		return storedUsers
	}

	//...
}

// loadAll을 사용해서 private
val userRepository = UserRepository()

val storedUsers = userRepository.loadAll() 
storedUsers[4] = "AAA"c
//...

print(userRepository.loadAll()) // {4=AAA}
```

- 처리하는 방법

  - 리턴되는 mutable 객체를 복제하기

  - ```kotlin
    class UserHolder {
    	private val user: MutableUser()
    
    	fun get(): MutableUser {
    		return user.copy()
    
    		//...
    }
    ```

  - 컬렉션을 읽기 전용 슈퍼타입으로 업캐스트해서 가변성을 제한하기

  - ```kotlin
    class UserRepository {
    	private val storedUsers: MuatableMap<Int, String> = mutableMapOf()
    
    	fun loadAll(): Map<Int,String> {
    		return storedUsers
    	}
    
    	//...
    }
    ```



### 정리

- 코틀린은 가변성을 제한하기 위한 도구들을 제공한다. 이를 활용해 가변 지점을 제한하며 코드를 작성하자.
  - var 보다는 val을 사용하는 것이 좋다.
  - mutable 프로퍼티보다는 immutable 프로퍼티를 사용하는 것이 좋다.
  - mutable 객체와 클래스보다는 immutable 객체와 클래스를 사용하는 것이 좋다.
  - 변경이 필요한 대상을 만들어야 한다면 immutable 데이터 클래스로 만들고 copy를 활용하는 것이 좋다.
  - 컬렉션에 상태를 저장해야 한다면, mutable 컬렉션보다는 읽기 전용 컬렉션을 사용하는  것이 좋다.
  - 변이 지점을 적절하계 설계하고 불필요한 변이 지점은 만들지 않는 것이 좋다.
  - mutable 객체를 외부에 노출하지 않는 것이 좋다.





## 아이템 2: 변수의 스코프를 최소화하라

- 상태를 정의할 때는 변수와 프로퍼티의 스코프를 최소화하는 것이 좋다.
  - 프로퍼티보다는 지역 변수를 사용하는 것이 좋다.
  - 최대한 좁은 스코프를 갖게 하는 변수를 사용하자. 

```kotlin
// 나쁜 예
var user: User
for (i in users.indices) {
	user = users[i]
	print("User at $i is $user")
}

// 조금 더 좋은 예
for (i in users.indices) {
	val user = users[i]
	print("User at $i is $user")
}

// 제일 좋은 예
for ((i, user) in users.withIndex()) {
	print("User at $i is $user")
}
```

- 스코프가 좁으면 좋은 이유
  - 프로그램을 추적하고 관리기 쉽다.
  - 다른 개발자에 의해서 변수가 잘못 사용되는 것을 방지할 수 있다.



- 변수는 읽기 전용 또는 읽고 쓰기 전용 여부와 상관없이 변수를 정의할 때 초기화하는 것이 좋다.

```kotlin
//나쁜 예
val user: User
if (hasValue) {
	user = getValue()
} else {
	user = User()
}

// 조금 더 좋은 예
val user: User = if(hasValue) {
	getValue()
} else {
	User()
}
```

- 여러 프로퍼티를 한꺼번에 설정해야 하는 경우엔 구조분해 선언을 활용하는 것이 좋다.

```kotlin
// 나쁜 예
fun updateWeather(degrees: Int) {
	val description: String
	val color: Int
	if (degrees < 5) {
		description = "cold"
		color = Color.BULE
	} else if (degrees < 23) { 
		desciption = "mild"
		color = Color.YELLOW
	} else {
		desciption = "hot"
		color = Color.RED
	}
}

// 조금 좋은 예
fun updateWeather(degrees: Int) {
	val (description, color) = when {
		degrees < 5 -> "cold" to Color.BLUE
		degrees < 23 -> "mild" to Color.YELLOW
		else -> "hot" to Color.RED
	}
}
```



### 캡쳐링

- 외부에서 가져온 람다 내부에서 사용할 땐 항상 잠재적인 캡처 문제를 주의해야한다.
- 가변성을 피하고 스코프 범위를 좁게 만들면 이런 문제를 간단히 회피할 수 있다.



### 정리

- 변수의 스코프는 좁게 만들어서 활용하는 것이 좋다.
- var 보다 val이 좋다.
- 람다에서는 변수를 캡쳐하는 것을 꼭 기억하자.



## 아이템 3: 최대한 플랫폼 타입을 사용하지 말라

- 코틀린의 대표적인 기능 중 하나는 바로 null-safety이다.
- 플랫폼 타입
  - 코틀린은 자바 등의 다른 프로그래밍 언어에서 넘어온 타입들을 플랫폼 타입이라는 특수한 타입으로 다룬다.
  - 플랫폼 타입은 다른 프로그래밍 언어에서 전달되어 nullable인지 아닌지 알 수 없는 타입을 말한다.
  - ! 표시 ex: String! 하지만 코드에 직접적으로 나타나진 않는다.
- 실제로 코틀린에서 플랫폼 타입을 다루는 방법

```kotlin
// 자바
public class UserRepo {
	public User getUser() {
		//...
	}
}

// 코틀린
val repo = UserRepo()
val user1 = repo.user        // user1의 타입은 User!
val user2: User = repo.user  // user2의 타입은 User
val user3: User? = repo.user // user3의 타입은 User?

val users: List<User> = UserRepo().users
val users: List<List<User>> = UserRepo().groupedUsers
```

- 위 코드는 과연 안전한가?
  - null이 아니라고 생각했던 것이 null일 가능성이 있다.
  - 지금은 반드시 not null이라도 추후에 api가 변경될 수도 있다.
- 자바 코드를 수정할 수 있다?
  - 가능하면 @Nullable과 @NotNull 애너테이션을 붙여서 사용하자.
  - 이런 애너테이션을 붙이는 작업은 코틀린을 사용하는 사람들에게 매우 중요하다.
- 그래도 플랫폼 타입은 안좋기 때문에 최대한 빨리 제거하는 것이 좋다.

```kotlin
// 자바
public class JavaClass {
	public String getValue() {
		return null;
	}
}

// 코틀린
fun statedType() {
    val value: String = JavaClass().value // NPE
    // ...
    println(value.length)
}

fun platformType() {
    val value = JavaClass().value
    // ...
    println(value.length) // NPE
}
```

- statedType()에서는 자바에서 값을 가져올 때 NPE가 발생한다.
- platformType()에서는 값을 활용할 때 NPE가 발생한다.
- 플랫폼타입은 컴파일러가 오류를 검출해줄 수 없다. 그리고 이 오류를 찾는 데 오랜 시간이 걸릴 수 있다.

```kotlin
interface UserRepo {
	fun getUserName() = JavaClass().value
}

class RepoImpl: UserRepo {
	override fun getUserName(): String? {
		return null
	}
}

fun main() {
	val repo: UserRepo = RepoImpl()
	val text: String = repo.getUserName() // 런타임 NPE
	print("User name length is ${text.length}")
}
```



### 정리

- 다른 프로그래밍 언어에서 와서 nullable 여부를 알 수 없는 타입을 플랫폼 타입이라고 부른다.
- 플랫폼타입은 컴파일타임에 타입 체크를 하지 못하기 때문에 코틀린의 null-safety 기능을 제대로 활용하지 못하고 이는 곧 위험한 코드다.
- 플랫폼 타입을 사용하는 곳이 있다면 제거하자.
- 연결되어있는 자바 api에 nullable 여부를 지정하는 애너테이션을 활용하자.

## 아이템 4: inffered 타입으로 리턴하지 말라

- 코틀린의 타입 추론은 코틀린의 특징이다.
- inferred 타입은 정확하게 오른쪽에 있는 피연산자에 맞게 설정된다는 것을 기억해야 한다.

```kotlin
open class Animal
class Zebra: Animal()

fun main() {
	var animal = Zebra()
	animal = Animal() // 오류: Type mismatch

fun main() {
	var animal: Animal = Zebra()
	animal = Animal() // 성공
```

```kotlin
// 1
interface CarFactory {
  fun produce(): Car
}

val DEFAULT_CAR: Car = Fiat126P()


// 2.produce() 의 타입 제거
interface CarFactory {
	fun produce() = DEFAULT_CAR
}

val DEFAULT_CAR: Car = Fiat126P()

// 3. DEFAULT_CAR의 타입 제거
interface CarFactory {
	fun produce() = DEFAULT_CAR
}

val DEFAULT_CAR = Fiat126P()
```

- 3번이 되는 경우 CarFactory는 더이상 Fiat126P 이외의 자동차를 생산할 수 없다. 만약 이 인터페이스가 외부API라면 클라이언트들이 문제를 쉽게 해결할 수 없다.
- 리턴 타입을 쓰자.
  - 리턴 타입은 API를 잘 모르는 사람에게 전달해줄 수 있는 중요한 정보다.
  - 리턴 타입은 외부에서 확인할 수 있게 명시적으로 지정해주는 것이 좋다.



### 정리

- 타입을 확실히 지정해야 하는 경우 명시적으로 타입을 지정해야 한다는 원칙만 갖고 있으면 된다.
- 안전을 위해서 외부 API를 만들 때는 반드시 타입을 지정하고 이렇게 지정한 타입을 특별한 이유와 확실한 확인 없이는 제거하지 말자.
- Inferred 타입은 프로젝트가 진전될 때, 제한이 너무 많아지거나 예측하지 못한 결과를 낼 수 있다.





## 아이템 5: 예외를 활용해 코드에 제한을 걸어라

- 코틀린에서 코드의 동작에 제한거는 방법
  - require 블록: 아규먼트를 제한할 수 있다.
  - check 블록: 상태와 관련된 동작을 제한할 수 있다.
  - assert 블록: 어떤 것이 true인지 확인할 수 있다. assert블록은 테스트 모드에서만 작동한다.
  - return 또는 throw와 함께 활용하는 Elvis연산자.
- 제한을 걸었을때의 장점
  - 제한을 걸면 문서를 읽지 않은 개발자도 문제를 확인할 수 있다.
  - 문제가 있을 경우에 함수가 예상하지 못한 동작을 하지 않고 예외를 throw 한다. 이로 인해 코드가 더 안정적으로 작동할 수 있다.
  - 코드가 어느 정도 자체적으로 검사된다. 따라서 단위 테스트를 줄일 수 있다.
  - 스마트 캐스트 기능을 활용할 수 있으므로 타입 변환을 적게할 수 있다.



### 아규먼트

- 함수를 정의할 때 타입 시스템을 화용해서 아규먼트에 제한을 거는 코드를 많이 사용한다.
  - 숫자가 양수여야 할 때
  - 목록이 비어있지 않아야 할 때
  - 이메일 형식이 올바른지
- 위와 같은 경우엔 require 함수를 사용한다. require 함수는 제한을 확인하고, 제한을 만족하지 못할 경우 예외를 throw 한다.

```kotlin
    /**
     * @property registrationNumber 사업자번호
     * */
    @Embeddable
    data class RegistrationNumber(
        val registrationNumber: String
    ) {
        init {
            require(registrationNumber.length == 10) { "The length of registration number can only 10" }
            require(registrationNumber.all { it.isDigit() }) { "registration number can only digit" }
        }
    }
```

- require함수는 조건을 만족하지 못할 때 무조건적으로 IllegalArgumentException을 발생시킨다.



### 상태

- 어떤 구체적인 조건을 만족해야만 함수를 사용할 수 있게 해야 할 때
  - 객체가 미리 초기화되어 있어야만 하는 경우
  - 객체가 특정 상태여야만 처리해야하는 경우

```kotlin
fun speak(text: String) {
  check(isInitilized)
  ...
}

fun getUserInfo(): UserInfo {
  checkNotNull(token)
  ...
}
```

- check 함수는 지정된 예측을 만족하지 못할 때 IllegalStateException을 throw한다.
- require와 check가 같이 있다면 require를 앞에 둔다.
- 이런 check는 사용자가 규약을 어기거나, 사용하면 안되는 곳에서 함수를 호출하고 있다고 의심될때 한다.



### Assert 계열 함수 사용

- 단위 테스트로 테스트를 검증하는 방법도 있지만 함수에 assert문을 둬서 테스트하는 방법도 있다.
- assert문을 사용하면 프로덕션 환경에서는 오류가 발생하지 않고 테스트에서만 활성화된다. 만약 이 오류가 심각하다면 check를 사용하면 된다.
- assert문은 단위테스트를 위한 양념 느낌 ..?



### nullability와 스마트 캐스팅

- 스마트 캐스팅

  - require와 check 블록을 사용해서 어떤 조건을 확인했다면 이후엔 스마트캐스팅이 적용된다.

  - ```kotlin
    class Person(val email: String?)
    
    fun sendEmail(person: Person, message: String) {
      require(person.email != null)
      val email: String = person.email
    }
    ```

  - 이러한 특징은 어떤 대상이 null인지 확인할 때 굉장히 유용하다.

  - 또는 requireNonNull, checkNotNull 이라는 특수한 함수를 사용해도 된다.

- nullability

  - nullability를 목적으로 Elvis 연산자를 활용하는 코드는 굉장히 읽기 쉽고 유연하게 사용할 수 있다.

  - ```kotlin
    fun sendEmail(person: Person, text: String) {
      val email: String = person.email ?: return // 함수 중단
      ...
    }
    ```

  - ```kotlin
    fun sendEmail(person: Person, text: String) {
      val email: String = person.email ?: run {
        log(" 어쩌구 .. 저쩌구 ..") // 로그 남기기
        return
      }
      ...
    }
    ```

  - return과 throw를 활용한 Elvis 연산자는 nullable을 확인할 때 굉장히 많이 사용되는 관용적인 방법이다.



### 정리

- 이번 절에서 활용한 내용을 기반으로 얻을 수 있는 것들
  - 제한을 훨씬 더 쉽게 확인할 수 있다.
  - 애플리케이션을 더안정적으로 지킬 수 있다.
  - 코드를 잘못 쓰는 상황을 막을 수 있다.
  - 스마트 캐스팅을 활용할 수 있다.
- 매커니즘
  - require 블록: 아규먼트와 관련된 예측을 정의할때 사용하는 범용적인 방법: IllegalArgumentException
  - check 블록: 상태와 관련된 예측을 정의할 때 사용하는 범용적인 방법: IllegalStateException
  - assert 블록: 테스트 모드에서 테스트를 할 때 사용하는 범용적인 방법
  - return과 throw와 함께 Elvis 연산자 사용하기



## 아이템 6: 사용자 정의 오류보다는 표준 오류를 사용하라

- require, check, assert를 사용하면 대부분의 코틀린 오류를 처리할 수 있다.
- 하지만 필요한 경우 사용자 정의 오류를 발생해야 하지만 가능하다면 직접 오류를 정의하는 것보다는 최대한 표준 라이브러리의 오류를 사용하는 것이 좋다.
- 일반적으로 사용되는 예외들
  - IllegalArgumentException, IllegalStateException
  - IndexOutOfBoundsException: 인덱스의 파라미터의 값이 범위를 벗어난것을 의미
  - ConcurrentModificationException: 동시 수정을 금지했는데 발생해버렸다는 의미
  - UnsupportedOperationException: 사용자가 사용하려고 했던 메서드가 현재 객체에서는 사용할 수 없다는 것을 의미
  - NoSuchElementException: 사용자가 사용하려고 했던 요소가 존재하지 않음을 나타내는 의미

## 아이템 7: 결과 부족이 발생할 경우 null과 Failure를 사용하라

- 함수과 원하는 결과를 만들어 낼 수 없을 때

  - 서버로부터 데이터를 읽어려하는데 네트웍 연결이 안될때
  - 조건에 맞는 첫 번째 요소를 찾으려 했는데 조건에 맞는 요소가 없는 경우
  - 텍스트를 파싱해서 객체를 만들려고 했는데 텍스트의 형식이 맞지 않는 경우

- 이러한 상황을 처리하는 메커니즘

  1. null 또는 실패를 나타내는 sealed 클래스(일반적으로 Failure라는 이름을 붙임)를 리턴한다.
  2. 예외를 throw 한다.

- 1번 사용하기

  - 예외는 잘못된 특별한 상황을 나타내야하고 정보를 전달하는 용도로는 사용하면 안된다.
    - 많은 개발자가 예외가 전파되는 과정을 제대로 추적하지 못한다.
    - 코틀린의 모든 예외는 unchecked 예외다. 따라서 사용자가 예외를 처리하지 않을 수 있고 이런 내용은 문서에도 제대로 드러나지 않는다.
    - 예외는 예외적인 상황을 처리하기 위해 만들어졌으므로 명시적인 테스트만큼 빠르게 동작하지 않는다.
    - try-catch 블록 내부에 코드를 배치하면 컴파일러가 할 수 있는 최적화가 제한된다.
  - 예측하기 어려운 예외적인 범위의 오류는 예외를 throw해서 처리하자.

- 2번 사용하기

  - 충분히 예측할 수 있는 범위의 오류는 null과 Failure를 사용하는게 좋다.

  - ```kotlin
    inline fun <reified T> String.readObjectOrNull(): T? {
       //...
       if (incorrectSign) {
           return null
       }
       //...
       return result
    }
    
    inline fun <reified T> String.readObject(): Result<T> {
       //...
       if (incorrectSign) {
           return Failure(JsonParsingException())
       }
       //...
       return Success(result)
    }
    
    sealed class Result<out T>
    class Success<out T>(val result: T) : Result<T>()
    class Failure(val throwable: Throwable) : Result<Nothing>()
    
    class JsonParsingException : Exception()
    ```

  - null처리를 해야 한다면 Elvis같은 다양한 널 안정성 기능을 활용하면 된다.

  - ```
    val age = userText.readObjectOrNull<Person>()?.age ?: -1
    ```

  - Result와 같은 공용체를 리턴하기로했다면 when 표현식을 사용할 수 있다.

  - ```kotlin
    val personResult = userText.readObject<Person>()
    val age = when(personResult) {
        is Success -> personResult.value.age
        is Failure -> -1
    }
    ```

  - null 값과 sealed result의 차이

    - 추가적인 정보가 필요하다면 sealed result
    - 그렇지 않으면 null

- 개발자에게 null이 발생할 수 있다는 경고를 주려면 getOrNull 등을 사용해서 무엇이 리턴되는지 예측할 수 있게 하는 것이 좋다.



## 아이템 8: 적절하게 null을 처리하라

- 기본적으로 nullable 타입을 처리하는 세가지 방법
  - ?. , 스마트 캐스팅, Elvis 연산자 등을 활용해서 안전하게 처리
  - 오류를 throw 하기
  - 함수 또는 프로퍼티를 리팩터링해서 nullable 타입이 나오지 않게 바꾸기



### null을 안전하게 처리하기

- 코틀린은 nullable 변수와 관련된 처리를 굉장히 광범위하게 지원해준다.

```kotlin
printer?.print() // Safe call
if (printer != null) printer.print() // Smart casting
```

```kotlin
val printerName1 = printer?.name ?: "Unnamed"
val printerName2 = printer?.name ?: return
val printerName3 = printer?.name ?: 
    throw Error("Printer must be named")
```

- 방어적 프로그래밍과 공격적 프로그래밍
  - 방어적 프로그래밍
    - 모든 가능성을 올바른 방식으로 처리하는 것을 방어적 프로그래밍이라 한다.
  - 공격적 프로그래밍
    - 예상하지 못한 상황이 발생했을 때 이러한 문제를 개발자에게 알려서 수정하게 만드는 것을 공격적 프로그래밍이라한다.
  - 이 둘은 코드의 안전을 위해서 반드시 필요하다.

### 오류 throw 하기

- 다른 개발자가 어떤 코드를 보고 '당연히 그럴 것이다' 라고 생각하게 되는 부분이 있고 그 부분에서 문제가 발생할 경우에는 개발자에게 오류를 강제로 발생시켜주는 것이 좋다.
- 오류를 강제로 발생시킬 때는 throw, !!, requireNotNull, checkNotNull 등을 활용하자.

```kotlin
fun process(user: User) {
    requireNotNull(user.name)
    val context = checkNotNull(context)
    val networkService = 
        getNetworkService(context) ?: 
        throw NoInternetConnection()
    networkService.getData { data, userData ->
        show(data!!, userData!!)
    }
}
```



### not-null assertion(!!)과 관련된 문제

- !! 사용에 대한 고찰

  - !!은 사용하기 쉽지만 좋은 해결 방법이 아니다.
  - !!타입은 nullable이지만 null이 나오지 않는다는 것이 거의 확실한 상황에서 많이 사용된다. 하지만 현재 확실하다고 미래에도 확실한건은 아니다.

- !! 의 안좋은 case

  - 변수를 null로 설정하고 이후에 !! 연산자를 사용하는 방법은 좋지 않다.

  - ```kotlin
    class UserControllerTest {
    
        private var dao: UserDao? = null
        private var controller: UserController? = null
    
        @BeforeEach
        fun init() {
            dao = mockk()
            controller = UserController(dao!!)
        }
    
        @Test
        fun test() {
            controller!!.doSomething()
        }
    
    }
    ```

  - 이럴때는 lateinit 또는 Delegates.notNull을 사용하자.

- 일반적으로 !!연산자는 피해야한다. 최대한 피해보자!!



### 의미없는 nullability 피하기

- nullability 피하기
  - 필요한 경우가 아니라면 nullability 자체는 피하는게 좋다!!
  - 이유없이 null을 사용하면 다른 개발자들은 !!를 사용하게되고 의미없이 코드를 더럽히는 예외처리를 해야 한다.
- nullability 피하는 방법
  - 클래스에서 nullability에 따라 여러 함수 만들기 ex: get and getOrNull
  - 어떤 값이 클래스 생성 이후에 확실하게 설정된다는 보장이 있다면 lateinit 프로퍼티와 notNull 델리게이트 사용하기
  - 빈 컬렉션 대신 null 리턴하지 않기. 요소가 없다면 빈 컬렉션을 사용하기



### lateinit 프로퍼티와 notNull 델리게이트

```kotlin
class UserControllerTest {

    private var dao: UserDao? = null
    private var controller: UserController? = null

    @BeforeEach
    fun init() {
        dao = mockk()
        controller = UserController(dao!!)
    }

    @Test
    fun test() {
        controller!!.doSomething()
    }

}
```

- @BeforeEach에서 반드시 프로퍼티들이 설정됨을 알 수 있기 때문에 lateinit을 사용한다.

```kotlin
class UserControllerTest {
   private lateinit var dao: UserDao
   private lateinit var controller: UserController

   @BeforeEach
   fun init() {
       dao = mockk()
       controller = UserController(dao)
   }

   @Test
   fun test() {
       controller.doSomething()
   }
}
```

- lateinit이 만병통치약?

  - lateinit은 초기화 전에 값을 사용하려고 하면 예외가 발생한다.
  - 사용하기 전에 반드시 초기화가 되어 있을 경우에만 lateinit을 붙여야 한다.

- lateinit과 nullable의 차이

  - !! 연산자로 언팩하지 않아도 된다.
  - 이후에 어떤 의미를 나타내기 위해서 null을 사용하고 싶을 때 nullable로 만들 수도 있다.
  - 프로퍼티가 초기화된 이후에는 초기화되지 않은 상태로 돌아갈 수 없다.

- lateinit은 프로퍼티를 처음 사용하기 전에 반드시 초기화 될 거라고 예상되는 상황에 활용한다.

- lateinit을 사용할 수 없을 때?

  - Int, Long, Double과 같은 기본 타입과 연결된 타입으로 프로퍼티를 초기화해야할 때는 lateinit보다는 Delegates.notNull을 사용하자.

  - ```kotlin
    class DoctorActivity: Activity() {
       private var doctorId: Int by Delegates.notNull()
       private var fromNotification: Boolean by 
           Delegates.notNull()
    
       override fun onCreate(savedInstanceState: Bundle?) {
           super.onCreate(savedInstanceState)
           doctorId = intent.extras.getInt(DOCTOR_ID_ARG)
           fromNotification = intent.extras
              .getBoolean(FROM_NOTIFICATION_ARG)
       }
    }
    ```

  - 프로퍼티 위임을 사용할 수도 있다. (자세한 설명은 아이템21에서 .. )

  - ```kotlin
    class DoctorActivity: Activity() {
       private var doctorId: Int by arg(DOCTOR_ID_ARG)
       private var fromNotification: Boolean by 
           arg(FROM_NOTIFICATION_ARG)
    }
    ```

    





## 아이템 9: use를 사용하여 리소스를 닫아라

- 더 이상 필요하지 않을 때 close 메서드를 사용해서 명시적으로 닫아야하는 리소스들

  - InputStream과 OutStream

  - java.sql.Connection

  - java.io.Reader

  - java.new.Socket

  - java.util.Scanner

  - 자바에서는 위와 같은 리소스들을 try-finally 블록을 사용해서 처리했다.

  - ```kotlin
    fun countCharactersInFile(path: String): Int {
       val reader = BufferedReader(FileReader(path))
       try {
           return reader.lineSequence().sumBy { it.length }
       } finally {
           reader.close()
       }
    }
    ```

- use함수 사용하기

  - 모든  Closeable 객체에 사용할 수 있다.

  - ```kotlin
    fun countCharactersInFile(path: String): Int {
       val reader = BufferedReader(FileReader(path))
       reader.use {
           return reader.lineSequence().sumBy { it.length }
       }
    }
    ```

  - ```kotlin
    fun countCharactersInFile(path: String): Int {
       BufferedReader(FileReader(path)).use { reader ->
           return reader.lineSequence().sumBy { it.length }
       }
    }
    ```

  - 파일을 한 줄씩 처리할 때 활용할 수 있는 useLines 함수

  - ```kotlin
    fun countCharactersInFile(path: String): Int =
       File(path).useLines { lines -> 
           lines.sumBy { it.length } 
       }
    ```



### 정리

- use를 사용하면 Closeable/AutoCloseable을 구현한 객체를 쉽고 안전하게 처리할 수 있다.



## 아이템 10: 단위 테스트를 만들어라

- 코드를 안전하게 만드는 가장 궁극적인 방법은 다양한 종류의 테스트를 하는 것이다.
- 단위 테스트는 일반적으로 다음과 같은 내용을 확인한다.
  - 일반적인 유스케이스: 요소가 사용될 거라고 예상되는 일반적인 방법을 테스트한다.
  - 일반적인 오류 케이스와 잠재적인 문제: 제대로 동자하지 않을거라고 예상하는 되는 일반적인 부분 또는 과거에 문제가 발생했던 부분을 테스트한다.
  - 엣지 케이스와 잘못된 아규먼트: Int.MAX_VALUE 또는 nullcheck 를 테스트한다.
- 단위 테스트의 장점
  - 테스트가 잘 된 요소는 신뢰할 수 있다. 요소를 신뢰할 수 있으므로 요소를 화룡한 작업에 자신감이 생긴다.
  - 테스트가 잘 만들어져 있다면, 리팩터링하는 것이 두렵지 않다. 테스트가 있으므로 리팩터링했을 때 버그가 생기는지 쉽게 확인하 수 있다. 따라서 테스트를 잘 만든 프로그램은 코드가 점점 발전한다.
  - 수동으로 테스트하는 것보다 단위 테스트로 확인하는 것이 빠르다.
  - 버그를 빨리 찾을 수 있으므로 수정하는 비용이 줄어든다.
- 단위 테스트의 단점
  - 단위 테스트를 만드는 데 시간이 걸린다. (오로지 테스트를 만드는데만 걸리고 결과적으로는 단위테스트를 만드는게 속도가 빠름)
  - 테스트를 활용할 수 있게 코드를 조정해야 한다. (결론적으로 테스트하기 좋은 아키텍처는 잘 정리된 아키텍처다.)
  - 좋은 단위 테스트를 만드는 작업이 꽤 어렵다.
- 단위테스트가 필요한 부분
  - 복잡한 부분
  - 계속해서 수정이 일어나고 리팩터링이 일어날 수 있는 부분
  - 비즈니스 로직 부분
  - 공용 API 부분
  - 문제가 자주 발생하는 부분
  - 수정해야 하는 프로덕션 버그

### 정리

- 가장 중요한 것은 애플리케이션이 진짜로 올바르게 동작하는지를 확인하는 것!

# #2 가독성

- 코틀린은 간결성을 목표로 설계된 프로그래밍 언어가 아니라 가독성을 좋게 하는 데 목표를 두고 설계된 프로그래밍 언어다.

## 아이템 11: 가독성을 목표로 설계하라

- "개발자가 코드를 작성하는 데는 1분이 걸리지만 이를 읽는 데는 10분이 걸린다." - 로버트 마틴
- 프로그래밍은 쓰기보다 읽기가 중요하다. 항상 가독성을 생각하면서 코드를 작성해야 한다.

### 인식 부하 감소

- 기본적으로 인지 부하를 줄이는 방향으로 코드를 작성하자.

  - 뇌는 기본적으로 짧은 코드를 빠르게 읽일 수 있지만 익숙한 코드는 더 빠르게 읽을 수 있다.

  - ```kotlin
    // Implementation A
    if (person != null && person.isAdult) {
       view.showPerson(person)
    } else {
       view.showError()
    }
    
    // Implementation B
    person?.takeIf { it.isAdult }
       ?.let(view::showPerson)
       ?: view.showError()
    ```

  - A가 읽기 좋고 수정하기도 좋은 코드다.

### 극단적이 되지 않기

- let은 좋은 코드를 만들기 위해서 다양하게 활용되는 인기있는 관용구다.

- let의 활용

  - 안전한 호출

  - ```kotlin
    class Person(val name: String)
    var person: Person? = null
    
    fun printName() {
        person?.let {
            print(it.name)
        }
    }
    ```

  - 연산을 아규먼트 처리 후로 이동시킬 때

  - ```kotlin
    students
         .filter { it.result >= 50 }	
         .joinToString(separator = "\n") { 
            "${it.name} ${it.surname}, ${it.result}" 
         }
         .let(::print)
    ```

  - 데코레이터를 사용해서 객체를 랩할 때

  - ```kotlin
    var obj = FileInputStream("/file.gz")
        .let(::BufferedInputStream)
        .let(::ZipInputStream)
        .let(::ObjectInputStream)
        .readObject() as SomeObject
    ```

- 관용구 등을 사용해서 어떤 코드에 비용을 지불하는것에 대한 균형을 맞추는것이 중요하다. 어떤 구조둘이 어떤 복잡성을 가져오는지 등을 파악하는 것이 좋다.

- 복잡성은 1+1 = 2 가 아니다. 훨씬 커진다.



### 컨벤션

## 아이템 12: 연산자 오버로드를 할 때는 의미에 맞게 사용하라

- 관례에 맞게 연산자 오버로드를 사용해야 한다. 팩토리얼을 !로 배웠다고해서 !연산자 오버로딩을 사용하면 관례가 무너진다.

### 분명하지 않은 경우

### 규칙을 무시해도 되는 경우

### 정리

- 연산자 오버로딩은 그 이름의 의미에 맞게 사용하자.
- 연산자의 의미가 명확하지 않다면, 연산자 오버로딩을 사용하지 않는 것이 좋다. 대신 이름이 있는 일반 함수를 사용하자.
- 꼭 연산자 같은 형태로 사용하고 싶다면 infix확장 함수 또는 톱레벨 함수를 활용하자.

##  

## 아이템 13: Unit?을 리턴하지 말라

- Unit?은 Unit 또는 null이라는 값을 가질 수 있다. 따라서 Boolean과 Unit? 타입은 서로 바꿔서 사용할 수 있다.
- Unit?으로 Boolean을 표현하는 것은 오해의 소지가 있고 예측하기 어려운 오류를 만들 수 있다.
- 기본적으로 Unit?을 리턴하거나 이를 기반으로 연산하지 않는 것이 좋다.



## 아이템 14: 변수 타임이 정확하지 않은 경우 확실하게 지정하라

- 코틀린은 개발자가 타입을 지정하지 않아도 타입을 지정해주는 수준 높은 타입 추론 시스템을 갖고 있다.
- 그러나 가독성을 위해 코드를 설계할 때는 읽는 사람에게 중요한 정보를 숨겨서는 안된다.

```kotlin
val data = getSomeData()
```

- '코드를 읽으면서 함수 정의를 보며 타입을 확인하면 되지 않나?' 라고 생각할 수 있지만 이는 가독성이 떨어진다는 의미다.

```kotlin
val data: UserData = getSomeData()
```

- 타입을 개발자와 컴파일러에게 모두 중요한 정보다. 타입 추론은 적재적소에 잘 사용해야 한다.



## 아이템 15: 리시버를 명시적으로 참조하라

- 스코프 내부에 리시버가 한 개라면 큰 문제가 되지 않는다.

### 여러 개의 리시버

- 스코프 내부에 둘 이상의 리시버가 있는 경우, 리시버를 명시적으로 나타내면 좋다.

```kotlin
class Node(val name: String) {

   fun makeChild(childName: String) =
       create("$name.$childName")
           .apply { print("Created ${name}") }

   fun create(name: String): Node? = Node(name)
} 

fun main() {
   val node = Node("parent")
   node.makeChild("child")
}

// 결과는 예상과 다르게 "Created parent"가 출력된다.
```

- 위 코드에 리시버를 명시적으로 붙이면 아래와 같다.

```kotlin
class Node(val name: String) {

   fun makeChild(childName: String) =
       create("$name.$childName")
           .apply { print("Created ${this?.name}") }

   fun create(name: String): Node? = Node(name)
}

fun main() {
    val node = Node("parent")
    node.makeChild("child") 
    // Prints: Created parent.child
}
```

- 사실 이는 apply의 잘못된 사용이다. also를 사용하면 명시적으로 리시버를 지정하게된다. 일반적으로 also또는 let을 사용하는 것이 nullable 값을 처리할 때 훨씬 좋은 선택지다.

```kotlin
class Node(val name: String) {

   fun makeChild(childName: String) =
       create("$name.$childName")
           .also { print("Created ${it?.name}") }

   fun create(name: String): Node? = Node(name)
}
```

- 리시버가 명확하지 않다면 명시적으로 리시버를 적어서 이를 명확하게 하자.
- 레이블 없이 리시버를 사용하면, 가장 가까운 리시버를 의미한다. 외부에 있는 리시버를 사용하려면, 레이블을 사용해야 한다.

```kotlin
class Node(val name: String) {

    fun makeChild(childName: String) =
        create("$name.$childName").apply { 
           print("Created ${this?.name} in "+
               " ${this@Node.name}") 
        }

    fun create(name: String): Node? = Node(name)
}

fun main() {
    val node = Node("parent")
    node.makeChild("child") 
    // Created parent.child in parent
}
```

- 이렇게하면 어떤 리시버를 활용하는지 의미도 명확헤지고 코드 안전성, 코드 가독성도 향상된다.

### DSL 마커

### 정리

- 짧게 적을 수 있다는 이유만으로 리시버를 제거하지 말자.
- 여러 개의 리시버가 있는 상황 등에는 리시버를 명시적으로 적어주는 것이 좋다. 
- 리시버를 명시적으로 지정하면 어떤 리시버의 함수인지 명확하게 알 수 있으므로 가독성이 향상된다.

## 아이템 16: 프로퍼티는 동작이 아니라 상태를 나타내야 한다.

- 자바의 필드와 코틀린의 프로퍼티는 비슷해보이지만 사실 완전히 다른 개념이다.
- 코틀린의 프로퍼티는 사용자 정의 세터와 게터를 가질 수 있다.

```kotlin
var name: String? = null
   get() = field?.toUpperCase()
   set(value) {
       if(!value.isNullOrBlank()) {
           field = value
       }
   }

```

- backing field

  - field라는 예약어는 프로퍼티의 데이터를 저장해두는 백킹 필드에 대한 레퍼런스다.
  - 이러한 필드는 세터와 게터의 디폴트 구현에 사용되므로 따로 만들지 않아도 디폴트로 생성된다.
  - val일 경우 field가 만들어지지 않는다.

- 파생 프로퍼티

  - var를 사용해서 만든 읽고 쓸 수 있는 프로퍼티는 게터와 세터를 정의할 수 있다.
  - 이런 프로퍼티를 파생 프로퍼티라고하고 자주 사용된다.

- 프로퍼티는 필드가 필요 없다. 오히러 프로퍼티는 개념적으로 접근자를 나타낸다. 따라서 인터페이스에도 프로퍼티를 정의할 수 있다.

- 프로퍼티는 함수다.

  - 프로퍼티는 본질적으로 함수이기 때문에 확장 프로퍼티를 만들 수 있다.

  - ```kotlin
    val Context.preferences: SharedPreferences
       get() = PreferenceManager
           .getDefaultSharedPreferences(this)
    
    val Context.inflater: LayoutInflater
       get() = getSystemService(
           Context.LAYOUT_INFLATER_SERVICE) as LayoutInflater
    
    val Context.notificationManager: NotificationManager
       get() = getSystemService(Context.NOTIFICATION_SERVICE) 
           as NotificationManager
    ```

  - 하지만 프로퍼티에서 아래와 같이 알고리즘의 동작을 나타내는것은 좋지않다.

  - ```kotlin
    // DON’T DO THIS!
    val Tree<Int>.sum: Int
       get() = when (this) {
           is Leaf -> value
           is Node -> left.sum + right.sum
       }
    ```

  - sum은 모든 요소를 반복하므로 컬렉션이 크다면 많은 계산량이 필요하다. 사용자는 게터에 이런 계산이 필요하다고 생각하지 않기 때문에 이렁 경우엔 함수로 빼야 한다.

  - ```kotlin
    fun Tree<Int>.sum(): Int = when (this) {
       is Leaf -> value
       is Node -> left.sum() + right.sum()
    }
    ```

- 원칙적으로 프로퍼티는 상태를 나타내거나 설정하기 위한 목적으로만 사용하는 것이 좋고 다른 로직 등을 포함하지 않아야 한다.

- 어떤것을 프로퍼티로 해야하는지 판단할 수 있는 간단한 질문

  - "이 프로퍼티를 함수로 정의할 경우 접두사로 get또는 set을 붙일 것인가?" 만약 아니라면 이를 프로퍼티로 만드는 것은 좋지 않다.

- 구체적으로 프로퍼티 대신 함수를 사용하는 것이 좋은 경우

  - 연산 비용이 높거나 복잡도가 O(1)보다 큰 경우
  - 비즈니스 로직을 포함하는 경우
  - 멱등적이지 않은 경우
  - 변환의 경우 (Int.toDouble()같은 걸 사용할 때)
  - 게터에서 프로퍼티의 상태 변경이 일어나는 경우

- 많은 사람들은 프로퍼티는 상태 집합을 나타내고 함수는 행동을 나타낸다고 생각한다.

## 아이템 17: 이름 있는 아규먼트를 사용하라

- 코드에서 아규먼트의 의미가 명확하지 않다면 이름 있는 아규먼트를 사용하자!

```kotlin
val text = (1..10).joinToString("|")

val text = (1..10).joinToString(separator = "|")

val separator = "|"
val text = (1..10).joinToString(separator)
```

### 이름 있는 아규먼트는 언제 사용해야 할까?

- 이름 있는 아규먼트의 장점
  - 이름을 기반으로 값이 무엇을 나타내는지 알 수 있다.
  - 파라미터 입력 순서와 상관 없으므로 안전하다.
- 아래와 같은 경우 이름 있는 아규먼트를 추천한다.
  - 디폴트 아규먼트의 경우
  - 같은 타입의 파라미터가 많은 경우
  - 함수 타입의 파라미터가 있는 경우 (마지막 경우 제외)

### 디폴트 아규먼트의 경우

- 프로퍼티가 디폴트 아규먼트를 가질 경우 항상 이름을 붙여서 사용하는 것이 좋다.

### 같은 타입의 파라미터가 많은 경우

- 파라미터에 같은 타입이 있다면 순서를 잘못 입력했을 때 문제를 찾아내기 어려울 수 있다. 이러한 함수가 있다면 이름 있는 아규먼트를 사용하는게 좋다.

```kotlin
sendEmail(
   to = "contact@kt.academy",
   message = "Hello, ..."
)
```

### 함수 타입 파라미터

- 일반적으로 함수 타입 파라미터는 마지막 위치에 배치하는 것이 좋다.
- 그 밖의 모든 함수 타입 아규먼트는 이름 있는 아규먼트를 사용하는 것이 좋다.

```kotlin
val view = linearLayout {
   text("Click below")
   button({ /* 1 */ }, { /* 2 */ })
}

// 아래가 나음

val view = linearLayout {
   text("Click below")
   button(onClick = { /* 1 */ }) {
      /* 2 */
   }
}
```

```kotlin
fun call(before: ()->Unit = {}, after: ()->Unit = {}){
   before()
   print("Middle")
   after()
}

call({ print("CALL") }) // CALLMiddle
call { print("CALL") }  // MiddleCALL

//아래가 나음 

call(before = { print("CALL") }) // CALLMiddle
call(after = { print("CALL") })  // MiddleCALL

```

```java
//java

observable.getUsers()
       .subscribe((List<User> users) -> { // onNext
           // ...
       }, (Throwable throwable) -> { // onError
           // ...
       }, () -> { // onCompleted
           // ...
       });
```

```kotlin
//kotlin

observable.getUsers()
   .subscribeBy(
       onNext = { users: List<User> ->
           // ...
       },
       onError = { throwable: Throwable ->
           // ...
       },
       onCompleted = {
           // ...
       })
```



### 정리

- 이름 있는 아규먼트는 디폴트 값들을 생략할때만 유용한게 아니라 가독성과 코드의 안전성도 향상시킬 수 있다.
- 함수에 같은 타입의 파라미터가 여러개 있는 경우, 함수 타입의 파라미터가 있는 경우, 옵션 파라미터가 있는 경우에는 이름 있는 아규먼트를 활용하는 것이 좋다.



## 아이템 18: 코딩 컨벤션을 지켜라
