# 예제

https://github.com/Jpub/SpringInAction5





# #1 스프링 기초 

## 1.1 스프링이란

- 대부분의 애플리케이션은 애플리케이션 전체 기능 중 일부를 담당하는 많은 컴포넌트로 구성되며 각 컴포넌트는 다른 애플리케이션 구성 요소와 협력해서 작업을 처리함
- 스프링은 스프링 애플리케이션 컨텍스트라는 컨테이너를 제공하는데 여기서 컴포넌트들을 생성하고 관리함 (bean) bean은 각 각 컴포넌트가 됨
- 빈의 상호 연결은 의존성 주입이라고 알려진 패턴을 기반으로 수행됨

## 1.2 스프링 애플리케이션 초기 설정하기

- 스타터 의존성의 장점 세가지
  - 우리가 필요ㅗ로 하는 모든 라이브러리의 의존성을 선언하지 않아도 되므로 빌드 파일이 훨씬 작아지고 관리하기 쉬워짐
  - 라이브러리 이름이 아닌 기능의 관점으로 의존성을 생각할 수 있음 따라서 만일 웹 애플리케이션을 개발한다면 웹 애플리케이션을 작성할 수 있게 라이브러리들을 일일이 지정하느 ㄴ대신에 여기처럼 웹 스타터 의존성만 추가하면 됨
  - 버전도 알아서 맞춰줌
- @SpringBootApplication은 @SpringBootConfiguration, @EnableAutoConfiguration, @ComponentScan 총 세개로 이루어진 애너테이션

## 1.3 스프링 애플리케이션 작성하기

### 웹 요청 처리하기

### 뷰 정의하기

###  컨트롤러 테스트하기

- MockMVC 테스트할때 클래스레벨에 @WebMvcTest({controller.claass}) 지정하면 스프링 MVC 애플리케이션의 형태로 테스트가 실행됨

```javascript
@RunWith(SpringRunner.class)
@WebMvcTest(HomeController.class)    // HomeController의 웹 페이지 테스트
public class HomeControllerTest {

  @Autowired
  private MockMvc mockMvc;    // MockMvc를 주입한다

  @Test
  public void testHomePage() throws Exception {
    mockMvc.perform(get("/"))      // GET /를 수행한다

      .andExpect(status().isOk())     // HTTP 200이 되어야 한다

      .andExpect(view().name("home"))  // home 뷰가 있어야 한다

      .andExpect(content().string(       // 콘텐츠에 Welcome to...가 포함되어야 한다
          containsString("Welcome to...")));
  }
}

```

### 애플리케이션 빌드하고 실행하기

### 스프링 부트 DevTools 알아보기

- 코드가 변경될 때 자동으로 애플리케이션을 다시 시작시킨다.
- 브라우저로 전송되는 리소스가 변경될때 자동으로 브라우저를 새로고침한다
- 템플릿 캐시를 자동으로 비활성화 한다
- 만일 h2데이터베이스가 사용중이라면 자동으로 h2 콘솔을 활성화한다.

<br>

- 템플릿 엔진에서는 템플릿 파싱 결과를 캐시에 저장하기때문에 개발단계에서는 좋지 않음



### 리뷰하기

- 스프링을 사용해서 실제 비지니스 로직에만 집중할 수 있게 함
- web과 thymeleaf 의존성을 선언하면 부트에서 일어나는 일들
  - 스프링 mvc를 활성화하기 위해 스프링 애플리케이션 컨텍스트에 관련된 빈들을 구성함
  - 내장된 톰캣 서브를 스프링 애플리케이션 컨텍스트에 구성함
  - thymeleaf 뷰 리졸버를 구성함

## 1.4 스프링 살펴보기

### 핵심 스프링 프레임 워크

### 스프링 부트

- 액추에이터는 애플리케이션의 내부 작동을 런타임 시에 살펴볼 수 있는 기능을 제공하고 메트릭, 스레드 덤프 정보, 애플리케이션의 상태, 애플리케이션에서 사용할 수 있는 환경 속성 포함 제공
- 환경 속성의 명세 제공
- 핵심 프레임워크에 추가되는 테스트 지원 제공
- 스프링 부트 cli도 제공함 이걸 사용하면 애플리케이션 전체를 그루비 스크립트들로 작성해서 명령행에서 실행 가능

### 스프링 데이터

- 다양한 dbms 사용 가능

### 스프링 시큐리티

### 스프링 통합과 배치

### 스프링 클라우드

## 요약

- 웹 애플리케이션 생성, 데이터베이스 사용, 애플리케이션 보안, 마이크로서비스 등에서 개발자의 노력을 덜어주는 것이 스프링의 목표
- 스프링 부트는 손쉬운 의존성 관리, 자동-구성, 런타임 시의 애플리케이션 내부 작동 파악을 스프링에서 할 수 있게 함
- 스프링 애플리케이션은 스프링 Initializr를 사용해서 초기 설정 가능 스프링 Initializr는 웹을 기반으로 하며 대부분 자바 개발 환경을 지원함
- 빈이라고 하는 컴포넌트는 스프링 애플리케이션 컨텍스트에서 자바나 xml로 선언할 수 있으며, 컴포넌트 탐색으로 찾거나 스프링 부트 자동- 구성에서 자동으로 구성할 수도 있음



# #2 웹 애플리케이션 개발하기

- @CreditCardNumber는 Luhn 알고리즘 검사에 합격한 유효한 신용카드 번호를 validate 해줌

## 요약

