



# Effective Kotlin

코드 참고: [https://github.com/f-lab-edu/kts-effective-kotlin/](https://github.com/f-lab-edu/kts-effective-kotlin/)

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
storedUsers[4] = "AAA"
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

## 아이템 3: 최대한 플랫폼 타입을 사용하지 말라

## 아이템 4: inffered 타입으로 리턴하지 말라

## 아이템 5: 예외를 활용해 코드에 제한을 걸어라

## 아이템 6: 사용자 정의 오류보다는 표준 오류를 사용하라

## 아이템 7: 결과 부족이 발생할 경우 null과 Failure를 사용하라

## 아이템 9: use를 사용하여 리소스를 닫아라

## 아이템 10: 단위 테스트를 만들어라



