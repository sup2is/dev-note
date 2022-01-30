



# 리팩토링









# #1 맛보기 예제



## 원래의 프로그램

- 예제는 비디오 대여점에서 고객의 대여료 내역을 계산하고 출력하는 간단한 프로그램
- 프로그램이 당장은 문제가 없을지 몰라도 나중엔 사용자가 요구한 기능을 수정하기 힘들어서 애먹을 수 있다. 이 상황이 바로 리팩토링 해야 하는 시점이다.
- 프로그램에 기능을 추가해야 하는데 코드 구조가 조잡해서 그 기능을 추가하기 힘들다면, 우선 리팩토링을 실시해서 기능을 추가하기 쉽게 만든 후 그 기능을 추가하자.



## 리팩토링 첫 단계

- 리팩토링할 코드 부분에 대한 신뢰도 높은 각종 테스트를 작성한다.
- 적절한 테스트코드를 작성하는 것은 리팩토링의 기본이다.



## statement 메서드 분해와 기능 재분재

## 가격 책정 부분의 조건문을 재정의로 교체

## 고찰

- 가장 중요한 교훈은 '간단한 수정 -> 테스트'를 리듬처럼 반복하는 것이다. 이 리듬을 지킬 때만이 리팩토링을 빠르고 안정적으로 완료할 수 있다.



# #2 리팩토링 개론

## 리팩토링은 무엇인가

- 리팩토링의 목적은 소프트웨어를 더 이해하기 쉽고 수정하기 쉽게 만드는 것이다.
- 리팩토링을 수행하면 겉으로 드러나는 기능에 거의 또는 아예 영향을 주지 않은 채 소프트웨어의 각종 기능을 변경할 수 있다.
- 리팩토링은 겉으로 드러나는 소프트웨어 기능에 영향을 주지 않는다.

`모자 두 개`

- 소프트웨어 개발에서 기능을 추가할 땐 코드를 수정하지 말고 기능만 추가해야 한다. 진행상태를 파악하려면 테스트를 추가하고 그 테스트들이 제대로 되는지 알아내면 된다.
- 리팩토링할때는 코드를 추가하지 말고 코드 구조 개선만 해야 한다.
- 인터페이스가 변경되어 그에 맞게 코드를 고치는 것이 불가피할 땐 테스트만 변경해야 한다.
- 각자 단계를 나눠서 본인이 현재 하고 있는 작업의 일관성을 유지해야 한다.



## 리팩토링은 왜 해야 하나

`소프트웨어 설계가 개선되니까`

- 리팩토링을 실시하지 않으면 프로그램 설계는 점점 노후된다.
- 코드의 설계를 파악하기 힘들어질수록 설계를 보수하기도 힘들어지고 악순환이 반복된다.
- 코드의 처음 설계 구조를 유지하려면 정기적으로 리팩토링을 실히해야 한다.
- 설계를 개선하는 주요 비법중 하나는 바로 그런 중복 코드를 없애는 것이다.
- 중복 코드 제거는 깔끔한 설계의 필수 요건이다.



`소프트웨어를 이해하기가 더 쉬어지니까`

- 개발자가 프로그램을 제대로 실행되게 만드는데만 집착하고 수정이 어렵고 이해하기 어렵게 만드는 것은 문제가 된다.
- 코드를 쉽게 만드는 것은 중요하다.



`버그를 찾기가 쉬워지니까`

- 코드를 파악하기 쉬우면 버그 발견도 쉬워진다.
- 리팩토링을 통해 빈틈없는 코드를 훨씬 효과적으로 작성할 수 있다.



`프로그래밍 속도가 빨라지니까`

- 깔끔한 설계야말로 소프트웨어 개발 속도를 높이기 위한 핵심이다.
- 리팩토링을 통해 깔끔해지므로 소프트웨어 개발이 더 빨라진다.



## 리팩토링은 어떨 때 필요한가

- 리팩토링은 작정해서 하는게 아니라 뭔가 다른 걸 해야 겠는데 리팩토링을 실시하면 그작업이 쉬워지기 때문에 하는 것이다.

`같은 작업의 삼진 아웃 때`

- 비슷한 작업을 세번째 하게 되면 그때 리팩토링을 실시한다.

`기능을 추가할 때`

- 리팩토링을 통해 코드를 이해하기 쉽게 만드는 과정에서 해당 코드에 대해 더 깊게 이해할 수 있다.
- 설계가 지저분해서 어떤 기능을 추가하기 힘들 때 리팩토링 한다.

