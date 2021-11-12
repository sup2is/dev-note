

# 토비의  스프링 vol.2





# #1 IoC 컨테이너와 DI



## IoC 컨테이너: 빈 팩토리와 애플리케이션 컨텍스트

- 스프링에서는 오브젝트의 생성과 관계설정, 사용, 제거 등의 작업을 애플리케이션 코드 대신 독립된 컨테이너가 담당하는데 이 컨테이너를 IoC 컨테이너라고 한다.
- 이 IoC 컨테이너는 애플리케이션 컨텍스트라고도 불리고 애플리케이션 컨텍스는 IoC와 DI를 위한 빈 팩토리 또는 그 이상의 기능을 가졌다고 보면 된다.
- 스프링의 빈 팩토리와 애플리케이션 컨텍스트는 각각 기능을 대표하는 `BeanFactory`, `ApplicationContext` 로 정의되어 있다.



`BeanFactory`

```java
public interface BeanFactory {
  
  
	Object getBean(String name) throws BeansException;
  
	<T> T getBean(String name, Class<T> requiredType) throws BeansException;
  
	Object getBean(String name, Object... args) throws BeansException;
  
	<T> T getBean(Class<T> requiredType) throws BeansException;
  
	<T> T getBean(Class<T> requiredType, Object... args) throws BeansException;
  
	<T> ObjectProvider<T> getBeanProvider(Class<T> requiredType);
  
	<T> ObjectProvider<T> getBeanProvider(ResolvableType requiredType);
  
	boolean containsBean(String name);
  
	boolean isSingleton(String name) throws NoSuchBeanDefinitionException;
  
	boolean isPrototype(String name) throws NoSuchBeanDefinitionException;
  
	boolean isTypeMatch(String name, ResolvableType typeToMatch) throws NoSuchBeanDefinitionException;
  
	boolean isTypeMatch(String name, Class<?> typeToMatch) throws NoSuchBeanDefinitionException;
  
	@Nullable
	Class<?> getType(String name) throws NoSuchBeanDefinitionException;
  
	@Nullable
	Class<?> getType(String name, boolean allowFactoryBeanInit) throws NoSuchBeanDefinitionException;
  
	String[] getAliases(String name);
}
```



`ApplicationContext`

```java
public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory,
		MessageSource, ApplicationEventPublisher, ResourcePatternResolver {

	@Nullable
	String getId();

	String getApplicationName();

	String getDisplayName();

	long getStartupDate();

	@Nullable
	ApplicationContext getParent();

	AutowireCapableBeanFactory getAutowireCapableBeanFactory() throws IllegalStateException;

}

```

- `ApplicationContext` 는 `ListableBeanFactory`, `HierarchicalBeanFactory` 를 상속받고 있는데 `ListableBeanFactory` , `HierarchicalBeanFactory` 는 `BeanFactory` 를 확장하는 인터페이스이기 때문에 `ApplicationContext` 는 `BeanFactory` 의 서브인터페이스라고 볼 수 있다.
- 실제로 스프링 IoC 컨테이너는 `ApplicationContext` 를 구현했고 최초 한 개 이상의 애플리케이션 컨텍스트 오브젝트를 갖고 있다.



### IoC 컨테이너를 이용해 애플리케이션 만들기

- 가장 간단하게 IoC 컨테이너를 만드는 방법은 다음 과같이 ApplicationContext 구현 클래스의 인스턴스를 만들면 된다.

```java
StaticApplicationContext ac = new StaticApplicationContext();
```

- 이렇게 만들어진 컨테이너가 본격적인 IoC 컨테이너로서 동작하려면 POJO 클래스와 설정 메타정보가 필요하다.

#### POJO 클래스

- 먼저 애플리케이션의 핵심 코드를 담고 있는 POJO 클래스를 준비해야한다.
- 아래 Hello 클래스와 Printer 인터페이스, 그리고 Printer 인터페이스를 구현한 StringPrinter, ConsolePrinter 를 준비한다.

![1](./images/toby-spring-vol2/1.png)

