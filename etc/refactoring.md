



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





## 클래스 양방향 연결을 단방향으로 전환 Change Bidirectional Association to Unidirectional

`동기`

- 양방향 연결은 객체 관리가 더 힘들어진다.
- 양방향 연결로 인해 두 클래스는 서로 종속된다.
- 양방향 연결은 꼭 필요할 때만 사용해야 한다.



## 마법 숫자를 기호 상수로 전환 Replace Magic Number with Symbolic Constant

- 특수 의미를 지닌 리터럴 숫자가 있을 땐 의미를 살린 이름의 상수를 작성한 후 리터럴 숫자를 그 상수로 교체하자 

```java
	double potentialEnergy(double mass, double height) {
		return mass * 9.81 * height;
	}
```

to

```java
	double potentialEnergy(double mass, double height) {
		return mass * GRAVITATIONAL_CONSTANT * height;
	}
	static final double GRAVITATIONAL_CONSTANT = 9.81;
```

`동기`

- 변경될 가능성이 있는 매직 넘버를 상수화해야한다.



## 필드 캡슐화  Encapsulate Field

- public 필드가 있을 땐 그 필드를 pr ivate로 만들고 필드용 읽기 메서드와 쓰기 메서드를 작성하자.

```java
	public String _name
```

to

```java
	private String _name;
	public String getName() {return _name;}
	public void setName(String arg) {_name = arg;}
```

`동기`

-  데이터가 public 하게 공개되면 모르는사이에 다른 객체가 데이터 값을 읽고 변경할 수 있다.
- 데이터와 데이터를 사용하는 기능이 한 곳에 모여 있으면 수정한 코드가 프로그램 여기저기에 흩어져 있지 않고 한 곳에 있어서 코드를 수정하기 쉽다.



## 컬렉션 캡슐화 Excapsulate Collection

- 메서드가 컬렉션을 반환할 땐 그 메서드가 읽기전용 뷰를 반환하게 수정하고 추가 메서드와 삭제 메서드를 작성하자.

```java
	class Person {
		Person (String name){
			HashSet set new HashSet()
		}
		Set getCourses(){}
		void setCourses(:Set){}
	}
```

to

```java
	class Person {
		Person (String name){
			HashSet set new HashSet()
		}
		Unmodifiable Set getCourses(){}
		void addCourses(:Course){}
		void removeCourses(:Course){}
	}
```

`동기`

- 읽기 메서드는 컬렉션 자체를 반환하면 안된다.
- 컬렉션 조작이 불가능한 형태로 반환하고 불필요하게 자세한 컬렉션 구조는 감춰야한다.
- 컬렉션도 적절하게 캡슐화 되어야 한다.



## 레코드를 데이터 클래스로 전환 Replace Record with Data Class

- 전통적인 프로그래밍 환경에서 레코드 구조를 이용한 인터페이스를 제공해야 할 땐 레코드 구조를 저장할 덤 데이터 객체를 작성하자.

`동기`

- 외부 레코드를 처리할 인터페이스 역할을 하는 클래스를 작성하는게 좋다.
- dto?



## 분류 부호를 클래스로 전환 Replace Type Code with Class

- 기능에 영향을 미치는 숫자형 분류 부호가 든 클래스가 있을 땐 그 숫자를 새 클래스로 바꾸자

```java
	class Person{
		O:Int;
		A:Int;
		B:Int;
		AB:Int;
		bloodGroup:Int;
	}
```



to

```java
	class Person{
		bloodGroup:BloodGroup;
	}

	class BloodGroup{
		O:BloodGroup;
		A:BloodGroup;
		B:BloodGroup;
		AB:BloodGroup;
	}
```

`동기`

- 숫자형 분류 부호를 클래스로 빼자



## 분류 부호를 하위 클래스로 전환 Replace Type Code with Subclasses

- 클래스 기능에 영향을 주는 변경불가 분류 부호가 있을 땐 분류 부호를 하위 클래스로 만들자

```java
	class Employee...
		private int _type;

		static final int ENGINEER = 0;
		static final int SALESMAN = 1;
		static final int MANAGER = 2;

		Employee (int type) {
			_type = type;
		}
	}
```

to

```java
	abstract int getType();
	static Employee create(int type) {
		switch (type) {
			case ENGINEER:
				return new Engineer();
			case SALESMAN:
				return new Salesman();
			case MANAGER:
				return new Manager();
			default:
				throw new IllegalArgumentException("Incorrect type code value");
		}
	}
```