- 스프링은 스프링 MVC 라는 강력한 웹 프레임워크를 제공하는데, 스프링 MVC는 스프링 애플리케이션의 웹 프론트엔드 개발에 사용함
- 스프링 MVC는 애노테이션을 기반으로 하며, @RequestMapping, @GetMapping, @PostMapping과 같은 애노테이션을 사용해서 요청 처리 메서드를 선언할 수 있음
- 대부분의 요청 처리 메서드들은 마지막에 Thymeleaf 템플릿과 같은 논리 뷰 이름을 반환함. 모데이터와 함께 해당 요청을 전달하기 위함임
- 스프링 MVC는 자바 빈 유효성 검사 api와 Hibernamte Validator 등의 유효성 검사 api 구현 컴포넌트를 통해 유효성 검사를 지원함
- 모델 데이터가 없거나 처리할 필요가 없는 HTTP GET 요청을 처리할 때는 뷰 컨트롤러를 사용할 수 있음
- Thymeleaf에 추가하여 스프링은 다양한 뷰 템플릿을 지원함





# #3 데이터로 작업하기

## JDBC를 사용해서 데이터 읽고 쓰기

- 스프링 JDBC 지원은 JdbcTemplate 클래스에 기반을 둠모든 형식적이고 상투적인 코드 없이 개발자가 rdb에 대한 sql 연산을 수행할 수 있는 방법을 제공함
- jdbctemplate.update()는 upsert 가능



## 스프링 데이터 JPA를 사용해서 데이터 저장하고 사용하기

## 요약

- 스프링의 jdbctemplate는 jdbc 작업을 굉장히 쉽게 해준다.
- 데이터베이스가 생성해주는 id의 값을 알아야 할 때는 preparedStatementCreator와 keyholder를 함께 사용할 수 있다.
- 데이터 추가를 쉽게 실행할 때는 Simplejdbcinsert를 사용하자
- 스프링 데이터 jpa는 리퍼지터리 인터페이스를 작성하듯이 jpa 퍼시스턴스를 쉽게 해준다.



# #4 스프링 스큐리티

## 스프링 시큐리티 활성화하기

- security 모듈을 추가함으로써 생기는일
  - 모든 hhtp 요청 경로는 인즈오디어야함
  - 어떤 특정 역할이나 권한은 없음
  - 로그인 페이지가 따로 없음
  - 스프링 시큐리티 http 기본 인증을 사용해서 인증됨
  - 사용자는 하나만 있으며 이름은 user, 패스워드는 로그 확인

## 요약

- 스프링 시큐리티의 자동 구성은 보안을 시작하는데 좋은 방법이다. 그러나 대부분의 애플리케이션에서는 나름의 보안 요구사항을 충족하기 위해 별도의 보안 구성이 필요하다.
- 사용자 정보는 여러 종류의 사용자 스토어에 저장되고 관리될 수 있다. 예를 들어 관계형 데이터베이스, LDAP 등이다.
- 스프링 시큐리티는 자동으로 CSRF 공격을 방어한다.
- 인증된 사용자에 관한 정보는 Securitycontext 객체를 통해 얻거나 @AuthenticationPricipal을 사용해서 컨트롤러에 주입하면 된다.

# #5 구성 속성 사용하기

## 자동 구성 세부 조정하기

- 빈 연결: 스프링 애플리케이션 컨텍스트에서 빈으로 생성되는 애플리케이션 컴포넌트 및 상호간에 주입되는 방법을 선언하는 구성
- 속성 주입: 스프링 애플리케이션 컨텍스트에서 빈의 속성 값을 설정하는 구성

### 스프링 환경 추상화 이해하기

- 속성의 근원을 추상화하여 각 속성을 필요로 하는 빈이 스프링 자체에서 해당 속성을 사용할 수 있게 해줌
- application.yml 등등

### 데이터 소스 구성하기

### 내장 서버 구성하기

- 포트를 0으로하면 무작위로 됨 자동화할때 편리

## 요약

- 스프링 빈에 @configurationProperties를 지정하면 여러 가지 원천 속성으로부터 구성 속성 값의 주입을 활성화 할 수 있다.
- 구성 속성은 명령행 인자, 환경 변수, jvm 시스템 속성, 속성 파일,ymaml 파일, 커스텀 속성 등에서 설정할 수 있다.
- 데이터 소스 url과 로깅 수준의 지정을 포함해서 구성 속성은 스프링의 자동 구성 설정을 변경하는데 사용할 수 있다.
- 스프링 프로파일은 활성화된 프로파일을 기반으로 구성속성을 설정하기 우해 사용할 수 있다.

# #6 REST 서비스 생성하기

## REST 컨트롤러 작성하기

- @RestController는 하위 메서드 전부에 @ResponseBody가 붙음
- @RequestBody 로 json 타입의 커맨드객체와 바인딩되도록 함
- @ResponseStatus로 해당 요청이 성공이면 응답코드 반환할 수 있음
- put은 데이터 전체 변경 patch는 데이터 일부 변경

## 하이퍼 미디어 사용하기

- 스프링 HATEOAS 프로젝트는 하이퍼링크를 스프링에 지원함 스프링 mvc 컨트롤러에서 리소스를 반환하기 전에 해당 리소스에 링크를 추가하는데 사용할 수 있는 클래스와 리스소 어셈블러들을 제공함
- 클라이언트가 최소한의 api url 만 알면 반한되는 리소스와 관련하여 처리 가능한 다른 api url 들을 알아내어 사용할 수 있음
- HATEOAS는 하이퍼링크 리소스를 나타내는 두 개의 기본 타입인 Resource와 Resources를 제공함
- 두 타입 모두 다른 리소스를 링크할 수 있음
- controllerLinkBuilder를 사용하면 호스트 이름을 하드코딩할 필요갸 없음
- 어디에 써야할지 개인적으로 잘 모르겠음 pass...

## 요약

- REST 엔드포인트는 스프링 MVC 그리고 브라우저 지향의 컨트롤러와 동일한 프로그래밍 모델을 따르는 컨트롤러로 생성할 수 있다.
- 모델과 뷰를 거치지 않고 요청 응답 몸체에 직접 데이터를 쓰기 위해 컨트롤러의 핸들러 메서드에는 @ResponseBody 애노테이션을 지정할 수 있으며, ResponseEntity 객체를 반환할 수 있다.
- @RestController 애노테이션을 컨트롤러에 지정하면 해당 컨트롤러의 각 핸들러 메서드에 @ResponseBody가 붙는다
- 스프링 HATEOAS는 스프링 MVC에서 반환되는 리소스의 하이퍼링크를 추가할 수 있게 한다
- 스프링 데이터 리퍼지터리는 스프링 데이터 REST를 사용하는 REST API로 자동 노출될 수 있다.

