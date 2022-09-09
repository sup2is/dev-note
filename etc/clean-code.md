



# Clean Code

- 예제 참고
  - [https://github.com/Yooii-Studios/Clean-Code](https://github.com/Yooii-Studios/Clean-Code)



# #0 들어가면서



# #1 깨끗한 코드

## 코드가 존재하리라

## 나쁜 코드

## 나쁜 코드로 치르는 대가

- 빨리가는 유일한 방법은 언제나 코드를 최대한 깨끗하게 유지하는 습관이다.
- 테스트 코드
  - 테스트 케이스가 없는 코드는 깨끗한 코드가 아니다.
  - 아무리 코드가 우아해도, 아무리 가독성이 높아도, 테스트 케이스가 없으면 깨끗하지 않다.
- 론 제프리스의 클린 코드 꿀팁
  - 중복 줄이기
  - 표현력 높이기
  - 초반부터 간단한 추상화 고려하기

## 우리들 생각

## 우리는 저자다

## 보이스카우트 규칙

- "캠프장은 처음 왔을때보다 더 깨끗하게 놓고 떠나라"

### 프리퀄과 원칙

### 결론



# #2 의미 있는 이름

## 들어가면서

## 의도를 분명히 밝혀라

- 의도가 분명한 이름은 정말 x 1000000 중요하다.

## 그릇된 정보는 피하라

- 다른사람이 읽었을때 애매하다고 생각하는 정보들은 전부 없애자.

### 의미 있게 구분하라

### 발음하기 쉬운 이름을 사용하라

### 검색하기 쉬운 이름을 사용하라

### 인코딩을 피하라

### 클래스 이름

- 클래스 이름과 객체 이름은 명사나 명사구가 적합하다.
- Customer, Account, Manager, Processor

### 메서드 이름

- 메서드 이름은 동사나 동사구가 적합하다
- postPayment, deletePage, save

### 기발한 이름은 피하라

### 한 개념에 한 단어를 사용하라

### 해법 영역에서 가져온 이름을 사용하라

### 문제 영역에서 가져온 이름을 사용하라

### 의미있는 맥락을 추가하라

### 불필요한 맥락을 없애라

### 마치면서

- 좋은 이름을 선택하려면 설명 능력이 뛰어나야 하고 문화적인 배경이 같아야 한다. 
- 사람들이 이름을 바꾸지 않으려는 이유 하나는 다른 개발자가 반대할까 두려워서 이지만 코드를 개선하려는 노력을 중단해서는 안된다. 바꿨을때 좋으면 다른사람들한테도 좋은거다 ...





# #3 함수

## 작게 만들어라!

- 함수를 만드는 첫째 규칙은 '작게!'다. 둘째 규칙도 '작게!'
- 들여쓰기 레벨
  - if문, while문 등에 들어가는 블록의 들여쓰기는 1단만 가능하게 해야 이해하기 쉬운 함수를 만들 수 있다.



## 한 가지만 해라!

- **함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한 가지만을 해야 한다.**
- 어떻게 한가지인지 판단해?
  - 지정된 함수 이름 아래에서 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한가지 작업만 하는 함수다.
  - 단순히 다른 표현이 아니라 의미있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는 여러 작업을 하는 셈이다.



## 함수 당 추상화 수준은 하나로!

## Switch 문

## 서술적인 이름을 사용하라!

- 길고 서술적인 이름이 짧고 어려운 이름보다 좋다.

## 함수 인수

- 함수에서 이상적인 인수 개수
  1. 0개
  2. 1개
  3. 2개
  4. 3개 이상 부터는 피하고
  5. 4개 이상부터는 특별한 이유가 있지 않은 이상 사용하지 말 것
- flag 변수 사용하지 마세요
- 인수의 개수는 가장 작게 유지하세요



## 부수 효과를 일으키지 마라!

```java
public class UserValidator {
  private Cryptographer cryptographer;
  public boolean checkPassword(String userName, String password) { 
    User user = UserGateway.findByName(userName);
    if (user != User.NULL) {
      String codedPhrase = user.getPhraseEncodedByPassword(); 
      String phrase = cryptographer.decrypt(codedPhrase, password); 
      if ("Valid Password".equals(phrase)) {
        Session.initialize();
        return true; 
      }
    }
    return false; 
  }
}
```

- Session.initialize() 는 부수 효과다. checkPassword() 메서드 이름에 알맞게 이런 부수효과는 없어야한다. 만약 필요하다면 메서드 명을 변경해야 한다.



## 명령과 조회를 분리하라!

- 함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야 한다.

```java

// worse
if(set(“username”, “unclebob”)) {
  ...
}

// better
if(attributeExists(“username”)) {
  setAttribute("username", "unclebob")
  ...
}
```





## 오류 코드보다 예외를 사용하라!

- 오류 코드 대신 예외를 사용하면 오류 처리 코드가 원래 코드에서 분리되므로 코드가 깔끔해진다.

```java

// worse
if (deletePage(page) == E_OK) {
  if (registry.deleteReference(page.name) == E_OK) {
    if (configKeys.deleteKey(page.name.makeKey()) == E_OK) {
      logger.log("page deleted");
    } else {
      logger.log("configKey not deleted");
    }
  } else {
    logger.log("deleteReference from registry failed"); 
  } 
} else {
  logger.log("delete failed"); return E_ERROR;
}


// better
public void delete(Page page) {
  try {
      deletePageAndAllReferences(page);
    } catch (Exception e) {
      logError(e);
    }
}

private void deletePageAndAllReferences(Page page) throws Exception { 
  deletePage(page);
  registry.deleteReference(page.name); 
  configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) { 
  logger.log(e.getMessage());
}
```

- 함수는 한 가지 작업만 처리해야 하기 때문에 오류 처리도 오류 처리만 하는 함수가 처리해야 마땅하다. try catch만 하는 함수로 분리하자.

## 반복하지 마라!

- 중복의 문제
  - 코드 길이가 늘어남
  - 알고리즘이 변하면 중복된 곳 모두 수정해야 함
  - 위 문제로 버그가 발생할 확률도 높아짐
- 정말 같은 역할을 하는 메서드의 중복은 반드시 피하자.



## 구조적 프로그래밍

## 함수를 어떻게 짜죠?

## 결론

- 진짜 목표
  - 진짜 목표는 시스템이라는 이야기를 풀어가는데 있다는 사실을 명심하자.
  - 함수가 분명하고 정확한 언어로 깔끔하게 같이 맞아떨어져야 이야기를 풀어가기가 쉬워진다.





# #4 주석

- 잘 달린 주석은 유용하지만 근거 없는 주석은 코드를 이해하기 어렵게 만든다.
- 코드만이 정확한 정보를 제공하는 유일한 출처다. 그러므로 우리는 주석을 가능한 줄이도록 꾸준히 노력해야 한다.



## 주석은 나쁜 코드를 보완하지 못한다

- 코드에 주석을 추가하는 일반적인 이유는 코드 품질이 나쁘기 때문이다.

## 코드로 의도를 표현하라!

```java

//worse
// 직원에게 복지 혜택을 받을 자격이 있는지 검사한다. 
if ((emplotee.flags & HOURLY_FLAG) && (employee.age > 65)

//better
if (employee.isEligibleForFullBenefits())
```



## 좋은 주석

- ### 법적인 주석

- 저작권 정보, 소유권 정보

```java
// Copyright (C) 2003, 2004, 2005 by Object Montor, Inc. All right reserved.
// GNU General Public License
```

### 정보를 제공하는 주석

```java
// 테스트 중인 Responder 인스턴스를 반환
protected abstract Responder responderInstance();
```

### 의도를 설명하는 주석

```java
// 스레드를 대량 생성하는 방법으로 어떻게든 경쟁 조건을 만들려 시도한다. 
for (int i = 0; i > 2500; i++) {
    WidgetBuilderThread widgetBuilderThread = 
        new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
    Thread thread = new Thread(widgetBuilderThread);
    thread.start();
}
```

### 의미를 명료하게 밝히는 주석

### 결과를 경고하는 주석

```java
// 여유 시간이 충분하지 않다면 실행하지 마십시오.
public void _testWithReallyBigFile() {

}
```

### TODO 주석

```java
// TODO-MdM 현재 필요하지 않다.
// 체크아웃 모델을 도입하면 함수가 필요 없다.
protected VersionInfo makeVersion() throws Exception {
    return null;
}
```

### 중요성을 강조하는 주석

```java
String listItemContent = match.group(3).trim();
// 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
// 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다. 
new ListItemWidget(this, listItemContent, this.level + 1);
return buildList(text.substring(match.end()));
```

### 공개 API에서 javadocs





## 나쁜 주석

### 주절거리는 주석

### 같은 이야기를 반복하는 주석

### 오해할 여지가 있는 주석

### 의무적으로 다는 주석

### 이력을 기록하는 주석

### 있으나 마나 한 주석

### 무서운 잡음

### 함수나 변수로 표현할 수 있다면 주석을 달지 마라

```java
// worse
// 전역 목록 <smodule>에 속하는 모듈이 우리가 속한 하위 시스템에 의존하는가?
if (module.getDependSubsystems().contains(subSysMod.getSubSystem()))


// better
ArrayList moduleDependencies = smodule.getDependSubSystems();
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))

```

### 위치를 표시하는 주석

```java
// Actions /////////////////////////////////////////////
```

- 극히 드물게 위와 같은 배너 아래 특정 기능을 모아놓으면 유용한 경우가 있지만 제거하는게 좋을 것 같다.



### 닫는 괄호에 다는 주석

```java
try {
  if() {
  	while() {
  	
  	} //while
  } //if
} // try

```

- 이런 닫는 괄호에 다는 주석이 있다면 함수로 분리하자.

### 공로를 돌리거나 저자를 표시하는 주석

- git이 알아서 기록해준다. 필요없다.

### 주석으로 처리한 코드

- git이 알아서 기록해준다. 필요없다.

### HTML 주석

### 전역 정보

### 너무 많은 정보

### 모호환 관계

### 함수 헤더

### 비공개 코드에서의  javadocs

### 예제





# #5 형식 맞추기

- 프로그래머라면 형식을 깔끔하게 맞춰 코드를 짜야 한다. 코드 형식을 맞추기 위한 간단한 규칙을 정하고 그 규칙을 착실히 따라야 한다.
- 팀으로 일한다면 팀이 합의해 규칙을 정하고 모두가 그 규칙을 따라야 한다. 필요하다면 규칙을 자동으로 적용하는 도구를 활용한다.



## 형식을 맞추는 목적

- 코드 형식은 중요하다!!!
- 오늘 구현한 코드의 가독성은 앞으로 바뀔 코드의 품질에 지대한 영향을 미친다.



## 적절한 행 길이를 유지하라

- 일반적으로 큰 파일보다 작은 파일이 이해하기 쉽다.
- 작은 행으로도 충분히 커다란 프로그램을 만들 수 있다.



### 신문 기사처럼 작성하라

- 이름은 간단하게. 설명이 가능하게.
- 이름만 보고도 올바른 모듈을 살펴보고 있는지 아닌지 판단할 정도로 신경써서 짓자.
- 소스 파일 첫 부분은 고차원 개념과 알고리즘을 설명해야한다.



### 개념은 빈 행으로 분리하라

```java

// worse
package fitnesse.wikitext.widgets;
import java.util.regex.*;
public class BoldWidget extends ParentWidget {
	public static final String REGEXP = "'''.+?'''";
	private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
		Pattern.MULTILINE + Pattern.DOTALL);
	public BoldWidget(ParentWidget parent, String text) throws Exception {
		super(parent);
		Matcher match = pattern.matcher(text); match.find(); 
		addChildWidgets(match.group(1));}
	public String render() throws Exception { 
		StringBuffer html = new StringBuffer("<b>"); 		
		html.append(childHtml()).append("</b>"); 
		return html.toString();
	} 
}

// better

// 빈 행을 넣을 경우
package fitnesse.wikitext.widgets;

import java.util.regex.*;

public class BoldWidget extends ParentWidget {
	public static final String REGEXP = "'''.+?'''";
	private static final Pattern pattern = Pattern.compile("'''(.+?)'''", 
		Pattern.MULTILINE + Pattern.DOTALL
	);
	
	public BoldWidget(ParentWidget parent, String text) throws Exception { 
		super(parent);
		Matcher match = pattern.matcher(text);
		match.find();
		addChildWidgets(match.group(1)); 
	}
	
	public String render() throws Exception { 
		StringBuffer html = new StringBuffer("<b>"); 
		html.append(childHtml()).append("</b>"); 
		return html.toString();
	} 
}
```



### 수직 거리

- 서로 밀접한 개념은 세로로 가까이 둬야 한다. 물론 두 개념이 서로 다른 파일에 속한다면 규칙이 통하지 않지만 타당한 근거가 없다면 서로 밀접한 개념은 한 파일에 속해야 마땅하다.
- 변수는 사용하는 위치에 최대한 가까이 선언한다.
- 인스턴스 변수는 클래스 맨 처음에 선언한다.
- 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다.
- 비슷한 개념의 함수는 종속적인 관계가 없더라도 세로로 가까이 배치한다.



### 세로 순서

- 호출되는 함수를 호출하는 함수보다 나중에 배치하면 소스코드 모듈이 고차원에서 저차원으로 자연스럽게 내려간다.
- 가장 중요한 개념을 표현할 때는 세세한 사항을 최대한 배제해서 독자가 첫 함수 몇개만 읽어도 개념을 파악하기 쉽도록 해야 한다.



## 가로 형식 맞추기

- 짧은 행이 바람직하다.



### 가로 공백과 밀집도

- 가로 공백을 사용해 밀접한 개념과 느슨한 개념을 표현한다. 특히 수식 ..

### 가로 정렬

### 들여 쓰기

### 가짜 범위

## 팀 규칙

- 팀은 한 가지 규칙에 합의해야 하고 모든 팀원은 그 규칙을 따라야 한다.
- 개개인이 따로국밥..?ㅋㅋㅋㅋ 처럼 맘대로 짜대는 코드는 피해야 한다.





# #6 객체와 자료 구조

## 자료 추상화

- 변수를 private으로 설정하더라도 각 값마다 getter, setter를 제공한다면 구현을 외부로 노출하는 셈이다.
- 구현을 감추려면 추상화가 필요하다. 단순히 함수가 아닌 추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스다.
- 자료를 세세하게 공개하기보다는 추상적인 개념으로 표현하는 편이 좋다. 단순히 인터페이스, 함수가 아니다. 개발자는 객체가 포함하는 자료를 표현할 가장 좋은 방법을 심각하게 고민해야 한다. 아무 생각 없이 조회/설정 함수를 추가하는 방법이 가장 나쁘다.



## 자료/객체 비대칭

- 자료와 객체의 상호 보완적인 특징
  - 자료구조를 사용하는 절차적인 코드는 기존 자료구조를 변경하지 않으면서 새 함수를 추가하기 쉽다. 반면 객체 지향 코드는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.
  - 절차적인 코드는 새로운 자료구조를 추가하기 어렵다. 그러려면 모든 함수를 고쳐야 한다. 객체 지향 코드는 새로운 함수를 추가하기 어렵다. 그러려면 모든 클래스를 고쳐야 한다.
- 객체지향에서 어려운 변경은 절차적인 코드에서 쉽고 절차적인 코드에서 어려운 변경은 객체지향 코드에서 쉽다.
- 모든것이 객체라는 생각은 미신이다.
  - 때로는 단순한 자료 구조와 절차적인 코드가 가장 적합한 상황도 있다.



## 디미터 법칙

- 디미터 법칙

  - 모듈은 자식이 조작하는 객체의 속사정을 몰라야 한다.
  - 클래스 C의 f메서드를 호출할 수 있는 객체 종류
    - 클래스 C
    - f가 생성한 객체
    - f 인수로 넘어온 객체
    - C 인스턴스 변수에 저장된 객체

- 디미터 법칙을 깨는 예 (자료가 아니라 객체라면)

  - ```java
    final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath()
    ```



### 기차 충돌

- ```java
  final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath()
  ```

- 이 예제가 디미터 법칙을 위반하는지 여부는 ctxt, Options, ScratchDir이 객체인지 자료구조인지에 달렸다.

  - 객체라면 내부 구조를 숨겨야 하므로 디미터 법칙을 위반한다.

  - 자료구조라면 내부 구조를 노출하므로 디미터 법칙이 적용되지 않는다.

    - 만약 자료구조라면 아래 코드가 더 적합하다.

    - ```java
      final String outputDir = ctxt.options.scratchDir.absolutePath
      ```

- 자료는 무조건 함수 없이 공개변수만 포함하고 객체는 비공개 변수와 공개 함수를 포함한다면 이문제가 훨씬 간단해진다. (특정 프레임워크에서는 힘들 수 있지만 .. )



### 잡종 구조

- 잡종 구조는 피하자
  - 이런 혼란으로 절반은 객체, 절반은 자료구조인 잡종 구조가 나오는데 이는 좋지 않다.
  - 잡종 구조는 되도록 피하는 편이 좋다.



### 구조체 감추기

- 객체라면 의도에 맞게.. 디미터 법칙을 위반하지 않게 구현하자 ..
- 외부에 내부 구조를 드러내지 않고 함수로만 얘기하면 편할 것 같다.



## 자료 전달 객체

- DTO (Data Transfer Object)
  - 공개 변수만 있고 함수가 없는 클래스
  - 데이터베이스와 통신하거나 소켓에서 받은 메시지의 구문을 분석할 때 유용하게 사용



### 활성 레코드

- 활성 레코드는 잡종 구조다
  - DTO에 save나 find같은 메서드를 제공한다.
  - 활성 레코드는 데이터베이스 테이블이나 다른 소스에서 자료를 직접 변환한 결과이다. 이는 바람직하지 않다.
  - 활성 레코드는 자료 구조로 취급해야 하고 비즈니스 규칙을 담으면서 내부 자료를 숨기는 객체는 따로 생성해야 한다.



## 결론

- 객체는 동작을 공개하고 자료를 숨긴다. 그래서 기존 동작을 변경하지 않으면서 새 객체 타입을 추가하기 쉬운 반면 기존 객체에 새 동작을 추가하기는 어렵다.
- 자료 구조는 별다른 동작 업이 자료를 노출한다. 그래서 기존 자료 구조에서 새 동작을 추가하기는 쉬우나, 기존 함수에 새 자료 구조를 추가하기는 어렵다.
- **어떤 시스템을 구현할 때, 새로운 자료 타입을 추가하는 유연성이 필요하면 객체가 더 적합하다. 다른 경우로 새로운 동작을 추가하는 유연성이 필요하면 자료 구조와 절차적인 코드가 더 적합하다.**
- 우수한 소프트웨어 개발자는 편견 없이 이 사실을 이해해 직면한 문제에 최적인 해결책을 선택한다.



# #7 오류 처리

- 뭔가 잘못되면 바로 잡을 책임은 바로 우리 프로그래머에게 있다.

## 오류 코드보다 예외를 사용하라

```java
// Bad
public class DeviceController {
  ...
  public void sendShutDown() {
    DeviceHandle handle = getHandle(DEV1);
    // Check the state of the device
    if (handle != DeviceHandle.INVALID) {
      // Save the device status to the record field
      retrieveDeviceRecord(handle);
      // If not suspended, shut down
      if (record.getStatus() != DEVICE_SUSPENDED) {
        pauseDevice(handle);
        clearDeviceWorkQueue(handle);
        closeDevice(handle);
      } else {
        logger.log("Device suspended. Unable to shut down");
      }
    } else {
      logger.log("Invalid handle for: " + DEV1.toString());
    }
  }
  ...
}
```

```java
// Good
public class DeviceController {
  ...
  public void sendShutDown() {
    try {
      tryToShutDown();
    } catch (DeviceShutDownError e) {
      logger.log(e);
    }
  }
    
  private void tryToShutDown() throws DeviceShutDownError {
    DeviceHandle handle = getHandle(DEV1);
    DeviceRecord record = retrieveDeviceRecord(handle);
    pauseDevice(handle); 
    clearDeviceWorkQueue(handle); 
    closeDevice(handle);
  }
  
  private DeviceHandle getHandle(DeviceID id) {
    ...
    throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    ...
  }
  ...
}
```



## Try-Catch-Finally 문부터 작성하라

- 먼저 강제로 예외를 일으키는 테스트케이스를 작성한 후 테스트를 통과하게 코드를 작성하는 방법을 권장한다.
- 자연스럽게 try 블록의 트랜잭션 범위부터 구현하게 되므로 범위 내에서 트랜잭션 본질을 유지하기 쉬워진다.



## unchecked 예외를 사용하라

- checked예외 없이도 안정적인 프로그램을 만들 수 있다.
- 때로는 checked이 유용한 경우가 있긴하지만 모든 함수가 최하위 함수에서 던지는 예외를 알아야하므로 캡슐화가 깨진다.



## 예외에 의미를 제공하라

- 예외에는 충분한 오류 메시지와 로깅이 필요하다.



## 호출자를 고려해 예외 클래스를 정의하라

- 오류를 분류하는 방법은 많지만 가장 중요한 관심사는 오류를 잡아내는 방법이 되어야 한다.



```java
  // Bad
  // catch문의 내용이 거의 같다.
  
  ACMEPort port = new ACMEPort(12);
  try {
    port.open();
  } catch (DeviceResponseException e) {
    reportPortError(e);
    logger.log("Device response exception", e);
  } catch (ATM1212UnlockedException e) {
    reportPortError(e);
    logger.log("Unlock exception", e);
  } catch (GMXError e) {
    reportPortError(e);
    logger.log("Device response exception");
  } finally {
    ...
  }
```

```java
  // Good
  // ACME 클래스를 LocalPort 클래스로 래핑해 new ACMEPort().open() 메소드에서 던질 수 있는 exception들을 간략화
  
  LocalPort port = new LocalPort(12);
  try {
    port.open();
  } catch (PortDeviceFailure e) {
    reportError(e);
    logger.log(e.getMessage(), e);
  } finally {
    ...
  }
  
  public class LocalPort {
    private ACMEPort innerPort;
    public LocalPort(int portNumber) {
      innerPort = new ACMEPort(portNumber);
    }
    
    public void open() {
      try {
        innerPort.open();
      } catch (DeviceResponseException e) {
        throw new PortDeviceFailure(e);
      } catch (ATM1212UnlockedException e) {
        throw new PortDeviceFailure(e);
      } catch (GMXError e) {
        throw new PortDeviceFailure(e);
      }
    }
    ...
  }
```

- 외부 API를 감싸면서 얻는 장점
  - 프로그램 사이에 의존성이 줄어듦
  - 나중에 다른 라이브러리로 갈아타도 비용이 적음
  - 테스트가 쉬워짐



## 정상 흐름을 정의하라

```java
  // Bad

  try {
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += expenses.getTotal();
  } catch(MealExpensesNotFound e) {
    m_total += getMealPerDiem();
  }
```

- 예외가 논리를 따라가기 어렵게하는 경우. 특수한 상황을 처리할 필요가 없다면 더 좋을 수 있다.

```java
  // Good

  // caller logic.
  ...
  MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
  m_total += expenses.getTotal();
  ...
  
  public class PerDiemMealExpenses implements MealExpenses {
    public int getTotal() {
      // return the per diem default
    }
  }
  
  // 이해를 돕기 위해 직접 추가한 클래스
  public class ExpenseReportDAO {
    ...
    public MealExpenses getMeals(int employeeId) {
      MealExpenses expenses;
      try {
        expenses = expenseReportDAO.getMeals(employee.getID());
      } catch(MealExpensesNotFound e) {
        expenses = new PerDiemMealExpenses();
      }
      
      return expenses;
    }
    ...
  }
```

- 특수 사례 패턴
  - 클래스를 만들거나 객체를 조작해 특수 사례를 처리하는 방식
  - 클래스나 객체가 예외적인 상황을 캡슐화 하기 때문에 클라이언트 코드가 예외적인 상황을 처리할 필요가 없어진다.



## null을 반환하지 마라

```java
  // BAD!!!!

  public void registerItem(Item item) {
    if (item != null) {
      ItemRegistry registry = peristentStore.getItemRegistry();
      if (registry != null) {
        Item existing = registry.getItem(item.getID());
        if (existing.getBillingPeriod().hasRetailOwner()) {
          existing.register(item);
        }
      }
    }
  }
  
```

- null을 반환하는 코드와 습관은 매우매우 좋지 않다.
- 메서드에 null을 반환하고 싶은 유혹이 든다면 그 대신 예외를 던지거나 특수 사례 객체를 반환하는게 해결책이다.

```java
// BAD
List<Employee> employees = getEmployees();
if (emloyees != null) {
  for(Employee e : employees) {
    totalPay += e.getPay();
  }
} 

// GOOD
// 빈 리스트를 반환하는게 더 낫다.
List<Employee> employees = getEmployees();
for(Employee e : employees) {
  totalPay += e.getPay();
}

public List<Employee> getEmployees() {
  if ( ... ) 
    return Collections.emptyList();
}

```





## null을 전달하지 마라

- 메서드의 null을 반환하는것도 나쁜데 메서드로 null을 전달하는건 더 나쁘다.
- 코틀린 사용하자 ..! ㅎㅎ..





## 결론

- 깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.
- 오류 처리를 프로그램 논리와 분리해 독자적인 사안으로 고려하면 튼튼하고 깨끗한 코드를 작성할 수 있다.
- 오류 처리를 프로그램 논리와 분리하면 독립적인 추론이 가능해지며 코드 유지보수성도 크게 높아진다.