`동기`

-  분류 부호가 든 클래스 코드를 이용해 각 분류 부호별 하위 클래스를 작성하자



## 분류 부호를 상태/전략 패턴으로 전환 Replace Type Code with State/Strategy

- 분류 부호가 클래스의 기능에 영향을 주지만 하위클래스로 전환할 수 없을 땐 그 분류 부호를 상태 객체로 만들자.

```java
	class Employee {
		private int _type;

		static final int ENGINEER = 0;
		static final int SALESMAN = 1;
		static final int MANAGER = 2;

		Employee (int type) {
			_type = type;
		}
		int payAmount() {
			switch (_type) {
				case ENGINEER:
					return _monthlySalary;
				case SALESMAN:
					return _monthlySalary + _commission;
				case MANAGER:
					return _monthlySalary + _bonus;
				default:
					throw new RuntimeException("Incorrect Employee");
				}
			}
		}
	}
```

to

```java
	class Employee...
		static final int ENGINEER = 0;
		static final int SALESMAN = 1;
		static final int MANAGER = 2;

		void setType(int arg) {
			_type = EmployeeType.newType(arg);
		}
		class EmployeeType...
			static EmployeeType newType(int code) {
				switch (code) {
					case ENGINEER:
						return new Engineer();
					case SALESMAN:
						return new Salesman();
					case MANAGER:
						return new Manager();
					default:
						throw new IllegalArgumentException("Incorrect Employee Code");
				}
			}
		}
		int payAmount() {
			switch (getType()) {
				case EmployeeType.ENGINEER:
					return _monthlySalary;
				case EmployeeType.SALESMAN:
					return _monthlySalary + _commission;
				case EmployeeType.MANAGER:
					return _monthlySalary + _bonus;
				default:
					throw new RuntimeException("Incorrect Employee");
			}
		}
	}
```

`동기`



## 하위클래스를 필드로 전환 Replace Subclass with Fields

- 여러 하위클래스가 상수 데이터를 반환하는 메서드르만 다를 땐 각 하위 클래스의 메서드를 상위클래스 필드로 전환하고 하위클래스는 전부 삭제하자.

```java
	abstract class Person {
		abstract boolean isMale();
		abstract char getCode();
		...
	}
	class Male extends Person {
			boolean isMale() {
			return true;
		}
		char getCode() {
			return 'M';
		}
	}
	class Female extends Person {
		boolean isMale() {
			return false;
		}
		char getCode() {
			return 'F';
		}
	}
```

to

```java
	class Person{
		protected Person (boolean isMale, char code) {
			_isMale = isMale;
			_code = code;
		}
		boolean isMale() {
			return _isMale;
		}
		static Person createMale(){
			return new Person(true, 'M');
		}
		static Person createFemale(){
			return new Person(false, 'F');
		}
	}
```



`동기`

- 상수 메서드란 하드코딩된 값을 반환하는 메서드다.





# #9 조건문 간결화



## 조건문 쪼개기  Decompose Conditional

- 복잡한 조건문이 있을 땐  if, then, else 부분을 각 메서드로 빼내자

```java
	if (date.before (SUMMER_START) || date.after(SUMMER_END))
		charge = quantity * _winterRate + _winterServiceCharge;
	else charge = quantity * _summerRate;
```

to

```java
	if (notSummer(date))
		charge = winterCharge(quantity);
	else charge = summerCharge (quantity);
```



`동기`

- 프로그램에서 가장 복잡한 부분은 주로 복잡한 조건문이다.
- 큰 덩어리의 코드를 잘게 쪼개고 각 코드 조각을 용도에 맞는 이름의 메서드 호출로 바꾸면 코드의 용도가 분명히 드러난다. 



## 중복 조건식 통합 Consolidate Conditional Expression

- 여러 조건 검사식의 결과가 같을 땐 하나의 조건문으로 합친 후 메서드로 빼내자.

```java
	double disabilityAmount() {
	if (_seniority < 2) return 0;
	if (_monthsDisabled > 12) return 0;
	if (_isPartTime) return 0;
	// compute the disability amount
```

to

```java
	double disabilityAmount() {
	if (isNotEligableForDisability()) return 0;
	// compute the disability amount
```