# #7 REST 서비스 사용하기



# #8 비동기 메시지 전송하기

- 비동기 메시징은 애플리케이션 간에 응답을 기다리지 않고 간접적으로 메시지를 전송하는 방식 통신하는 애플리케이션 간의 결하보를 낮추고 확장성을 높여줌
- JMS, RabbitMQ, AMQP, Apache Kafka

## JMS로 메시지 전송하기

- 두 개 이상의 클라이언트 간에 메시지 통신을 위한 공통 api를 정의하는 자바 표준
- ActiveMQ, Artemis 를 사용함
- JMSTemplate를 사용해서 자동으로 부트가 메시지 브로커와의 연결 및 세션 생성을 해줌.
- 메시지를 수신하는 방식은 요청하고 도착할때까지 기다리는 풀모델과 자동전달하는 푸시 모델이 있음
- JMSTemplate는 모든 수신하는 모든 메서드가 풀모델임. 스레드에서 메시지를 수신할 수 있을 떄까지 기다림
- 근데 푸시모델도 된대 ... 이상하네 진짜 .. (@JMSListner로 되는듯)
- 일반적인 경우 블록이 안되는 푸시모델이 좋음



## rabbitMQ와 AMQP 사용하기

- 메시지가 RabbitMQ 브로커에 도착하면 주소로 지정된 거래소에 들어감. 거래소느 ㄴ하나 이상의 큐에 메시지를 전달할 책임이 있음
- 이때 거래소 타입, 거래소와 큐 간의 바인딩, 메시지의 라우팅 키값을 기반으로 처리함
- 거래소?의 종류
  - 기본: 브로커가 자동으로 생성하는 특별한 거래소. 해당 메시지의 라우팅 키와 이름이 같은 큐로 메시지를 전달함. 모든 큐는 자동으로 기본 거래소와 연결됨
  - 디렉트: 바인딩 키가 해당 메시지의 라우팅 키와 같은 큐에 메시지를 전달함
  - 토픽: 바인딩 키가 해당 메시지의 라우팅 키와 일치하는 하나 이상의 큐에 메시지를 전달함
  - 팬아웃: 바인딩 키나 라우팅 키에 상관없이 모든 연결된 키에 메시지를 전달함
  - 헤더: 토픽 거래소와 유사하며, 라우팅 키 대신 메시지 헤더 값을 기반으로 한다는 것만 다름
  - 데드 레터: 전달 불가능한 즉, 정의된 어떤 거래소 큐 바인딩과도 일치하지 않는 모든 메시지를 보관하는 잡동사니 거래소
- 거래소의 가장 간단한 형태는 기본 거래소와 팬아웃 거래소
- RabbitTemplate를 사용함
- 수신할때는 라우팅 키 상관 없이 큐만 바라보면 됨
- jms랑 매우 비슷하고 풀 모델기반임 푸시모델 기반으로 사용하려면 RabbitListener를 사용하면 됨

## 카프카 사용하기

- 카프카는 높은 확장성을 제공하는 클러스터로 실행되도록 설계됨
- rabbitmq가 거래소와 큐를 사용해서 메시지를 처리하는 반면 카프카는 토픽만 사용함

## 요약

- 애플리케이션 간 비동기 메시지 큐를 이용한 통신 방식은 간접 계층을 제공하므로 애플리케이션 간의 결합도는 낮추면서 확장성은 높인다.
- 스프링은 JMS, RabbitMq 또는 아파치 카프카를 사용해서 비동기 메시징을 지원한다
- 스프링 애플리케이션은 템플릿 기반의 클라이언트인 JmsTemplate, RabbitTemplate 또는 KafkaTemplate를 사용해서 메시지 브로커를 통한 메시지 전송을 할 수 있다.
- 메시지 수신 애플리케이션은 같은 템플릿 기반의 클라이언트들을 사용해서 풀 모델 형태의 메시지 소비를 할 수 있다.
- 메시지 리스너 애노테이션인 @JmsListener, @RabbitListner 또는 @KafkaListener를 빈 메서드에 지정하면 푸시 모델의 형태로 컨슈머에게 메시지가 전송될 수 있다.



# #9 스프링 통합하기

## 간단한 통합 플로우 선언하기

- 애플리케이션은 통합 플로우를 통해서 외부 리소스나 애플리케이션 자체에 데이터를 수신 또는 전송할 수 있으며 스프링 통합은 이런 통합 플로우를 생성하게 해줌
- 파일 시스템에 데이터를 쓰는 통합 플로우 작성

```java
package me.sup2is;

import org.springframework.integration.annotation.MessagingGateway;
import org.springframework.integration.file.FileHeaders;
import org.springframework.messaging.handler.annotation.Header;

@MessagingGateway(defaultRequestChannel = "textInChannel")
public interface FileWriterGateway {

    void writeToFile(@Header(FileHeaders.FILENAME) String filename, String data);
}
```

* 알아서 이 인터페이스의 구현체를 스프링이 생성해줌
* defaultRequestChannel은 해당 인터페이이스의 메서드 호출로 생성된 메시지가 이 속성에 지정된 메시지 채널로 전송된다느 ㄴ것을 나타냄
* 여기에선 writeTofile 의 호출로 생긴 메시지가 textInChannel이라는 채널로 전송됨

