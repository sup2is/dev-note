



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

- 코딩컨벤션을 지켜야하는 이유
  - 어떤 프로젝트를 접해도 쉽게 이해할 수 있다.
  - 다른 외부 개발자도 프로젝트의 코드를 쉽게 이해할 수 있다.
  - 다른 개발자도 코드의 작동 방식을 쉽게 추측할 수 있다.
  - 코드를 병합하고, 한 프로젝트의 코드 일부를 다른 코드로 이동하는 것이 쉽다.
  - [https://kotlinlang.org/docs/coding-conventions.html](https://kotlinlang.org/docs/coding-conventions.html)
- 컨벤션을 지켜주는 도구
  - Intellij 포매터
  - ktlint



# #3 재사용성

- 프로그래밍 언어의 핵심 특징은 재사용성이다.

## 아이템 19: knowledge를 반복하여 사용하지 말라

- "프로젝트에서 이미 있던 코드를 복사해서 붙여넣고 있다면 무언가가 잘못된 것이다."

### knowledge

- knowledge: 의도적인 정보, 프로젝트를 진행할 때 정의한 모든 것
- knowledge의 종류: 알고리즘 작동 방식, UI의 형태 등등 우리가 원하는 결과 등이 모두 의도적인 정보이고 knowledge다.
- 프로그램에서 중요한 knowledge
  - 로직: 프로그램이 어떠한 식으로 동작하는지와 프로그램이 어떻게 보이는지
  - 공통 알고리즘: 원하는 동작을 하기 위한 알고리즘



### 모든 것은 변화한다

- 프로그래밍에서 유일하게 유지되는 것은 '변화한다는 속성' 이다.
- 모든것은 변화하고 우리는 이에 대비해야 한다. 변화할 때 가장 큰 적은 knowledge가 반복되어 있다는 부분이다.
- knowledge의 반복은 확장성을 막고 쉽게 깨지게 만든다.



### 언제 코드를 반복해도 될까?

- 반대로 추출을 통해 knowledge 반복을 줄이면 안되는 상황이 있다.
- 독립적인 코드, 비즈니스 규칙이 다른 곳에서 왔다면 변경을 더 어렵게 만들 수 있다.
- 잘못된 코드 추출로부터 우리를 보호할 수 있는 규칙 -> 단일 책임 원칙



### 단일 책임 원칙

- 코드를 추출해도 되는지를 확인할 수 있는 원칙
- "클래스를 변경하는 이유는 단 한가지여야 한다."
- 단일 책임 원칙이 알려주는 두 가지 사실
  - 서로 다른 곳에서 사용하는 knowledge는 독립적으로 변경할 가능성이 많다. 따라서 비슷한 처리를 하더라도 완전히 다른 knowledge로 취급하는 것이 좋다.
  - 다른 knowledge는 분리해두는 것이 좋다. 그렇지 않으면 재사용해서는 안되는 부분을 재사용하려는 유혹이 발생할 수 있다.



### 정리

- 모든 것은 변화한다. 공통 knowledge가 있다면 이를 추출해서 이러한 변화에 대비해야 한다.
- 여러 요소에 비슷한 부분이 있는 경우, 변경이 필요할 때 실수가 발생할 수 있어서 이런 부분을 추출하는 것이 좋다. 
- 의도하지 않은 수정을 피하려면 또는 다른곳에서 조작하는 부분이 있다면 분리해서 사용하는 것이 좋다.
- 극단적인 것은 언제나 좋지 않다. 항상 균형이 중요하다.
- 어떤것을 추출해야 할지 결정하기 어려울 수 있다. 이는 수많은 시간과 많은 연습이 필요하다.



## 아이템 20: 일반적인 알고리즘을 반복해서 구현하지 말라

- 수학적인 연산, 수집 처리처럼 별도의 모듈 라이브러리로 분리할 수 있는 부분같은 알고리즘을 여러번 반복해서 구현한다.
- 반복이 아니라 이미 있는 것을 화용하면 단순하게 코드가 짧아진다는 것 이외에도 다양한 장점이 있다.
  - 코드 작성 속도가 빨라진다. 호출을 한 번 하는 것이 알고리즘을 만드는 것보다 빠르다.
  - 구현을 따로 읽지 않아도, 함수의 이름 등만 보고 무엇을 하는지 확실하게 알 수 있다.
  - 직접 구현할 때 발생할 수 있는 실수를 줄일 수 있다.
  - 제작자들이 한 번 최적화하면 이러한 함수를 활용하는 모든 곳이 최적화의 혜택을 받을 수 있다.

### 표준 라이브러리 살펴보기

- stdlib 함수들을 하나씩 살펴볼 것

```kotlin
override fun saveCallResult(item: SourceResponse) {
   var sourceList = ArrayList<SourceEntity>()
   item.sources.forEach {
       var sourceEntity = SourceEntity()
       sourceEntity.id = it.id
       sourceEntity.category = it.category
       sourceEntity.country = it.country
       sourceEntity.description = it.description
       sourceList.add(sourceEntity)
   }
   db.insertSources(sourceList)
}
```

- 동작은 하지만 위 코드는 자바빈 패턴을 갖고 있다. map과 apply를 사용해서 아래와 같이 변경하는 것이 좋다.

```kotlin
override fun saveCallResult(item: SourceResponse) {
   val sourceEntries = item.sources.map(::sourceToEntry)
   db.insertSources(sourceEntries)
}

private fun sourceToEntry(source: Source) = SourceEntity()
    .apply {
        id = source.id
        category = source.category
        country = source.country
        description = source.description
    }
```



### 나만의 유틸리티 구현하기

- 상황에 따라서 표준 라이브러리에 없는 알고리즘이 필요할 수도 있다.
- 널리 알려진 추상화일 경우 범용 유틸리티 함수로 정의하는 것이 좋다. (누가봐도 이상하지 않아야함)
- 여러번 사용되지 않더라도 온전히 추상화로 빼낼 수 있다면 범용 유리틸리티 함수로 빼자.

### 정리

- 일반적인 알고리즘을 반복해서 만들지 말자.
- 대부분 stdlib에 이미 정의되어 있을 가능성이 높기 때문에 stdlib를 공부해두면 좋다.
- stdlib에 없는 일반적인 알고리즘이 필요하거나, 특정 알고리즘을 반복해서 사용해야 하는 경우에는 프로젝트 내부에 직접 정의하자. 일반적으로 이런 알고리즘들은 확장 함수로 정의하는 것이 좋다.

## 아이템 21: 일반적인 프로퍼티 패턴은 프로퍼티 위임으로 만들어라

- 프로퍼티 위임

  - 코틀린은 코드 재사용과 관련해서 프로퍼티 위임이라는 새로운 기능을 제공한다.

  - 프로퍼티 위임을 사용하면 일반적인 프로퍼티의 행위를 추출해서 재사용할 수 있다.

  - 대표적인 예로 지연 프로퍼티가 있다. lazy 프로퍼티는 이후에 처음 사용하는 요청이 들어올 때 초기화되는 프로퍼티를 말한다.

  - ```kotlin
    val value by lazy { createValue() }
    ```

  - 프로퍼티 위임을 통해 observable 패턴도 쉽게 만들 수 있다.

  - ```kotlin
    var items: List<Item> by 
       Delegates.observable(listOf()) { _, _, _ ->
           notifyDataSetChanged()
       }
    
    var key: String? by 
       Delegates.observable(null) { _, old, new ->
           Log.e("key changed from $old to $new")
       }
    ```

- 코틀린 stdlib에서 다음과 같은 프로퍼티 델리게이터를 알아두면 좋다.

  - lazy
  - Delegates.observable
  - Delegates.vetoable
  - Delegates.notNull

### 정리

- 프로퍼티 델리게이트는 프로퍼티와 관련된 조작을 할 수 있으며, 컨텍스트와 관련된 대부분의 정보를 갖는다. 이러한 특징으로 인해서 다양한 프로퍼티의 동작을 추출해서 재사용할 수 있다.
- 프로퍼티 위임은 프로퍼티 패턴을 추출하는 일반적인 방법이라 많이 사용되고 있다. 따라서 코틀린 개발자라면 프로퍼티 위임이라는 강력한 도구와 관련된 내용을 잘 알고 있어야 한다. 이를 잘 알면 일반적인 패턴을 추출하거나 더 좋은 API를 만들 때 활용할 수 있을 것이다.

## 아이템 22: 일반적인 알고리즘을 구현할 때 제네릭을 사용하라

- 제네릭 함수

  - 아규먼트로 함수에 값을 전달할 수 있는 것처럼 타입 아규먼트를 사용하면 함수에 타입을 전달할 수 있다.

  - 타입 아규먼트를 사용하는 함수를 제네릭 함수라고 한다.

  - ```kotlin
    inline fun <T> Iterable<T>.filter(
        predicate: (T) -> Boolean
    ): List<T> {
        val destination = ArrayList<T>()
        for (element in this) { 
            if (predicate(element)) {
               destination.add(element)
            }
        }
        return destination   
    }
    
    ```

  - 타입 파라미터는 컴파일러에 타입과 관련된 정보를 제공하여 컴파일러가 타입을 조금이라도 더 정확하게 추측할 수 있게 해준다. 따라서 프로그램이 조금 더안전해지고 개발자는 프로그래밍이 편해진다.

  - 컴파일타임에서는 강제되지만 런타임에서 타입 정보는 삭제된다.



### 제네릭 제한

- 타입 파라미터의 중요한 기능 중 하나는 구체적인 타입의 서브타입만 사용하게 타입을 제한하는 것이다.

```kotlin
fun <T : Comparable<T>> Iterable<T>.sorted(): List<T> { 
   /*...*/ 
}

fun <T, C : MutableCollection<in T>>
Iterable<T>.toCollection(destination: C): C {
   /*...*/
}

class ListAdapter<T: ItemAdaper>(/*...*/) { /*...*/ }
```

- Any를 사용해서 nullable이 아닌 타입으로 제한할 수 있다.

```kotlin
inline fun <T, R : Any> Iterable<T>.mapNotNull(
   transform: (T) -> R?
): List<R> {
   return mapNotNullTo(ArrayList<R>(), transform)
}
```



### 정리

- 코틀린 자료형 시스템에서 타입 파라미터는 굉장히 중요한 부분이다.
- 일반적으로 이를 사용해서 type-safe 제네릭 알고리즘과 제네릭 객체를 구현한다.
- 타입 파라미터는 구체자료형의 서브타입을 제한할 수 있다. 이렇게하면 특정 자료형이 제공하는 메서드를 안전하게 사용할 수 있다.

## 아이템 23: 타입 파라미터의 섀도잉을 피하라

- 섀도잉

  - 프로퍼티와 파라미터가 같은 이름을 가질 수 있다.

  - ```kotlin
    class Forest(val name: String) {
      
       fun addTree(name: String) {
           // ...
       }
    }
    
    ```

  - 이렇게 되면 지역 파라미터가 외부 스코프에 있는 프로퍼티를 가린다. 이를 섀도잉이라고 한다.

  - 이게 만약 클래스 타입 파라미터와 함수 타입 파라미터 사이에서 발생한다면 큰 문제가 된다.

  - ```kotlin
    interface Tree
    class Birch: Tree
    class Spruce: Tree
    
    class Forest<T: Tree> {
    
       fun <T: Tree> addTree(tree: T) {
           // ...
       }
    }
    ```

  - 이렇게 작성하면 Forest와  addTree의 타입 파라미터가 독립적으로 동작한다.

  - addTree가 타입 파라미터인 T를 사용하게 하는게 좋다.

  - ```kotlin
    class Forest<T: Tree> {
    
       fun addTree(tree: T) {
           // ...
       }
    }
    
    // Usage
    val forest = Forest<Birch>()
    forest.addTree(Birch())
    forest.addTree(Spruce()) // ERROR, type mismatch
    ```



### 정리

- 타입 파라미터 섀도잉을 피하자.
- 타입 파라미터 섀도잉이 발생한 코드는 이해하기 어려울 수 있다.



## 아이템 24: 제네릭 타입과 variance 한정자를 활용하라

## 아이템 25: 공통 모듈을 추출해서 여러 플랫폼에서 재사용하라



# #4 추상화 설계

- 추상화
  - 컴퓨터 과학에서 추상화는 복잡한 자료, 모듈, 시스템 등으로부터 핵심적인 개념 또는 기능을 간추려 해는 것을 말한다.
  - 단순하게 추상화는 복잡성을 숨기기 위해 사용되는 단순한 형식을 의미한다. ex: 인터페이스

### 프로그래밍에서의 추상화

- 추상화 설계

  - 추상화를 설계한다는 것은 단순하게 모듈 또는 라이브러리로 분리한다는 의미가 아니다.

  - 함수를 정의할 때는 그 구현을 함수 시그니처 뒤에 숨기게 되는데 이것이 바로 추상화다.

  - maxOf를 아래와 같이 사용해도 아무런 문제가 없지만 기본적인 수준의 연산이 표면적으로 들어나고 이를 포현하는 능력이 굉장히 떨어진다고 할 수 있다.

  - ```kotlin
    val biggest = if (x > y) x else y
    
    val height = 
        if (minHeight > calculatedHeight) minHeight 
        else calculatedHeight
    ```



### 추상화와 자동차

- 자동차와 인터페이스
  - 자동차는 굉장히 잘 만들어낸 인터페이스다. 
  - 내부적으로는 복잡하지만 굉장히 쉽게 사용할 수 있다.
  - 추상화는 이처럼 내부적으로 일어나는 모든 것을 마법처럼 숨겨준다.
- 프로그래밍에서의 추상화의 목적
  - 복잡성을 숨기기 위해
  - 코드를 체계화하기 위해
  - 만드는 사람에게 변화의 자유를 주기 위해



## 아이템 26: 함수 내부의 추상화 레벨을 통일하라

- 계층이 잘 분리되었을때의 장점?
  - 어떤 계층에서 작업할 때 그 아래의 계층은 이미 완성되어 있으므로 해당 계층만 생각하면 된다.
  - 즉 전체를 이해할 필요가 없다.

### 추상화 레벨

- 일반적으로 높은 레벨일 수록 프로세서부터 멀어진다.
  - ex: C는 언매니지드언어, java는 매니지드언어

### 추상화 레벨 통일

- 함수
  - 추상화를 위한 가장 기본적인 도구
- 추상화 레벨 통일 원칙
  - 컴퓨터 과학이 높은 레벨과 낮은 레벨을 확실하게 구분하고 있는 것처럼 함수도 높은 레벨과 낮은 레벨을 구분해서 사용해야 한다는 원칙이 있다. 이를 추상화 레벨 통일 원칙이라 한다.
- 이런것도 추상화의 한 종류다.

```kotlin
// 추상화 없이 한곳에 코드 때려 박기
class CoffeeMachine {

   fun makeCoffee() {
       // Declarations of hundreds of variables
       // Complex logic to coordinate everything
       // with many low-level optimizations
   }
}

// 코드를 의미있는 함수로 분리
class CoffeeMachine {

   fun makeCoffee() {
       boilWater()
       brewCoffee()
       pourCoffee()
       pourMilk()
   }
  
   private fun boilWater() {
       // ...
   }
  
   private fun brewCoffee() {
       // ...
   }

   private fun pourCoffee() {
       // ...
   }
  
   private fun pourMilk() {
       // ...
   }
}
```

- 위 처럼 함수는 간단해야한다. 
  - '함수는 작아야 하며, 최소한의 책임만을 가져야 한다' - 로버트 마틴 (클린 코드)
  - 재사용과 테스트가 쉬워진다.



### 프로그램 아키텍처의 추상 레벨

- 추상화를 구분하는 이유
  - 추상화를 구분하는 이유는 서브시스템의 세부 사항을 숨김으로써 상호 운영성과 플랫폼 독립성을 얻기 위함이다.
- 계층화가 잘 된 프로젝트는 어떤 계층 위치에서 코드를 보아도 일관적인 관점을 얻을 수 있다.



### 정리

- 별도의 추상화 계층을 만드는 것은 프로그래밍에서 일반적으로 사용되는 개념이다.
- 이는 knowledge를 체계화하고, 서브시스템의 세부 사항을 숨김으로써 상호 운영성과 플랫폼 독립성을 얻게한다.
- 함수, 클래스, 모듈 등의 다양한 방식을 통해서 추상화를 분리한다. 이때 각각의 레이어가 너무 커지는 것은 좋지 않다.
- 작고 최소한의 책임만 갖는 함수가 이해하기 쉽다.
- 추상화 레벨은 구체적인 동작, 프로세서, 입출력과 가까울수록 낮을 레벨이라고 표현한다.
- 낮은 추상화 계층에서는 높은 계층에서의 사용하는 요소를 만든다.



## 아이템 27: 변화로부터 코드를 보호하려면 추상화를 사용하라

- 추상화의 장점
  - 함수와 클래스 등의 추상화로 실질적인 코르를 숨기면 사용자가 세부 사항을 알지 못해도 괜찮다.

### 상수

- 리터럴을 상수로 빼기
  - 코드에 반복적으로 등장하는 리터럴은 문제가 된다.
  - 리터럴을 상수 프로퍼티로 변경하면 해당 값에 의미있는 값을 붙일 수 있고 상수의 값을 변경해야 할 때 훨씬 쉽게 변경할 수 있다.

### 함수

- 자주 사용되는 기능을 함수로 빼기
  - 함수는 추상화를 표현하는 수단이며 함수 시그니처는 이 함수가 어떤 추상화를 표현하고 있는지 알려준다.
  - 의미있는 이름이 굉장히 중요하다.

### 클래스

- 클래스가 함수보다 강력한 이유
  - 많은 함수를 가질 수 있다.
  - 상태를 가질 수 있다.
  - 클래스 생성을 위임할 수 있다.
  - mock객체를 활용한 테스트

### 인터페이스

- 인터페이스로 표현하기
  - 인터페이스에 객체를 숨김으로써 실질적인 구현을 추상화하고 사용자가 추상화된 것에만 의존하게 만들 수 있다.



### ID 만들기

### 추상화가 주는 자유

- 추상화를 하는 몇가지 방법
  - 상수로 추출한다.
  - 동작을 함수로 래핑한다.
  - 함수를 클래스로 래핑한다.
  - 인터페이스 뒤에 클래스를 숨긴다.
  - 보편적인 객체를 특수한 객체로 래핑한다.
- 추상화를 구현할 때의 여러 도구
  - 제네릭 타입 파라미터를 사용한다.
  - 내부 클래스를 추출한다.
  - 생성을 제한한다.
- 추상화의 단점
  - 자유를주지만 코드를 이해하고 수정하기 어렵게 만든다.

### 추상화의 문제

- 어떤 방식으로 추상화를 하려면 코드를 읽는 사람이 해당 개념을 배우고 잘 이해해야 한다.
- 추상화도 비용이 발생하기 때문에 극단적으로 모든것을 추상화하면 득보다 실이 많아진다.



### 어떻게 균형을 맞춰야 할까?

- 추상화 정도의 최적의 답을 찾는 방법
  - 팀의 크기
  - 팀의 경험
  - 프로젝트의 크기
  - 특징 세트
  - 도메인 지식
- 몇가지 규칙들
  - 많은 개발자가 참여하는 프로젝트는 이후에 객체 생성과 사용 방법을 변경하기 어렵다. 따라서 추상화를 사용해서 모듈과 부분을 분리하는 것이 좋다.
  - 의존성 주입 프레임워크를 사용하면, 생성이 얼마나 복잡한지는 신경쓰지 않아도 된다.
  - 테스트를 하거나 다른 애플리케이션을 기반으로 새로운 애플리케이션을 만든다면 추상화를 사용하는 것이 좋다.
  - 프로젝트가 작고 실험적이라면 추상화를 하지 않고도 직접 변경해도 괜찮다. 문제가 발생했다면 최대한 빨리 직접 변경하면 된다.
- 항상 무언가 변화할 수 있다고 생각하는 것이 좋다.



## 정리

- 추상화는 단순하게 중복성을 제거해서 코드를 구성하기 위한 것이 아니다.
- 추상화는 코드를 변경해야 할 때 도움이 된다. 따라서 추상화를 사용하는 것은 굉장히 어렵지만, 이를 배우고 이해해야 한다.
- 추상화를 사용할 때의 장점과 단점을 모두 이해하고 프로젝트 내에서 그 균형을 찾아야 한다. 추상화가 너무 많거나 너무 적은 상황 모두 좋은 상황이 아니다.

## 아이템 28: API 안전성을 확인하라

- 작성자가 API 또는 API의 일부가 불안정하다면 이를 명확하게 알려줘야 한다.
- 시멘틱 버저닝
  - MAJOR: 호환되지 않는 수준의 API 변경
  - MINOR: 이전 변경과 호환되는 기능 추가
  - PATCH: 간단한 버그 수정



### 정리

- 사용자는 API의 안전성에 대해 알아야 한다.
- 모듈과 라이브러리를 만드는 사람과 이를 사용하는 사람들 사이에 커뮤니케이션이 중요하다.
- 안정적인 API에 변경을 가할 때는 사용자가 적응할 충분한 시간을 줘야 한다.

## 아이템 29: 외부 API를 랩(wrap)해서 사용하라

- 어쩔수 없다면?
  - 불안정한 API를 과도하게 사용하는 것은 굉장히 위험하다.
  - 어쩔수 없다면 최대한 이런 API를 로직과 직접 결합시키지 않는 것이 좋다.
  - 외부 API를 wrap해서 사용해야한다.
- 외부 API wrapping하면 아래와 같은 자유와 안전성을 얻을 수 있다.
  - 문제가 있다면 wrapper를 변경하면 되므로 API 변경에 쉽게 대응할 수 있다.
  - 프로젝트의 스타일에 맞춰서 API의 형태를 조정할 수 있다.
  - 특정 라이브러리에서 문제가 발생하면 래퍼를 수정해서 다른 라이브러리를 사용하도록 코드를 쉽게 변경할 수 있다.
  - 필요한 경우 쉽게 동작을 추가하거나 수정할 수 있다.
- 단점
  - 래퍼를 따로 정의해야 한다.
  - 대른 개발자가 프로젝트를 다루 때 어떤 래퍼들이 있는지 따로 확인해야 한다.
- API가 얼마나 안정적인지 확인할 수 있는 가장 기본적인 휴리스틱은 버전 번호와 사용자 수다. 사용자가 많을 수록 안정적이다.

## 아이템 30: 요소의 가시성을 최소화하라

- 요소의 가시성을 최소화해야 하는 이유
  - 이미 공개된 api의 변경은 이 코드를 사용하고 있는 모든 부분이 영향을 받는다. 따라서 최소화하는게 좋다.
  - 클래스의 변경을 쉽게 추적할 수 있다.
  - 상태 변경은 동시성과 연관이 있다. 가시성을 제한할 수록 병렬 프로그래밍에서 안전해진다.
- 일반적으로 코틀린에서는 구체 접근자(setter)의 가시성을 제한해서 모든 프로퍼티를 캡슐화하는 것이 좋다.

### 가시성 한정자 사용하기

- 기본적으로 클래스와 요소를 외부에 노출할 필요가 없다면 가시성을 제한해서 외부에서 접근할 수 없게 만드는 것이 좋다.
- 클래스 멤버의 가시성 한정자
  - public
  - private
  - protected
  - internal: 모듈 내부에서만 볼 수 있다.
- 톱레벨 요소의 가시성 한정자
  - public
  - private
  - internal: 모듈 내부에서만 볼 수 있다.
- 모듈 != 패키지
  - 코틀린에서 모듈이란 함께 컴파일되는 코틀린 소스를 의미한다.
- 한정자 지정법
  - 모듈이 다른 모듈에 의해서 사용될 가능성이 있다면 internal을 사용해서 공개하고 싶지 않은 요소 숨기기
  - 요소가 상속을 위해 설계됐다면 protected 사용하기
  - 동일한 파일 또는 클래스에서만 요소를 사용하게 만들고 싶다면  private 사용하기
- datamodel, dto에는?
  - 이러한 규칙은 데이터를 저장하도록 설계된 data클래스, dto에는 적용하지 않는 것이 좋다.
  - 데이터를 저장하도록 설계된 클래스는 숨길 이유가 없다.



### 정리

- 가시성은 최대한 제한적인 것이 좋다.
  - 인터페이스가 작을수록 이를 공부하고 유지하는 것이 쉽다.
  - 최대한 제한이 되어 있어야 변경하기 쉽다.
  - 클래스의 상태를 나타내는 프로퍼티가 노출되어 있다면, 클래스가 자신의 상태를 책임질 수 없다.
  - 가시성이 제한되면 API의 변경을 쉽게 추적할 수 있다.



## 아이템 31: 문서로 규약을 정의하라

### 규약

- 규약을 정의하면 양쪽 모두에게 좋다.
  - 개발자는 클래스가 어떻게 사용될지 걱정하지 않아도 된다.
  - 사용자는 클래스가 내부적으로 어떻게 구현되어 있는지를 걱정하지 않아도 된다.

### 규약 정의하기

- 대표적인 몇가지
  - 이름
  - 주석과 문서
  - 타입

### 주석을 써야 할까?

- 간단명료하면 쓰지말고 함수이름으로 뺄 수 있으면 함수 이름으로 빼서 주석을 쓰지 말자.
- 하지만 필요한 경우 주석으로 규약을 꼭 작성하자.

### KDoc 형식

- KDoc이란?
  - 주석으로 함수를 문서화할 때 사용되는 공식적인 형식
- KDoc 주석의 구조
  - 첫 번째 부분은 요소에 대한 요약 설명이다.
  - 두 번째 부분은 상세 설명이다.
  - 이어지는 줄은 모두 태그로 시작한다. 이러한 태그는 추가적인 설명을 위해 사용된다.
  - 사용할 수 있는 태그
    - @param
    - @return 
    - ...

### 타입 시스템과 예측

### 조금씩 달라지는 세부사항

### 정리

- 요소, 특히 외부 API를 구현할 떄는 규약을 잘 정의해야 한다.
- 이러한 규약은 이름, 문서, 주석, 타입을 통해 구현할 수 있다.
- 규약은 사용자가 객체를 사용하는 방법을 쉽게 이해하는 등 요소를 쉽게 예측할 수 있게 해준다.



## 아이템 32: 추상화 규약을 지켜라

- 규약은 개발자들의 단순한 합이라서 한쪽에서 규약을 위반할 수도 있다.
- 규약은 보증과 같다.

### 상속된 규약

- 클래스를 상속하거나, 다른 라이버르리의 인터페이스를 구현할 때는 규약을 반드시 지켜야 한다.

### 정리

- 프로그램을 안정적으로 유지하고 싶다면, 규약을 지키자.
- 규약을 깰 수밖에 없다면 이를 잘 문서화하자.









# #5 객체 생성

## 아이템 33: 생성자 대신 팩토리 함수를 사용하라

- 팩토리 함수
  - 생성자가 객체를 만들 수 있는 유일한 방법이 아니다.
  - 생성자의 역할을 대신 해주는 함수를 팩토리 함수라고 한다.
- 팩토리 함수를 사용할 때의 장점
  - 생성자와 다르게 함수에 이름을 붙일 수 있다.
  - 생성자와 다르게 함수가 원하는 형태의 타입을 리턴할 수 있다.
  - 생성자와 다르게 호출될 때마다 새 객체를 만들 필요가 없다.
  - 팩토리 함수는 아직 존재하지 않는 객체를 리턴할 수도 있다.
  - 객체 외부에 팩토리 함수를 만들면 그 가시성을 원하는 대로 제어할 수 있다.
  - 팩토리 함수는 인라인으로 만들 수 있고 그 파라미터들을 reified로 만들 수 있다.
  - 팩토리 함수는 생성자로 만들기 복잡한 객체도 만들어 낼 수 있다.
  - 생성자는 즉시 슈퍼클래스 또는 기본 생성자를 호출해야하지만 팩토리함수는 원하는 때에 생성자를 호출할 수 있다.
- 팩토리 함수의 종류
  - companion 객체 팩토리 함수
  - 확장 팩토리 함수
  - 톱레벨 팩토리 함수
  - 가짜 생성자
  - 팩토리 클래스의 메서드



### Companion 객체 팩토리 함수

- 팩토리 함수를 정의하는 가장 일반적인 방법

```kotlin
class MyLinkedList<T>(
   val head: T, 
   val tail: MyLinkedList<T>?
) {

   companion object {
      fun <T> of(vararg elements: T): MyLinkedList<T>? {
          /*...*/ 
      }
   }
}

// Usage
val list = MyLinkedList.of(1, 2)
```

- of 이외에도 많이 사용되는 이름들

  - from: 파라미터를 하나 받고, 같은 타입의 인스턴스 하나를 리턴하는 타입 변환 함수

    - ```kotlin
      val date: Date = Date.from(instant)
      ```

  - of: 파라미터를 여러 개 받고, 이를 통합해서 인스턴스를 만들어주는 함수

    - ```kotlin
      val faceCards: Set<Rank> = EnumSet.of(JACK, QUEEN, KING)
      ```

  - valueOf: from 또는 of와 비슷한 기능을 하면서도 의미를 조금 더 쉽게 읽을 수 있게 이름을 붙인 함수

    - ```kotlin
      val prime: BigInteger = BigInteger.valueOf(Integer.MAX_VALUE)
      ```

  - instance 또는 getInstance: 싱글턴

  - createInstance 또는 newInstance: 싱글턴이 아닌 호출때마다 새로운 인스턴스를 만들어서 리턴

  - getType: getInstance처럼 동작하지만 팩토리 함수가 다른 클래스에 있을 때 사용하는 이름

    - ```kotlin
      val fs: FileStore = Files.getFileStore(path)
      ```

  - newType: newInstance처럼 동작하지만 팩토리 함수가 다른 클래스에 있을 때 사용하는 이름

    - ```kotlin
      val br: BufferedReader = Files.newBufferedReader(path)
      ```

- companion 객체는 많은 기능이 있다.

  - companion 객체는 인터페이스를 구현할 수 있고 객체도 상속 가능하다.

  - ```kotlin
    abstract class ActivityFactory {
       abstract fun getIntent(context: Context): Intent
    
       fun start(context: Context) {
           val intent = getIntent(context)
           context.startActivity(intent)
       }
    
       fun startForResult(activity: Activity, requestCode: 
    Int) {
           val intent = getIntent(activity)
           activity.startActivityForResult(intent, 
    requestCode)
       }
    }
    
    class MainActivity : AppCompatActivity() {
       //...
    
       companion object: ActivityFactory() {
           override fun getIntent(context: Context): Intent =
               Intent(context, MainActivity::class.java)
       }
    }
    
    // Usage
    val intent = MainActivity.getIntent(context)
    MainActivity.start(context)
    MainActivity.startForResult(activity, requestCode)
    ```

  - 추상 companion 객체 팩토리는 값을 가질 수 있어서 캐싱을 구현하거나 테스트를 위한 가짜 객체 생성을 할 수 있다.

  - companion 객체를 제대로 사용하는 곳을 보고싶다면 코틀린 팀 제품의 구현을 확인해보자.

    - ex: 코루틴

### 확장 팩토리 함수

- 이미 companion 객체가 있고 수정이 불가능할 때는 다른 파일에 확장 함수를 적용하자.

- ```kotlin
  interface Tool {
     companion object { /*...*/ }
  }
  
  Tool.createBigTool()
  ```



### 톱레벨 팩토리 함수

- 객체를 만드는 흔한 방법중 하나로 톱레벨 팩토리 함수를 이용하는 방법이 있다.
  - listOf, setOf, mapOf
- 톱레벨 함수를 만들 때는 함수의 이름을 신중하게 생각해서 잘 지정해야 한다.



### 가짜 생성자

- 일반적인 사용의 관점에서 대문자로 시작하는지 아닌지는 생성자와 함수를 구분하는 기준이다.

  - List, MutableList는 인터페이스라서 생성자를 가질 수 없다. 하지만 코틀린 1.1부터 아래와 같은 톱레벨 함수가 추가됐다.

  - ```kotlin
    List(4) { "User$it" } // [User0, User1, User2, User3]
    ```

  - ```kotlin
    public inline fun <T> List(
       size: Int, 
       init: (index: Int) -> T
    ): List<T> = MutableList(size, init)
    
    public inline fun <T> MutableList(
       size: Int, 
       init: (index: Int) -> T
    ): MutableList<T> {
       val list = ArrayList<T>(size)
       repeat(size) { index -> list.add(init(index)) }
       return list
    }
    ```

  - 이러한 톱레벨 함수는 팩토리 함수와 같은 모든 장점을 갖는다. 

  - 이러한 톱레벨 함수를 가짜 생성자라고 부른다.

- 개발자가 진짜 생성자 대신 가짜 생성자를 만드는 이유

  - 인터페이스를 위한 생성자를 만들고 싶을 때
  - reified 타입 아규먼트를 갖게 하고 싶을 때

- 가짜 생성자를 선언하는 또다른 방법

  - invoke 연산자를 갖는  companion 객체를 사용하면, 비슷한 결과를 얻을 수 있다.

  - ```kotlin
    class Tree<T> {
      
       companion object {
           operator fun <T> invoke(size: Int, generator: 
    (Int)->T): Tree<T>{
               //...
           }
       }
    }
    
    // Usage
    Tree(10) { "$it" }
    ```

  - 방법이긴하지만 비추천

- 가짜 생성자를 반드시 만들어야 하는 경우

  - 기본 생성자를 만들 수 없는 상황
  - 생성자가 제공하지 않는 기능 (reified 타입 파라미터)
  - 이외에는 진짜 생성자로 만들자.

### 팩토리 클래스의 메서드

- 팩토리 클래스의 장점
  - 팩토리 함수와 다르게 팩토리 클래스는 프로퍼티, 상태를 가질 수 있다.
  - 캐싱을 활용할 수도 있고 이전에 만든 객체를 복제해서 객체를 생성하는 방법으로 객체 생성 속도를 높일 수 있다.



### 정리

- 코틀린은 팩토리 함수를 만들 수 있는 다양한 방법들을 제공한다.
- 가짜 생성자, 톱레벨 팩토리 함수, 확장 팩토리 함수 등 일부는 신중하게 사용해야 한다.
- 팩토리 함수를 정의하는 가장 일반적인 방법은 companion 객체를 사용하는 것이다. 이 방식은 자바 정적 팩토리 메서드 패턴과 굉장히 유사하고 대부분의 개발자에게 안전하고 익숙하다.



## 아이템 34: 기본 생성자에 이름 있는 옵션 아규먼트를 사용하라

- 객체를 정의하고 생성하는 방법을 지정할 때 사용하는 가장 기본적인 방법은 기본 생성자를 사용하는 것이다.

```kotlin
class User(var name: String, var surname: String)
val user = User("Marcin", "Moskała")
```

```kotlin
class QuotationPresenter(
       private val view: QuotationView,
       private val repo: QuotationRepository
) {
   private var nextQuoteId = -1

   fun onStart() {
       onNext()
   }

   fun onNext() {
       nextQuoteId = (nextQuoteId + 1) % repo.quotesNumber
       val quote = repo.getQuote(nextQuoteId)
       view.showQuote(quote)
   }
}
```

- 프로퍼티는 초기화되기만 하면 된다.
  - nextQuoteId는 프로퍼티는 항상 -1로 초기화된다.
  - 기본 생성자로 초기화되어도, 디폴트 값을 기반으로 초기화되어도 어떻게든 초기화만 되면 큰 문제가 없다.
- 생성자와 관련된 자바 패턴들
  - 점층적 생성자 패턴
  - 빌더 패턴

### 점층적 생성자 패턴

- 점층적 생성자 패턴
  - 점층적 생성자 패턴은 '여러 가지 종류의 생성자를 사용하는' 간단한 패턴이다.

```kotlin
class Pizza {
   val size: String
   val cheese: Int
   val olives: Int
   val bacon: Int

   constructor(size: String, cheese: Int, olives: Int, 
bacon: Int) {
       this.size = size
       this.cheese = cheese
       this.olives = olives
       this.bacon = bacon
   }
   constructor(size: String, cheese: Int, olives: Int):
this(size, cheese, olives, 0)
   constructor(size: String, cheese: Int): 
this(size, cheese, 0)
   constructor(size: String): this(size, 0)
}

```

- 코틀린의 디폴트 아규먼트를 사용하면?

```kotlin
class Pizza(
       val size: String,
       val cheese: Int = 0,
       val olives: Int = 0,
       val bacon: Int = 0
)

val myFavorite = Pizza("L", olives = 3)

val myFavorite = Pizza("L", olives = 3, cheese = 1)

```

- 디폴트 아규먼트가 점층적 생성자보다 좋은 이유

  - 파라미터들의 값을 원하는 대로 지정할 수 있다.

  - 아규먼트를 원하는 순서로 지정할 수 있다.

  - 명시적으로 이름을 붙여서 아규먼트를 지정하므로 의미가 훨씬 명확하다.

  - ```kotlin
    val villagePizza = Pizza(
       size = "L", 
       cheese = 1, 
       olives = 2, 
       bacon = 3
    )
    ```



### 빌더 패턴

- 빌더 패턴의 장점
  - 파라미터에 이름을 붙일 수 있다.
  - 파라미터를 원하는 순서로 지정할 수 있다.
  - 디폴트 값을 지정할 수 있다.
- 빌더 패턴을 사용하는 것보다 이름 있는 파라미터를 사용하는 것이 좋은 이유
  - 더 짧다.
  - 더 명확하다.
  - 더 사용하기 쉽다.
  - 동시성과 관련된 문제가 없다.
- 코틀린에서는 빌더 패턴을 거의 사용하지 않는다. 하지만 그래도 사용하는 경우
  - 빌더 패턴을 사용하는 다른 언어로 작성된 라이브러리를 그대로 옮길 때
  - 디폴트 아규먼트와 DSL을 지원하지 않는 다른 언어에서 쉽게 사용할 수 있게 API를 설계할 때



### 정리

- 일반적인 프로젝트에서는 기본 생성자를 사용해 객체를 만든다.
- 코틀린에서는 점층적 생성자 패턴, 빌더 패턴을 사용하지 않는다.



## 아이템 35: 복잡한 객체를 생성하기 위한 DSL을 정의하라



# #6 클래스 설계

## 아이템 36: 상속보다는 컴포지션을 사용하라

- 상속은 굉장히 강력한 기능이다.
  - 상속은 관계가 명확하지 않을 때 사용하면 여러가지 문제가 발생할 수 있다.
  - 단순하게 코드 추출 또는 재사용을 위해 상속을 하려고 한다면 조금 더 신중하게 생각해야 한다.
  - 일반적으로 이러한 경우에는 상속보다 컴포지션을 사용하는 것이 좋다.

### 간단한 행위 재사용

```kotlin
class ProfileLoader {

  fun load() {
       // show progress
       // load profile
       // hide progress
   }
}

class ImageLoader {

   fun load() {
       // show progress
       // load image
       // hide progress
   }
}

abstract class LoaderWithProgress {

   fun load() {
       // show progress
       innerLoad()
       // hide progress
   }
  
   abstract fun innerLoad()
}

class ProfileLoader: LoaderWithProgress() {

   override fun innerLoad() {
       // load profile
   }
}

class ImageLoader: LoaderWithProgress() {

   override fun innerLoad() {
       // load image
   }
}
```

- 이러한 코드의 단점
  - 상속은 하나의 클래스만을 대상으로 할 수 있다. 상속을 사용해서 행위를 추출하다보면 많은 함수를 갖는 거대한 BaseXXX 클래스를 만들게 되고 굉장히 깊고 복잡한 계층 구조가 만들어진다.
  - 상속은 클래스의 모든 것을 가져오게 된다. 따라서 불필요한 함수를 갖는 클래스가 만들어질 수 있다. (ISP 원칙 위반)
  - 상속은 이해하기 어렵다. 일반적으로 개발자가 메서드를 읽고 메서드의 작동 방식을 이해하기 위해 슈퍼클래스를 여러번 확인해야 한다면 문제가 있는거다.
- 컴포지션이 낫다.
  - 컴포지션을 사용한다는 것은 객체를 프로퍼티로 갖고 함수를 호출하는 형태로 재사용하는 것을 의미한다.

```kotlin
class Progress {
   fun showProgress() { /* show progress */ }
   fun hideProgress() { /* hide progress */ }
}

class ProfileLoader {
   val progress = Progress()
  
   fun load() {
       progress.showProgress()
       // load profile
       progress.hideProgress()
   }
}

class ImageLoader {
   val progress = Progress()

   fun load() {
       progress.showProgress()
       // load image
       progress.hideProgress()
   }
}
```

- 이런 코드의 장점
  - 코드를 읽는 사람들이 코드의 실행을 더 명확하게 예측할 수 있다.
  - 프로그레스 바를 훨씬 자유롭게 사용할 수 있다.



### 모든 것을 가져올 수밖에 없는 상속

- 상속의 특징
  - 상속은 슈퍼클래스의 메서드, 제약, 행위 등 모든 것을 가져온다.
  - 상속은 객체의 계층구조를 나타낼 때 굉장히 좋은 도구이다.
  - 하지만 일부분을 재사용하기 위한 목적으로는 적합하지 않다.
- 일부분만 재사용하고싶다면?
  - 컴포지션 사용하기

```kotlin
abstract class Dog {
   open fun bark() { /*...*/ }
   open fun sniff() { /*...*/ }
}
```

- 로봇 강아지의 등장

```kotlin
class Labrador: Dog()

class RobotDog : Dog() {
   override fun sniff() {
       throw Error("Operation not supported")
       // Do you really want that?
   }
}
```

- ISP 위반, LSP 위반
- 컴포지션을 사용하면 이런 문제가 전혀 발생하지 않는다.



### 캡슐화를 깨는 상속

- 상속을 사용하면 내부적인 구현 방법 변경에 의해서 클래스의 캡슐화가 깨질 수 있다.

```kotlin
class CounterSet<T>: HashSet<T>() {
   var elementsAdded: Int = 0
       private set

   override fun add(element: T): Boolean {
       elementsAdded++
       return super.add(element)
   }

   override fun addAll(elements: Collection<T>): Boolean {
       elementsAdded += elements.size
       return super.addAll(elements)
   }
}
```

- HashSet을 구현하는 CounterSet. HashSet의 내부 API가 변경되면 CounterSet은 예쌍하지 못한 형태로 동작한다.
- 컴포지션을 사용하면 문제가 없다.

```kotlin
class CounterSet<T> {
   private val innerSet = HashSet<T>()
   var elementsAdded: Int = 0
       private set

   fun add(element: T) {
       elementsAdded++
       innerSet.add(element)
   }

   fun addAll(elements: Collection<T>) {
       elementsAdded += elements.size
       innerSet.addAll(elements)
   }
}

val counterList = CounterSet<String>()
counterList.addAll(listOf("A", "B", "C"))
print(counterList.elementsAdded) // 3
```

- 만약 CounterSet이 반드시 Set 타입이어야 한다면 위임 패턴을 사용하면 된다.

```kotlin
class CounterSet<T> : MutableSet<T> {
   private val innerSet = HashSet<T>()
   var elementsAdded: Int = 0
       private set

   override fun add(element: T): Boolean {
       elementsAdded++
       return innerSet.add(element)
   }

   override fun addAll(elements: Collection<T>): Boolean {
       elementsAdded += elements.size
       return innerSet.addAll(elements)
   }

   override val size: Int
       get() = innerSet.size

   override fun contains(element: T): Boolean =
           innerSet.contains(element)

   override fun containsAll(elements: Collection<T>): 
Boolean = innerSet.containsAll(elements)

   override fun isEmpty(): Boolean = innerSet.isEmpty()

   override fun iterator() =
           innerSet.iterator()

   override fun clear() =
           innerSet.clear()

   override fun remove(element: T): Boolean =
           innerSet.remove(element)

   override fun removeAll(elements: Collection<T>): 
Boolean = innerSet.removeAll(elements)

   override fun retainAll(elements: Collection<T>): 
Boolean = innerSet.retainAll(elements)
}
```

- 위임 패턴
  - 위임 패턴은 클래스가 인터페이스를 상속받게 하고 포함한 객체의 메서드를 활용해서 인터페이스에서 정의한 메서드를 구현하는 패턴이다.
  - 이렇게 구현된 메서드를 포워딩 메서드라고 한다.
- 포워딩 메서드가 너무 많다면?
  - 코틀린은 위임 패턴을 지원한다.

```kotlin
class CounterSet<T>(
   private val innerSet: MutableSet<T> = mutableSetOf()
) : MutableSet<T> by innerSet {

   var elementsAdded: Int = 0
       private set

   override fun add(element: T): Boolean {
       elementsAdded++
       return innerSet.add(element)
   }

   override fun addAll(elements: Collection<T>): Boolean {
       elementsAdded += elements.size
       return innerSet.addAll(elements)
   }
}
```

- 간단 정리
  - 다형성이 필요하다면? -> 위임 패턴
  - 상속은 캡슐화를 깰 수 있다.
  - 컴포지션을 사용하면 재사용하기 쉽고 코드는 이해하기 쉬우며 유연하다.



### 오버라이딩 제한하기

- 개발자가 상속용으로 설계되지 않은 클래스를 상속하지 못하게하려면 final을 사용하면 된다.

- 만약 어떤 이유로는 상속을 허용하지만 메서드는 오버라이드하지 못하게 만들고 싶은 경우가 있다.

  - 이럴때는 메서드에 open 키워드를 사용한다.

  ```kotlin
  open class Parent {
     fun a() {}
     open fun b() {}
  }
  
  class Child: Parent() {
     override fun a() {} // Error
     override fun b() {}
  }
  ```

- 서브클래스에서 해당 메서드에 final을 붙일 수도 있다.

```kotlin
open class ProfileLoader: InternetLoader() {

   final override fun loadFromInterner() {
       // load profile
   }
}
```



### 정리

- 컴포지션과 상속의 차이점

  - 컴포지션은 더 안전하다. 다른 클래스의 내부적인 구현에 의존하지 않고 외부에서 관찰되는 동작에만 의존하므로 안전하다.
  - 컴포지션은 더 유연하다. 상속은 한 클래스만을 대상으로 할 수 있지만 컴포지션은 여러 클래스를 대상으로 할 수 있다. 상속은 모든 것을 받지만 컴포지션은 피룡한 것만 받을 수 있다. 슈퍼클래스의 동작을 변경하면, 서브클래스의 동작도 큰 영향을 받는다. 하지만 컴포지션을 할용하면 이러한 영향이 제한적이다.
  - 컴포지션은 더 명시적이다. 이것은 장점이다 단점. 슈퍼 클래스의 메서드를 사용할 때는 리시버를 따로 지정하지 않아도 된다. 즉 코드가 짤방질 수 있지만 메서드가 어디서 왔는지 혼동될 수 있으므로 위험할 수 있다. 컴포지션을 화용하면 리시버를 명시적으로 활용할 수밖에 없으므로 메서드가 어디에 있는 것인지 확실하게 알 수 있다.
  - 컴포지션은 생각보다 번거롭다. 컴포지션은 객체를 명시적으로 사용해야 하므로 대상 클래스에 일부 기능을 추가할 때 이를 포함하는 객체의 코드를 변경해야 한다. 그래서 상속을 사용할 때보다 수정해야 하는 경우가 더 많다.
  - 상속은 다형성을 활용할 수 있다. 하지만 이건 양날의 검! 상속을 사용할 경우 슈퍼클래스와 서브클래스의 규약을 항상 잘 지켜서 코드를 작성해야 한다.

- 일반적으로 OOP에서는 상속보다 컴포지션을 사용하는 것이 좋다.

- 상속은 언제?

  - 명확한 is-a 관계일때 상속하는 것이 좋다.
  - 슈퍼클래스를 상속하는 모든 서브클래스는 슈퍼클래스로도 동작할 수 있어야 한다.

  

## 아이템 37: 데이터 집합 표현에 data 한정자를 사용하라

```kotlin
data class Player(
       val id: Int,
       val name: String,
       val points: Int
)

val player = Player(0, "Gecko", 9999)
```

- data 한정자를 붙이면 생성되는 함수들

  - toString
  - equals와 hashCode
  - copy
  - componentN

- toString

  - toString 함수는 클래스의 이름과 기본 생성자 형태로 모든 프로퍼티와 값을 출력해준다.

  - ```kotlin
    print(player) // Player(id=0, name=Gecko, points=9999)
    ```

- equals와 hashCode

  - equals는 기본 생성자의 프로퍼티가 같은지 확인해준다.

  - hashCode는 equals와 같은 결과를 낸다.

  - ```kotlin
    player == Player(0, "Gecko", 9999) // true
    player == Player(0, "Ross", 9999) // false
    ```

    

- copy

  - copy는 immutable 데이터 클래스를 만들 때 편리하다.

  - copy는 기본 생성자 프로퍼티가 같은 새로운 객체를 복제한다.

  - 새로 만들어진 객체의 값은 이름있는 아규먼트를 활용해서 변경할 수 있다.

  - ```kotlin
    val newObj = player.copy(name = "Thor")
    print(newObj) // Player(id=0, name=Thor, points=9999)
    ```

- componentN 함수

  - componentN 함수는 위치를 기반으로 객체를 해제할 수 있게 해준다.

  - ```kotlin
    val (id, name, pts) = player
    ```

  - 코틀린은 내부적으로 componentN 함수를 사용해서 다음과 같은 코드로 변환한다.

  - ```kotlin
    // After compilation
    val id: Int  = player.component1()
    val name: String = player.component2()
    val pts: Int = player.component3()
    ```

  - 위치를 기반으로 객체를 해제하는 방법은 굉장히 위험하다. 따라서 객체 해제할때는 데이터 클래스의 기본 생성자에 붙어있는 프로퍼티 이름과 같은 이름을 사용하는 것이 좋다. ide에서 경고를 만들어준다.

### 튜플 대신 데이터 클래스 사용하기

- 튜플을 반드시 사용해야 하는 경우

  - 값에 간단하게 이름을 붙일 때

  - ```kotlin
    val (description, color) = when {
       degrees < 5 -> "cold" to Color.BLUE
       degrees < 23 -> "mild" to Color.YELLOW
       else -> "hot" to Color.RED
    }
    ```

  - 표준 라이브러리에서 볼 수 있는 것처럼 미리 알 수 없는 aggregate를 표현할 때

  - ```kotlin
    val (odd, even) = numbers.partition { it % 2 == 1 }
    val map = mapOf(1 to "San Francisco", 2 to "Amsterdam")
    ```

- 이 경우들을 제외하면 무조건 데이터클래스를 사용하는 것이 좋다.

```kotlin
// 구림 ..
fun String.parseName(): Pair<String, String>? {
   val indexOfLastSpace = this.trim().lastIndexOf(' ')
   if(indexOfLastSpace < 0) return null
   val firstName = this.take(indexOfLastSpace)
   val lastName = this.drop(indexOfLastSpace)
   return Pair(firstName, lastName)
}

// Usage
val fullName = "Marcin Moskała"
val (firstName, lastName) = fullName.parseName() ?: return
```

```
fun String.parseName(): Pair<String, String>? {
   val indexOfLastSpace = this.trim().lastIndexOf(' ')
   if(indexOfLastSpace < 0) return null
   val firstName = this.take(indexOfLastSpace)
   val lastName = this.drop(indexOfLastSpace)
   return Pair(firstName, lastName)
}

// Usage
val fullName = "Marcin Moskała"
val (firstName, lastName) = fullName.parseName() ?: return
```

```kotlin
// 굿
data class FullName(
    val firstName: String, 
    val lastName: String
)

fun String.parseName(): FullName? {
   val indexOfLastSpace = this.trim().lastIndexOf(' ')
   if(indexOfLastSpace < 0) return null
   val firstName = this.take(indexOfLastSpace)
   val lastName = this.drop(indexOfLastSpace)
   return FullName(firstName, lastName)
}

// Usage
val fullName = "Marcin Moskała"
val (firstName, lastName) = fullName.parseName() ?: return
```

- 장점
  - 이렇게 해도 추가비용이 거의 없다.
  - 함수의 리턴 타입이 더 명확해진다.
  - 리턴 타입이 더 짧아지며 전달하기 쉬워진다.
  - 사용자가 데이터 클래스에 적혀 있는 것을 다른 이름을 활용해 변수를 해제하면, 경고가 출력된다.
- 데이터 클래스를 잘 활용하자!!!



## 아이템 38: 연산 또는 액션을 전달할 때는 인터페이스 대신 함수 타입을 사용하라

- SAM(Single-Abstract Method)
  - 연산 또는 액션을 전달할 때 메서드가 하나만 있는 인터페이스

```kotlin
interface OnClick {
   fun clicked(view: View)
}

fun setOnClickListener(listener: OnClick) {
   //...
}

setOnClickListener(object : OnClick {
   override fun clicked(view: View) {
       // ...
   }
})

```

- 함수 타입으로 변경하면 더많은 자유를 얻을 수 있다.

```kotlin
fun setOnClickListener(listener: (View) -> Unit) {
   //... 
}
```

- 다양한 방법들

  - 람다 또는 익명 함수로 전달

  - ```kotlin
    setOnClickListener { /*...*/ }
    setOnClickListener(fun(view) { /*...*/ })
    ```

  - 함수 레퍼런스 또는 제한된 함수 레퍼런스로 전달

  - ```kotlin
    setOnClickListener(::println)
    setOnClickListener(this::showUsers)
    ```

  - 선언된 함수 타입을 구현한 객체로 전달

  - ```kotlin
    class ClickListener: (View)->Unit {
       override fun invoke(view: View) {
           // ...
       }
    }
    
    setOnClickListener(ClickListener())
    ```

- 장점

  - 타입 별칭을 사용하면 함수 타입도 이름을 붙일 수 있다.

  - ```kotlin
    typealias OnClick = (View) -> Unit
    ```

  - 파라미터도 이름을 가질 수 있다.

  - ```kotlin
    fun setOnClickListener(listener: OnClick) { /*...*/ }
    typealias OnClick = (view: View)->Unit
    ```

- 여러 옵저버를 설정할 때

```kotlin
class CalendarView {
   var listener: Listener? = null

   interface Listener {
       fun onDateClicked(date: Date)
       fun onPageChanged(date: Date)
   }
}
```

- 아래 있는 코드처럼 함수 타입을 따로 갖는 것이 훨씬 사용하기 쉽다.

```kotlin
class CalendarView {
   var onDateClicked: ((date: Date) -> Unit)? = null
   var onPageChanged: ((date: Date) -> Unit)? = null
}
```

- 인터페이스를 사용해야 하는 특별한 이유가 없다면 함수 타입을 사용하는게 좋다. 함수 타입은 다양한 지원을 받을 수 있다.

### 언제 SAM을 사용해야 할까?

- 딱 한가지 경우
  - 코틀린이 아닌 다른 언어에서 사용할 클래스를 설계할 때 ex: 자바



## 아이템 39: 태그 클래스보다는 클래스 계층을 사용하라

- 태그와 태그 클래스

  - 상수모드를 태그라고 부르고 태그를 포함한 클래스를 태그 클래스라고 부른다.

- 태그 클래스의 문제

  - 서로 다른 책임을 한 클래스에 태그로 구분해서 넣음
  - 한 클래스에 여러 모드를 처리하기 위한 상용구가 추가된다.
  - 여러 목적으로 사용해야 하므로 프로퍼티가 일관적이지 않게 사용될 수 있고 더 많은 프로퍼티가 필요하다. 
  - 요소가 여러 목적을 가지고 요소를 여러 방법으로 설정할 수 있는 경우에는 상태의 일관성과 정확성을 지키기 어렵다.
  - 팩토리 메서드를 사용해야 하는 경우가 많다. 그렇지 않으면 객체가 제대로 생성되었는지 확인하는 것 자체가 굉장히 어렵다.

  ```kotlin
  class ValueMatcher<T> private constructor(
     private val value: T? = null,
     private val matcher: Matcher
  ){
  
     fun match(value: T?) = when(matcher) {
         Matcher.EQUAL -> value == this.value
         Matcher.NOT_EQUAL -> value != this.value
         Matcher.LIST_EMPTY -> value is List<*> && 
  value.isEmpty()
         Matcher.LIST_NOT_EMPTY -> value is List<*> && 
  value.isNotEmpty()
     }
  
     enum class Matcher {
         EQUAL,
         NOT_EQUAL,
         LIST_EMPTY,
         LIST_NOT_EMPTY
     }
  
     companion object {
         fun <T> equal(value: T) =
             ValueMatcher<T>(value = value, matcher = 
  Matcher.EQUAL)
  
         fun <T> notEqual(value: T) =
             ValueMatcher<T>(value = value, matcher = 
  Matcher.NOT_EQUAL)
  
         fun <T> emptyList() =
             ValueMatcher<T>(matcher = Matcher.LIST_EMPTY)
  
         fun <T> notEmptyList() =
             ValueMatcher<T>(matcher = 
  Matcher.LIST_NOT_EMPTY)
     }
  }
  ```



- 코틀린의 sealed 클래스

  - 코틀린에서는 일반적으로 태그 클래스보다 sealed 클래스를 많이 사용한다.
  - 한 클래스에 여러 모드를 만드는 방법 대신에 각각의 모드를 여러 클래스로 만들고 타입 시스템과 다형성을 활용한다.

  ```kotlin
  sealed class ValueMatcher<T> {
      abstract fun match(value: T): Boolean
  
      class Equal<T>(val value: T) : ValueMatcher<T>() {
          override fun match(value: T): Boolean = 
              value == this.value
      }
  
      class NotEqual<T>(val value: T) : ValueMatcher<T>() {
          override fun match(value: T): Boolean = 
              value != this.value
      }
  
      class EmptyList<T>() : ValueMatcher<T>() {
          override fun match(value: T) = 
              value is List<*> && value.isEmpty()
      }
  
      class NotEmptyList<T>() : ValueMatcher<T>() {
          override fun match(value: T) = 
              value is List<*> && value.isNotEmpty()
      }
  }
  ```



### sealed 한정자

- sealed 한정자의 장점

  - abstract를 사용할 수 있지만 sealed한정자는 외부 파일에서 서브클래스를 만드는 행위 자체를 모두 제한한다.

  - 외부에서 추가적인 서브클래스를 만들 수 없으므로 타입이 추가되지 않을거라는게 보장된다.

  - 이는 when을 사용할 때 else브랜치를 따로 만들 필요가 없다.

  - ```kotlin
    fun <T> ValueMatcher<T>.reversed(): ValueMatcher<T> = 
    when (this) {
        is ValueMatcher.EmptyList -> 
            ValueMatcher.NotEmptyList<T>()
        is ValueMatcher.NotEmptyList -> 
            ValueMatcher.EmptyList<T>()
        is ValueMatcher.Equal -> ValueMatcher.NotEqual(value)
        is ValueMatcher.NotEqual -> ValueMatcher.Equal(value)
    }
    ```

- abstract는 상속과 관련된 설계를 할때 사용하고 클래스의 서브클래스를 제어하려면 sealed 한정자를 사용해야 한다.

### 태그 클래스와 상태 패턴의 차이

- 태그 클래스 != 상태 패턴
  - 상태 패턴은 객체의 내부 상태가 변화할 때 객체의 동작이 변하는 소프트웨어 디자인 패턴이다.



### 정리

- 코틀린에서는 태그 클래스보다 타입 계층을 사용하는 것이 좋다. 그리고 일반적으로 이러한 타입 계층을 만들 때는  sealed 클래스를 사용한다.
- 이는 상태 패턴과 다르다. 타입 계층과 상태 패턴은 실질적으로 함께 사용하는 협력 관계라고 할 수 있다.



## 아이템 40: equals의 규약을 지켜라

### 동등성

- 코틀린의 두가지 종류의 동등성
  - 구조적 동등성: eqauls메서드와 이를 기반으로 만들어진 == 연산자로 확인하는 동등성 a가 nullable이 아니라면 a == b는 a.eqauls(b). a가 nullable이라면 a?.eqauls(b) ?: (b === null)
  - 레퍼런스적 동등성: === 연산자로 확인하는 동등성 두 피연산자가 같은 객체를 가리키면 true를 리턴
- 다른 타입의 객체끼리는 eqauls를 사용할 수 없다.

```kotlin
open class Animal
class Book
Animal() == Book()  // Error: Operator == cannot be 
// applied to Animal and Book
Animal() === Book() // Error: Operator === cannot be 
// applied to Animal and Book
```



### equals가 필요한 이유

- Any에 구현되어있는 eqauls는 === 처럼 두 인스턴스가 완전히 같은 객체인지를 비교한다.

```kotlin
class Name(val name: String)
val name1 = Name("Marcin")
val name2 = Name("Marcin")
val name1Ref = name1

name1 == name1 // true
name1 == name2 // false
name1 == name1Ref // true

name1 === name1 // true
name1 === name2 // false
name1 === name1Ref // true
```

- 두 객체의 기본 생성자의 프로퍼티가 같다면 같은 객체로 봐야하는 경우 data한정자를 사용하면 자동으로 이와 같은 동등성으로 동작한다.

```kotlin
data class FullName(val name: String, val surname: String)
val name1 = FullName("Marcin", "Moskała")
val name2 = FullName("Marcin", "Moskała")
val name3 = FullName("Maja", "Moskała")
  
name1 == name1 // true
name1 == name2 // true, because data are the same
name1 == name3 // false
  
name1 === name1 // true
name1 === name2 // false
name1 === name3 // false
```

- 데이터 클래스에서 특정 타입에 대한 equals를 제거하고싶다면 생성자 밖으로 빼면 된다.

```kotlin
data class DateTime(
   private var millis: Long = 0L,
   private var timeZone: TimeZone? = null
) {
   private var asStringCache = ""
   private var changed = false
  
   //...
}

```

- 위 데이터클래스는 asStringCache와 changed 필드가 eqauls에서 제거되며 copy에도 해당 필드는 제거된다.
- 따라서 특별한 상황이 아닌 경우 코틀린에서는 eqauls를 직접 구현할 필요가 없다.

```kotlin
class User(
   val id: Int,
   val name: String,
   val surname: String
) {
   override fun equals(other: Any?): Boolean =
       other is User && other.id == id

   override fun hashCode(): Int = id
}
```

### eqauls의 규약

- eqauls은 반드시 다음과 같은 요구사항을 충족해야 한다.
  - 반사적 동작: x가 null이 아닌 값이라면, x.eqauls(x)는 true를 리턴해야 한다.
  - 대칭적 동작: x와 y가 null이 아닌 값이라면, e.eqauls(y)는 y.eqauls(x)와 같은 결과를 출력해야 한다.
  - 연속적 동작: x, y, z가 null이 아닌 값이고 x.eqauls(y)와 y.eqauls(z)가 true라면, x.equals(z)도 true여야 한다.
  - 일관적 동작: x와 y가 null이 아닌 값이라면, x.equals(y)는 여러번 실행하더라도 항상 같은 결과를 리턴해야 한다.
  - 널과 관련된 동작: x가 null이 아닌 값이라면 x.equals(null)은 항상 false를 리턴해야 한다.



`반사적 동작`

- x가 null이 아닌 값이라면, x.eqauls(x)는 true를 리턴해야 한다.

```kotlin
// DO NOT DO THIS!
class Time(
   val millisArg: Long = -1,
   val isNow: Boolean = false
) {
   val millis: Long get() =
       if (isNow) System.currentTimeMillis() 
       else millisArg

   override fun equals(other: Any?): Boolean =
       other is Time && millis == other.millis
}

val now = Time(isNow = true)
now == now // Sometimes true, sometimes false   
List(100000) { now }.all { it == now }
// Most likely false
```

- 이 코드는 실행할 때마다 결과가 달라질 수 있어서 일관적 동작도 위반한다

- 이렇게 수정되어야 한다.

  - 객체가 현재 시간을 나타내는가?를 확인하고 현재 시간을 나타내지 않는다면 같은 타임스탬프를 갖고 있는가?로 동등성을 확인해야한다.

  - 이는 태그클래스의 고전적인 예다.

  - ```kotlin
    sealed class Time
    data class TimePoint(val millis: Long): Time()
    object Now: Time()
    
    ```

  

`대칭적 동작`

- x와 y가 null이 아닌 값이라면, e.eqauls(y)는 y.eqauls(x)와 같은 결과를 출력해야 한다.

```kotlin
class Complex(
   val real: Double,
   val imaginary: Double
) {
   // DO NOT DO THIS, violates symmetry
   override fun equals(other: Any?): Boolean {
       if (other is Double) { 
          return imaginary == 0.0 && real == other
       }
       return other is Complex &&
               real == other.real &&
               imaginary == other.imaginary
   }
}
```

```kotlin
Complex(1.0, 0.0).equals(1.0) // true
1.0.equals(Complex(1.0, 0.0)) // false
```

- 대칭성을 깨면 디버깅중에 찾기가 매우매우 어렵다. 동등성을 구현할 때는 항상 대칭성을 고려해야 한다.
- 결론적으로 다른 클래스는 동등하지 않게 만드는게 좋다.



`연속적 동작`

- x, y, z가 null이 아닌 값이고 x.eqauls(y)와 y.eqauls(z)가 true라면, x.equals(z)도 true여야 한다.

```kotlin
open class Date(
   val year: Int,
   val month: Int,
   val day: Int
) {
   // DO NOT DO THIS, symmetric but not transitive
   override fun equals(o: Any?): Boolean = when (o) {
       is DateTime -> this == o.date
       is Date -> o.day == day && o.month == month && 
o.year == year
       else -> false
   }

   // ...
}

class DateTime(
   val date: Date,
   val hour: Int,
   val minute: Int,
   val second: Int
): Date(date.year, date.month, date.day) {
   // DO NOT DO THIS, symmetric but not transitive
   override fun equals(o: Any?): Boolean = when (o) {
       is DateTime -> o.date == date && o.hour == hour && 
o.minute == minute && o.second == second
       is Date -> date == o
       else -> false
   }

   // ...
}
```

```kotlin
val o1 = DateTime(Date(1992, 10, 20), 12, 30, 0)
val o2 = Date(1992, 10, 20)
val o3 = DateTime(Date(1992, 10, 20), 14, 45, 30)

o1 == o2 // true
o2 == o3 // true
o1 == o3 // false <- So equality is not transitive
```

- 상속대신 컴포지션을 사용하고 두 객체를 아예 비교하지 못하게 만드는 것이 좋다.

```kotlin
data class Date(
   val year: Int,
   val month: Int,
   val day: Int
)

data class DateTime(
   val date: Date,
   val hour: Int,
   val minute: Int,
   val second: Int
)

val o1 = DateTime(Date(1992, 10, 20), 12, 30, 0)
val o2 = Date(1992, 10, 20)
val o3 = DateTime(Date(1992, 10, 20), 14, 45, 30)

o1.equals(o2) // false
o2.equals(o3) // false
o1 == o3 // false

o1.date.equals(o2) // true
o2.equals(o3.date) // true
o1.date == o3.date // true
```

`일관성 동작`

- x와 y가 null이 아닌 값이라면, x.equals(y)는 여러번 실행하더라도 항상 같은 결과를 리턴해야 한다.
- 두 객체를 비교한 결과는 한 객체를 수정하지 않는 한 항상 같은 결과를 내야한다.



### URL과 관련된 equals 문제

```kotlin
import java.net.URL

fun main() {
   val enWiki = URL("https://en.wikipedia.org/")
   val wiki = URL("https://wikipedia.org/")
   println(enWiki == wiki)
}
```

- 일반적이라면 결과는 true, 하지만 인터넷이 끊긴 상황이라면 false다
- 이 설계의 문제점
  - 동작이 일관되지 않는다. 어떤 네트워크에서는 두 URL이 같을 수 있지만 다른 네트워크에서는 또 다를 수 있다.
  - 일반적으로 eqauls와 hashcode 처리는 빠를거라 예상하지만 네트워크 처리는 굉장히 느리다.
  - 동작 자체에 문제가 있다. 동일한 ip주소라고해도 동일한 콘텐츠를 나타내는 것은 아니다.

### eqauls 구현하기

- 특별한 이유가 없는 이상 직접 eqauls를 구현하는 것은 좋지 않다.
- 기본적으로 제공되는 것을 사용하거나 데이터 클래스로 만들어서 사용하는 것이 좋다.
- 만약 직접 구현해야 한다면 반사적, 대칭적, 연속적, 일관적 동작을 하는지 꼭 확인하자.
- 이러한 클래스는 final로 만드는 것이 좋다.



## 아이템 41: hashCode의 규약을 지켜라

### 해시 테이블

- 해시 테이블
  - 컬렉션에 요소를 빠르게 추가하고 컬렉션에서 요소를 빠르게 추출해야한다고 할때 사용할 수 있는 자료구조 : Map, Set
  - Map과 Set은 중복을 허용하지 않는다.
  - 성능을 좋게 만드는 방법은 바로 해시테이블이다.
  - 해시 테이블은 각 요소에 숫자를 할당하는 함수가 필요하고 이 함수를 해시함수라고한다.
- 해시 함수가 가져야할 특성
  - 빠른 속도
  - 충돌이 적은 알고리즘
- 해시 함수
  - 해시함수는 각각의 요소에 특정한 숫자를 할당하고 이를 기반으로 요소를 다른 버킷에 넣는다. 
  - 해시 함수의 기본적인 조건에 의해서 같은 요소는 항상 동일한 버킷에 넣게 된다.



### 가변성과 관련된 문제

- 요소가 추가될 때만 해시 코드를 계산한다.
- 요소가 변경되어도 해시코드는 계산되지 않으며, 배킷 재배치도 이뤄지지 않는다. 따라서 Set과 Map의 키로 mutable요소를 사용하면 안되고 사용하더라도 요소를 변경해서는 안된다.

```kotlin
data class FullName(
   var name: String,
   var surname: String
)

val person = FullName("Maja", "Markiewicz")
val s = mutableSetOf<FullName>()
s.add(person)
person.surname = "Moskała"
print(person) // FullName(name=Maja, surname=Moskała)
print(person in s) // false
print(s.first() == person) // true
```



### hashCode의 규약

- hashCode의 규약
  - 어떤 객체를 변경하지 않았다면 hashCode는 여러 번 호출해도 그 결과가 항상 같아야 한다.
  - eqauls 메서드의 실행 결과로 두 객체가 같다고 나온다면, hashCode 메서드의 호출 결과도 같다고 나와야 한다. 필수!!
- hashCode는 최대한 요소를 넓게 퍼뜨려야 한다. 다른 요소라면 최대한 다른 해시값을 찾는 것이 좋다.

```kotlin
class Proper(val name: String) {

   override fun equals(other: Any?): Boolean {
       equalsCounter++
       return other is Proper && name == other.name
   }

   override fun hashCode(): Int {
       return name.hashCode()
   }

   companion object {
       var equalsCounter = 0
   }
}

class Terrible(val name: String) {
   override fun equals(other: Any?): Boolean {
       equalsCounter++
       return other is Terrible && name == other.name
   }

   // Terrible choice, DO NOT DO THAT
   override fun hashCode() = 0

   companion object {
       var equalsCounter = 0
   }
}

val properSet = List(10000) { Proper("$it") }.toSet()
println(Proper.equalsCounter) // 0
val terribleSet = List(10000) { Terrible("$it") }.toSet()
println(Terrible.equalsCounter) // 50116683

Proper.equalsCounter = 0
println(Proper("9999") in properSet) // true
println(Proper.equalsCounter) // 1

Proper.equalsCounter = 0
println(Proper("A") in properSet) // false
println(Proper.equalsCounter) // 0

Terrible.equalsCounter = 0
println(Terrible("9999") in terribleSet) // true
println(Terrible.equalsCounter) // 4324

Terrible.equalsCounter = 0
println(Terrible("A") in terribleSet) // false
println(Terrible.equalsCounter) // 10001
```

### hashCode 구현하기

- 그냥 data 클래스 쓰자...
- 만약 equals를 재정의했다면 반드시 hashCode를 재정의해야 한다.
- hashCode 는 기본적으로 eqauls에서 비교에 사용되는 프로퍼티를 기반으로 해시 코드를 만들어야한다.
- 일반적으로 모든 해시 코드의 값을 더하고 더하는 과정마다 이전까지의 결과에 31을 곱한 뒤 더해준다. 31은 관례

```kotlin
class DateTime(
   private var millis: Long = 0L,
   private var timeZone: TimeZone? = null
) {
   private var asStringCache = ""
   private var changed = false

   override fun equals(other: Any?): Boolean =
       other is DateTime &&
               other.millis == millis &&
               other.timeZone == timeZone

   override fun hashCode(): Int {
       var result = millis.hashCode()
       result = result * 31 + timeZone.hashCode()
       return result
   }
}
```



## 아이템 42: compareTo의 규약을 지켜라

- compareTo 메서드는 Any 클래스에 있는 메서드가 아니다. 이는 수학적인 부등식으로 변환되는 연산자이다.

```kotlin
obj1 > obj2 // Translates to obj1.compareTo(obj2) > 0
obj1 < obj2 // Translates to obj1.compareTo(obj2) < 0
obj1 >= obj2 // Translates to obj1.compareTo(obj2) >= 0
obj1 <= obj2 // Translates to obj1.compareTo(obj2) <= 0
```

- compareTo는 다음과 같이 동작해야 한다.
  - 비대칭적 동작: a >= b 이고 b >= a 라면, a == b여야 한다. 즉 비교와ㅓ 동등성 비교에 어떠한 관계가 있어야 하며, 서로 일관성이 있어야 한다.
  - 연속적 동작: a >= b 이고 b >= c라면 a >= c여야 한다. 마찬가지로 a > b 이고 b > c 라면  a > c여야 한다. 이러한 동작을 하지 못하면 요소 정렬이 무한 반복에 빠질 수 있다.
  - 코넥스적 동작: 두 요소는 어떤 확실한 관계를 갖고 있어야 한다. 즉, a >= b 또는  b >=  a 중에 적어도 하나 이상은 ture 여야 한다. 두 요소 사이에 관계가 업으면 고전적인 정렬을 사용할 수 없다. 위상정렬과 같은 알고리즘은 사용할 수 있다.



### compareTo는 따로 정의해야 할까?

- 코틀린에서는 거~~의 없다.

- 컬렉션을 정렬하는 방법

  - 요소가 한개라면 sortedBy를 사용한다.

  - ```kotlin
    class User(val name: String, val surname: String)
    val names = listOf<User>(/*...*/)
    
    val sorted = names.sortedBy { it.surname }
    ```

  - 여러 프로퍼티를 기반으로 정렬해야 한다면 sortedWith 함수를 사용한다. 

  - ```kotlin
    val sorted = names
        .sortedWith(compareBy({ it.surname }, { it.name }))
    ```

- 객체가 자연스러운 순서인지 확실하지 않다면 비교기를 사용하는 것이 좋다. 이를 자주 사용한다면 클래스에 companion 객체로 만들어두는 것도 좋다.

```kotlin
class User(val name: String, val surname: String) {
   // ...

   companion object {
       val DISPLAY_ORDER =
               compareBy(User::surname, User::name)
   }
}

val sorted = names.sortedWith(User.DISPLAY_ORDER)
```



### compareTo 구현하기

- 두 값을 단순하게 비교하기만 한다면 compareValues 함수를 다음과 같이 활용할 수 있다.

```kotlin
class User(
   val name: String, 
   val surname: String
): Comparable<User> {
   override fun compareTo(other: User): Int =
           compareValues(surname, other.surname)
}
```

- 더 많은 값을 비교하거나 선택기를 활용하고싶다면 다음과 같이 compareValuesBy를 사용하면 된다.

```kotlin
class User(
   val name: String, 
   val surname: String
): Comparable<User> {
   override fun compareTo(other: User): Int =
     compareValuesBy(this, other, { it.surname }, { 
it.name })
}
```

- 이 함수는 비교기를 만들때 도움이 된다. 특별한 논리를 구현해야 하는 경우 이 함수가 다음 값을 리턴해야 한다
  - 0: 리시버와 other가 같은 경우
  - 양수: 리시버가 other보다 큰 경우
  - 음수: 리시버가 other보다 작은 경우



## 아이템 43: API의 필수적이지 않는 부분을 확장 함수로 추출하라

- 클래스의 메서드를 정의할 때는 메서드를 멤버로 정의할 것인지 아니면 확잠 하수로 정의할 것인지 결정해야 한다.

```kotlin
// Defining methods as members
class Workshop(/*...*/) {
   //...

   fun makeEvent(date: DateTime): Event = //...

   val permalink
       get() = "/workshop/$name"
}


// Defining methods as extensions
class Workshop(/*...*/) {
   //...
}

fun Workshop.makeEvent(date: DateTime): Event = //...

val Workshop.permalink
   get() = "/workshop/$name"
```

- 일단 두 방식중에 어떤 방식이 우월하다고 할 수 없다. 각자 장단점이 있으므로 필요한 경우에 잘 적절하게 활용하자.

- 차이점들

  - 확장 메서드의 경우 따로 가져와서 사용해야 한다. 일반적으로 확장은 다른 패키지에 위치한다.

  - 확장 메서드는 임포터해서 사용한다는 특징 덕분에 같은 타입에 같은 이름으로 메서드를 여러개 만들 수 있다. 하지만 이 방법은 위험할 수 있어서 비추천

  - 확장함수는 가상이 아니라서 파생 클래스에서 오버라이드할 수 없다. 상속을 목적으로 설계된 요소는 확장 함수로 사용할 수 없다.

  - ```kotlin
    open class C
    class D: C()
    fun C.foo() = "c"
    fun D.foo() = "d"
    
    fun main() {
       val d = D()
       print(d.foo()) // d
       val c: C = d
       print(c.foo()) // c
    
       print(D().foo()) // d
       print((D() as C).foo()) // c
    }
    ```

- 확장 함수는 클래스가 아닌 타입에 정의하는것이라 nullabe 또는 구체적인 제네릭 타입에도 확장 함수를 정의할 수 있다.

```kotlin
inline fun CharSequence?.isNullOrBlank(): Boolean {
   contract {
       returns(false) implies (this@isNullOrBlank != null)
   }

   return this == null || this.isBlank()
}

public fun Iterable<Int>.sum(): Int {
   var sum: Int = 0
   for (element in this) {
       sum += element
   }
   return sum
}
```

- 확장 함수는 클래스 레퍼런스에서 멤버로 표시되지 안흔ㄴ다. 따라서 애너테이션 프로세서가 따로 처리하지 않는다. 따라서 필수적이지 않은 요소를 확장함수로 추출하면 어노테이션 프로세스로부터 숨겨진다. 이는 확장 함수가 클래스 내부에 있는 것이 아니기 때문이다.



### 정리

- 멤버와 확장 함수의 차이 비교
  - 확장 함수는 import 해야 한다.
  - 확장 함수는 virtual이 아니다.
  - 멤버는 높은 우선순위를 갖는다.
  - 확장 함수는 클래스 위가 아니라 타입 위에 만들어진다.
  - 확장 함수는 클래스 레퍼런스에 나오지 않는다.
- 확장 함수는 우리에게 더 많은 자유와 유연성을 준다. 확장 함수는 상속, 어노테이션 처리 등을 지원하지 않고 클래스 내부에 없으므로 약간 혼동을 줄 수 있다.
- API의 필수적인 부분은 멤버로 두는 것이 좋지만 필수적이지 않은 부분은 확장 함수로 만드는 것이 여러모로 좋다.



## 아이템 44: 멤버 확장 함수의 사용을 피하라

- 가시성을 제한하기 위해 확장 함수를 멤버로 정의하는 것은 굉장히 좋지 않다.

```kotlin
// Bad practice, do not do this
class PhoneBookIncorrect {
   // ...  

   fun String.isPhoneNumber() =
     length == 7 && all { it.isDigit() }
}
```

- 확장 함수의 가시성을 제한하고 싶다면 가시성 한정자를 붙이면 된다.

```kotlin
// This is how we limit extension functions visibility
class PhoneBookCorrect {
   // ...  
}

private fun String.isPhoneNumber() = 
     length == 7 && all { it.isDigit() }
```

- 멤버 확장을 피해야 하는 몇가지 이유

  - 레퍼런스를 지원하지 않는다.

  - ```kotlin
    val ref = String::isPhoneNumber
    val str = "1234567890"
    val boundedRef = str::isPhoneNumber
    
    val refX = PhoneBookIncorrect::isPhoneNumber // ERROR
    val book = PhoneBookIncorrect()
    val boundedRefX = book::isPhoneNumber // ERROR
    ```

  - 암묵적 접근을 할 때 두 리시버 중에 어떤 리시버가 선택될지 혼동이다.

  - ```kotlin
    class A {
       val a = 10
    }
    class B {
       val a = 20
       val b = 30
      
       fun A.test() = a + b // Is it 40 or 50?
    }
    ```

  - 확장 함수가 외부에 있는 다른 클래스를 리시버로 받을 때, 해당 함수가 어떤 동작을 하는지 명확하지 않다.

  - ```kotlin
    class A {
       //...
    }
    class B {
       //...
      
       fun A.update() = ... // Does it update A or B?
    }
    ```

  - 경험이 적은 개발자의 경우 확장 함수를 보면 직관적이지 않다.



# #7 비용 줄이기

## 아이템 45: 불필요한 객체 생성을 피하라

- 불필요한 객체 생성을 피하는 것이 최적화 관점에서 좋다.
- JVM에서는 하나의 가상 머신에서 동일한 문자열을 처리하는 코드가 여러개라면 기존 문자열을 재사용한다.

```kotlin
val str1 = "Lorem ipsum dolor sit amet"
val str2 = "Lorem ipsum dolor sit amet"
print(str1 == str2) // true
print(str1 === str2) // true

// -128 ~ 127 사의의 작은 값도 캐싱한다.
val i1: Int? = 1
val i2: Int? = 1
print(i1 == i2) // true
print(i1 === i2) // true, because i2 was taken from cache 

val j1: Int? = 1234
val j2: Int? = 1234
print(j1 == j2) // true
print(j1 === j2) // false
```

- 기본형 예를들어 Int가 nullable한 경우 Integer로 컴파일되고 notnull이면 int형으로 컴파일된다. 이러한 메커니즘은 객체 생성 비용에 큰 영향을 준다.

### 객체 생성 비용은 항상 클까?

- 어떤 객체를 wrap하면 세가지 비용이 발생한다.
  - 객체는 더 많은 용량을 차지한다. 기본 자료형 int는 4바이트지만 Integer는 16바이트다. 큰부분은 아니지만 분명의 비용은 추가된다. 많은 경우 비용이 커질 수 있다.
  - 요소가 캡슐화되어 있다면, 접근에 추가적인 함수 호출이 필요하다. 함수를 사용하는 처리는 굉장히 빠르므로 마찬가지로 큰 비용이 발생하지는 않는다. 하지만 많은경우 비용이 커질 수 있다.
  - 객체는 생성이되어야 한다. 생성되고 메모리에 할당되고 레퍼런스를 만드는 과정이 필요한데 적은 비용이지만 모이면 큰 비용이된다.

```kotlin
class A
private val a = A()

// Benchmark result: 2.698 ns/op
fun accessA(blackhole: Blackhole) {
   blackhole.consume(a)
}

// Benchmark result: 3.814 ns/op
fun createA(blackhole: Blackhole) {
   blackhole.consume(A())
}

// Benchmark result: 3828.540 ns/op
fun createListAccessA(blackhole: Blackhole) {
   blackhole.consume(List(1000) { a })
}

// Benchmark result: 5322.857 ns/op
fun createListCreateA(blackhole: Blackhole) {
   blackhole.consume(List(1000) { A() })
}
```



### 객체 선언

- 싱글턴 사용하기
  - 매 순간 객체를 생성하지 않고 객체를 재사용하는 간단한 방법은 객체 선언을 사용하는 것이다.

```kotlin
sealed class LinkedList<T>

class Node<T>(
    val head: T, 
    val tail: LinkedList<T>
): LinkedList<T>()

class Empty<T>: LinkedList<T>()

// Usage
val list: LinkedList<Int> = 
    Node(1, Node(2, Node(3, Empty())))
val list2: LinkedList<String> = 
    Node("A", Node("B", Empty()))
```

- 위 코드에서 Empty는 항상 새로운 객체를 리턴한다.
  - 싱글턴으로 만들자니 제네릭타입이 걸린다. 이때 Nothing 타입의 리스트를 만들면 해결할수 있다.
  - Nothing은 모든 타입의 서브타입이다. 따라서 아래와 같이 변경하면 모든 코드에서 재사용할 수 있다.

```kotlin
sealed class LinkedList<out T>

class Node<out T>(
       val head: T,
       val tail: LinkedList<T>
) : LinkedList<T>()

object Empty : LinkedList<Nothing>()

// Usage
val list: LinkedList<Int> = 
    Node(1, Node(2, Node(3, Empty)))

val list2: LinkedList<String> = 
    Node("A", Node("B", Empty))
```



### 캐시를 활용하는 팩토리 함수

- 팩토리 함수는 캐시를 가질 수 있다. 따라서 팩토리 함수는 항상 같은 객체를 리턴하게 만들 수도 있다.

```kotlin
fun <T> emptyList(): List<T> = EmptyList
```

- 모든 순수 함수는 캐싱을 활용할 수 있다. -> 메모이제이션.. 생략
- WeakReference와 SoftReference의 차이
  - WeakReference는 가비지 컬렉터가 값을 정리하는 것을 막지 않는다. 다른 레퍼런스가 이를 사용하지 않으면 곧바로 제거된다.
  - SoftReference는 가비지 컬렉터가 값을 정리할 수도 있고 정리하지 않을 수도 있다. 일반적인 JVM 구현의 경우 메모리가 부족해서 추가로 필요한 경우에만 정리한다. 캐시를 만들때는 SoftReference를 활용하는 것이 좋다.
- 참고 사항
  - 캐시는 언제나 메모리와 성능의 트레이드 오프가 발생하므로 캐시를 잘 설계하는 것은 쉽지 않다.
  - 성능 문제를 메모리 부족 문제로 돌리고 싶은 사람은 아무도 없기 때문에 여러가지 상황을 잘 고려해서 현명하게 사용해야 한다.



### 무거운 객체를 외부 스코프로 보내기

- 컬렉션 처리에서 이루어지는 무거운 연산은 컬렉션 처리 함수 내부에서 외부로 빼는 것이 좋다.

- 정규식 사용하기

  - ```kotlin
    fun String.isValidIpAddress(): Boolean {
       return this.matches("\\A(?:(?:25[0-5]|2[0-4][0-9]
    |[01]?[0-9][0-9]?)\\.){3}(?:25[0-5]|2[0-4][0-9]|[01]
    ?[0-9][0-9]?)\\z".toRegex())
    }
    
    // Usage
    print("5.173.80.254".isValidIpAddress()) // true
    ```

  - 위 함수의 문제는 함수를 사용할 떄마다 Regex 객체를 계속해서 만든다.

  - ```kotlin
    private val IS_VALID_EMAIL_REGEX = "\\A(?:(?:25[0-5]
    |2[0-4][0-9]|[01]?[0-9][0-9]?)\\.){3}(?:25[0-5]|2[0-4]
    [0-9]|[01]?[0-9][0-9]?)\\z".toRegex()
    
    fun String.isValidIpAddress(): Boolean = 
        matches(IS_VALID_EMAIL_REGEX)
    ```

  - 이 함수가 한 파일에 다른 함수와 함께 있을 때, 함수를 사용하지 않는다면 정규 표현식이 만들어지는 것 자체가 낭비다. 이런 경우 지연 초기화를 하면 된다.

  - ```kotlin
    private val IS_VALID_EMAIL_REGEX by lazy { 
    "\\A(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\.)
    {3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\z".toRegex() 
    }
    ```



### 지연 초기화

- 무거운 클래스를 만들 때는 지연되게 만드는 것이 좋을 때가 있다.

```kotlin
class A {
   val b = B()
   val c = C()
   val d = D()

   //...
}

```

```kotlin
class A {
   val b by lazy { B() }
   val c by lazy { C() }
   val d by lazy { D() }

   //...
}
```

- 백엔드 에플리케이션의 지연 초기화
  - A가 HTTP 호출에 응답하는 백엔드 애플리케이션의 컨트롤러라고 생각하면 처음 호출될 때 무거운 객체들의 초기화가 필요하다.
  - 이런경우 첫 번째 호출 때 응답 시간이 굉장히 길어질 수 있기 때문에 백엔드 애플리케이션에서는 좋지 않다.
  - 지연초기화는 상황에 맞게 사용해야 한다.

### 기본 자료형 사용하기

- 코틀린은 웬만하면 기본 자료형을 사용한다.

- 아래와 같은 경우 wrap한 자료형이 사용된다.

  - nullable 타입을 연산할 때 (기본 자료형은 null이 될 수 없으므로)
  - 타입을 제네릭으로 사용할 때

- 코드와 라이브러리의 성능이 매우 중요하고 숫자를 많이 다루는 컬렉션이 있다면 기본 자료형을 사용하는게 좋다.

- 컬렉션 내부의 최댓값을 리턴하는 함수 예제

  - 이 함수는 컬렉션이 비어있으면 null을 리턴하고 아니면 그중 최댓값을 리턴한다.

  - ```kotlin
    fun Iterable<Int>.maxOrNull(): Int? {
       var max: Int? = null
       for (i in this) {
           max = if(i > (max ?: Int.MIN_VALUE)) i else max
       }
       return max
    }
    ```

  - 이 구현의 두가지 심각한 단점

    - 각각의 단계에서 엘비스 연산자를 사용해야 한다.
    - nullable 값을 사용했기 때문에 JVM 내부에서 int가 아니라 Integer를 사용한다.

  - 두가지 문제를 해결한 구현

  - ```kotlin
    fun Iterable<Int>.maxOrNull(): Int? {
       val iterator = iterator()
       if (!iterator.hasNext()) return null
       var max: Int = iterator.next()
       while (iterator.hasNext()) {
           val e = iterator.next()
           if (max < e) max = e
       }
       return max
    }
    
    ```

- 최적화가 매우매우 중요한 경우에는 위 사례가 중요할 수 있다.









## 아이템 46: 함수 타입 파라미터를 갖는 함수에 inline 한정자를 붙여라

- 코틀린 표준 라이브러리의 고차 함수를 살펴보면 대부분 inline 한정자가 붙어있다.
  - 고차함수: 함수를 파라미터로 받는 함수 또는 함수를 리턴하는 함수

```kotlin
inline fun repeat(times: Int, action: (Int) -> Unit) {
   for (index in 0 until times) {
       action(index)
   }
}
```

- inline 한정자의 역할

  - 컴파일 시점에 '함수를 호출하는 부분'을 '함수의 본문'으로 대체하는 것

  - ```kotlin
    // 이 코드는
    repeat(10) { 
       print(it) 
    }
    
    // 이렇게 컴파일 된다.
    for (index in 0 until 10) {
       print(index)
    }
    ```

  - 일반적인 함수를 호출하면 함수 본문으로 점프하고 본문의 모든 문장을 호출한 뒤에 함수를 호출했던 위치로 다시 점프하지만 inline은 이런 점프가 일어나지 않는다.

- inline 한정자의 장점

  - 타입 아규먼트에 reified 한정자를 붙여서 사용할 수 있다.
  - 함수 타입 파라미터를 가진 함수가 훨씬 빠르게 동작한다.
  - 비지역 리턴을 사용할 수 있다.



### 타입 아규먼트를 reified로 사용할 수 있다.

- 타입 소거

  - 과거 자바에서는 제네릭이 없었기 때문에 현재도 컴파일을 하면 제네릭 타입과 관련된 내용이 제거된다.
  - 따라서 `List`인지 확인할 수는 있지만 `List<Int>` 인지는 확인할 수 없다.

- 타입 파라미터에 대한 연산 오류

  - ```kotlin
    fun <T> printTypeName() {
       print(T::class.simpleName) // ERROR
    }
    ```

  - 위 코드는 컴파일이 불가능하지만 inline + reified 한정자를 지정하면 타입 파라미터를 사용한 호출 부분이 타입 아규먼트로 대체된다.

  - ```kotlin
    inline fun <reified T> printTypeName() {
       print(T::class.simpleName)
    }
    
    // Usage
    printTypeName<Int>()    // Int
    printTypeName<Char>()   // Char
    printTypeName<String>() // String
    
    //컴파일하는 동안 printTypeName의 본문이 실제로 대체된다.
    print(Int::class.simpleName) // Int
    print(Char::class.simpleName) // Char
    print(String::class.simpleName) // String
    
    ```



### 함수 타입 파라미터를 가진 함수가 훨씬 빠르게 동작한다

- inline 한정자를 붙이면 함수 호출과 리턴을 위해 점프하는 과정과 백스택을 추적하는 과정이 없어서 조금 더 빠르게 동작한다.

- 함수 파라미터를 가지지 않는 함수에서는 이러한 차이가 큰 성능 차이를 발생시키지 않는다.

  - 코틀린/JVM에서는 JVM 익명 클래스 또는 일반 클래스를 기반으로 함수를 객체로 만들어낸다.

  - ```kotlin
    // 이 코드는
    val lambda: ()->Unit = {
       // code
    }
    
    // 클래스로 컴파일된다.
    // Java
    Function0<Unit> lambda = new Function0<Unit>() {
       public Unit invoke() {
           // code
       }
    };
    Or it can be compiled into a normal class defined in a separate file:
    
    // 별도의 파일에 저장되어 있는 클래스로 컴파일하면 이렇게 된다.
    // Java
    public class Test$lambda implements Function0<Unit> {
       public Unit invoke() {
           // code
       }
    }
    
    // Usage
    Function0 lambda = new Test$lambda()
    ```

  - 두 결과 사이에 큰 차이는 없다.

- 함수 본문을 객체로 wrap하면 코드의 속도가 느려진다. 그래서 다음과 같은 두 함수가 있을 때, 첫 번째 함수가 더빠르다.

```kotlin
inline fun repeat(times: Int, action: (Int) -> Unit) {
   for (index in 0 until times) {
       action(index)
   }
}

fun repeatNoinline(times: Int, action: (Int) -> Unit) {
   for (index in 0 until times) {
       action(index)
   }
}

@Benchmark
fun nothingInline(blackhole: Blackhole) {
   repeat(100_000_000) {
       blackhole.consume(it)
   }
}

@Benchmark
fun nothingNoninline(blackhole: Blackhole) {
   noinlineRepeat(100_000_000) {
       blackhole.consume(it)
   }
}
```

- 첫번째 함수는 숫자로 반복을 돌면서, 빈 함수를 호출. 두번째 함수는 숫자로 반복을 돌면서 객체를 호출하고 이 객체가 빈 함수를 호출한다.

- 인라인 함수와 인라인 함수가 아닌 함수의 더 중요한 차이는 함수 리터럴 내부에서 지역 변수를 캡처할 때 확인할 수 있다.

  - ```kotlin
    var l = 1L
    noinlineRepeat(100_000_000) {
       l += it
    }
    ```

  - 인라인이 아닌 람다에서는 지역변수 l을 직접 사용할 수 없다. l은 컴파일 과정 중에 다음과 같이 레퍼런스 객체로 래핑되고 람다 표현식 내부에서는 이를 사용한다.

  - ```kotlin
    val a = Ref.LongRef()
    a.element = 1L
    noinlineRepeat(100_000_000) {
       a.element = a.element + it
    }
    ```

  - ```kotlin
    @Benchmark
    // On average 30 ms
    fun nothingInline(blackhole: Blackhole) {
       var l = 0L
       repeat(100_000_000) {
           l += it
       }
       blackhole.consume(l)
    }
    
    @Benchmark
    // On average 274 ms
    fun nothingNoninline(blackhole: Blackhole) {
       var l = 0L
       noinlineRepeat(100_000_000) {
           l += it
       }
       blackhole.consume(l)
    }
    ```

- 일반적으로 함수 타입의 파라미터가 어떤식으로 동작하는지 이해하기 어렵기 때문에 함수 타입 파라미터를 활용해서 유틸리티 함수를 만들때는 그냥 인라인을 붙여준다 생각하는것도 좋다.

## 아이템 47: 인라인 클래스의 사용을 고려하라

## 아이템 48: 더 이상 사용하지 않는 객체의 레퍼런스를 제거하라



# #8 효율적인 컬렉션 처리

## 아이템 49: 하나 이상의 처리 단계를 가진 경우에는 시퀀스를 사용하라

## 아이템 50: 컬렉션 처리 단계 수를 제한하라

## 아이템 51: 성능이 중요한 부분에는 기본 자료형 배열을 사용하라

## 아이템 52: mutable 컬렉션 사용을 고려하라