`버그를 수정할 때`

- 버그를 수정할 땐 주로 코드를 이해하기 쉽게 만드려고 리팩토링 한다.
- 코드의 기능을 파악하려다 이해하기 쉽게 만들려고 리팩토링을 실시한다.

`코드를 검수할 때`

- 코드 검수는 명료한 코드를 작성하기 위해서 꼭 필요하다.
- 코드 검수를 통해 개발 팀원 모두가 코드를 파악하게 되고 선익 개발자가 경험이 적은 개발자에게 지식을 전수하는 결과도 얻게 된다.



### 리팩토링의 효용성

- 과거의 판단이 현재의 기준으로 불합리하다는 사실을 발견했다면 그 판단을 수정해야 한다. 그래야 현재 작업을 할 수 있다.
- 프로그램은 다음 4가지 상황일 때 수정하기 힘들어진다.
  - 코드를 알아보기 힘들 때
  - 중복된 로직이 들어 있을 때
  - 추가 기능을 넣어야 해서 실행중인 코드를 변경해야 할 때
  - 조건문 구조가 복잡할 때
- 코드를 알아보기 쉽고 기존 기능을 건드릴 필요 없이 조건문 구조가 최대한 간결하게끔 작성해야 한다.
- 리팩토링은 실행 중인 프로그램을 기능을 바꾸는 작업이 아니고 신속한 개발 공정을 가능하게 하는 이런 성질을 가중하면서 가치를 높이는 것이다.



## 팀장에게 어떻게 말을 꺼내나

- 품질에 큰 비중을 두는 팀장이면 품질적인 면을 부각시켜 말해야 한다.
- 일정이 빠듯할 경우에는 리팩토링 얘길 꺼내지 말고 몰래 실시하자.
- 소프트웨어 개발자의 임무는 전문가로서 효과적인 소프트웨어를 최대한 신속히 제작하기만 하면 된다.



### 인다이렉션과 리팩토링

- 리팩토링은 방대한 객체와 장황한 메서드를 잘게 쪼개는 경향이 있다.
- 인다이렉션은 양날의 검!
- 인다이렉션을 사용하는것은 가능하면 자제하면 좋지만 아래와 같은 장점이 있다.

`로직을 공유`

- 두 위치에서 호출되는 하위 메서드나 모든 하위클래스가 공유하는 상위클래스의 메서드 등이 있다. 이런 식으로 하나의 로직을 여러 곳에서 공유할 수 있다.

`의도와 구현부를 따로 나타냄`

- 클래스명과 메서드 명을 정해서 의도한 바를 드러낼 수 있고 클래스나 메서드 내부 코드를 통해 그 의도를 어떻게 구현했는지 보여줄 수 있다.

`수정 부분을 분리`

- 한 객체를 두 위치에 사용했는데 두 경우 한 상황에 대해 동작을 수정해야 할 때 그 객체를 수정하면 두 상황이 모두 변경될 위험이 있다.
- 이런 경우 하위클래스를 만들고 변하는 경우에 참조하게 만들자.

`조건문을 코드화`

- 조건문을 메시지로 바꾸면 중복 코드가 줄어들어 명료해지며 동시에 유연성도 높아진다.

  

## 리팩토링 관련 문제들

`데이터 베이스`

`인터페이스 변경`

`리팩토링을 어렵게 하는 설계를 수정하는 일`

`리팩토링하면 안되는 상황`

-  코드를 처음부터 새로 작성해야 할 때는 리팩토링을 하지 말아야 한다.
- 코드는 반드시 대부분 제대로 돌아가는 것이 우선이고 리팩토링은 나중 일이다.
- 납기가 임박했을 때도 리팩토링은 삼가해야 한다.



## 리팩토링과 설계

- 일단 머릿속 아이디어를 코딩하고 잘 돌아가게 만든 후 그것을 리팩토링으로 세밀히 다듬어야 한다.
- 나중에 수정할 필요가 없도록 사전 설계 에 더 많은 시간과 큰 노력을 들여야 한다.
- 시스템 전체를 유연하게 만들 필요는 없다. 유연성이 필요한 부분을 이부분이고 어딘지 예측도 불가능하다.



### 빈 날짜 인스턴스를 생성하느라 낭비한 시간



## 리팩토링과 성능