```java
package me.sup2is;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.integration.annotation.ServiceActivator;
import org.springframework.integration.annotation.Transformer;
import org.springframework.integration.file.FileWritingMessageHandler;
import org.springframework.integration.file.support.FileExistsMode;
import org.springframework.integration.transformer.GenericTransformer;

import java.io.File;

@Configuration
public class FileWriterIntegrationConfig {

    @Bean
    @Transformer(inputChannel = "textInChannel", outputChannel = "fileWriterChannel")
    public GenericTransformer<String, String> upperCaseTransFormer() {
        return String::toUpperCase;
    }

    @Bean
    @ServiceActivator(inputChannel = "fileWriterChannel")
    public FileWritingMessageHandler fileWriter() {
        FileWritingMessageHandler handler = new FileWritingMessageHandler(new File("C:\\Users\\ChoiHyeonSeop\\temp.txt"));
        handler.setExpectReply(false);
        handler.setFileExistsMode(FileExistsMode.APPEND);
        return handler;
    }

}

```

- 대충 파일 쓰기 채널과 파일 읽기채널 부분 설정한거임
- 스프링 통합 자바 dsl 구성을 쓰면 더 간소화 할 수 있음

```java
    @Bean
    public IntegrationFlow fileWriterFlow() {
        return IntegrationFlows.from(MessageChannels.direct("textInChannel"))
                .<String, String>transform(t -> t.toUpperCase())
                .channel(MessageChannels.direct("fileWriterChannel"))
                .handle(Files
                        .outboundAdapter(new File("C:\\Users\\ChoiHyeonSeop\\temp.txt"))
                        .fileExistsMode(FileExistsMode.APPEND)
                        .appendNewLine(true)
                ).get();
        
    }
```



## 스프링 통합의 컴포넌트 살펴보기

- 통합 플로우는 하나 이상의 컴포넌트로 구성됨
- 통합 플로우에서 각 컴포넌트의 역할
  - 채널: 한 요소로부터 다른 요소로 메시지를 전달
  - 필터: 조건에 맞는 메시지가 플로우를 통과하게 해줌
  - 변호나기: 메시지 값을 변경하거나 메시지 페이로드의 타입을 다른 타입으로 변환
  - 라우터: 여러 채널 중 하나로 메시지를 전달하며 대개 메시지 헤더를 기반으로 함
  - 분배기: 들어오는 메시지를 두 개 이상의 메시지로 분할하며, 분할된 각 메시지는 다른 채널로 전송
  - 집적기: 분배기와 상반된 것으로 별개의 채널로부터 전달되는 다수의 메시지를 하나의메시지로 결합함
  - 서비스 액티베이터: 메시지를 처리하도록 자바 메서드에 메시지를 넘겨준 후 메서드의 반환값을 출력 채널로 전송
  - 채널 어댑터: 외부 시스템에 채널을 연결함. 외부 시스템으로부터 입력을 받거나 쓸 수 있음
  - 게이트웨이: 인터페이스를 통해 통합플로우로 데이터를 전달

### 메시지 채널

- 파이프라인을 통해서 메시지가 이동하는 수단 채널은 스프링 통합의 다른 부분을 연결하는 통로
- PublishSubscribeChannel: 하나 이상의 컨슈머로 전달됨 컨슈머가 여럿일 때는 모든 컨슈머가 해당 메시지를 수신함
- QueueChannel: FIFO 방식으로 컨슈머가 가져갈 때까지 큐에 저장됨 큐잉모델
- PriorityChannel: QueueChannel과 유사하지만 FIFO 방식 대신 메시지의 우선순위를 기반으로 컨슈머가 메시지를 가져감
- RendezvousChannel: QueueChannel과 유사하지만 컨슈머가 메시지를 수신할 때까지 메시지 전송자가 채널을 차단함 전송자와 컨슈머를 동기화함
- DirectChannel: PublishSubscribeChannel과 유사하지만 전송자와 동일한 스레드로 실행되는 컨슈머를 호출하여 단일 컨슈머에게 메시지를 전송함 이 채널은 트랜잭션 지원
- ExecjutorChannel: DirectChannel과 유사하지만 TaskExecutor를 통해서 메시지가 전송됨 이 채널 타입은 트랜잭션을 지원하지 않음
- FluxMessageChannel: 프로젝트 리액터의 플럭스를 기반으로 하는 리액티브 스트림즈 퍼블리셔 채널임

- 기본적으로 자동 생성되고 DirectChannel을 사용

```java
@Bean
public MessageChannel orderChannel() {
  return new PublishSubscribeChannel()
}
```

- 빈으로 등록한 메서드이름이 채널의 이름이됨

```java
@ServiceActivator(inputChannel="orderChannel", poller=@Poller(fixedRate="1000"))
```

- orderChannel은 QueueChannel임
- 이 서비스 액티베이터는 저 채널로 부터 매 1초당 1번씩 읽을 메시지가 있는지 확인함

### 필터

- 필터는 통합 파이프라인의 중간에 위치할 수 있고 플로우의 전 단계로부터 다음 단계로의 메시지 전달을 허용 또는 불허함

```java
@Filter(inputCjhannel="numberChannel", outputChannel="evenNumberChannel")
public boolean evenNumberFilter(Integer num) {
  return number % 2 == 0;
}
```

- 짝수만 보내는 필터

### 변환기

- 메시지 값의 변경이나 타입을 변환하는 일을 수행함

```java
@Transformer(inputChannel="numberChannel", outputChannel="romanNumberChannel")
public GenericTransformer<Integer, String> romanNumTransformer() {
  return RomanBumbers::toRoman;
}
```

- DSL로도 가능 transform();

### 라우터

- 전달 조건을 기반으로 통합 플로우 내부를 분기함
- 메시지에 적용된 조건을 기반으로 서로 다른 채널로 메시지를 전달함

```java

    @Bean
    @Router(inputChannel = "numberChannel")
    public AbstractMessageRouter evenOddRouter() {
        return new AbstractMessageRouter() {
            @Override
            protected Collection<MessageChannel> determineTargetChannels(Message<?> message) {
                Integer num = (Integer) message.getPayload();
                if(num % 2 == 0) {
                    return Collections.singleton(evenChannel());
                }else {
                    return Collections.singleton(oddChannel());
                }
            }
        };
    }

    @Bean
    public MessageChannel evenChannel() {
        return new DirectChannel();
    }

    @Bean
    public MessageChannel oddChannel() {
        return new DirectChannel();
    }

```