`동기`

- 서로 다른 여러 개의 조건 검사식의 결과가 모두 같을 때가 있다.
- 조건식을 메서드로 빼내는 것은 코드를 알아보기 쉽게 만드는 최선의 방법이다.



## 조건문의 공통 실행 코드 빼내기 Consolidate Duplicate Conditional Fragments

- 조건문의 모든 절에 같은 실행 코드가 있을 땐 같은 부분을 조건문 밖으로 뺴자

```java
	if (isSpecialDeal()) {
		total = price * 0.95;
		send();
	}
	else {
		total = price * 0.98;
		send();
	}
```

to

```java
	if (isSpecialDeal()) {
		total = price * 0.95;
	}
	else {
		total = price * 0.98;
	}
	send();
```

`동기`

- 조건문의 절마다 같은 실행 코드가 들어있다면 그 부분을 조건문 밖으로 빼내자.



## 제어 플래그 제거 Remove Control Flag

- 논리 연산식의 제어 플래그 역할을 하는 변수가 있을 땐 그 변수를 break 문이나 return 문으로 바꾸자.

```java
	void checkSecurity(String[] people) {
		boolean found = false;
			for (int i = 0; i < people.length; i++) {
				if (! found) {
					if (people[i].equals ("Don")){
						sendAlert();
						found = true;
					}
					if (people[i].equals ("John")){
						sendAlert();
						found = true;
					}
				}
		}
	}
```

to

```java
	void checkSecurity(String[] people) {
		for (int i = 0; i < people.length; i++) {
			if (people[i].equals ("Don")){
				sendAlert();
				break; // or return
			}
			if (people[i].equals ("John")){
				sendAlert();
				break; // or return
			}
		}
	}
```

`동기`

- 이탈점을 하나만 사용하면 코드 안의 각종 특이한 플래그로 인해 조건문이 복잡해질 수 있다.
- 제어 플래그는 제거하고 break, return, continue를 사용하자.



## 여러 겹의 조건문을 감시 절로 전환 Replace Nested Conditional with Guard Clauses

- 메서드에 조건문이 있어서 정상적인 실행 경로를 파악하기 힘들 땐 모든 특수한 경우에 감시 절을 사용하자.

```java
	double getPayAmount() {
		double result;
		if (_isDead) result = deadAmount();
		else {
			if (_isSeparated) result = separatedAmount();
			else {
				if (_isRetired) result = retiredAmount();
				else result = normalPayAmount();
			};
		}
		return result;
	};
```

to

```java
	double getPayAmount() {
		if (_isDead) return deadAmount();
		if (_isSeparated) return separatedAmount();
		if (_isRetired) return retiredAmount();
		return normalPayAmount();
	};
```

`동기`

- 조건문의 의도는 코드에 반영되어 드러나야 한다.
- 감시절은 값을 반환하거나 예외를 통지하는 부분이다.



## 조건문을 재정의로 전환 Replace Conditional with PolyMorphism

- 객체 타입에 따라 다른 기능을 실행하는 조건문이 있을 땐 조건문의 각 절의 하위 클래스의 재정의 메서드 안으로 옮기고 원본 메서드는  abstract 타입으로 수정하자.

```java
	class Employee {
		private int _type;

		static final int ENGINEER = 0;
		static final int SALESMAN = 1;
		static final int MANAGER = 2;

		Employee (int type) {
			_type = type;
		}
		int payAmount() {
			switch (_type) {
				case ENGINEER:
					return _monthlySalary;
				case SALESMAN:
					return _monthlySalary + _commission;
				case MANAGER:
					return _monthlySalary + _bonus;
				default:
					throw new RuntimeException("Incorrect Employee");
				}
			}
		}
	}
```

to

```java
	class Employee...
		static final int ENGINEER = 0;
		static final int SALESMAN = 1;
		static final int MANAGER = 2;

		void setType(int arg) {
			_type = EmployeeType.newType(arg);
		}
		int payAmount() {
			return _type.payAmount(this);
		}
	}

	class Engineer...
		int payAmount(Employee emp) {
			return emp.getMonthlySalary();
		}
	}
```



`동기`

-  다형성을 통해 일일이 조건문을 작성하지 않아도 다형적으로 호출되게할 수 있다.





## Null 검사를 널 객체에 위임 Introduce Null Object