- 소프트웨어 성능을 올리려면 먼저 소프트웨어를 튜닝 가능하게 만들어 놓고 나중에 충분한 속도가 나오게 튜닝하는 것이다.
- 빠른 소프트웨어를 작성할 수 있는 일반적인 세가지 방법
  - 철저한 실시간 시스템에 주로 사용되는 시간 분배
  - 성능에 꾸준한 관심 갖기
  - 성능 최적화 전까지는 성능에 신경쓰지 않고 프로그램을 잘 쪼개진 방식으로 제작한 뒤 성능 최적화 단계에서 성능을 튜닝하기
- 프로그램을 잘 쪼개놓으면 성능을 분석할 때 더 정밀한 분석이 가능해진다.
- 리팩토링하는 동안에는 단기적으로 소프트웨어가 느려지지만 최적화를 거치면서 튜닝하기거 훨씬 쉬워져서 결과적으로는 소프트웨어 개발이 더 빨라진다.



## 리팩토링의 유래

### 급여 시스템 최적화



# #3 코드의 구린내

## 중복 코드

- 중복 코드는 구린내의 제왕!
- 중복 코드가 있는 경우 메서드 추출, 템플릿 메서드 패턴, 모듈 추출 등의 방법을 적용해서 중복을 제거해야 한다.



## 장황한 메서드

- 메서드는 훨씬 과감하게 쪼개야 한다.
- 메서드명은 그 코드의 의도를 잘 반영하는 것으로 정해야 한다.
- 메서드 추출, 임시변수를 메서드 호출로 전환 등등의 방법을 사용해야 한다.



## 방대한 클래스

- 기능이 지나치게 많은 클래스에는 보통 엄청난 수의 인스턴스 변수가 있다.
- 클래스 추출을 실시해서 성격이 비슷한 인스턴스 변수를 하나의 클래스로 뺄 수 있다.



## 과다한 매개변수

- 매개변수 세트가 길면 서로 일관성이 없어지거나 사용이 불편해지고 계속된 수정에 매개변수들을 이해하기 어렵다.
- 객체를 통째로 전달하는 방법을 사용하면 된다.



## 수정의 산발

- 한 클래스가 다양한 원인 때문에 다양한 방식으로 자주 수정될 때면 하나의 클래스를 여러 개의 변형 객체로 분리하는 것이 좋다.



## 기능의 산재

- 수정할 때마다 여러 클래스에서 수많은 자잘한 부분을 고쳐야 한다면 메서드 이동, 필드이동을 적용해서 수정할 부분들을 전부 하나의 클래스 안에 넣어야 한다.
- 수정의 산발이나 기능의 산재인 경우 어느 경우든 수정과 클래스는 1:1 대응이 되게 깔끔하게 정리되어야 한다.



## 잘못된 소속

- 소속이 잘못된 메서드는 더 많이 접근하는 클래스에 들어가는 것이 마땅하다.



## 데이터 뭉치

- 두 클래스에 있는 인스턴스 변수나 여러 메서드 시그니처에 들어있는 매개변수처럼 동일한 3~4개의 데이터 항목이 여러 위치에 몰려있는 경우 클래스 추출 기법을 적용해야 한다.



## 강박적 기본 타입 사용

- 돈 관련 클래스, 전화번호, 우편번호같은 특수한 문자열 클래스를 객체로 활용해야 한다.



## switch 문

- switch 문의 단점은 반드시 중복이 생긴다.
- 대부분의 switch 문은 override 하는 방식으로 해결할 수 있다.
- case 문이 2~3개밖에 없고 나중에 그 모든 case 문을 수정할 일이 없을 것 같으면 재정의로 전환하는 것은 과하다. 매개변수를 메서드로 변환하는 방법으로 적용하는 편이 낫다.



## 평행 상속 계층

- 이 문제점이 있으면 한 클래스의 하위 클래스를 만들 때마다 매번 다른 클래스의 하위 클래스도 만들어야 한다.
- 중복 코드 부분을 제거하려면 보통은 한 상속 계층의 인스턴스가 다른 상속 계층의 인스턴스를 참조하게 만들면 된다.



## 직무유기 클래스

- 비용만큼의 기능을 수행하지 못하는 비효율적 클래스는 없애야 한다.



## 막연한 범용 코드

- 아직은 필요없는 기능을 가진 만역한 범용코드는 구린내나는 코드다.



## 임시 필드

- 어떤 객체 안에 인스턴스 변수가 특정 상황에서만 할당되는 경우 클래스 추출을 사용해서 분리해야 한다.



## 메시지 체인

