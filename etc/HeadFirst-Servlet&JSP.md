







# #1 서블릿과 JSP는 어디에 쓰는 물건인고?



# #2 웹 애플리케이션 아키텍처



# #3 초간단 미니 MVC 튜토리얼

- 개발환경 만들기

![KakaoTalk_Image_2021-08-18-08-30-55_002](https://user-images.githubusercontent.com/30790184/129813880-e723f6da-c536-4975-a895-3391306f5db9.jpeg)


- 배포환경 만들기

![KakaoTalk_Image_2021-08-18-08-30-55_001](https://user-images.githubusercontent.com/30790184/129813886-aeabd278-61d8-4097-927c-fe44cdfd5919.jpeg)

## 초간단 MVC ver.1

```html
<html><body>
<h1 align="center">Beer Selection Page</h1>
	<center>
		<form method="POST" action="/SelectionBeer.do">
			<select name="color" size="1">
				<option value="light"> light </option>
				<option value="blue"> blue </option>
				<option value="red"> red </option>
			</select>
			<input type="submit"/>
	</center>
</form>
</body>
</html>

```

- form method에 POST

  - POST 요청은 html from을 통해 서버에 전송하고 서버에 변경사항을 만듦.

  - 요청 본문의 유형은 Content-Type 헤더로 결정됨

    - 여러 Content-Type들 [https://www.freeformatter.com/mime-types-list.html](https://www.freeformatter.com/mime-types-list.html)

    | 요청에 본문 존재                                             | 예                  |
    | :----------------------------------------------------------- | ------------------- |
    | 성공 응답에 본문 존재                                        | 예                  |
    | [안전함](https://developer.mozilla.org/ko/docs/Glossary/Safe) | 아니오              |
    | [멱등성](https://developer.mozilla.org/ko/docs/Glossary/Idempotent) | 아니오              |
    | [캐시 가능 (en-US)](https://developer.mozilla.org/en-US/docs/Glossary/cacheable) | 신선도 정보 포함 시 |
    | HTML 양식에서 사용 가능                                      | 예                  |

  - 안전함은 맥락에 따라 여러 의미를 가질 수 있지만 HTTP 메서드가 서버의 상태를 바꾸지 않으면 그 메서드가 안전하다고 말함. ex GET, HEAD, OPTIONS

  - 멱등성은 요청을 한 번 보내는 것과 여러번 연속으로 보내는 것이 같은 효과를 지니고 서버의 상태도 동일하게 남을 때 멱등성이 있다고함. GET, HEAD, PUT, DELETE 메서드는 멱등성을 가지지만 POST는 멱등성을 갖지 않음

  - 기타 HTTP 요청 메서드에 대한 내용 [https://developer.mozilla.org/ko/docs/Web/HTTP/Methods](https://developer.mozilla.org/ko/docs/Web/HTTP/Methods)

- form action에 SelectionBeer.do

  - SelectionBeer.do는 실제 파일명이 아닌 논리적인 이름
  - 작동하는 방식을 알기 위해서는 web.xml(배포 서술자)를 확인하면 됨
  - 클라이언트가 요청한 SelectionBeer.do는 컨테이너가 사용하는 실제 서블릿 클래스 파일에 매핑되어 있음
  - .do는 논리적인 이름의 일부이고 실제 파일의 확장자나 타입이 아님

```xml
<?xml version="1.0" encoding="UTF-8"?>

<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
                      https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
  version="5.0"
  metadata-complete="true">

  <servlet>
    <servlet-name>Ch3 Beer></servlet-name>
    <servlet-class>com.example.web.BeerSelect</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>Ch3 Beer></servlet-name>
    <url-pattern>/SelectionBeer.do</url-pattern>
  </servlet-mapping>

</web-app>

```

- tomcat 디렉토리 모습

![스크린샷 2021-08-18 오전 9 51 24](https://user-images.githubusercontent.com/30790184/129819283-da0dc9ab-fedf-434c-a64d-3455f2963079.png)

- 사이트 모습

![스크린샷 2021-08-18 오후 9 02 03](https://user-images.githubusercontent.com/30790184/129896702-5e9b26af-5a2a-4af0-9d6c-271410eee95c.png)

**'제출'버튼 을 클릭했을때의 과정**

- `action="/SelectionBeer.do"` 에서 `/` 는 context root를 의미함
- submit을 하면 컨에티어는 xml의 `<servlet-mapping>` 항목에서 `/SelectionBeer.do` 이라는 값을 가진 `<url-pattern>`을 찾음
- 일치하는 `<servlet-mapping>`이 있다면 `<servlet-name>` 을 통해 `<servlet-class>` 를 찾음. 이 값이 바로 요청을 처리할 서블릿 클래스
- 이 과정에서 서블릿이 초기화된 적이 없다면 컨테이너는 클래스를 로드하고 초기화함

> 위 문장은 한번 초기화되어 컨테이너에 올라가면 계속 재사용한다는 뜻..?

- 컨테이너는 요청을 처리할 새로운 스레드를 시작하고 서블릿의 service() 메서드에 Request 객체 참조를 인자로 넘김
- 스레드가 완료되면 클라이언트에게 응답을 보냄.









# #4 서블릿이 되어보자

- 컨테이너와 서블릿 생성주기

  1. 사용자가 서블릿에 대한 링크를 클릭함
  2. 컨테이너는 요청된 Request가 서블릿이라는 것을 간파하고는 다음 두개의 객체를 생성함
     1. HttpServletResponse
     2. HttpServletRequest
  3. 접수한 요청의 URL을 분석해서 어떤 서블릿을 요청했는지 파악함. 그 다음 해당 서블릿 스레드를 생성하여 Request, Response 객체 참조를 넘김
  4. 컨테이너는 서블릿 service()메서드를 호출함. 브라우저에서 지정한 방식에 따라 doGet()을 호출할지 doPost()를 호출할지 결정함. 클라이언트가 HTTP GET 메서드를 날렸다면 service() 메서드는 서블릿의 doGet() 메서드를 호출함. 호출할 때 Reqeust와 Response 객체를 인자로 넘김
  5. 서블릿은 클라이언트에게 응답을 작성하기 위해 Response 객체를 사용함. 이 작업을 완료하면, Response에 대한 제어는 컨테이너에게 넘어감
  6. service() 메서드가 끝나면, 스레드를 소멸하거나 아니면 컨테이너가 관리하는 스레드 풀로 돌려 보냄. 그 다음 Request와 Response객체는 가비지 컬렉션이 될 준비를 할 것이며, 이 객체에 대한 참조는 이제 범위를 벗어나기에 사라짐. 마지막으로 클라는 서버로부터 응답을 받게 됨








**References**

- [https://developer.mozilla.org/ko/docs/Web](https://developer.mozilla.org/ko/docs/Web)

# #5 웹 애플리케이션이 되어보자



# #6 대화 상태 유지하기



# #7 JSP가 되어 보자



# #8 스크립트가 없는 페이지



# #9 막강한 커스텀 태그



# #10 JSTL만으로 만족스럽지 않을 때



# #11 웹 애플리케이션을 배포해보자



# #12 비밀스럽게, 안전하게



# #13 막강 필터



# #14 엔터프라이즈 디자인 패턴