- null 값을 검사하는 코드가 계속 나올 땐 null 값을 널 객체로 만들자

- ```java
  	if (customer == null) plan = BillingPlan.basic();
  	else plan = customer.getPlan();
  ```

  to

  ```java
  	class Customer {}
  
  	class NullCusomer extends Customer {}
  ```

`동기`

- Objects.isNull()은 어떨까?



## 어설션 넣기 Introduce Assertion

- 일부 코드가 프로그램의 어떤 상태를 전제할 땐 어설션을 넣어서 그 전제를 확실하게 코드로 작성하자.

```java
	double getExpenseLimit() {
		// should have either expense limit or a primary project
		return (_expenseLimit != NULL_EXPENSE) ?
			_expenseLimit:
			_primaryProject.getMemberExpenseLimit();
	}
```

to

```java
	double getExpenseLimit() {
		Assert.isTrue (_expenseLimit != NULL_EXPENSE || _primaryProject	!= null);
		return (_expenseLimit != NULL_EXPENSE) ?
			_expenseLimit:
			_primaryProject.getMemberExpenseLimit();
	}
```

- 어설션이랑 항상 참으로 전제되는 조건문을 뜻한다.
- 어설션이 실패하면 그건 프로그래머가 오류를 범한 것이다.
- 어설션은 대개 제품화 단계에서 삭제한다.
- 어설션은 시스템에 영향을 미치지 않는다.
- 어설션은 의사소통과 디버깅에 도움을 주고 좀 더 근원적인 버그를 잡아낼 수 있다.



# #10 메서드 호출 단순화

- 좋은 인터페이스는 보여줘야 할 만큼만 딱 보여준다. 공개할 필요가 없는 것들을 감추는 것만으로도 인터페이스를 개선할 수 있다.

## 메서드명 변경

- 메서드명을 봐도 기능을 알 수 없을 땐 메서드명의 직관적인 이름으로 바꾸자.

```java
	getinvcdtlmt()
```

to

```java
	getInvoiceableCreditLimit()
```

`동기`

- 복잡한 과정을 여러 작은 메서드로 쪼개는 과정에서 작은 메서드들의 역할을 파악하기 힘들어질 수도 있다.
- 메서드 명을 잘 지어야 한다.
- 메서드의 이름이 명확하지 않다면 반드시 변경해야 한다.



## 매개변수 추가 Add Parameter

- 메서드가 자신을 호출한 부분의 정보를 더 많이 알아야 할 땐 객체에 그 정보를 전달할 수 있는 매개변수를 추가하자.

```java
	getContact()
```

to

```java
	getContact(:Date)
```



`동기`

- 사실 이 방법은 매개변수 세트가 더 길어지기 때문에 좋지 않을 수도 있다.
- 매개변수를 객체로 전환하는 방법도 있다.



## 매개변수 제거 Remove Parameter

- 메서드가 어떤 매개변수를 더 이상 사용하지 않을 땐 그 매개변수를 삭제하자.

```
	getContact(:Date)
```

to

```
	getContact()
```



`동기`

- 사용하지 않는 파라미터는 제거하자.



## 상태 변경 메서드와 값 반환 메서드를 분리 Separate Query from Modifier

- 값 반환 기능과 객체 상태 변경 기느이 한 메서드에 들어 있을 땐 질의 메서드와 변경 메서드로 분리하자.

```java
	getTotalOutStandingAndSetReadyForSummaries()
```

to

```java
	getTotalOutStanding()
	SetReadyForSummaries()
```

`동기`

- 값을 반환하는 모든 메서드는 눈에 띄는 부작용이 없어야 한다는 규칙을 따르는 것이 좋다.
- 부작용이 있는 메서드와 부작용 없는 메서드의 차이를 명확하게 분리하는게 좋다.



## 메서드를 매개변수로 전환 Parameterize Method

- 여러 메서드가 기능은 비슷하고 안에 든 값만 다를 땐 서로 다른 값을 하나의 매개변수로 전달받는 메서드를 하나 작성하자.

```java
	fivePercentRaise()
	tenPercentRaise()
```

to

```java
	raise(percentage)
```

`동기`

- 기능은 비슷하지만 몇가지 값에 따라 결과가 달라지는 메서드가 여러개 있을 때 각 메서드를 전달된 매개변수에 따라 다른 작업을 처리하는 하나의 메서드로 만들면 편리하다.



