



# Clean Code





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