- Hello 클래스는 Printer 인터페이스를 참조로 두고 있는 약결합상태
- 각자 기능에 충실하게 독립적으로 설계된 POJO 클래스를 만들고, 결합도가 낮은 유연한 관계를 가질 수 있도록 인터페이스를 이용해 연결해주는 것 까지가 IoC 컨테이너가 사용할 POJO를 준비하는 첫 단계



#### 설정 메타정보

- 두 번째 필요한것은 앞에서 만든 POJO 클래스들 중에 애플리케이션에서 사용할 것을 선정하고 이를 IoC 컨테이너가 제어할 수 있도록 적절한 메타정보를 만들어 제공하는 작업
- IoC 컨테이너의 가장 기초적인 역할은 오브젝트를 생성하고 이를 관리하는것. 이런 오브젝트는 빈이라고부른다.
- 스프링의 설정 메타정보는 `BeanDefinition` 인터페이스로 표현되는 순수한 추상 정보

```java
public interface BeanDefinition extends AttributeAccessor, BeanMetadataElement {

	// Modifiable attributes
	void setParentName(@Nullable String parentName);

	@Nullable
	String getParentName();

	void setBeanClassName(@Nullable String beanClassName);
  
	@Nullable
	String getBeanClassName();

	void setScope(@Nullable String scope);

	@Nullable
	String getScope();

	void setLazyInit(boolean lazyInit);

	boolean isLazyInit();

	void setDependsOn(@Nullable String... dependsOn);

	@Nullable
	String[] getDependsOn();

	void setAutowireCandidate(boolean autowireCandidate);

	boolean isAutowireCandidate();

	void setPrimary(boolean primary);

	boolean isPrimary();

	void setFactoryBeanName(@Nullable String factoryBeanName);

	@Nullable
	String getFactoryBeanName();

	void setFactoryMethodName(@Nullable String factoryMethodName);

	@Nullable
	String getFactoryMethodName();

	ConstructorArgumentValues getConstructorArgumentValues();

	default boolean hasConstructorArgumentValues() {
		return !getConstructorArgumentValues().isEmpty();
	}
  
	MutablePropertyValues getPropertyValues();

	default boolean hasPropertyValues() {
		return !getPropertyValues().isEmpty();
	}

	void setInitMethodName(@Nullable String initMethodName);

	@Nullable
	String getInitMethodName();

	void setDestroyMethodName(@Nullable String destroyMethodName);

	@Nullable
	String getDestroyMethodName();

	void setRole(int role);

	int getRole();

	void setDescription(@Nullable String description);

	@Nullable
	String getDescription();

	// Read-only attributes
	ResolvableType getResolvableType();

	boolean isSingleton();

	boolean isPrototype();

	boolean isAbstract();

	@Nullable
	String getResourceDescription();

	@Nullable
	BeanDefinition getOriginatingBeanDefinition();

}

```

- 스프링의 메타정보는 특정한 파일 포멧, 형식과 상관없이 `BeanDefinition`으로 정의되는 스프링의 설정 메타정보의 내용을 표현한 것이 있다면 어느것이든 사용이 가능하다.
- 원본의 포맷과 구조, 자료의 특성에 맞게 읽어와 `BeanDefinition` 오브젝트로 변환해주는 `BeanDefinitionReader`가 있으면 된다.
- `BeanDefinition` 인터페이스로 정의되는 IoC 컨테이너가 사용하는 빈 메타정보
  - 빈 아이디, 이름, 별칭: 빈 오브젝트를 구분할 수 있는 식별자
  - 클래스 또는 클래스 이름: 빈으로 만들 POJO 클래스 또는 서비스 클래스 정보
  - 스코프: 싱글톤, 프로토타입과 같은 빈의 생성 방식과 존재 범위
  - 프로퍼티 값 또는 참조: DI에 사용할 프로퍼티 이름과 값 또는 참조하는 빈의 이름
  - 생성자 파라미터 값 또는 참조: DI에 사용할 생성자 파라미터 이름과 값 또는 참조할 빈의 이름
  - 지연된 로딩 여부, 우선 빈 여부, 자동와이어링 여부, 부모 빈 정보, 빈 팩토리 이름 등등...