## 매개변수를 메서드로 전환 Replace Parameter with Explicit Methods

- 매개변수로 전달된 값에 따라 메서드가 다른 코드를 실행할 땐 그 매개변수로 전달될 수 있는 모든 값에 대응하는 메서드를 각각 작성하자.

```java
	void setValue (String name, int value) {
		if (name.equals("height")){}
			_height = value;
			return;
		}
		if (name.equals("width")){
			_width = value;
			return;
		}
		Assert.shouldNeverReachHere();
	}
```

to

```java
	void setHeight(int arg) {
		_height = arg;
	}
	void setWidth (int arg) {
		_width = arg;
	}
```

`동기`

- 일반적으로 한 매개변수의 값이 여러 개가 될 수 있을때 조건문 안에서 각 값을 검사하여 다른 기능을 수행하는 메서드에 적용 가능하다.
- 인터페이스의 명료성은 언제나 큰 장점이다.



## 객체를 통째로 전달 Preserve Whole Object

- 객체에서 가져온 여러 값을 메서드 호출에서 매개변수로 전달할 땐 그 객체를 통째로 전달하게 수정하자.

```java
	int low = daysTempRange().getLow();
	int high = daysTempRange().getHigh();
	withinPlan = plan.withinRange(low, high);
```

to

```java
	withinPlan = plan.withinRange(daysTempRange());
```

`동기 `

- 한 객체에 든 여러 값을 메서드 호출할 때
- 매개변수 세트의 변경의 편의성뿐 아니라 코드를 알아보기도 쉬워진다.



## 매개변수 세트를 메서드로 전환 Replace Parameter with Method

- 객체가 A 메서드를 호출해서 그 결과를 B 메서드에 매개변수로 전달하는데, 결과를 매개변수로 받는 B 메서드도 직접 A메서드로 호출할 수 있을 땐 매개변수를 없애고 A 메서드를 B 메서드가 호출하게 하자.

```java
	int basePrice = _quantity * _itemPrice;
	discountLevel = getDiscountLevel();
	double finalPrice = discountedPrice (basePrice, discountLevel);
```

to

```java
	int basePrice = _quantity * _itemPrice;
	double finalPrice = discountedPrice (basePrice);
```

`동기`

- 메서드가 매개변수로 전달받는 값을 다른 방법으로 가져올 수 있다면 그 방법을 택해야 한다.
- 매개변수가 많아지면 코드가 복잡해지므로 가능하면 매개변수를 줄여야 한다.



## 매개변수 세트를 객체로 전환 Introduce Parameter Object

- 여러 개의 매개변수가 항상 붙어 다닐 땐 그 매개변수들을 객체로 바꾸자

```java
	class Customer{
		amountInvoicedIn (start : Date, end : Date)
		amountReceivedIn (start : Date, end : Date)
		amountOverdueIn (start : Date, end : Date)
	}
```



to

```java
	class Customer{
		amountInvoicedIn (: DateRange)
		amountReceivedIn (: DateRange)
		amountOverdueIn (: DateRange)
	}
```

`동기`

- 특정 매개변수들이 늘 함께 전달되는 경우 데이터 그룹으로 묶으려면 이 매개변수들을 객체로 바꾸는 것이 좋다.



## 쓰기 메서드 제거 Remove Setting Method

- 생성할 때 지정한 필드 값이 절대로 변경되지 말아야 할 땐 그 필드를 설정하는 모든 쓰기 메서드를 삭제하자.

```java
	class Employee{
		setImmutableValue()
	}
```

to

```java
	class Employee{
		¯\_(ツ)_/¯
	}
```

`동기`

- 필드가 불변이어야 한다면 쓰기 메서드를 삭제하자.



## 메서드 은폐 Hide Method

- 메서드가 다른 클래스에 사용되지 않을 땐 그 메서드의 반환 타입을 private로 만들자

```java
	class Employee{
		public method()
	}
```

to

```java
	class Employee{
		private method()
	}
```



`동기`

-  다른 클래스가 그 메서드를 사용한다면 개방도를 높이고 사용하지 않는다면 개방도를 낮추자.



## 생성자를 팩토리 메서드로 전환 Replace Constructor with Factory Method

- 객체를 생성할 때 단순한 생성만 수행하게 해야 할 땐 생성자를 팩토리 메서드로 교체하자.