- 메시지 체인은 클라이언트가 한 객체에 제 2의 객체를 요청하면 제 2의 객체가 3의 객체를 요청하는 식으로 연쇄적 요청이 발생하는 문제점을 뜻한다.
- 대리 객체 은폐 등의 문제로 해결할 수 있다.



## 과잉 중개 메서드

## 지나친 관여

## 인터페이스가 다른 대용 클래스

## 미흡한 라이브러리 클래스

## 데이터 클래스

## 방치된 상속물

## 불필요한 주석

- 주석을 넣어야겠다는 생각이 들 땐 먼저 코드를 리팩토링해서 주서을 없앨 수 있게 해보자



# #4 테스트 작성

- 리팩토링을 실시하기 위한 필수 전제조건은 반드시 견고한 테스트를 해야 한다는 것이다.

## 자가 테스트 코드의 가치

- 모든 테스트를 완전히 자동화하고 결과를 자체적으로 검사하게 하자.
- 테스트 스위트는 버그를 찾는 시간을 획기적으로 줄여주는 강력한 버그 감지 도구다.



## JUnit 테스트 프레임워크



`단위 테스트와 기능 테스트`

- 단위 테스트의 목적은 프로그래밍 생산성 향상이다.
- 기능 테스트는 소프트웨어 전반이 제대로 돌아가는지 확인하는 것이다.



## 테스트 추가

- 테스트는 위험을 위주로 작성해야 한다.
- 단순한 읽기 쓰기 메서드는 테스트가 필요 없다. (요즘에 공감되는 내용)
- 버그 가능성이 거의 없는 부분은 애시당초 테스트 작성 대상에서 제외시켜야 한다.
- 완벽한 테스트에 목표를 두지 말고 차근차근 테스트를 만들어 가야 한다.





# #5 리팩토링 기법 카탈로그에 대해







# #6 메서드 정리