## 

### 분배기

- 통합 플로우에서 하나의 메시지를 여러 개로 분할하여 독립적으로 처리하는 것이 유용할 수 있음
- 분배기를 사용할 수 있는 중요한 두가지 경우
  - 메시지 페이로드가 같은 타입의 컬렉션항목들을 포함하며, 각 메시지 페이로드 별로 처리하고자 할 때 예를 들어 여러 가지 종류의 제품이 있으며, 제품리스트를 전달하는 메시지는 각각 한 종류 제품의 페이로드를 갖는 다수의 메시지로 분할 될 수 있다.
  - 연관된 정보를 함께 전달하는 하나의 메시지 페이로드는 두 개 이상의 서로 다른 타입 메시지로 분할될 수 있다. 예를 들어 주문 메시지는 배달 정보 대금 청구 정보 주문 항목 정보를 전달할 수 있으며, 각 정보는 서로 다른 하위 플로우에서 처리될 수 있다. 이 경우는 일반적으로 분배기 다음에 페이로드 타입 별로 메시지를 전달하는 라우터가 연결된다. 적합한 하위 플로우에서 데이터가 처리되도록 하기 위해서다.

```java
public class OderSplitter {
  public Collection<Object> splitOrderIntoParts(PurchaseOrder po) {
    ArrayList<Object> parts = new ArrayList<>();
    parts.add(po.getBillingInfo());
    parts.add(po.getLineItems());
    return parts;
  }
}




...

@Bean
@Splitter(inputChannel="poChannel", outputChannel="splitOrderChannel")
public OrderSplitter orderSplitter() {
    return new OrderSplitter();
}



...
    
    
    
@Bean
@Router(inputChannel="splitOrderChannel")
public MessageRouter splitOrderRouter() {
    PayloadTypeRouter router = new PayloadTypeRouter();
    router.setChannelMapping(BillingInfo.class.getname(), "billingInfoChannel");
    router.setChannelMapping(List.class.getname(), "lineItemsChannel");
    return router;
}




//다시 lineItemsChannel로 들어오는 List를 처리하고싶다면 ?
@Splitter(inputChannel="lineItemsChannel", outputChannel="lineItemChannel")
public List<LineItem> lineItemSplitter(List<LineItem> lineItems) {
    return lineItems;
}


```

### 서비스 액티베이터

- 입력 채널로부터 메시지를 수신하고 이 메시지를 MessageHandler 인터페이스를 구현한 클래스에 전달함
- 스프링 통합은 MessageHandler를 구현한 여러 클래스를 제공함

```java
    @Bean
    @ServiceActivator(inputChannel ="someChannel")
    public MessageHandler sysoutHandler() {
        return message -> System.out.println("message payload: " + message.getPayload());
    }

```

```java
    @Bean
    @ServiceActivator(inputChannel ="someChannel", outputChannel = "completeChannel")
    public GenericHandler<Order> orderHandler(OrderRepository orderRepository) {
        return (o, messageHeaders) -> orderRepository.save(); 
    }
```



### 게이트웨이

- 애플리케이션이 통합 플로우로 데이터를 제출하고 선택적으로 플로우의 처리 결과인 응답을 받을 수 있는 수단임.
- 스프링 통합에 구현된 게이트웨이는 애플리케이션이 통합 플로우로 메시지를 전송하기 위해 호출할 수 있는 인터페이스로 구체화 되어 있음
- FileWriterGateway는 단방향 게이트웨이.

```java
package me.sup2is;

import org.springframework.integration.annotation.MessagingGateway;
import org.springframework.stereotype.Component;

@Component
@MessagingGateway(defaultRequestChannel = "inChannel", defaultReplyChannel = "outChannel")
public interface UpperCaseGateway {
    String uppercase(String in);
}

```

- 스프링이 알아서 구현체를 제공함

### 채널 어댑터

- 채널 어댑터는 통합 플로우의 입구와 출그를 나타냄 데이터는 인바운드 채널 어댑터를 통해 통합 플로우로 들어오고 아웃바운드 채널 어댑터를 통해 통합 플로우에서 나감

```java

    @Bean
    @InboundChannelAdapter(poller = @Poller(fixedRate = "1000", errorChannel = "numberChannel"))
    public MessageSource<Integer> numberSource(AtomicInteger source) {
        return () -> new GenericMessage<>(source.getAndIncrement());
    }

```

- 이 빈은 주입된 AutomicInteger로부터 numberChannel 이라는 이름의 채널로 매초 마다 한번씩 숫자를 전달함
- 메시지 핸들러로 구현되는 서비스 액티베이터는 아웃바운드 채널 어댑터로 자주 사용됨 특히 데이터가 애플리케이션 자체에 전달될 필요가 있을때

### 엔드포인트 모듈

- 약 24개 이상있음 다양한 외부 시스템과의 통합을 위해 채널 어댑터가 포함된 엔드포인트 모듈을 스프링 통합이 제공함



## 요약

- 스프링 통합은 플로우를 정의할 수 있게 해준다. 데이터는 애플리케이션으로 들어오거나 나갈 때 플로우를 통해 처리할 수 있다.
- 통합 플로우는 xml, java, java dsl을 사용해서 정의할 수 있다.
- 메시지 게이트웨이와 채널 어댑터는 통합 플로우의 입구나 출구의 역할을 한다.
- 메시지는 플로우 내부에서 변환, 분할, 집적, 전달될 수 있으며, 서비스 액티베이터에 의해 처리될 수 있다.
- 메시지 채널은 통합 플로우의 컴포넌트들을 연결한다.

# #10 리액터 개요