```java
	Employee (int type) {
		_type = type;
	}
```

to

```java
	static Employee create(int type) {
		return new Employee(type);
	}
```

`동기`



## 하향 타입 변환을 캡슐화 Encapsulate Downcase

- 메서드가 반환하는 객체를 호출 부분에서 하향 타입 변환해야할 땐 하향 타입 변환 기능을 메서드 안으로 옮기자.

```java
	Object lastReading() {
		return readings.lastElement();
	}
```



to

```java
	Reading lastReading() {
		return (Reading) readings.lastElement();
	}
```

`동기`

- 클라이언트가 하향 타입을 변환하게 하지 말고 가능하면 항상 가장 구체적인 타입을 제공해야 한다.



## 에러 부호를 예외 통지로 교체 Replace Error Code with Exception

- 메서드가 에러를 나타내는 특수한 부호를 반환할 땐 그 부호 반환 코드를 에외 통지 코드로 바꾸자.

```java
	int withdraw(int amount) {
		if (amount > _balance)
			return -1;
		else {
			_balance -= amount;
			return 0;
		}
	}
```



to

```java
	void withdraw(int amount) throws BalanceException {
		if (amount > _balance)
			throw new BalanceException();
		_balance -= amount;
	}
```

`동기`

- 예외는 에러 처리를 일반적인 처리와 확실히 분리시키기 때문에 좋다.



## 예외 처리를 테스트로 교체 Replace Exception with Test

- 호출 부분에 사전 검사 코드를 넣으면 될 상황인데 예외 통지를 사용했을 땐 호출 부분이 사전 검사를 실시하게 수정하자.

```java
	double getValueForPeriod (int periodNumber) {
		try {
			return _values[periodNumber];
		} catch (ArrayIndexOutOfBoundsException e) {
			return 0;
		}
	}
```



to

```java
	double getValueForPeriod (int periodNumber) {
		if (periodNumber >= _values.length)
			return 0;
		return _values[periodNumber];
}
```



`동기`

- 예외 처리는 예외적 기능, 즉 예기치 못한 에러에 사용해야 한다. 예외 처리를 조건문 대용으로 사용해선 안된다.





# #11 일반화 처리

## 필드 상향 Pull Up Field

- 두 하위클래스에 같은 필드가 들어 있을 땐 필드를 상위클래스로 옮기자.

*Move the field to the superclass*

```java
	class Salesman extends Employee{
		String name;
	}
	class Engineer extends Employee{
		String name;
	}
```

to

```java
	class Employee{
		String name;
	}
	class Salesman extends Employee{}
	class Engineer extends Employee{}
```

`동기`

- 중복된 필드가 하위 클래스에 존재한다면 상위 클래스로 옮기자.



## 메서드 상향 Pull Up Method

- 기능이 같은 메서드가 여러 하위 클래스에 들어 있을 땐 그 메서드를 상위 클래스 옮기자.

```java
	class Salesman extends Employee{
		String getName();
	}
	class Engineer extends Employee{
		String getName();
	}
```

to

```java
	class Employee{
		String getName();
	}
	class Salesman extends Employee{}
	class Engineer extends Employee{}
```

`동기`

-  하위클래스의 메서드가 상위클래스 메서드를 재정의함에도 불구하고 기능이 같을 때 이 방법을 사용하자.



## 생성자 내용 상향 Pull Up Constructor Body

- 하위클래스마다 거의 비슷한 내용의 생성자가 있을 땐 상위 클래스에 생성자를 작성하고, 그 생성자를 하위클래스의 메서드에서 호출하자

```java
	class Manager extends Employee...
		public Manager (String name, String id, int grade) {
		_name = name;
		_id = id;
		_grade = grade;
	}
```

to

```java
	public Manager (String name, String id, int grade) {
		super (name, id);
		_grade = grade;
	}
```

`동기`



## 메서드 하향 Push Dwon Method

- 상위 클래스에 있는 기능을 일부 하위클래스만 사용할 땐 그 기능을 관련된 하위 클래스 안으로 옮기자.

```java
	class Employee{
		int getQuota();
	}
	class Salesman extends Employee{}
	class Engineer extends Employee{}
```

to

```java
	class Salesman extends Employee{
		int getQuota();
	}
	class Engineer extends Employee{}
```

`동기`



## 필드 하향 Push Down Field