- 스프링 IoC 컨테이너는 각 빈에 대한 정보를 담은 설정 메타정보를 읽어들인 뒤에 이를 참고해서 빈 오브젝트를 생성하고 프로퍼티나 생성자를 통해 의존 오브젝트를 주입해주는 DI 작업을 수행한다.
- 이런 작업으로 만들어진 빈들은 DI로 연결되고 하나의 애플리케이션을 구성한다.

![2](./images/toby-spring-vol2/2.png)

- 스프링 애플리케이션이란 POJO 클래스와 설정 메타정보를 이용해 IoC 컨테이너가 만들어주는 오브젝트의 조합이라고 할 수 있다.

`StaticApplicationContext.registerSingleton() 메서드 사용하기`

```java

StaticApplicationContext ac = new StaticApplicationContext();
ac.registerSingleton("hello1", Hello.class);

```

- 사용한 `registerSingleton()` 은 아래와 같이 `GenericBeanDefinition` 을 내부적으로 만드는 모습을 확인할 수 있다.

`StaticApplicationContext.registerSingleton() 메서드`

```java
	public void registerSingleton(String name, Class<?> clazz) throws BeansException {
		GenericBeanDefinition bd = new GenericBeanDefinition();
		bd.setBeanClass(clazz);
		getDefaultListableBeanFactory().registerBeanDefinition(name, bd);
	}
```

- 직접 `BeanDefinition` 타입의 설정 메타정보를 만들어서 IoC 컨테이너에 등록할 수 있다.

`RootDefinition 사용하기`

```java
//가장 기본적인 BeanDefinition 구현체인 RootBeanDefinition 생성
BeanDefinition helloDef = new RootBeanDefinition(Hello.class);

//addPropertyValue를 통해 필드 셋팅
helloDef.getPropertyValues().addPropertyValue("name", "Spring");

//빈 등록
ac.registerSingleton("hello2", helloDef);

```

- 아래와 같이 객체간에 의존관계도 설정할 수 있다.

```java

StaticApplicationContext ac = new StaticApplicationContext();
ac.registerBeanDefinition("printer", new RootBeanDefinition(SpringPrinter.class));

BeanDefinition helloDef = new RootBeanDefinition(Hello.class);
helloDef.getPropertyValues().addPropertyValue("name", "Spring");

//아이디가 printer인 빈에 대한 레퍼런스를 프로퍼티로 등록
helloDef.getPropertyValues().addPropertyValue("printer", new RuntimeBeanReference("printer"));

ac.registerSingleton("hello", helloDef);


```

- `RuntimeBeanReference` 는 런타임에 해결될 팩토리의 다른 빈에 대한 참조일때 사용하는  placeholder 클래스이다.
- 이런식으로 애플리케이션을 구성하는 빈 오브젝트를 생성하는 것이 IoC 컨테이너의 핵심 기능
- 기본적으로 싱글톤 빈은 애플리케이션 컨텍스트의 초기화 작업중에 모두 만들어진다.



### IoC 컨테이너의 종류와 사용 방법

- `ApplicationContext` 인터페이스를 바르게 구현했다면 어떤 클래스든 스프링의 IoC 컨테이너로 사용할 수 있다.
- 개발자가 `ApplicationContext` 구현할 일은 없고 스프링이 제공하는 `ApplicationContext` 구현체를 사용하면 된다.
- `ApplicationContext`는 직접 빈으로 등록할 필요도 없고 자동으로 만들어지는 방법을 사용한다.

#### StaticApplicationContext

- `StaticApplicationContext` 는 코드를 통해 빈 메타정보를 등록하기 위해 사용한다.
- 테스트용도이기 때문에 실전에서 사용하지 말고 테스트 목적으로 코드를 통해 빈을 등록하고 컨테이너가 어떻게 동작하는지 확인하고 싶을 때 사용하면 된다.

#### GenericApplicationContext