- 명령형 코드: 순차적으로 연속되는 작업, 각 작업은 한번에 하나씩 그리고 이전 작업 다음에 실행됨 데이터는 모아서 처리되고 이전 작업이 데이터 처리를 끝낸 후에 다름 작업으로 넘어갈 수 있음
- 리액티브 코드: 데이터 처리를 위해 일련의 작업들이 정의되지만 이 작업들은 병렬로 실행될 수 있음 각 작업은 부분 집합의 데이터를 처리할 수 있고 처리가 끝난 데이터를 다음 작업에 넘겨주고 다른 부분 집합의 데이터로 계속 작업할 수 있음

## 리액티브 프로그래밍 이해하기

- 리액티브 프로그래밍은 명령형 프로그래밍의 대안이 되는 패러다임임
- 명령형 프로그래밍은 한 번에 하나씩 만나는 순서대로 실행되는 명령어들로 코드를 작성하면 됨 그러나 동기식 처리에 대한 문제가 있음
- 리액티브 프로그래밍은 본질적으로 함수적이면서 선언적임. 순차적으로 수행되는 작업 단계를 나타낸 것이 아니라 데이터가 흘러가는 파이프라인이나 스트림을 포함함
- 리액티브 스트림은 데이터 전체를 사용할 수 있을 때까지 기다리지 않고 사용 가능한 데이터가 있을 때마다 처리되므로 사실상 입력되는 데이터는 무한할 수 있음

### 리액티브 스트림 정의하기

- 리액티브 스트림은 넷플릭스, 라이트밴드, 피보탈의 엔지니어들에 의해 2013년 말에 시작됨
- 동시에 여러 작업을 수행하여 더 큰 확장성을 얻게 해줌
- 백 프레셔는 데이터를 소비하는 컨슈머가 처리할 수 있는 만큼으로 전달 데이터를 제한함으로써 지나치게 빠른 데이터 소스로부터의 데이터 전달 폭주를 피할 수 있는 수단임
- 리액티브 스트림은 4개의 인터페이스인 publisher, subscriber, subscription, Processor 로 구분됨
- publisher는 하나의 Subscription당 하나의 Subscriber에 발행하는 데이터를 생성함 publisher 인터페이스에는 subscriber가 publisher를 구독 신청할 수 있는 subscriber() 메서드가 한개있음
- subscriber가 구독 신청되면 publisher로부터 이벤트를 수신 가능함 이 이벤트들은 subscriber 인터페이스의 메서드를 통해 전송됨 일반적인 펍섭 모델인듯

## 리액터 시작하기

- 리액티브 프로그래밍은 명령형 프로그래밍과 매우 다른 방식으로 접근해야함
- 일련의 작업 단계를 기술하는 것이 아니라 데이터가 전달될 파이프라인을 구성해야함 이 파이프라인을 통해 데이터가 전달되는 동안 어떤 형태로든 변경 또는 사용될 수 있음

```java
//명령형 프로그래밍
String name = "Craig";
String capitalName = name.toUppercase();
String greeting = "Hello" , + capitalName + "!";
System.out.println(greeting);


//선언형 프로그래밍 
Mono.just("Craig")
    .map(n -> n.toUpperCase())
    .map(cn -> "Hello, " + cn + "!")
    .subscribe(System.out::println)
```

- Mono와 Flux는 리액터의 두가지 핵심 타입 중하나이고 모두 리액티브 스트림의 Publisher 인터페이스를 구현한것임
- Flux는 0, 1 또는 다수의 데이터를 갖는 파이프라인이고 Mono는 하나의 데이터 항목만 갖는 데이터셋에 최적화된 리액티브 타입임

### 리액티브 플로우의 다이어그램

- 리액티브 플로우는 마블 다이어그램으로 나타내곤 함 마블 다이어그램의 제일 위에는 Flux나 Mono를 통해 전달되는 데이터의 타임라인을 나타내고 중앙에는 오퍼레이션을 제일 밑에는 결과로 생성되는 Flux나 Mono의 타임라임을 나타냄.

## 리액티브 오퍼레이션 적용하기

- Flux와 Mono는 리액터가 제공하는 가장 핵심적인 구성 요소임
- Flux와 Mono가 제공하는 오퍼레이션들은 두 타입을 함께 결합하여 데이터가 전달될 수 있는 파이프라인을 생성함
- Flux와  Mono에는 500개 이상의 오퍼레이션이 있으며, 각 오퍼레이션은 다음과 같이 분류됨
  - 생성 오퍼레이션
  - 조합 오퍼레이션
  - 변환 오퍼레이션
  - 로직 오퍼레이션

### 리액티브 타입 생성하기

- 스프링에서 리액티브 타입을 사용할 때는 리퍼지터리나 서비스로부터 Flux나 Mono가 제공되므로 우리의 리액티브 타입을 생성할 필요가 없음
- 데이터를 발행하는 새로운 리액티브 발행자를 생성해야 할 때가 있음
- 각각 생성 오퍼레이션을 제공함

**객체로부터 생성하기**

```java
    @Test
    public void createAFlux_just() {
        Flux<String> fruitFlux = Flux.just("Apple", "Orange", "Grape", "Banana", "Strawberry");
        fruitFlux.subscribe(f -> System.out.println(f));

        StepVerifier.create(fruitFlux)
                .expectNext("Apple")
                .expectNext("Orange")
                .expectNext("Grape")
                .expectNext("Banana")
                .expectNext("Strawberry")
                .verifyComplete();
        

    }
```

- 구독자가 있어야 데이터가 전달됨
- 테스트할때는 StepVerifier를 사용할 것
- StepVerifier는 해당 리액티브 타입을 구독한 뒤에 스트림을 통해전달되는 데이터에 대해 어셔션을 적용함

**컬렉션으로부터 생성하기**

- flux는 또한 배열, Iterable객체, 자바 Stream 객체로부터도 생성될 수 있음

```java

    @Test
    public void createAFlux_fromArray() {
        //given
        String[] fruits = new String[] {"Apple", "Orange", "Grape", "Banana", "Strawberry"};
        //when

        Flux<String> fruitFlux = Flux.fromArray(fruits);

        //then
        StepVerifier.create(fruitFlux)
                .expectNext("Apple")
                .expectNext("Orange")
                .expectNext("Grape")
                .expectNext("Banana")
                .expectNext("Strawberry")
                .verifyComplete();
    }
```