- 일부 하위클래스만이 사용하는 필드가 있을 땐 그 필드를 사용하는 하위클래스로 옮기자.

```java
	class Employee{
		int quota;
	}
	class Salesman extends Employee{}
	class Engineer extends Employee{}
```



to

```java
	class Salesman extends Employee{
		int quota;
	}
	class Engineer extends Employee{}
```





## 하위클래스 추출 Extract Subclass

- 일부 인스턴스에만 사용되는 기능이 든 클래스가 있을 땐 그 기능 부분을 전담하는 하위클래스를 작성하자.

```java
	class JobItem	{
		getTotalPrices()
		getUnitPrice()
		getEmployee()
	}
```



to

```java
	class JobItem	{
		getTotalPrices()
		getUnitPrice()
	}
	class class LabotItem extends JobItem	{
		getUnitPrice()
		getEmployee()
	}
```





## 상위클래스 추출 Extract Superclass

- 기능이 비슷한 두 클래스가 있을 땐 상위 클래스를 작성하고 공통된 기능들을 그 상위 클래스로 옮기자.

```java
	class Department{
		getTotalAnnualCost()
		getName()
		getHeadCount
	}
	class Employee{
		getAnnualCost()
		getName()
		getId
	}
```

to

```java
	class Party{
		getAnnualCost()
		getName()
	}
	class Department {
		getAnnualCost()
		getHeadCount
	}
	class Employee {
		getAnnualCost()
		getId
	}
```

 

## 인터페이스 추출

- 클래스 인터페이스의 같은 부분을 여러 클라이언트가 사용하거나 두 클래스에 인터페이스의 일부분이 공통으로 들어 있을 땐 곹오 부분을 인터페이스로 빼내자.

```java
	class Employee {
		getRate()
		hasSpecialSkill()
		getName()
		getDepartment()
	}
```

to

```java
	interface Billable	{
		getRate()
		hasSpecialSkill()
	}
	class Employee implements Billable	{
		getRate
		hasSpecialSkill()
		getName()
		getDepartment()
	}
```

`동기`



## 계층 병합 Collapse Hierarchy

- 상위클래스와 하위클래스가 거의 다르지 않을 땐 둘을 합치자

```java
	class Employee{	}
	class Salesman extends Employee{	}
```



to

```java
	class Employee{	}
```





## 템플릿 메서드 형성 Form Template Method

- 하위클래스 안의 두 메서드가 거의 비슷한 단계들을 같은 순서로 수행할 떈 그 단계들을 시그니처가 같은 두 개의 메서드로 만들어서 두 원본 메서드를 같게 만든 후, 두 메서드를 상위클래스로 옮기자.

```java
	class Site{}
	class ResidentialSite extends Site{
		getBillableAmount()
	}
	class LifelineSite extends Site{
		getBillableAmount()
	}
```

to

```java
	class Site{ 	
		getBillableAmount()
		getBaseAmount()
		getTaxAmount()
	}
	class ResidentialSite extends Site{
		getBaseAmount()
		getTaxAmount()
	}
	class LifelineSite extends Site{
		getBaseAmount()
		getTaxAmount()
	}
```

`동기`

- 템플릿 메서드 패턴을 확인하자.



## 상속을 위임으로 전환 Replace Inheritance with Delegation 

- 하위클래스가 상위클래스 인터페이스의 일부만 사용할 때나 데이터를 상속받지 않게 해야 할 땐 상위클래스에 필드를 작성하고, 모든 메서드가 그 상위클래스에 위임하게 수정한 후 하위클래스를 없애자.

```java
	class Vector{
		isEmpty()
	}

	class Stack extends Vector {}
```

to

```java
	class Vector {
		isEmpty()
	}

	class Stack {
		Vector vector
		isEmpty(){
			return vector.isEmpty()
		}
	}
```



## 위임을 상속으로 전환 Replace Delegation with Inheritance

- 위임을 이용 중인데 인터페이스 전반에 간단한 위임으로 도배하게 될 땐 위임 클래스를 대리 객체의 하위클래스로 만들자.

```java
	class Person {
		getName()
	}

	class Employee {
		Person person
		getName(){
			return person.getName()
		}
	}
```



to

```java
	class Person{
		getName()
	}
	class Employee extends Person{}
```





# #12 복합 리팩토링

##  상속 구조 정리 Tease Apart Inheritance