- `GenericApplicationContext` 는 가장 일반적인 애플리케이션 컨텍스트의 구현 클래스
- 모든 기능을 갖추고 있고 컨테이너의 주요 기능을 DI를 통해 확장할 수 있도록 설계되었다.
- `GenericApplicationContext` 는 xml 파일과 같은 외부의 리소스에 있는 빈 설정 메타정보를 리더를 통해 읽어들여서 메타정보로 전환해서 사용한다.
- 외부 리소스를 `BeanDefinition` 정보로 변환하는 기능을 가진 오브젝트는 `BeanDefinitionReader` 를 구현해서 만들고 대표적으로 `GroovyBeanDefinitionReader`, `XmlBeanDefinitionReader` 가 있다.

`XmlBeanDefinitionReader 사용해서 컨텍스트에 등록하기`

```java


GenericApplicationContext ac = new GenericApplicationContext();
XmlBeanDefinitionReader reader = new XmlBeanDefinition(ac);
reader.loadBeanDefinitions("xmldir/genericApplicationContext.xml");
  
//모든 메타정보가 등록되었으니 애플리케이션 컨텍스트를 초기화하라는 뜻
ac.refresh();

Hello hello = ac.getBean("Hello", Hello.class);


```

- `refresh()` 는 `AbstractApplicationContext` 에서 정의되어있고 일종의 퍼사드형태로 구현되어있다.

`AbstractApplicationContext.refresh()`

```java


	@Override
	public void refresh() throws BeansException, IllegalStateException {
		synchronized (this.startupShutdownMonitor) {
			StartupStep contextRefresh = this.applicationStartup.start("spring.context.refresh");

			// Prepare this context for refreshing.
			prepareRefresh();

			// Tell the subclass to refresh the internal bean factory.
			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

			// Prepare the bean factory for use in this context.
			prepareBeanFactory(beanFactory);

			try {
				// Allows post-processing of the bean factory in context subclasses.
				postProcessBeanFactory(beanFactory);

				StartupStep beanPostProcess = this.applicationStartup.start("spring.context.beans.post-process");
				// Invoke factory processors registered as beans in the context.
				invokeBeanFactoryPostProcessors(beanFactory);

				// Register bean processors that intercept bean creation.
				registerBeanPostProcessors(beanFactory);
				beanPostProcess.end();

				// Initialize message source for this context.
				initMessageSource();

				// Initialize event multicaster for this context.
				initApplicationEventMulticaster();

				// Initialize other special beans in specific context subclasses.
				onRefresh();

				// Check for listener beans and register them.
				registerListeners();

				// Instantiate all remaining (non-lazy-init) singletons.
				finishBeanFactoryInitialization(beanFactory);

				// Last step: publish corresponding event.
				finishRefresh();
			}

			catch (BeansException ex) {
				if (logger.isWarnEnabled()) {
					logger.warn("Exception encountered during context initialization - " +
							"cancelling refresh attempt: " + ex);
				}

				// Destroy already created singletons to avoid dangling resources.
				destroyBeans();

				// Reset 'active' flag.
				cancelRefresh(ex);

				// Propagate exception to caller.
				throw ex;
			}

			finally {
				// Reset common introspection caches in Spring's core, since we
				// might not ever need metadata for singleton beans anymore...
				resetCommonCaches();
				contextRefresh.end();
			}
		}
	}

```

- 빈 설정 리더를 만들기만하면 어떤 형태로도 빈 설정 메타정보를 작성할 수 있다.
- 스프링에서는 대표적으로 xml, 애너테이션, 클래스 세가지 방식으로 빈 설정 메타정보를 작성할 수 있음. 필요하다면 충분히 다른 방법으로 유연하게 확장도 가능하다.
- `GenericApplicationContext` 역시 실제 코드레벨에서 사용할 경우는 거의 없지만 아래와 같이 JUnit Test에서 `ApplicationContext` 를 주입받아서 사용하는 경우와 같이 직접적으로 사용하는 경우도 있다.

```java


@RunWith(SpringJUnit4ClassRunner.class)
//애플리케이션 컨텍스트 생성과 동시에 xml 파일을 읽어오고 초기화까지 수행한다.
@ContextConfiguration(location = "/test-appalicationContext.xml")
public class UserServiceTest {
  @Autowired
  ApplicationContext applicationContext; //GenericApplicationContext 주입
}

```