- 이외에도 fromIterable(), fromStream() 이 있음

**Flux 데이터 생성하기**

- 데이터 없이 매번 새 값으로 증가하는 숫자를 방출하는 카운터역할의 flux만 필요하면 range()를 사용하면됨

```java
@Test
public void test() {
    //given
    Flux<Integer> range = Flux.range(1, 5);
    //when

    //then
    StepVerifier.create(range)
            .expectNext(1)
            .expectNext(2)
            .expectNext(3)
            .expectNext(4)
            .expectNext(5)
            .verifyComplete();
}
```
- interval()로도 가능, 시작 값과 종료 값대신 값이 방출되는 시간이나 간격 주기를 지정

```java
    @Test
    public void createAFlux_interval() {
        //given
        Flux<Long> take = Flux.interval(Duration.ofSeconds(1)).take(5);
        //when

        //then
        StepVerifier.create(take)
                .expectNext(0L)
                .expectNext(1L)
                .expectNext(2L)
                .expectNext(3L)
                .expectNext(4L)
                .verifyComplete();
    }

```

### 리액티브 타입 조합하기

- 두 개의 리액티브 타입을 결합해야 하거나 하나의 Flux를 두 개 이상의 리액티브 타입으로 분할해야 하는 경우가 있을 수 있음

**리액티브 타입 결합하기**

- 하나의 Flux를 다른 것과 결합하려면 mergeWith()로 조합

```java
    @Test
    public void mergeFlux() {
        Flux<String> fruitFlux1 = Flux.just("Apple", "Orange")
                .delayElements(Duration.ofMillis(500));
        Flux<String> fruitFlux2 = Flux.just("Grape", "Banana", "Strawberry")
                .delaySubscription(Duration.ofMillis(250))
                .delayElements(Duration.ofMillis(500));

        Flux<String> merged = fruitFlux1.mergeWith(fruitFlux2);

        //then
        StepVerifier.create(merged)
                .expectNext("Apple")
                .expectNext("Grape")
                .expectNext("Orange")
                .expectNext("Banana")
                .expectNext("Strawberry")
                .verifyComplete();
    }
```

- delayElements()를 사용해서 조금 느리게 방출되도록할 수 있음
- delaySubscription()으로도 조금 느리게 구독 및 방출할 수 있음
- 이런식으로하면 두 객체가 결합되면서 한 개씩 섞이게 됨
- 이런식으로 완벽하게 섞어서 동작하게하려면 zip()을 사용하면 됨

```java
    @Test
    public void zipFluxes() {
        //given
        Flux<String> fruitFlux1 = Flux.just("Apple", "Orange");
        Flux<String> fruitFlux2 = Flux.just("Grape", "Banana", "Strawberry");
        //when
        Flux<Tuple2<String, String>> zip = Flux.zip(fruitFlux1, fruitFlux2);
        //then

        StepVerifier.create(zip)
                .expectNextMatches(p ->
                        p.getT1().equals("Apple") && p.getT2().equals("Grape"))
                .expectNextMatches(p ->
                        p.getT1().equals("Orange") && p.getT2().equals("Banana"))
                .expectNextMatches(p ->
                        p.getT1().equals("Strawberry"));


    }



```

- 객체로 묶을수도 있음 

```java
    @Test
    public void zipFluxesToObject() {
        //given
        Flux<String> fruitFlux1 = Flux.just("Apple", "Orange");
        Flux<String> fruitFlux2 = Flux.just("Grape", "Banana", "Strawberry");
        //when
        Flux<String> zip = Flux.zip(fruitFlux1, fruitFlux2, (s, s2) -> s + "and " + s2);
        //then

        StepVerifier.create(zip)
                .expectNext("Apple and Grape")
                .expectNext("Orange and Banana")
                .expectNext("Strawberry");

    }
```

**먼저 값을 방출하는 리액티브 타입 선택하기**

- 먼저 값만 가져오는거인거같음

```java
    @Test
    public void firstFlux() {
        //given
        Flux<String> fruitFlux1 = Flux.just("Apple", "Orange")
                .delaySubscription(Duration.ofMillis(200));
        Flux<String> fruitFlux2 = Flux.just("Grape", "Banana", "Strawberry");
        //when

        Flux<String> first = Flux.first(fruitFlux1, fruitFlux2);

        //then
        StepVerifier.create(first)
                .expectNext("Grape")
                .expectNext("Banana")
                .expectNext("Strawberry")
                .verifyComplete();
    }
```

- fruitFlux1은 늦게나오기때문에 fruitFlux2만 가져옴

### 리액티브 스트림의 변환과 필터링

- 디터가 스트림을 통해 흐르는 동안 일부 값을 필터링하거나 다른 값으로 변경해야 할 경우가 있음

**리액티브 타입으로부터 데이터 필터링하기**

- skip()으로 맨 앞부터 원하는 개수의 항목을 무시할 수 있음

```java

    @Test
    public void skipFew() {
        //given
        Flux<String> fruitFlux = Flux.just("Apple", "Orange", "Grape", "Banana", "Strawberry")
                .skip(3);
        //when

        //then
        StepVerifier.create(fruitFlux)
                .expectNext("Banana")
                .expectNext("Strawberry");

    }

```

- skip()을 사용해서 4초 동안 기다렸다가 값을 방출하는 결과 Flux
- skip()은 처음부터 여러개의 항목을 건너뛰는 반면 take()는 처음부터 지정된 수의 항목만을 방출함
- filter()를 사용해 범용적으로 필터링 할 수 있음 filter는 조건식을 받음

```java

    @Test
    public void skipFew() {
        //given
        Flux<String> fruitFlux = Flux.just("Apple", "Orange", "Grape", "Banana", "Strawberry")
                .skip(3);
        //when

        //then
        StepVerifier.create(fruitFlux)
                .expectNext("Banana")
                .expectNext("Strawberry");

    }

```