- 하나의 상속 계층이 두 작업을 동시에 수행할 땐 상속 계층을 하나 더 만들어서 위임을 통해 다른 계층을 호출하자.

```java
	class Deal{}
	class ActiveDeal extends Deal{}
	class PassiveDeal extends Deal{}
	class TabularActiveDeal extends ActiveDeal{}
	class TabularPassiveDeal extends PassiveDeal{}
```

to

```java
	class Deal{
		PresentationStyle presettationStyle;
	}
	class ActiveDeal extends Deal{}
	class PassiveDeal extends Deal{}

	class PresentationStyle{}
	class TabularPresentationStyle extends PresentationStyle{}
	class SinglePresentationStyle extends PresentationStyle{}
```

`동기`



## 절차 코드를 객체로 전환 Convert Procedural Design to Object

- 코드가 절차식으로 작성되어 있을 땐 데이터 레코드를 객체로 바꾸고, 기능을 쪼개서 각각의 객체로 옮기자.

```java
	class OrderCalculator{
		determinePrice(Order)
		determineTaxes(Order)
	}
	class Order{}
	class OrderLine{}
```

to

```java
	class Order{
		getPrice()
		getTaxes()
	}
	class OrderLine{
		getPrice()
		getTaxes()
	}
```



## 도메인 로직을 표현과 분리 Separate Domain from Presentation

- 도메인 로직이 들어 있는 GUI 클래스가 있을 땐 도메인 로직을 별도의 도메인 클래스로 떼어내자.

```java
	class OrderWindow{}
```

to

```java
	class OrderWindow{
		Order order;
}
```



## 계층구조 추출 Extract Hierarchy

- 한 클래스에 기능이 너무 많고 일부분에라도 조건문이 많을 땐 각 조건에 해당하는 하위클래스를 작성해서 계층 구조를 만들자.

```java
	class BillingScheme{}
```

to

```java
	class BillingScheme{}
	class BusinessBillingScheme extends BillingScheme{}
	class ResidentialBillingScheme extends BillingScheme{}
	class DisabilityBillingScheme extends BillingScheme{}
```





# #13 리팩토링, 재사용, 현실성

## 현실성 검토

## 개발자가 리팩토링을 꺼리는 이유

- 리팩토링 방법을 몰라서
- 리팩토링의 장점은 오랜 시간이 흘러야 가시화될 테고, 그때가 되면 프로젝트 팀원도 아닐텐데 공연히 리팩토링에 힘 뺄 필요 없어서
- 코드를 리팩토링하는 일은 추가적인 부담인데다, 월급은 새 기능을 추가하라고 주는 거지 리팩토링하라고 주는건 아니여서
- 리팩토링하다가 괜히 멀쩡한 프로그램을 망가뜨릴까 걱정돼서



`리팩토링은 어디에 어떻게 적용하나`

- 리팩토링해야 하는 공통적인 하나의 이유는 새 기능을 더 쉽게 추가할 수 있게 프로그램을 개선하기 위해서이다.



`단기적 목적 달성을 위한 리팩토링`

- 소프트웨어의 수정과 유지보수가 간편해진다.



`리팩토링의 추가 부담 줄이기`

- 널려 있는 도구와 기술을 사용하면 신속하고 간편하게 리팩토링할 수 있다.
- 일부 객체지향 개발자의 경험상 리팩토링으로 인한 추가 부담은 프로그램의 각 개발 단계마다 줄어든 수고와 시간을 상쇄하고도 남는다.
- 리팩토링이 처음엔 서툴고 부담되는 작업으로 느껴질 수 있지만, 프로그램 개발 과정에 하나의 단계로 넣으면 안해도 되는 번외 업무라는 생각은 사라지고 필수적인 작업으로 여겨진다.
- 일단 해보자..!!!!



`안전하게 리팩토링하기`

- 자신의 코딩 능력에 신념을 갖자.
- 자신이 놓친 에러는 컴파일러가 잡아서 처리하리란 믿음을 갖자.
- 컴파일러마저 놓친 에러는 테스트 스위트가 잡아 처리하리란 신뢰를 갖자.
- 테스트 스위트마저 놓친 에러는 코드 검수 단계에서 발견하리란 확신을 갖자.



## 현실성 다시 검토하기

## 리팩토링 자료와 참고문헌

## 소프트웨어 재사용과 기술 변경이 미치는 영향