#### GenericXmlApplicationContext

- `GenericXmlApplicationContext` 는 `XmlBeanDefinitionReader` 를 내장하고 있기 때문에 xml 파일을 읽어들이고 refresh() 를 통해 초기화해주는 역할을 한다.

```java
//애플리케이션 컨텍스트 생성과 동시에 xml 파일을 읽어오고 초기화까지 수행한다.
GenericXmlApplicationContext ac = new GenericXmlApplicationContext("xmlDir/genericApplicationContext.xml");
```





#### WebApplicationContext

- 스프링 애플리케이션에서 가장 많이 사용되는 애플리케이션 컨텍스트

```java
public interface WebApplicationContext extends ApplicationContext {

	String ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE = WebApplicationContext.class.getName() + ".ROOT";

	String SCOPE_REQUEST = "request";
  
	String SCOPE_SESSION = "session";

	String SCOPE_APPLICATION = "application";

	String SERVLET_CONTEXT_BEAN_NAME = "servletContext";

	String CONTEXT_PARAMETERS_BEAN_NAME = "contextParameters";

	String CONTEXT_ATTRIBUTES_BEAN_NAME = "contextAttributes";

	@Nullable
	ServletContext getServletContext();

}
```

- 기존 `ApplicationContext` 에서 웹 환경에서 사용할 수 있도록 확장된 인터페이스
- SpringBoot 2.x 대 기준으로 `AnnotationConfigServletWebServerApplicationContext` 가 `ApplicationContext` 구현체로 로드된다.
- `AnnotationConfigServletWebServerApplicationContext ` 는 `ServletWebServerApplicationContext` 를 확장하는 애너테이션 기반 애플리케이션 컨텍스트이고 `@Configuration` 뿐만 아니라 `java.inject`의 `@Component` 클래스 및 JSR-330 호환 클래스도 허용한다.

`ApplicationContextFactory에서AnnotationConfigServletWebServerApplicationContext 생성하는 부분 `

```java
@FunctionalInterface
public interface ApplicationContextFactory {
    ApplicationContextFactory DEFAULT = (webApplicationType) -> {
        try {
            switch(webApplicationType) {
            case SERVLET:
                return new AnnotationConfigServletWebServerApplicationContext();
            case REACTIVE:
                return new AnnotationConfigReactiveWebServerApplicationContext();
            default:
                return new AnnotationConfigApplicationContext();
            }
        } catch (Exception var2) {
            throw new IllegalStateException("Unable create a default ApplicationContext instance, you may need a custom ApplicationContextFactory", var2);
        }
    };
  ...
}
```

- `ServletWebServerApplicationContext` 는 `ApplicationContext` 의 `onRefresh()` 를 구현하는데 여기에서 웹서버를 생성하는 모습을 확인할 수 있음

`ServletWebServerApplicationContext.onRefresh()`

```java

	@Override
	protected void onRefresh() {
		super.onRefresh();
		try {
			createWebServer();
		}
		catch (Throwable ex) {
			throw new ApplicationContextException("Unable to start web server", ex);
		}
	}

	private void createWebServer() {
		WebServer webServer = this.webServer;
		ServletContext servletContext = getServletContext();
		if (webServer == null && servletContext == null) {
			StartupStep createWebServer = this.getApplicationStartup().start("spring.boot.webserver.create");
			ServletWebServerFactory factory = getWebServerFactory();
			createWebServer.tag("factory", factory.getClass().toString());
			this.webServer = factory.getWebServer(getSelfInitializer());
			createWebServer.end();
			getBeanFactory().registerSingleton("webServerGracefulShutdown",
					new WebServerGracefulShutdownLifecycle(this.webServer));
			getBeanFactory().registerSingleton("webServerStartStop",
					new WebServerStartStopLifecycle(this, this.webServer));
		}
		else if (servletContext != null) {
			try {
				getSelfInitializer().onStartup(servletContext);
			}
			catch (ServletException ex) {
				throw new ApplicationContextException("Cannot initialize servlet context", ex);
			}
		}
		initPropertySources();
	}

```