- distinct() 중복 제거 가능



**리액티브 데이터 매핑하기**

- Flux 나 Mono에 가장 많이 사용하는 오퍼레이션 중 하나는 발행된 항목을 다른 형태나 타입으로 매핑하는 것
- 리액터 타입은 map(), flatMap()을 제공함

```java
  @Test
  public void map() {
    Flux<Player> playerFlux = Flux
      .just("Michael Jordan", "Scottie Pippen", "Steve Kerr")
      .map(n -> {
        String[] split = n.split("\\s");
        return new Player(split[0], split[1]);
      });
    
    StepVerifier.create(playerFlux)
        .expectNext(new Player("Michael", "Jordan"))
        .expectNext(new Player("Scottie", "Pippen"))
        .expectNext(new Player("Steve", "Kerr"))
        .verifyComplete();
  }
```

- 위 map()의 경우 각 항목이 소스 Flux로 부터 발행될 때 동기적 매핑이 수행됨 따라서 비동기적으로 매핑을 수행하고 싶다면 flatMap() 오퍼레이션을 사용해야함
- flatMap()에서는 각 객체를 새로운 Mono나 Flux로 매핑하며, 해당 Mono나 Flux들의 결과는 하나의 새로운 Flux가 됨
- flatMap()을 subscribeOn()과 함께 사용하면 리액터 타입의 변환을 비동기적으로 수행할 수 있음

```java
  @Test
  public void flatMap() {
    Flux<Player> playerFlux = Flux
      .just("Michael Jordan", "Scottie Pippen", "Steve Kerr")
      .flatMap(n -> Mono.just(n)
          .map(p -> {
              String[] split = p.split("\\s");
              return new Player(split[0], split[1]);
            })
          .subscribeOn(Schedulers.parallel())
        );
    
    List<Player> playerList = Arrays.asList(
        new Player("Michael", "Jordan"), 
        new Player("Scottie", "Pippen"), 
        new Player("Steve", "Kerr"));

    StepVerifier.create(playerFlux)
        .expectNextMatches(p -> playerList.contains(p))
        .expectNextMatches(p -> playerList.contains(p))
        .expectNextMatches(p -> playerList.contains(p))
        .verifyComplete();
  }
  
```

- subscribeOn()에 의해 비동기적으로 수행됨
- subscribeOn()은 subscribe()와 유사하지만 subscribe()는 이름이 동사형이면서 리액티브 플로우를 구독 요청하고 실제로 구독하는 반면 subscribeOn()은 이름이 더 서술적이면서 구독이 동시적으로 처리되어야 한다는 것을 지정함 
- flatMap()이나 subscribeOn()을 사용할 때의 장점은 다수의 병행 스레드에 작업을 분할하여 스트림의 처리량을 증가시킬 수 있다 하지만 작업의 순서 보장이 안되어서 Flux에서 방출되는 항목의 순서를 알 방법이 없음



**리액티브 스트림의 데이터 버퍼링하기**

- Flux를 통해 전달되는 데이터를 처리하는 동안 데이터 스트림을 작은 덩어리로 분할하면 도움이 될 수 있음 buffer()를 사용하면 됨\
- buffer()는 지정된 최대 크기의 리스트로 된 Flux를 생성함

```java
  @Test
  public void buffer() {
    Flux<String> fruitFlux = Flux.just(
        "apple", "orange", "banana", "kiwi", "strawberry");
    
    Flux<List<String>> bufferedFlux = fruitFlux.buffer(3);
    
    StepVerifier
        .create(bufferedFlux)
        .expectNext(Arrays.asList("apple", "orange", "banana"))
        .expectNext(Arrays.asList("kiwi", "strawberry"))
        .verifyComplete();
  }
```

- 먼말인지 모르겠음 ...
- collectList() collectMap() 도 있음



### 리액티브 타입에 로직 오퍼레이션 수행하기

- Mono나 Flux가 발행한 항목이 어떤 조건과 일치하는지만 알아야 할 경우가 ㅣㅆ음
- all(), any() 오퍼레이션

```java
  @Test
  public void all() {
    Flux<String> animalFlux = Flux.just(
        "aardvark", "elephant", "koala", "eagle", "kangaroo");
    
    Mono<Boolean> hasAMono = animalFlux.all(a -> a.contains("a"));
    StepVerifier.create(hasAMono)
      .expectNext(true)
      .verifyComplete();
    
    Mono<Boolean> hasKMono = animalFlux.all(a -> a.contains("k"));
    StepVerifier.create(hasKMono)
      .expectNext(false)
      .verifyComplete();
  }
  
  @Test
  public void any() {
    Flux<String> animalFlux = Flux.just(
        "aardvark", "elephant", "koala", "eagle", "kangaroo");
    
    Mono<Boolean> hasAMono = animalFlux.any(a -> a.contains("a"));
    
    StepVerifier.create(hasAMono)
      .expectNext(true)
      .verifyComplete();
    
    Mono<Boolean> hasZMono = animalFlux.any(a -> a.contains("z"));
    StepVerifier.create(hasZMono)
      .expectNext(false)
      .verifyComplete();
  }
  
```



## 리액티브 오퍼레이션 테스트 프로젝트 빌드 및 실행하기

## 요약

- 리액티브 프로그래밍에서는 데이터가 흘러가는 파이프라인을 생성한다.
- 리액티브 스트림은 Publisher, Subscriber, Subscription, Transformer의 네가지 타입을 정의한다.
- 프로젝트 리액터는 리액티브 스트림을 구현하며 수많은 오퍼레이션을 제공하는 Flux와 Mono의 두 가지 타입으로 스트림을 정의한다.
- 스프링 5는 리액터를 사용해서 리액티브 컨트롤러, 리퍼지터리, Rest 클라이언트를 생성하고 다른 리액티브 프레임워크를 지원한다.





