- 리팩토링의 주된 작업은 코드를 포장하는 메서드를 적절히 정리하는 것이다.
- 참고 [https://github.com/HugoMatilla/Refactoring-Summary](https://github.com/HugoMatilla/Refactoring-Summary)



## 메서드 추출 Extract MEthod

- 어떤 코드를 그룹으로 묶어도 되겠다고 판단될 땐 그 코드를 뺴내어 목적을 잘 나타내는 직관적 이름의 메서드로 만들자.

```java
	void printOwing(double amount) {
		printBanner();
		//print details
		System.out.println ("name:" + _name);
		System.out.println ("amount" + amount);
	}
```



```java
	void printOwing(double amount) {
		printBanner();
		printDetails(amount);
	}

	void printDetails (double amount) {
		System.out.println ("name:" + _name);
  	System.out.println ("amount" + amount);
	}
```

`동기`

- 메서드 추출 기법은 제일 많이 사용된다.
- 직관적인 이름의 간결한 메서드가 좋은 이유
  - 메서드가 적절히 잘게 쪼개져 있으면 다른 메서드에서 쉽게 사용할 수 있다.
  - 상위 계층의 메서드에서 주석 같은 더 많은 정보를 읽어들일 수 있다.
  - 재정의하기 수월하다.
- 메서드의 이름을 잘 짓는게 중요하다.
- 메서드의 길이는 중요하지 않고 메서드 명과 메서드 내용의 의미가 명확해야 한다.





## 메서드 내용 직접 삽입 Inline Method

- 메서드 기능이 너무 단순해서 메서드 명만 봐도 너무 뻔할 땐 그 메서드의 기능을 호출하는 메서드에 넣어버리고 그 메서드는 삭제하자.

```java
	int getRating() {
		return (moreThanFiveLateDeliveries()) ? 2 : 1;
	}

	boolean moreThanFiveLateDeliveries() {
		return _numberOfLateDeliveries > 5;
	}
```

to

```java
	int getRating() {
		return (_numberOfLateDeliveries > 5) ? 2 : 1;
	}
```



`동기`

- 지나치게 단순한 메서드는 정리해야 한다.
- 과다한 인다이렉션은 좋지 않다.





## 임시변수 내용 직접 삽입 Inline Temp

- 간단한 수식을 대입받는 임시변수로 인해 다른 리팩토링 기법 적용이 힘들 땐 그 임시변수를 참조하는 부분을 전부 수식으로 치환하자.

```java
	double basePrice = anOrder.basePrice();
	return (basePrice > 1000)
```

to

```java
	return (anOrder.basePrice() > 1000)
```



`동기`



## 임시변수를 메서드 호출로 전환 Replace Temp with Query

- 수식의 결과를 저장하는 임시변수가 있을 땐 그 수식을 뺴내어 메서드로 만든 후, 임시변수 참조 부분을 전부 수식으로 교체하자. 새로 만든 메서드는 다른 메서드에서도 호출 가능하다.

```java
	double basePrice = _quantity * _itemPrice;
	if (basePrice > 1000)
		return basePrice * 0.95;
	else
		return basePrice * 0.98;
```

to

```java
	if (basePrice() > 1000)
		return basePrice() * 0.95;
	else
		return basePrice() * 0.98;
	...
	double basePrice() {
		return _quantity * _itemPrice;
	}
```



`동기`

- 임시변수는 일시적이고 적용이 국소적 범위로 제한된다는 단점이 있다.
- 임시변수는 자신이 속한 메서드 안에서만 인식되므로 코드는 길어지게 된다.
- 임시변수를 메서드 호출로 수정하면 클래스 안 모든 메서드가 그 정보에 접근할 수 있다.



## 직관적 임시변수 사용 Introduce Explaining Variable

- 사용된 수식이 복잡할 땐 수식의 결과나 수식의 일부분을 용도에 부합하는 직관적 이름의 임시변수에 대입하자.

```java
	if ( (platform.toUpperCase().indexOf("MAC") > -1) &&
		(browser.toUpperCase().indexOf("IE") > -1) &&
		wasInitialized() && resize > 0 )
	{
		// do something
	}
```

to

```java
	final boolean isMacOs = platform.toUpperCase().indexOf("MAC") >-1;
	final boolean isIEBrowser = browser.toUpperCase().indexOf("IE") >-1;
	final boolean wasResized = resize > 0;
	if (isMacOs && isIEBrowser && wasInitialized() && wasResized) {
		// do something
	}
```



`동기`

- 수식이 너무 복잡하면 임시변수를 사용해서 직관적으로 수정할 수 있다.
- 임시변수는 가능하면 사용하지 말고 메서드 추출로 해결해보자



## 임시변수 분리 Split Temporary Variable

- 루프나 변수나 값 누적용 임시변수가 아닌 임시변수에 여러번 값이 대입될 땐 각 대입마다 다른 임시변수를 사용하자

```java
	double temp = 2 * (_height + _width);
	System.out.println (temp);
	temp = _height * _width;
	System.out.println (temp);
```

to

```java
	final double perimeter = 2 * (_height + _width);
	System.out.println (perimeter);
	final double area = _height * _width;
	System.out.println (area);
```



`동기`

- 임시변수에는 값이 한번만 대입되어야 한다.



## 매개변수로의 값 대입 제거 Remove Assignments to Parameters

- 매개변수로 값 대입하는 코드가 있을 땐 매개변수 대신 임시변수를 사용하게 수정하자.

```java
	int discount (int inputVal, int quantity, int yearToDate) {
		if (inputVal > 50) inputVal -= 2;
```

to

```java
	int discount (int inputVal, int quantity, int yearToDate) {
		int result = inputVal;
		if (inputVal > 50) result -= 2;
```



`동기`

- 전달받은 매개변수에 다른 값을 대입하면 코드의 명료성이 떨어진다.



## 메서드를 메서드 객체로 전환 Replace Method with Method Object

- 지역변수 때문에 메서드 추출을 적용할 수 없는 긴 메서드가 있을 땐 그 메서드 자체를 객체로 전환해서 모든 지역변수를 객체의 필드로 만들자. 그런 다음 그 메서드를 객체 안의 여러 메서드로 쪼개면 된다.

```java
	class Order...
		double price() {
			double primaryBasePrice;
			double secondaryBasePrice;
			double tertiaryBasePrice;
			// long computation;
			...
		}
```

to

```java
	class Order...
		double price(){
			return new PriceCalculator(this).compute()
		}
	}

	class PriceCalculato {
    double primaryBasePrice;
    double secondaryBasePrice;
    double tertiaryBasePrice;
    
		compute(){
      // long computation;
      return ...
    }
  }
```



`동기`

- 이 책에서 강조하는 핵심은 간결한 메서드의 아름다움 이다.
- 메서드를 메서드 객체로 전환 기법을 적용하면 모든 지역변수가 메서드 객체의 속성이 된다.



## 알고리즘 전환 Substitute Algorithm

- 알고리즘을 더 분명한 것으로 교체해야 할 땐 해당 메서드의 내용을 새 알고리즘으로 바꾸자.

```java
	String foundPerson(String[] people){
		for (int i = 0; i < people.length; i++) {
			if (people[i].equals ("Don")){
				return "Don";
			}
			if (people[i].equals ("John")){
				return "John";
			}
			if (people[i].equals ("Kent")){
				return "Kent";
			}
		}
		return "";
	}
```

to

```java
	String foundPerson(String[] people){
		List candidates = Arrays.asList(new String[] {"Don", "John","Kent"});
	for (int i = 0; i<people.length; i++)
		if (candidates.contains(people[i]))
			return people[i];
	return "";
	}
```



`동기`

- 어떤 기능을 수행하기 위한 비교적 간단한 방법이 있다면 복잡한 방법을 좀 더 간단한 방법으로 교체해야 한다.
- 이런 경우 메서드를 잘게 유지해야 변경하기 쉬워진다.



# #7 객체 간의 기능 이동



## 메서드 이동 Move Method

- 메서드가 자신이 속한 클래스보다 다른 클래스의 기능을 더 많이 이용할 땐 그 메서드가 제일 많이 이용하는 클래스 안에서 비슷한 내용의 새 메서드를 작성하자.
- 기존 메서드는 간단한 대리 메서드로 전환하던지 아예 삭제하자.

```java
	class Class1 {
		aMethod()
	}

	class Class2 {	}
```

to

```java
	class Class1 {	}

	class Class2 {
		aMethod()
	}
```

`동기`

- 클래스에 기능이 너무 많거나 클래스가 다른 클래스와 과하게 연동되어 의존성이 지나칠 때는 메서드를 옮기는 것이 좋다.



## 필드 이동 Move field

- 어떤 필드가 자신이 속한 클래스보다 다른 클래스에서 더 많이 사용될 때는 대상 클래스 안에 새 필드를 선언하고 그 필드 참조 부분을 전부 새 필드 참조로 수정하자

```java
	class Class1 {
		aField
	}

	class Class2 {	}
```

to

```java
	class Class1 {	}

	class Class2 {
		aField
	}
```



`동기`

- 한 클래스에서 다른 클래스로 상태와 기능을 옮기는 것은 리팩토링의 기본이다.
- 지금은 올바르다고 판단해도 나중엔 아닐 수 있는데 문제는 그게 아니라 그런 상황에서도 가만히 냅두는게 문제다.
- 어떤 필드가 자신이 속한 클래스보다 다른 클래스에 있는 메서드를 더 많이 참조해서 정보를 이용한다면 그 필드를 옮기는 것을 생각해 보자.



## 클래스 추출 Extract Class

- 두 클래스가 처리해야 할 기능이 하나의 클래스에 들어 있을 땐 새 클래스를 만들고 기존 클래스의 관련 필드와 메서드를 새 클래스로 옮기자.

```java
	class Person {
		name,
		officeAreaCode,
		officeNumber,
		getTelephoneNumber()
	}
```



to

```java
	class Person {
		name,
		getTelephoneNumber()
	}

	class TelephoneNumber {
		areaCode,
		number,
		getTelephoneNumber()
	}
```

`동기`

- 클래스는 확실하게 추상화되어야 하고, 두세 가지의 명확한 기능을 담당해야 한다.



## 클래스 내용 직접 삽입 Inline Class

- 클래스에 기능이 너무 적을 땐 그 클래스의 모든 기능을 다른 클래스로 합쳐 넣고 원래의 클래스는 삭제하자.

```java
	class Person {
		name,
		getTelephoneNumber()
	}

	class TelephoneNumber {
		areaCode,
		number,
		getTelephoneNumber()
	}
```



to

```java
	class Person {
		name,
		officeAreaCode,
		officeNumber,
		getTelephoneNumber()
	}
```

`동기`

- 클래스 추출과는 반대되는 과정
- 클래스가 더 이상 제 역할을 수행하지 못하여 존재할 이유가 없을 때 실시한다.



## 대리 객체 은폐 Hide Delegate

- 클라이언트가 객체의 대리 클래스를 호출할 땐 대리 클래스를 감추는 메서드를 서버에 작성하자.

```java
	class ClientClass {
		//Dependencies
		Person person = new Person()
		Department department = new Department()
		person.doSomething()
		department.doSomething()
	}
```

to

```java
	class ClientClass {
		Person person = new Person()
		person.doSomething()
	}

	class Person{
		Department department = new Department()
		department.doSomething()
	}
```

`동기`

- 객체에서 핵심 개념 중 하나가 바로 캡슐화다.
- 객체를 캡슐화하면 무언가를 변경할 때 그 변화를 전달해야 할 객체가 줄어드므로 변경하기가 쉬워진다.



```java
> manager = john.getDepartment().getManager();
	class Person {
		Department _department;
		public Department getDepartment() {
			return _department;
		}
		public void setDepartment(Department arg) {
			_department = arg;
			}
		}

	class Department {
		private String _chargeCode;
		private Person _manager;
		public Department (Person manager) {
			_manager = manager;
		}
		public Person getManager() {
			return _manager;
		}
		...
```

to

```java
> manager = john.getManager();
	class Person {
		...
		public Person getManager() {
			return _department.getManager();
		}
	}
```

## 

## 과잉 중개 메서드 제거 Remove Middle Man

- 클래스에 자잘한 위임이 너무 많을 땐 대리 객체를 클라이언트가 직접 호출하게 하자.

```java
	class ClientClass {
		Person person = new Person()
		person.doSomething()
	}

	class Person{
		Department department = new Department()
		department.doSomething()
	}
```



to

```java
	class ClientClass {
		//Dependencies
		Person person = new Person()
		Department department = new Department()
		person.doSomething()
		department.doSomething()
	}
```

- 6개월 전에 수행했던 대리 객체 은폐가 현재는 부적절해질 수도 있다.
- 리팩토링에서는 후회는 불필요하며, 필요해질 때마다 보수하면 된다.



## 외래 클래스에 메서드 추가 Introduce Foreign Method

- 사용 중인 서버 클래스에 메서드를 추가해야 하는데 그 클래스를 수정할 수 없을 땐 클라이언트 클래스 안에 서버 클래스의 인스턴스를 첫 번째 인자로 받는 메서드를 작성하자.



```java
	Date newStart = new Date(previousEnd.getYear(),
                           previousEnd.getMonth(),
                           previousEnd.getDate() + 1);
```

to

```java
	Date newStart = nextDay(previousEnd);

	private static Date nextDay(Date date){
		return new Date(date.getYear(),
                    date.getMonth(),
                    date.getDate() + 1);
	}
```



`동기`

- 원본 클래스를 수정할 수 없을 땐 그 메서드를 클라이언트 클래스 안에 작성해야 한다.



## 국조석 상속확장 클래스 사용 Introduce Local Extension

- 사용 중인 서버 클래스에 여러 개의 메서드를 추가해야 하는데 클래스를 수정할 수 없을 땐 새 클래스를 작성하고 그 안에 필요한 여러 개의 메서드를 작성하자.
- 이 상속확장 클래스를 원본 클래스의 하위클래스나 래퍼 클래스로 만들자.

```java
	class ClientClass(){

		Date date = new Date()
		nextDate = nextDay(date);

		private static Date nextDay(Date date){
			return new Date(date.getYear(),date.getMonth(),date.getDate()+1);
		}
	}
```

to

```java
	class ClientClass() {
		MfDate date = new MfDate()
		nextDate = nextDate(date)
	}
	class MfDate() extends Date {
		...
		private static Date nextDay(Date date){
			return new Date(date.getYear(),date.getMonth(),date.getDate()+1);
		}
	}
```



`동기`

- 필요한 메서드가 한두 개일 경우 외래 클래스에 메서드 추가 기법을 실시하면되지만 필요한 메서드 수가 세 개 이상이면 그 기법으론 무리다.
- 이런 경우 하위 클래스와 wrapper화를 시켜서 필요한 메서드를 모아둘 수 있다.





# #8 데이터 체계화

## 필드 자체 캡슐화 Self Encapsulate Field

- 필드에 직접 접근하던 중 그 필드로의 결합에 문제가 생길 땐 그 필드용 읽기/쓰기 메서드를 작성해서 두 메서드를 통해서만 필드에 접근하게 만들자.

```java
	private int _low, _high;
	boolean includes (int arg) {
		return arg >= _low && arg <= _high;
	}
```

to

```java
	private int _low, _high;
	boolean includes (int arg) {
		return arg >= getLow() && arg <= getHigh();
	}
	int getLow() {return _low;}
	int getHigh() {return _high;}
```

`동기`

- 클래스 내에서 변수에 직접 접근할지, 간접적으로 접근할지에 대한 내용
- 맘가는대로 하면 될듯?



## 데이터 값을 객체로 전환 Replace Data Value with Object

- 데이터 항목에 데이터나 기능을 더 추가해야 할 때는 데이터 항목을 객체로 만들자.

```java
	class Order...{
		private String _customer;
		public Order (String customer) {
			_customer = customer;
		}
	}
```

to

```java
	class Order...{
		public Order (String customer) {
			_customer = new Customer(customer);
		}
	}

	class Customer {
		public Customer (String name) {
			_name = name;
		}
	}
```



`동기`

- 시간이 지나면서 단순한 값의 크기가 커진다면 데이터 객체로 전환하자.





## 값을 참조로 전환 Change Value to Reference

- 클래스에 같은 인스턴스가 많이 들어 있어서 이것들을 하나의 객체로 바꿔야 할 땐 그 객체를 참조 객체로 전환하자.

```java
	class Order...{
		public Order (String customer) {
			_customer = new Customer(customer);
		}
	}

	class Customer {
		public Customer (String name) {
			_name = name;
		}
	}
```

to

```java
	//Use Factory Method
	class Customer...
		static void loadCustomers() {
			new Customer ("Lemon Car Hire").store();
			new Customer ("Associated Coffee Machines").store();
			new Customer ("Bilston Gasworks").store();
		}
		private void store() {
			_instances.put(this.getName(), this);
		}
		public static Customer create (String name) {
			return (Customer) _instances.get(name);
		}
```

`동기`



## 참조를 값으로 전환 Change Reference to Value

- 참조 객체가 작고 수정할 수 없고 관리하기 힘들 땐 그 참조 객체를 값 객체로 만들자.

```java
	new Currency("USD").equals(new Currency("USD")) // returns false
```

to

```java
	// Add `equals` and `hashCode` to the Currency class 
	class Currency{ 
		...
		public boolean equals(Object arg) {
	 		if (! (arg instanceof Currency)) return false;
	 		Currency other = (Currency) arg;
			return (_code.equals(other._code));
		}

		public int hashCode() {
 			return _code.hashCode();
 		}
 	}

 	// Now you can do
	new Currency("USD").equals(new Currency("USD")) // now returns true
```



`동기`

- 값 객체는 변경할 수 없어야 한다는 주요 특징이 있다.





## 배열을 객체로 전환 Replace Array with Object

- 배열을 구성하는 특정 원소가 별의별 의미를 지닐 땐 그 배열을 각 원소마다 필드가 하나씩 든 객체로 전환하자.

```java
	String[] row = new String[3];
	row [0] = "Liverpool";
	row [1] = "15";
```

to

```java
	Performance row = new Performance();
	row.setName("Liverpool");
	row.setWins("15");
```

`동기`

- 배열은 비슷한 객체들의 컬렉션을 일정 순서로 담는 용도로만 사용해야 한다.



## 관측 데이터 복제 Duplicate Observed Data

- 도메인 데이터는 GUI 컨트롤 안에서만 사용 가능한데, 도메인 메서드가 그 데이터에 접근해야 할땐 그 데이터를 도메인 객체로 복사하고, 양측의 데이터를 동기화하는 관측 인터페이스 observer를 작성하자.



`동기`



## 클래스의 단방향 연결을 양방향으로 전환 Change Unidirectional Association to Bidirectional

- 두 클래스가 서로의 기능을 사용해야 하는 데 한 방향으로만 연결되어 있을 땐 역포이넡를 추가하고 두 클래스 모두 업데이트할 수 있게 접근 한정자를 수정하자.

```java
	class Order...
		Customer getCustomer() {
			return _customer;
		}
		void setCustomer (Customer arg) {
			_customer = arg;
		}
		Customer _customer;
	}
```

to

```java
	class Order...
		Customer getCustomer() {
			return _customer;
		}
		void setCustomer (Customer arg) {
			if (_customer != null) _customer.friendOrders().remove(this);
			_customer = arg;
			if (_customer != null) _customer.friendOrders().add(this);
		}
		private Customer _customer;

		class Customer...
			void addOrder(Order arg) {
				arg.setCustomer(this);
			}
			private Set _orders = new HashSet();

			Set friendOrders() {
				/** should only be used by Order */
				return _orders;
			}
		}
	}

	// Many to Many
	class Order... //controlling methods
		void addCustomer (Customer arg) {
			arg.friendOrders().add(this);
			_customers.add(arg);
		}
		void removeCustomer (Customer arg) {
			arg.friendOrders().remove(this);
			_customers.remove(arg);
		}
	class Customer...
		void addOrder(Order arg) {
			arg.addCustomer(this);
		}
		void removeOrder(Order arg) {
			arg.removeCustomer(this);
		}
	}
```



`동기`