- Spring Boot가 아닌 일반 Spring에서는 main() 메서드를 호출할 방법이 없었기 때문에 웹 환경에서 main() 메서드 대신 서블릿 컨테이너가 브로우저로 오는 HTTP 요청을 받아서 해당 요청에 매핑되어 있는 서블릿을 실행해주는 방법으로 동작했음. 서블릿이 일종의 main() 메서드 역할을 했다.
- 







### IoC 컨테이너 계층구조

#### 부모컨텍스트를 이용한 계층구조 효과

#### 컨텍스트 계층구조 테스트

### 웹 애플리케이션의 컨테이너 구성

#### 웹 애플리케이션의 컨텍스트 구성 방법

#### 루트 애플리케이션 컨텍스트 등록

#### 서블릿 애플리케이션 컨텍스트 등록



## IoC/DI를 위한 빈 설정 메타정보 작성

### 빈 설정 메타정보

#### 빈 설정 메타정보 항목

### 빈 등록 방법

#### XML: \<bean\> 태그

#### XML: 네임스페이스와 전용 태그

#### 자동인식을 이용한 빈 등록: 스테레오타입 애너테이션과 빈 스캐너

#### 자바 코드에 의한 빈 등록: @Configuration 클래스의 @Bean 메서드

#### 자바 코드에 의한 빈 등록: 일반 빈 클래스의 @Bean 메서드

#### 빈 등록 메타정보 구성 전략

### 빈 의존관계 설정 방법

#### XML: \<property\>, \<constructor-arg\>

#### XML: 자동 와이어링

#### XML: 네임스페이스와 전용 태그

#### 애너테이션: @Resource

#### 애너테이션: @Autowired/ @Inject

#### @Autowired와 getBean(), 스프링 테스트

#### 자바 코드에 의한 의존관계 설정

#### 빈 의존관계 설정 전략

### 프로퍼티 값 설정 방법

#### 메타정보 종류에 따른 값 설정 방법

#### PropertyEditor와 ConversionService

#### 컬렉션

#### Null과 빈 문자열

#### 프로퍼티 파일을 이용한 값 설정

### 컨테이너가 자동 등록하는 빈

#### ApplicationContext, BeanFactory

#### ResourceLoader, ApplicationEventPublisher

#### systemProperties, systemEnvironment



## 프로토타입과 스코프

### 프로토타입 스코프

#### 프로토타입 빈의 생명주기와 종속성

#### 프로토타입 빈의 용도

#### DI와 DL

#### 프로토타입 빈의 DL 전략

### 스코프

#### 스코프의 종류

#### 스코프 빈의 사용 방법

#### 커스텀 스코프와 상태를 저장하는 빈 사용하기



## 기타 빈 설정 메타정보

### 빈 이름

#### XML 설정에서의 빈 식별자와 별칭

#### 애너테이션에서의 빈 이름

### 빈 생명주기 메서드

#### 초기화 메서드

#### 제거 메서드

### 팩토리 빈과 팩토리 메서드



## 스프링 3.1의 IoC 컨테이너와 DI

### 빈의 역할과 구분

#### 빈의 종류

#### 컨테이너 인프라 빈과 전용 태그

#### 빈의 역할

### 컨테이너 인프라 빈을 위한 자바 코드 메타정보

#### IoC/DI 설정 방법의 발전

#### 자바 코드를 이용한 컨테이너 인프라 빈 등록

### 웹 애플리케이션의 새로운 IoC 컨테이너 구성

### 런타임 환경 추상화와 프로파일

#### 환경에 따른 빈 설정정보 변경 전략과 한계

#### 런타임 환경과 프로파일

#### 활성 프로파일 지정 방법

#### 프로파일 활용 전략

### 프로퍼티 소스

#### 프로퍼티

#### 스프링에서 사용되는 프로퍼티 종류

#### 프로파일의 통합과 추상화

#### 프로퍼티 소스의 사용

#### @PropertySource와 프로퍼티 파일

#### 웹 환경에서 사용되는 프로퍼티 소스와 프로퍼티 소스 초기화 오브젝트