

[https://github.com/Cr0ffle/Spring](https://github.com/Cr0ffle/Spring)

# 토비의  스프링 vol.2





# #1 IoC 컨테이너와 DI



## IoC 컨테이너: 빈 팩토리와 애플리케이션 컨텍스트

- 스프링에서는 오브젝트의 생성과 관계설정, 사용, 제거 등의 작업을 애플리케이션 코드 대신 독립된 컨테이너가 담당하는데 이 컨테이너를 IoC 컨테이너라고 한다.
- 이 IoC 컨테이너는 애플리케이션 컨텍스트라고도 불리고 애플리케이션 컨텍스트는 IoC와 DI를 위한 빈 팩토리 또는 그 이상의 기능을 가졌다고 보면 된다.
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

- 가장 간단하게 IoC 컨테이너를 만드는 방법은 다음 과같이 `ApplicationContext` 구현 클래스의 인스턴스를 만들면 된다.

```java
StaticApplicationContext ac = new StaticApplicationContext();
```

- 이렇게 만들어진 컨테이너가 본격적인 IoC 컨테이너로서 동작하려면 POJO 클래스와 설정 메타정보가 필요하다.

#### POJO 클래스

- 먼저 애플리케이션의 핵심 코드를 담고 있는 POJO 클래스를 준비해야한다.
- 아래 `Hello` 클래스와 `Printer` 인터페이스, 그리고 `Printer` 인터페이스를 구현한 `StringPrinter`, `ConsolePrinter` 를 준비한다.

![1](./images/toby-spring-vol2/1.png)

- `Hello` 클래스는 `Printer` 인터페이스를 참조로 두고 있는 약결합상태
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

- `refresh()` 는 `AbstractApplicationContext` 에서 정의되어있고 일종의 퍼사드 + 팩토리 메서드 형태로 구현되어있다.

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

- `GenericXmlApplicationContext` 는 `XmlBeanDefinitionReader` 를 내장하고 있기 때문에 xml 파일을 읽어들이고 `refresh()` 를 통해 초기화해주는 역할을 한다.

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

- 독립적인 자바 프로그램으로 만들어진 웹애플리케이션에서는 `main()`메서드를 호출할 방법이 없었기 때문에 웹 환경에서 `main()` 메서드 대신 서블릿 컨테이너가 브라우저로 오는 HTTP 요청을 받아서 해당 요청에 매핑되어 있는 서블릿을 실행해주는 방법으로 동작했한다. 서블릿이 일종의 `main()` 메서드와 같은 역할을 하는 셈

  

![3](./images/toby-spring-vol2/3.png)

- `WebApplicationContext` 는 설정 메타정보를 읽어들여서 초기화한다.
- 서블릿 컨테이너는 클라이언트의 요청을 받아서 서블릿을 동작시키는 일을 맡는다.
- 서블릿은 웹 애플리케이션이 시작될 때 미리 만들어 둔 웹 애플리케이션 컨텍스트에게 빈 오브젝트로 구성된 애플리케이션의 기동 역할을 해줄 빈을 요청해서 받아둔다. 
- 스프링은 웹 환경에서  클라이언트로부터 들어오는 요청마다 적절한 빈을 찾아서 이를 실행해주는 기능을 가진 `DispatcherServlet`이라는 이름의 서블릿을 제공한다. 
- 스프링이 제공해주는 `DispatcherServlet` 을 web.xml에 등록하는 것 만으로도 웹 애플리케이션을 실행하는데 필요한 대부분 준비가 끝난다.
- Spring Boot에서는 web.xml을 더이상 사용하지 않고 `DispatcherServletAutoConfiguration`에서 자동으로 `DispatcherServlet` 을 등록시켜준다.



```java
@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)
@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass(DispatcherServlet.class)
@AutoConfigureAfter(ServletWebServerFactoryAutoConfiguration.class)
public class DispatcherServletAutoConfiguration {

  
  
  ...
    
	@Configuration(proxyBeanMethods = false)
	@Conditional(DefaultDispatcherServletCondition.class)
	@ConditionalOnClass(ServletRegistration.class)
	@EnableConfigurationProperties(WebMvcProperties.class)
	protected static class DispatcherServletConfiguration {

		@Bean(name = DEFAULT_DISPATCHER_SERVLET_BEAN_NAME)
		public DispatcherServlet dispatcherServlet(WebMvcProperties webMvcProperties) {
			DispatcherServlet dispatcherServlet = new DispatcherServlet();
			dispatcherServlet.setDispatchOptionsRequest(webMvcProperties.isDispatchOptionsRequest());
			dispatcherServlet.setDispatchTraceRequest(webMvcProperties.isDispatchTraceRequest());
			dispatcherServlet.setThrowExceptionIfNoHandlerFound(webMvcProperties.isThrowExceptionIfNoHandlerFound());
			dispatcherServlet.setPublishEvents(webMvcProperties.isPublishRequestHandledEvents());
			dispatcherServlet.setEnableLoggingRequestDetails(webMvcProperties.isLogRequestDetails());
			return dispatcherServlet;
		}

		@Bean
		@ConditionalOnBean(MultipartResolver.class)
		@ConditionalOnMissingBean(name = DispatcherServlet.MULTIPART_RESOLVER_BEAN_NAME)
		public MultipartResolver multipartResolver(MultipartResolver resolver) {
			// Detect if the user has created a MultipartResolver but named it incorrectly
			return resolver;
		}

	}

  ...
  
}

```



### IoC 컨테이너 계층구조

- 일반적인 경우라면 IoC 컨테이너는 애플리케이션마다 한개만 있으면 충분하다.
- 하지만 트리 모양의 계층구조를 만들어야한다면 한 개 이상의 IoC 컨테이너를 만들어야한다.

#### 부모 컨텍스트를 이용한 계층구조 효과

![4](./images/toby-spring-vol2/4.png)

- 계층 구조 안의 모든 컨텍스트는 각자 독립적인 설정정보를 이용해 빈 오브젝트를 만들고 관리한다.

- 자신이 관리하는 빈 중에서 필요한 빈을 찾아보고 없으면 부모 컨텍스트에서 검색, 최종적으로 루트 컨텍스트까지 검색하는 방식으로 동작한다.

- 형제 레벨이나 자식 레벨의 컨텍스트는 검사하지 않는것이 특징

- 검색순서는 자신부터 시작해서 순서대로 올라가기때문에 현재 컨텍스트와 부모 컨텍스트에 동일한 빈이 정의되어있어도 현재 컨텍스트에서 필요한 빈을 찾았다면 부모 컨텍스트에서 정의된 빈은 무시된다.

- 이런식으로 기존 설정을 수정하지 않고 사용하지만 일부 빈 구성을 바꾸고 싶은 경우, 애플리케이션 컨텍스트의 계층구조를 만드는 방법이 편리한 방법. 추가로 여러 애플리케이션 컨텍스트가 공유하는 설정을 만들기 위해서 계층구조를 사용한다.

- 이런 컨텍스트 계층구조를 사용한다면 반드시 컨텍스트 계층 구조에 대해서 자세하게 알아야한다. 컨텍스트 계층구조의 특성이나 설정을 완벽하게 이해하지 못한 채로 사용하면 뜻하지 않은 에러를 만나거나 원하는 대로 동작하지 않는 문제가 발생할 수 있다.

  

#### 컨텍스트 계층구조 테스트

`parentContext.xml`

```xml

<bean id="printer" class="springbook.leaningtest.spring.ioc.bean.StringPrinter"/>

<bean id="hello" 
class="springbook.leaningtest.spring.ioc.bean.Hello">
	<property name="name" value="parent"/>
	<property name="printer" value="printer"/>
</bean>
```

`childContext.xml`

```xml
<bean id="hello" 
class="springbook.leaningtest.spring.ioc.bean.Hello">
	<property name="name" value="child"/>
	<property name="printer" value="printer"/>
</bean>
```

- 자식 컨텍스트에는 `printer` 라는 id를 갖는 빈이 없기 때문에 부모 컨텍스트에서 검색해서 사용한다.



`계층구조 컨텍스트`

```java

ApplicationContext parent = GenericApplicationContext("parentContexst.xml");

ApplicationContext child = GenericApplicationContext(parent);

XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(child);
reader.loadBeanDefinitions("childContext.xml");
child.refresh();


//printer 빈은 부모 컨텍스트에서 가져온다.
Printer printer = child.getBean("printer", Printer.class);

//hello 빈은 자식 컨텍스트에서 가져온다.
Hello hello = child.getBean("hello", Hello.class)

```

- 이런 계층구조 컨텍스트의 특징으로 같은 타입의 빈 정의를 허용한다고 하더라도 가능하다면 중복된 빈이 정의되는것을 피해야한다. 이러한 복잡한 계층구조와 혼란스러운 빈 정의는 발견하기 매우 힘든 버그를 만들어낼 가능성이 높다.



### 웹 애플리케이션의 IoC 컨테이너 구성

- 스프링은 프론트 컨트롤러 패턴을 사용한다.
- 프론트 컨트롤러 패턴이란 대표 서블릿이 중앙집중식으로 모든 요청을 다 받아서 처리하는 방식을 말한다.
- 웹 애플리케이션 안에서 동작하는 IoC 컨테이너는 두가지 방식으로 만들어진다.
  - 스프링 애플리케이션의 요청을 처리하는 서블릿 안에서 생성
  - 웹 애플리케이션 레벨에서 생성
- 스프링 웹 애플리케이션에는 두 개의 컨테이너가 만들어진다. <= 이부분 이해가 안됨

#### 웹 애플리케이션의 컨텍스트 계층구조

- 웹 애플리케이션 레벨에서 등록되는 컨테이너는 보통 루트 웹 애플리케이션 컨텍스트라고 한다.
- 이 컨텍스트는 전체 계층구조에서 가장 최상단에 위치한다.
- 스프링 애플리케이션에는 하나 이상의 프론트 컨트롤러 역할을 하는 서블릿이 등록될 수 있고 서블릿에는 각각 독립적으로 애플리케이션 컨텍스트가 만들어진다.
- 루트 애플리케이션 컨텍스트에 공통적인 빈들을 설정해둔다면 서블릿별로 중복돼서 생성되는걸 방지할 수 있다.

![5](./images/toby-spring-vol2/5.png)

- 위 그림은 이론상으로는 가능하나 실제로 N개의 프론트 컨트롤러를 등록할 이유가 없기 때문에 일반적인 경우라면 1:1로 매칭한다.
- 그럼에도 컨텍스트를 계층구조로 분리하는 이유는 웹 기술에 의존적인 부분과 그렇지 않은 부분을 구분하기 위해서 나눈다.
- 스프링 서블릿을 사용하는 스프링의 웹 기술 외의 웹 기술을 고려하고 있다면 계층형태로 컨텍스트를 구분해두는것이 바람직하다.

![6](./images/toby-spring-vol2/6.png)



#### 웹 애플리케이션의 컨텍스트 구성 방법

`서블릿 컨텍스트와 루트 애플리케이션 컨텍스트 계층 구조`

- 웹 관련 빈들은 서블릿의 컨텍스트에 두고 나머지는 루트 애플리케이션 컨텍스트에 등록하는 방법

`루트 애플리케이션 컨텍스트 단일구조`

- 스프링 웹 기술을 사용하지 않고 서드파티 웹 프레임워크나 서비스 엔진만을 사용해서 프레젠테이션계층을 만든다면 스프링 서블릿을 둘 이유가 없다.
- 루트 애플리케이션 컨텍스트만 등록하는 방법

`서블릿 컨텍스트 단일구조`

- 스프링 웹 기술을 사요하면서 스프링 외의 프레임워크나 서비스엔진에서 스프링의 빈을 이용할 생각이 아니라면 루트 애플리케이션 컨텍스트를 생략할 수 있다.
- 서블릿 안에 만들어지는 애플리케이션 컨텍스트가 스스로 루트 컨텍스트가 된다.
- 스프링 부트에서는 이 방법을 사용하는 것 같다.

`ServletWebServerApplicationContext.prepareWebApplicationContext() 메서드`

```java
protected void prepareWebApplicationContext(ServletContext servletContext) {
   Object rootContext = servletContext.getAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE);
   if (rootContext != null) {
      if (rootContext == this) {
         throw new IllegalStateException(
               "Cannot initialize context because there is already a root application context present - "
                     + "check whether you have multiple ServletContextInitializers!");
      }
      return;
   }
   servletContext.log("Initializing Spring embedded WebApplicationContext");
   try {
      servletContext.setAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE, this);
      if (logger.isDebugEnabled()) {
         logger.debug("Published root WebApplicationContext as ServletContext attribute with name ["
               + WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE + "]");
      }
      setServletContext(servletContext);
      if (logger.isInfoEnabled()) {
         long elapsedTime = System.currentTimeMillis() - getStartupDate();
         logger.info("Root WebApplicationContext: initialization completed in " + elapsedTime + " ms");
      }
   }
   catch (RuntimeException | Error ex) {
      logger.error("Context initialization failed", ex);
      servletContext.setAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE, ex);
      throw ex;
   }
}
```



#### 루트 애플리케이션 컨텍스트 등록

- 루트 웹 애플리케이션 컨텍스트를 등록하는 가장 간단한 방법은 서블릿의 이벤트 리스너를 이용하는. 것
- `ServiceContextListener`를 구현한 리스너는 웹 애플리케이션 전체에 적용 가능한 DB 연결 기능이나 로깅 같은 서비스를 만드는데 유용하게 쓰인다.
- 아래와 같이 ContextLoaderListener 를 사용해서 웹 애플리케이션이 시작될때 자동으로 루터 애플리케이션 컨텍스트를 만들어주고 초기화시킬 수 있다. 

```xml
<listener>
  <listener-class>
  	org.springframework.web.context.ContextLoaderListener
  </listener-class>
</listener>
```

- 아무런 설정도 하지 않으면 아래 설정이 기본 설정이 된다.
  - 애플리케이션 컨텍스트 클래스 : `XmlWebApplicationContext`
  - XML 설정파일 위치: `/WEB-INF/applicationContext.xml`
- `contextConfigLocation`으로 설정해서 직접 위치를 지정할 수 있다

```xml
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>
    /WEB-INF/daoContext.xml
    /WEB-INF/applicationContext.xml
	</param-value>
</context-param>
```

- 애너테이션 기반으로 설정하기

```xml
<context-param>
	<param-name>contextClass</param-name>
	<param-value>
		org.springframework.web.context.support.AnnotationConfigWebApplicationContext
	</param-value>
</context-param>
```

- `AnnotationConfigWebApplicationContext` 을 사용할 경우에는 반드시 `contextConfigLocation` 파라미터를 선언해줘야한다. 이때는 xml 파일의 위치가 아니라 설정 메타정보를 담고 있는 클래스 또는 패키지를 지정할 수 있다.

#### 서블릿 애플리케이션 컨텍스트 등록

- 서블릿의 이름을 다르게 지정해준다면 `DispatcherServlet` 을 여러개 등록할 수 있다.
- 각 `DispatcherServlet` 은 서블릿이 초기화 될 때 자신만의 컨텍스트를 생성하고 초기화한다. 동시에 웹 애플리케이션 레벨에 등록된 루트 애플리케이션 컨텍스트를 찾아서 이를 자신의 부모 컨텍스트로 사용한다.

```xml
<servlet>
	<servlet-name>spring</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <load-on-startup>1</load-on-startup>
</serlvet>
```

`servlet-name`

- `DispatcherServlet` 에 의해 만들어지는 애플리케이션 컨텍스트는 모두 독립적인 네임스페이스를 갖는데 이때 `servlet-name` + `-servlet` -> `spring-servlet`  이라는 이름으로 생성한다. 
- 이 네임스페이스는 컨텍스트 파일 위치를 나타내기 때문에 중요하다. 위와 같이 설정했다면 디폴트 설정이 `WEB-INF/spring-servlet.xml` 이 된다.

`load-on-startup`

- 서블릿 컨테이너가 등록된 서블릿을 언제 만들고 초기화할지 순서를 정하는 정수값



## IoC/DI를 위한 빈 설정 메타정보 작성

- IoC 컨테이너의 가장 기본적인 역할은 코드를 대신해서 애플리케이션을 구성하는 오브젝트를 생성하고 관리하는 것
- 컨테이너는 빈 설정 메타정보를 통해 빈의 클래스와 이름을 제공받고 파일이나 애너테이션같은 리소스로부터 전용 리더를 통해 `BeanDefinition` 타입의 오브젝트로 변환된다.
- 적절한 리더나 `BeanDefinition` 생성기를 사용할 수만 있다면 빈 설정 메타정보를 담은 소스는 어떤 식으로 만들어도 상관 없다.

![7](./images/toby-spring-vol2/7.png)

### 빈 설정 메타정보

- `BeanDefinition`에는 IoC 컨테이너가 빈을 만들 때 필요한 핵심 정보가 담겨 있다.
- 설정 메타정보가 같지만 이름이 다른 여러 개의 빈 오브젝트를 만들 수 있기 때문에 `BeanDefinition` 은 여러 개의 빈을 만드는 데 재사용될 수 있다.

#### 빈 설정 메타정보 항목

| 이름                       | 내용                                                         | 디폴트 값      |
| -------------------------- | ------------------------------------------------------------ | -------------- |
| beanClassName              | 빈 오브젝트의 클래스 이름. 빈 오브젝트는 이 클래스의 인스턴스가 된다. | 없음. 필수항목 |
| parentName                 | 빈 메타정보를 상속받을 부모 BeanDefinition의 이름. 빈의 메타정보는 계층구조로 상속할 수 있다. | 없음           |
| factoryBeanName            | 팩토리 역할을 하는 빈을 이용해 빈 오브젝트를 생성하는 경우에 팩토리 빈의 이름을 지정한다. | 없음           |
| factoryMethodName          | 다른 빈 또는 클래스의 메서드를 통해 빈 오브젝트를 생성하는 경우 그 메서드 이름을 지정한다. | 없음           |
| scope                      | 빈 오베즉트의 생명주기를 결정하는 스코프를 지정한다. 크게 싱글톤과 프로토타입 스코프로 구분할 수 있다. | 싱글톤         |
| lazyInit                   | 빈 오브젝트의 생성을 최대한 지연할 것인지를 지정한다. 이 값이 true이면 컨테이너는 빈 오브젝트의 생성을 꼭 필요한 시점까지 미룬다. | false          |
| dependsOn                  | 먼저 만들어져야 하는 빈을 지정할 수 있다. 빈 오브젝트의 생성 순서가 보장돼야 하는 경우 이용한다. 하나 이상의 빈 이름을 지정할 수 있다. | 없음           |
| autowireCandidate          | 명시적인 설정이 없어도 미리 정해진 규칙을 가지고 자동으로 DI 후보를 결정하는 자동와이어링의 대상으로 포함시킬지의 여부 | true           |
| primary                    | 자동와이어링 작업 중에 DI 대상 후보가 여러 개가 발생하는 경우가 있다. 이때 최종 선택의 우선권을 부여할지 여부, primary가 지정된 빈이 없이 여러 개의 후보가 존재하면 자동와이어링 예외가 발생한다. | false          |
| abstract                   | 메타정보 상속에만 사용할 추상 빈으로 만들지의 여부, 추상 빈이 되면 그 자체는 오브젝트가 생성되지 않고 다른 빈의 부모 빈으로만 사용된다. | false          |
| autowireMode               | 오토와이어링 전략. 이름, 타입, 생성자, 자동인식 등의 방법이 있다. | 없음           |
| dependencyCheck            | 프로퍼티 값 또는 레퍼런스가 모두 설정되어 있는지를 검증하는 작업의 종류 | 체크하지 않음  |
| initMethod                 | 빈이 생성되고 DI를 마친 뒤에 실행할 초기화 메서드의 이름     | 없음           |
| destroyMethod              | 빈의 생명주기가 다 돼서 제거하기 전에 호출할 메서드의 이름   | 없음           |
| propertyValues             | 프로퍼티 이름과 설정 값 또는 레퍼런스. 수정자 메서드를 통한 DI 작업에서 사용한다. | 없음           |
| constructorArgumentsValues | 생성자의 이름과 설정 값 또는 레퍼런스. 생성자를 통한 DI 작업에서 사용한다. | 없음           |
| annotationMetadata         | 빈 클래스에 담긴 애너테이션과 그 애트리뷰트 값. 애너테이션을 이용하는 설정에서 활용한다. | 없음           |

- 빈 설정 메타정보 항목 중에서 가장 중요한 것은 클래스의 이름
- 추상 빈으로 정의하지 않는 한 클래스 정보는 반드시 필요하다.
- 컨테이너 빈의 메타정보가 등록될 때 꼭 필요한 것은 클래스 이름과 함께 빈의 아이디 또는 이름이다.



### 빈 등록 방법

- 빈 등록은 빈 메타정보를 작성해서 컨테이너에게 건네주는 방법으로 등록이 가능하다.
- Spring boot 에서는 여러 `BeanDefinition` 타입을 미리 `DefaultListableBeanFactory` 에 등록시켜서 꺼내오는 방식으로 빈들 을 등록한다.

`DefaultListableBeanFactory.registerBeanDefinition() 메서드`

```java
public class DefaultListableBeanFactory extends AbstractAutowireCapableBeanFactory
		implements ConfigurableListableBeanFactory, BeanDefinitionRegistry, Serializable {
  

  ...

  private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<>(256);
  
  
	@Override
	public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition)
			throws BeanDefinitionStoreException {

		Assert.hasText(beanName, "Bean name must not be empty");
		Assert.notNull(beanDefinition, "BeanDefinition must not be null");

		if (beanDefinition instanceof AbstractBeanDefinition) {
			try {
				((AbstractBeanDefinition) beanDefinition).validate();
			}
			catch (BeanDefinitionValidationException ex) {
				throw new BeanDefinitionStoreException(beanDefinition.getResourceDescription(), beanName,
						"Validation of bean definition failed", ex);
			}
		}

		BeanDefinition existingDefinition = this.beanDefinitionMap.get(beanName);
		if (existingDefinition != null) {
			if (!isAllowBeanDefinitionOverriding()) {
				throw new BeanDefinitionOverrideException(beanName, beanDefinition, existingDefinition);
			}
			else if (existingDefinition.getRole() < beanDefinition.getRole()) {
				// e.g. was ROLE_APPLICATION, now overriding with ROLE_SUPPORT or ROLE_INFRASTRUCTURE
				if (logger.isInfoEnabled()) {
					logger.info("Overriding user-defined bean definition for bean '" + beanName +
							"' with a framework-generated bean definition: replacing [" +
							existingDefinition + "] with [" + beanDefinition + "]");
				}
			}
			else if (!beanDefinition.equals(existingDefinition)) {
				if (logger.isDebugEnabled()) {
					logger.debug("Overriding bean definition for bean '" + beanName +
							"' with a different definition: replacing [" + existingDefinition +
							"] with [" + beanDefinition + "]");
				}
			}
			else {
				if (logger.isTraceEnabled()) {
					logger.trace("Overriding bean definition for bean '" + beanName +
							"' with an equivalent definition: replacing [" + existingDefinition +
							"] with [" + beanDefinition + "]");
				}
			}
			this.beanDefinitionMap.put(beanName, beanDefinition);
		}
		else {
			if (hasBeanCreationStarted()) {
				// Cannot modify startup-time collection elements anymore (for stable iteration)
				synchronized (this.beanDefinitionMap) {
					this.beanDefinitionMap.put(beanName, beanDefinition);
					List<String> updatedDefinitions = new ArrayList<>(this.beanDefinitionNames.size() + 1);
					updatedDefinitions.addAll(this.beanDefinitionNames);
					updatedDefinitions.add(beanName);
					this.beanDefinitionNames = updatedDefinitions;
					removeManualSingletonName(beanName);
				}
			}
			else {
				// Still in startup registration phase
				this.beanDefinitionMap.put(beanName, beanDefinition);
				this.beanDefinitionNames.add(beanName);
				removeManualSingletonName(beanName);
			}
			this.frozenBeanDefinitionNames = null;
		}

		if (existingDefinition != null || containsSingleton(beanName)) {
			resetBeanDefinition(beanName);
		}
		else if (isConfigurationFrozen()) {
			clearByTypeCache();
		}
	}

  
  ...
    
}
```

- 스프링을 사용하는 입장에서 자주 사용되는 빈의 등록 방법은 크게 다섯가지가 있다.

#### XML: \<bean\> 태그

- 기본적으로 `<bean>` 태그를 사용해서 빈을 생성할 수 있다. 아래와 같이 id와 class 라는 두 개의 애트리뷰트가 필요하고 id는 생략할 수 있다.

```xml
<bean id="hello" class="com.example.Hello">
	...
</bean>
```



#### XML: 네임스페이스와 전용 태그

- `<bean>` 태그 외에도 다양한 스키마에 정의된 전용 태그를 사용해 빈을 등록하는 방법이 있다.
- 스프링의 빈을 분류하면 크게 애플리케이션의 핵심 코드를 담은 컴포넌트와 서비스 또는 컨테이너 설정을 위한 반으로 구분할 수 있는데 이 두개의 성격이 다르기때문에 설정을 위한 전용 네임스페이스와 태그를 통해 설정할 수 있게 해준다.

```xml
<bean id="mypointcut" class="org.springframework.aop.aspectj.AspectJExpressionPintcut"> 
	<property name="expression" value="execution(* * ..*ServiceImple.upgrade*(..))"></property>
</bean>

<aop:pointcut id="mypointcut" expression="execution(* * ..*ServiceImple.upgrade*(..))"> </aop:pointcut>
```

- 전용 네임스페이스 태그를 활용했기때문에 같은 설정이라도 `<aop:pintcut>` 태그를 사용한 포인트컷이 의미가 더 명확해졌다.
- 스프링이 제공해주는것 외에도 개발자가 스스로 커스텀 태그를 만들어서 적용할 수도 있다.

#### 자동인식을 이용한 빈 등록: 스테레오타입 애너테이션과 빈 스캐너

- 빈으로 사용될 클래스에 특별한 애너테이션을 부여해주면 이런 클래스를 자동으로 찾아서 빈으로 등록하게 할 수 있다.
- 특정 애너테이션이 붙은 클래스를 자동으로 찾아서 빈으로 등록해주는 방식을 빈 스캐닝을 통한 자동인식 빈 등록 기능이라고 하고 이런 스캐닝 작업을 담당하는 오브젝트를 빈 스캐너라고 한다.
- 스프링의 빈 스캐너는 지정된 클래스패스 아래에 있는 모든 패키지의 클래스를 대상으로 필터를 적용해서 빈 등록을 위한 클래스들을 선별해낸다.
- 빈 스캐너에 내장된 디폴트 필터는 `@Component` , `@Service` 나 `@Repository`도 모두 `@Component`를 사용하고 있다.
- 스프링에서는 빈 스캐너에 적용되는 애너테이션을 스테레오타입 애너테이션이라 부른다.

```java
@Component
public class AnnotatedHello {
  ...
}
```

- 빈 스캐너는 기본적으로 클래스 이름을 빈의 아이디로 사용하고 아래와 같이 이름을 지정해줄 수 있다.

```java
@Component("myHello")
public class AnnotatedHello {
  ...
}
```

- Spring Boot에서는 `@ComponentScan` 와 위에서 확인한 `AnnotationConfigServletWebServerApplicationContext` 를 사용해서 스테레오 타입 애너테이션을 빈으로 등록한다.



`스테레오타입 애너테이션 종류`

| 스테레오타입 애너테이션 | 적용 대상                                                    |
| ----------------------- | ------------------------------------------------------------ |
| `@Repository`           | 데이터 액세스 계층의 DAO 또는 리포지토리 클래스에 사용된다.  |
| `@Service`              | 서비스 계층의 클래스에 사용된다.                             |
| `@Controller`           | 프레젠테이션 계층의 MVC 컨트롤러에 사용된다. <br />스프링 웹 서블릿에 의해 웹 요청을 처리하는 컨트롤러 빈으로 선정된다. |

- 특정계층으로 분류하기 힘든 경우에는 `@Component`를 사용하는 것이 바람직하다.

#### 자바 코드에 의한 빈 등록: @Configuration 클래스의 @Bean 메서드

- `@Configuration` 이 붙은 클래스 안에 `@Bean` 이 붙은 메서드를 사용해서 빈으로 정의할 수 있다.

```java
@Configuration
public class AnnotationHelloConfig {
    @Bean
    public AnnotatedHello annotatedHello() {
        return new AnnotatedHello();
    }
}
```

- 선언된 메서드의 이름으로 빈의 이름이 된다.
- 애너테이션을 제거하고 보면 단순한 오브젝트 팩토리 기능의 메서드를 가진 클래스로 보이지만 `@Configuration` 과 `@Bean` 이 붙으면 스프링 컨테이너가 인식 할 수 있는 빈 메타정보 겸 오브젝트 팩토리가 된다.
-  `@Configuration` 이 붙은 클래스도 빈의 대상이 되는것이 특징이다.
- `AnnotationHelloConfig` 클래스를 컨테이너에서 직접 가져와서 아래와 같이 직접 `annotatedHello()` 메서드를 사용한다고 하더라도 `@Bean` 설정은 기본적으로 싱글톤이기 때문에 모든 `annotatedHello()` 메서드의 호출은 같은 인스턴스를 반환하는것도 특징이다.

```java


@Configuration
public class AnnotationHelloConfig {
    @Bean
    public AnnotatedHello annotatedHello() {
        return new AnnotatedHello();
    }
    
    @Bean
    public Foo foo() {
        Foo foo = new Foo();
        foo.setAnnotatedHello(annotatedHello()); //매번 같은 인스턴스
        return foo;
    }
    
    @Bean
    public Bar bar() {
        Bar bar = new Bar();
        bar.setAnnotatedHello(annotatedHello()); //매번 같은 인스턴스
        return bar;
    }
}

```

- 자바 코드를 이용한 빈 등록은 단순한 빈 스캐닝을 통한 자동인식으로는 등록하기 힘든 기술 서비스의 빈의 등록이나 컨테이너 설정용 빈을 xml 없이 등록하려고 할 때 유용하게 쓸 수 있다.
- 자바코드에 의한 설정이 xml과 같은 외부 설정파일을 이용하는 것보다 유용한 이유
  - 컴파일러나 IDE를 통한 타입 검증이 가능하다
  - 자동완성과 같은 IDE 지원 기능을 최대한 이용할 수 있다
  - 이해하기 쉽다
  - 복잡한 빈 설정이나 초기화 작업을 손쉽게 적용할 수 있다



#### 자바 코드에 의한 빈 등록: 일반 빈 클래스의 @Bean 메서드

- `@Configuration` 이 없더라도 일반 클래스가 빈으로 등록된다면 `@Bean` 애너테이션을 통해 리턴 오브젝트를 빈으로 등록하게 할 수 있다.
- 하지만 이런 경우 `@Configuration` 을 사용했을 때와 달리  `@Bean` 가 리턴하는 오브젝트의 인스턴스가 매번 다르기때문에 주의해야한다.

#### 빈 등록 메타정보 구성 전략

- 여러가지 방법으로 빈을 설정할 수 있고 애플리케이션의 특성과 개발팀의 문화, 기업의 정책에 맞는 적절한 조합을 찾아내고 일관성 있게 사용하는것이 중요하다.

### 빈 의존관계 설정 방법

- DI할 대상을 선정하는 방법으로 분류해보면 명시적으로 빈을 지정하는 방법과 일정한 규칙에 따라 자동으로 선정하는 방법으로 나눌 수 있다.
- 전자는 DI할 빈의 아이디를 직접 지정하는 것이고, 후자는 주로 타입 비교를 통해서 호환되는 타입의 빈을 DI 후보로 삼는 방법이다. 후자의 방법은 autowiring 이라고 한다.

#### XML: \<property\>, \<constructor-arg\>

- `<bean>` 을 이용해 빈을 등록했다면 프로퍼티와 생성자 두가지 방식으로 DI를 지정할 수 있다.
- 프로퍼티는 자바빈 규약을 따르는 수정자 메서드를 사용하고, 생성자는 클래스의 생성자를 이용하는 방법이다.

`<property>: 수정자 주입`

- 수정자를 통해 의존관계 빈을 주입하려면 `<property>` 태그를 사용하면 된다.
- 다음과 같이 ref 애트리뷰트를 사용하면 빈 이름을 이용해 주입할 빈을 찾는다.

```xml
<bean>
    <property name="printer" ref="defaultPrinter" />
</bean>

<bean id="defaultPrinter" class="..."></bean>
```

`<constructor-arg>: 생성자 주입`

- `<constructor-arg>`는 생성자를 통한 빈 또는 값의 주입에 사용된다.

```xml
<bean>
    <constructor-arg index="0" ref="defaultPrinter" />
    <constructor-arg index="1" value="30" />
</bean>

```

- value 프로퍼티는 값 또는 빈이 아닌 오브젝트를 주입할때 사용한다.
- 파라미터에 중복되는 타입이 없으면 index 프로퍼티가 아닌 타입을 지정해줘서 주입해줄 수 있고 name 프로퍼티를 사용해서 파라미터 이름을 사용할 수 있다

```xml
<constructor-arg type="java.lang.String" value="Hello" />
<constructor-arg type="com.example.Printer" ref="printer" />
    
<constructor-arg name="hello" value="Hello" />
<constructor-arg name="printer" ref="printer" />
```



#### XML: 자동 와이어링

- 자동 와이어링은 명시적으로 프로퍼티나 생성자 파라미터를 지정하지 않고 미리 정해진 규칙을 이용해 자동으로 DI 설정을 컨테이너가 추가하도록 만드는 것이다.

`byName: 빈 이름 자동 와이어링`

- 일반적으로 주입이 필요한 필드의 이름과 주입의 대상이되는 빈의 이름이 같은 관례를 사용해서 아래와 같이 `<property>` 태그를 생략하는 방법으로 자동와이어링을 구성할 수 있다.

```xml
<bean class="..." autowired="byName">
    <property name="myPrinter" ref="defaultPrinter" /> <!-- id와 name이 동일하기 때문에 이 부분을 생략 가능-->
</bean>

<bean id="myPrinter" class="com.example.Printer"></bean>
```

- 이름을 이용한 자동와이어링은 빈의 모든 프로퍼티에 대해 이름이 동일한 빈을 찾아서 연결해준다.
- 프로퍼티와 이름이 같은 빈이 없는 경우는 무시한다.

`byType: 타입에 의한 자동 와이어링`

- 이름을 이용하는 자동와이어링 방식은 명명 규칙을 엄하게 지켜야만 한다는 부담이 있다.
- 타입에 의한 자동와이어링은 프로퍼티의 타입과 각 빈의 타입을 비교해서 자동으로 연결해주는 방법이다.

```xml
<bean class="..." autowired="byType">
	...
</bean>

<bean id="myPrinter" class="com.example.Printer"></bean>
```

- 타입에 의한 자동 와이어링의 단점은 같은 타입의 빈이 두개 있을때 스프링이 어떤 빈을 사용해야할지 모르기때문에 적용되지 못한다.
- 모든 타입들을 비교하기때문에 byName보다 성능이 떨어진다.

----

- xml 안에서 자동 와이어링을 사용하는 방식의 단점
  - xml만 봐서 빈 의존관계를 알기 힘들다.
  - 하나의 빈에 여러방식의 자동와이어링 방식을 적용할 수 없다.

#### 애너테이션: @Resource

- `@Resource`는 주입할 빈을 아이디로 지정하는 방법이다.
- `@Resource`는 수정자 메서드, 필드 타입에 붙일 수 있다.

`수정자 메서드`

```java
public class Hello {
    private Printer printer;
    
    @Resource(name="printer")
    public void setPrinter(Printer printer) {
        this.printer = printer;
    }
}
```

- 자바빈의 수정자 메서드의 관례에 따라서 메서드 이름으로부터 프로퍼티 이름을 가져온다.
- `Hello` 클래스는 빈 스캐닝의 대상이 되도록 `@Component` 를 붙여주거나 xml을 통해 빈으로 등록되어야 의존관계가 주입된다.

`필드`

```java
@Component
public class Hello {
    @Resource(name="printer") //필드의 이름과 프로퍼티의 이름이 같다면 name="printer" 부분도 생략 가능
    private Printer printer;
}
```

- 필드 주입은 수정자 메서드가 없어도 리플렉션 api를 사용해서 의존관계를 설정한다.
- 코드는 간결해졌지만 컨테이너 밖에서 수동으로 DI가 불편하기 때문에 단위테스트에서 사용하기엔 바람직하지 못하다.

----

- xml의 자동 와이어링은 각 프로퍼티에 주입할 후보 빈이 없을경우에 무시하고 넘어가지만 `@Resource`방식은 참조할 빈이 반드시 있어야하고 만약 DI 할 빈을 찾을 수 없다면 예외가 발생한다.
- `@Resource`는 기본적으로 이름을 사용해서 빈을 찾지만 이름으로 참조가 실패했다면 타입을 이용해서 다시 한번 빈을 찾기도 한다.
- 타입 주입을 위해 `@Resource`를 사용하는것은 권장되지 않는다.



#### 애너테이션: @Autowired/ @Inject

- `@Autowired`와 `@Inject` 모두 타입에 의한 자동와이어링 방식으로 동작한다.
- `@Autowired`는 스프링 2.5부터 적용된 스프링 전용 애너테이션이고  `@Inject` 는 JavaEE6의 표준 스펙 JSR-330에 정의되어 있어 JavaEE6스펙을 따르는 다른 프레임워크에서도 동일한 의미로 사용되는 DI를 위한 애너테이션이다.
- 스프링을 사용한다면 어떤것을 사용해도 좋지만 일관성을 지키는게 좋다

`수정자 메서드와 필드`

```java

//필드주입
public class Hello {
	@Autowired
    private Printer printer;
}

//수정자 주입
public class Hello {
    private Printer printer;
    
    @Autowired
    public void setPrinter(Printer printer) {
        this.printer = printer;
    }
}
```

- `@Resource`와 비슷하지만 `@Autowired`는 타입을 통해서 찾는다



`생성자`

```java
public class BasSqlService implements SqlService {
    protected SqlReader sqlReader;
    protected SqlRegistry sqlRegistry;
    
    @Autowired
    public BasSqlService(SqlReader sqlReader, SqlRegistry sqlRegistry) {
        this.sqlReader = sqlReader;
        this.sqlRegistry = sqlRegistry;
    }
}
```

- 한개의 생성자에만 `@Autowired` 를 적용할 수 있는 제약이 있다.

`일반 메서드`

- 수정자방식, 생성자방식에 대한 각기 장단점을 보완하기위해 `@Autowired`에는 일반 메서드에도 적용할 수 있다.
- 파라미터를 가진 메서드에 `@Autowired` 를 붙여주면 각 파라미터 타입을 기준으로 자동와이어링을 해서 DI 해줄 수 있다.

```java
public class BasSqlService implements SqlService {
    protected SqlReader sqlReader;
    protected SqlRegistry sqlRegistry;
    
    @Autowired
    public config(SqlReader sqlReader, SqlRegistry sqlRegistry) {
        this.sqlReader = sqlReader;
        this.sqlRegistry = sqlRegistry;
    }
}
```



`컬렉션과 배열`

- 타입에 의한 자동와이어링은 같은 타입이 두 개 이상 있을때 문제가될 수 있다.
-  `@Autowired` 의 대상이 되는 필드나 프로퍼티, 메서드의 파라미터를 컬렉션이나 배열로 선언하면 같은타입의 빈을 모두 주입받을 수 있다.

```java
@Autowired
Collection<Printer> printers;

@Autowired
Printer[] printers;

@Autowired
Map<String, Printer> printerMap; //빈 아이디가 키가 된다.
```

- 컬렉션이나 배열로 `@Autowired`를 받는 방식은 충돌을 피하는 목적으로 사용하면 안되고 의도적으로 여러 개의 빈을 모두 참조하거나 그중에서 선별적으로 필요한 빈을 찾을 때 사용하는 것이 좋다.

`@Qaulifier`

- `@Qaulifier`는 타입 외의 정보를 추가해서 자동와이어링을 세밀하게 제어할 수 있는 보조적인 방법이다.
- `@Autowired` 와 함께 사용해서 같은 타입이 두 개 이상 있을때 사용할 수 있다.

```java
@Autowired
@Qualifier("mainDB")
DataSource dataSource;
```

- `@javax.inject.Inject` ,`@javax.inject.Qualifier` 를 사용하는 방법도 있지만 제한적인 부분이 있기 때문에 특별한 이유가 없다면 스프링에서 제공하는  `@Autowired` 와 `@Qaulifier`  를 사용하는 것이 좋다.



#### 자바코드에 의한 의존관계 설정

`애너테이션에 의한 설정 @Autowired, @Resource`

```java
public class Hello {
  @Autowired Printer printer;
}

...

@Configuration
public class Config {
  @Bean
  public Hello hello() {
    return new Hello();
  }
  
  @Bean
  public Printer printer() {
    return new Printer();
  }
}

```

- 다음과 같이 빈의 오브젝트만 생성해서 등록해주면 의존관계는 애너테이션 의존관계 설정용 후처리기 `AutowiredAnnotationBeanPostProcessor`에 의해 별도로 설정된다.

`@Bean 메서드 호출`

```java
@Configuration
public class Config {
  @Bean
  public Hello hello() {
    Hello hello = new Hello();
    hello.setPrinter(printer());
    return hello;
  }
  
  @Bean
  public Printer printer() {
    return new Printer();
  }
}
```

- `printer()` 메서드를 직접 사용해서 의존관계를 설정해준다.
- `@Configuration` 이 등록된 클래스에서만 사용해야 `printer()` 가 항상 싱글톤 인스턴스를 반환하므로 주의해서 사용해야한다.

`@Bean과 메서드 자동와이어링`

```java
@Configuration
public class Config {
  @Bean
  public Hello hello(Printer printer) {
    Hello hello = new Hello();
    hello.setPrinter(printer);
    return hello;
  }
  
  @Bean
  public Printer printer() {
    return new Printer();
  }
}
```

- `@Bean` 이 붙은 메서드는 기본적으로 `@Autowired`가 붙은 메서드처럼 동작한다. 따라서 `hello()` 메서드가 실행될 때 printer 파라미터에는 `Printer` 타입의 빈이 자동으로 주입돼서 DI할 수 있다.

#### 빈 의존관계 설정 전략

- 빈 등록 방법과 마찬가지로 빈 의존관계 설정도 여러가지이기 때문에 반드시 팀 컨벤션에 따라서 미리 가이드라인을 정해두는게 중요하다!

### 프로퍼티 값 설정 방법

#### 메타정보 종류에 따른 값 설정 방법

`XML: <property>와 전용 태그`

```xml
<bean id="hello" ...>
  <property name="name" value="Everyone"/>
</bean>
```

`애너테이션: @Value`

- 빈 의존관계는 아니지만 어떤 값을 외부에서 받아야할 경우 `@Value` 애너테이션을 사용해서 코드와 분리할 수 있다. 이런 방법을 통해 환경별로 유연하게 동작하게 만들 수 있다.

```java
@Configuration
public class Config {
  
  @Value("${database.username")
  private String name;
  
  @Bean
  public Hello hello() {
    Hello hello = new Hello();
    hello.setName(this.name);
    return hello;
  }

}
```

- 아래와 같이 메서드 파라미터에도 사용이 가능하다.

```java
  @Bean
  public Hello hello(@Value("${database.username") String name) {
    Hello hello = new Hello();
    hello.setName(this.name);
    return hello;
  }
```



#### PropertyEditor와 ConversionService

- xml의 value 애트리뷰트나 `@Value` 앨리먼트는 모두 String 타입으로 취급이된다. 그 외의 타입인 경우라면 타입을 변경하는 과정이 필요하다.
- 이를 위해 스프링은 두가지 종류의 타입 변환 서비스를 제공하는데 디폴트로 사용되는 타입 변환기는 `PropertyEditor`라는 java.beans의 인터페이스를 구현한 것이다.
- 프로퍼티 에디터라고 불리는 오브젝트는 원래 GUI 개발환경에서 자바빈 오브젝트의 프로퍼티 값을 직접 넣어주기 위해 만들어졌는데 스프링은 프로퍼티 에디터의 개념을 xml 또는 `@Value` 애너테이션에 적용했다.
- SpringBoot 2.x 기준으로 `@Value` 애너테이션은 `AutowiredAnnotationBeanPostProcessor` 에 의해 자동으로 주입된다. PropertyEditor는 안쓰는것 같다.. (뇌피셜)

- 스프링이 기본적으로 제공해주는 타입변환목록

`기본 타입`

- boolean, Boolean, byte, Byte, short, Short, int, Integer, long, Long, float, Float, double, Double, BigDecimal, BigInteger, char, Character, String

```java
boolean flag;
public void setFlag(boolean flag) {this.flag = flag;}
```

```xml
<property name="flag" value="true" />
```



`배열`

- 기본 타입의 배열로 선언된 프로퍼티에는 한 번에 배열 값을 주입할 수 있다.

```java
@Value("1,2,3,4") int[] intArr;
```



`기타`

- Charset, Class, Currency 등등 ...

----

- 스프링 3.0 부터는 `PropertyEditor`대신 사용할 `ConversionService`를 지원했다.
- Spring Boot 2.x 기준으로 `ConversionService` 타입의 `DefaultConversionService` 을 사용한다

```java
public class DefaultConversionService extends GenericConversionService {

	@Nullable
	private static volatile DefaultConversionService sharedInstance;


	/**
	 * Create a new {@code DefaultConversionService} with the set of
	 * {@linkplain DefaultConversionService#addDefaultConverters(ConverterRegistry) default converters}.
	 */
	public DefaultConversionService() {
		addDefaultConverters(this);
	}


	/**
	 * Return a shared default {@code ConversionService} instance,
	 * lazily building it once needed.
	 * <p><b>NOTE:</b> We highly recommend constructing individual
	 * {@code ConversionService} instances for customization purposes.
	 * This accessor is only meant as a fallback for code paths which
	 * need simple type coercion but cannot access a longer-lived
	 * {@code ConversionService} instance any other way.
	 * @return the shared {@code ConversionService} instance (never {@code null})
	 * @since 4.3.5
	 */
	public static ConversionService getSharedInstance() {
		DefaultConversionService cs = sharedInstance;
		if (cs == null) {
			synchronized (DefaultConversionService.class) {
				cs = sharedInstance;
				if (cs == null) {
					cs = new DefaultConversionService();
					sharedInstance = cs;
				}
			}
		}
		return cs;
	}

	/**
	 * Add converters appropriate for most environments.
	 * @param converterRegistry the registry of converters to add to
	 * (must also be castable to ConversionService, e.g. being a {@link ConfigurableConversionService})
	 * @throws ClassCastException if the given ConverterRegistry could not be cast to a ConversionService
	 */
	public static void addDefaultConverters(ConverterRegistry converterRegistry) {
		addScalarConverters(converterRegistry);
		addCollectionConverters(converterRegistry);

		converterRegistry.addConverter(new ByteBufferConverter((ConversionService) converterRegistry));
		converterRegistry.addConverter(new StringToTimeZoneConverter());
		converterRegistry.addConverter(new ZoneIdToTimeZoneConverter());
		converterRegistry.addConverter(new ZonedDateTimeToCalendarConverter());

		converterRegistry.addConverter(new ObjectToObjectConverter());
		converterRegistry.addConverter(new IdToEntityConverter((ConversionService) converterRegistry));
		converterRegistry.addConverter(new FallbackObjectToStringConverter());
		converterRegistry.addConverter(new ObjectToOptionalConverter((ConversionService) converterRegistry));
	}

	/**
	 * Add common collection converters.
	 * @param converterRegistry the registry of converters to add to
	 * (must also be castable to ConversionService, e.g. being a {@link ConfigurableConversionService})
	 * @throws ClassCastException if the given ConverterRegistry could not be cast to a ConversionService
	 * @since 4.2.3
	 */
	public static void addCollectionConverters(ConverterRegistry converterRegistry) {
		ConversionService conversionService = (ConversionService) converterRegistry;

		converterRegistry.addConverter(new ArrayToCollectionConverter(conversionService));
		converterRegistry.addConverter(new CollectionToArrayConverter(conversionService));

		converterRegistry.addConverter(new ArrayToArrayConverter(conversionService));
		converterRegistry.addConverter(new CollectionToCollectionConverter(conversionService));
		converterRegistry.addConverter(new MapToMapConverter(conversionService));

		converterRegistry.addConverter(new ArrayToStringConverter(conversionService));
		converterRegistry.addConverter(new StringToArrayConverter(conversionService));

		converterRegistry.addConverter(new ArrayToObjectConverter(conversionService));
		converterRegistry.addConverter(new ObjectToArrayConverter(conversionService));

		converterRegistry.addConverter(new CollectionToStringConverter(conversionService));
		converterRegistry.addConverter(new StringToCollectionConverter(conversionService));

		converterRegistry.addConverter(new CollectionToObjectConverter(conversionService));
		converterRegistry.addConverter(new ObjectToCollectionConverter(conversionService));

		converterRegistry.addConverter(new StreamConverter(conversionService));
	}

	private static void addScalarConverters(ConverterRegistry converterRegistry) {
		converterRegistry.addConverterFactory(new NumberToNumberConverterFactory());

		converterRegistry.addConverterFactory(new StringToNumberConverterFactory());
		converterRegistry.addConverter(Number.class, String.class, new ObjectToStringConverter());

		converterRegistry.addConverter(new StringToCharacterConverter());
		converterRegistry.addConverter(Character.class, String.class, new ObjectToStringConverter());

		converterRegistry.addConverter(new NumberToCharacterConverter());
		converterRegistry.addConverterFactory(new CharacterToNumberFactory());

		converterRegistry.addConverter(new StringToBooleanConverter());
		converterRegistry.addConverter(Boolean.class, String.class, new ObjectToStringConverter());

		converterRegistry.addConverterFactory(new StringToEnumConverterFactory());
		converterRegistry.addConverter(new EnumToStringConverter((ConversionService) converterRegistry));

		converterRegistry.addConverterFactory(new IntegerToEnumConverterFactory());
		converterRegistry.addConverter(new EnumToIntegerConverter((ConversionService) converterRegistry));

		converterRegistry.addConverter(new StringToLocaleConverter());
		converterRegistry.addConverter(Locale.class, String.class, new ObjectToStringConverter());

		converterRegistry.addConverter(new StringToCharsetConverter());
		converterRegistry.addConverter(Charset.class, String.class, new ObjectToStringConverter());

		converterRegistry.addConverter(new StringToCurrencyConverter());
		converterRegistry.addConverter(Currency.class, String.class, new ObjectToStringConverter());

		converterRegistry.addConverter(new StringToPropertiesConverter());
		converterRegistry.addConverter(new PropertiesToStringConverter());

		converterRegistry.addConverter(new StringToUUIDConverter());
		converterRegistry.addConverter(UUID.class, String.class, new ObjectToStringConverter());
	}

}

```

- 직접 `ConversionService`를 구현해서 타입 변환기를 등록할 수 있다.

#### 컬렉션

- yml 파일 기준

`List`

```yaml
fruit:
  list:
    - name : banana
      color : yellow
    - name : apple
      color : red
    - name : water melon
      color : green
```

```java
@Data
@Component
@ConfigurationProperties("fruit")
public class FruitProperty {
    private List<Fruit> list;
}
```



`Map`

```yaml
// application.yml

network:
  environment: 
    TEST1: 
      logicalName: TEST1 
      port: 8888 

    TEST2: 
      logicalName: TEST2 
      port: 8889 


```

```java

@Configuration 
@ConfigurationProperties("network") 
public class NetworkPropertiesManager { 
  Map<String, NetworkConfig> environment = new HashMap<>(); 
  public Map<String, NetworkConfig> getEnvironment() { 
    return environment; 
  } 
  public void setEnvironment(Map<String, NetworkConfig> environment) {
    this.environment = environment; 
  } 
}

```

```java
public class NetworkConfig {
  private String logicalName;
  private int port;
  
  public String getLogicalName() {
    return logicalName; 
  }
  
  ...

}
```



### 컨테이너가 자동 등록하는 빈

#### ApplicationContext, BeanFactory

- 스프링에서는 컨테이너 자신을 빈으로 등록해두고 필요하면 일반 빈에서 DI받아 사용할 수 있다
- 스프링 컨테이너인 애플리케이션 컨텍스트는 `ApplicationContext` 인터페이스를 구현한 것이다.
- SpringBoot 2.x 기준으로 `AnnotationConfigServletWebServerApplicationContext`가 기본 구현체로 등록된다.
- 모든 `ApplicationContext`는 `BeanFactory`의 하위 타입이므로 `BeanFactory`에서 제공하는 메서드를 사용할 수 있다.
- 스프링의 내부 구조를 자세히 알고 특별한 기능을 이용해야 하는 경우엔  `BeanFactory`를 직접 DI 받아야하지만 일반적인 경우엔 `ApplicationContext`만 사용해도 충분하다.

#### ResourceLoader, ApplicationEventPublisher

- 스프링 컨테이너는 `ResourceLoader`이기도 하다. (`AnnotationConfigServletWebServerApplicationContext` 역시 `ResourceLoader`의 하위타입이다.)
- 코드에서 서블릿 컨텍스트의 리소스를 읽어오려면 `ResourceLoader` 타입으로 DI 받아서 활용하면 된다.
- `ApplicationEventPublisher`는 `ApplicationListener` 인터페이스를 구현한 빈에게 이벤트를 발생시킬 수 있는 `publishEvent()` 메서드를 가진 인터페이스이고 `ApplicationContext` 역시 `ApplicationEventPublisher`를 구현하고 있다. 

#### systemProperties, systemEnvironment

- systemProperties 빈은 `System.getProperties()` 메서드가 돌려주는 `Properties` 타입의 오브젝트를 읽기전용으로 접근할 수 있게 만든 빈 오브젝트다.
- systemProperties을 통해 JVM이 생성해주는 시스템 프로퍼티값을 읽을 수 있게 해준다.
- 아래와 같이 직접 `Properties` 타입으로 받아도되고 SpEL을 사용해서 받아도 된다.

```java
@Resource
Properties systemProperties;


...

@Value("#{systemProperties['os.name']}") String osName;

```



## 프로토타입과 스코프

- 기본적으로 스프링의 빈은 싱글톤으로 만들어진다.
- 사용자의 요청이 있을 때마다 매번 오브젝트를 새로 만드는건 비효율적이기 때문이다.
- 때로는 빈을 싱글톤이 아닌 방식으로 사용해야할 때가 있는데 이때 프로토타입을 사용하면 된다.
- 여기서 스코프란 빈 오브젝트가 만들어져 존재할 수 있는 범위를 의미한다.

### 프로토타입 스코프

- 싱글톤 스코프는 컨텍스트당 한 개의 빈 오브젝트만 만들어 여러번 DI하더라도 매번 같은 인스턴스가 주입된다.
- 프로토타입 스코프는 컨테이너에 빈을 요청할 때마다 새로운 오브젝트를 만든다.

#### 프로토타입 빈의 생명주기와 종속성

- IoC 컨테이너의 기본 개념은 애플리케이션을 구성하는 핵심 오브젝트를 컨테이너에서 관리해서 오브젝트의 모든 생명주기를 담당한다.
- 하지만 프로토타입빈은 생성해주고 DI까지는 해주지만 이후에는 컨테이너가 관리하지 않는 특징이 있다.
- 때문에 빈 오브젝트 관리는 DI 받은 오브젝트에 달려있다.

#### 프로토타입 빈의 용도

- 대부분의 애플리케이션 로직은 싱글톤 빈으로 만들어두고 사용자별로 상태를 따로 갖지 않게해도 충분하다. 상태를 저장해야할 경우엔 DTO 등과 같은 오브젝트를 생성하고 전달해서 사용하면된다.
- 프로토타입이 사용이 필요한 경우는 사용자의 요청에 따라 매번 독립적인 오브젝트를 만들어야하는데 매번 새롭게 만들어지는 오브젝트가 컨테이너 내의 빈을 사용해야하는 경우에 사용한다. (DI가 필요한 경우)

```java
@Component
@Scope("prototype")
public class ServiceRequest {

  ...

}
```



#### 프로토타입 빈의 DL 전략

- DL(Dependency Lookup) : 의존대상을 검색을 통해 반환받는 방식
- 프로토타입 빈은 DL 방식을 사용해 매번 컨테이너에 빈을 요청해서 새로운 오브젝트를 받아서 사용해야 한다.
- DL 방식을 사용하기위해 아래와같은 전략이 있다.

`ApplicationContext, BeanFactory`

- 코드내에서 `ApplicationContext`를 사용해서 매번 `getBean()` 메서드를 호출하는 방법
- 이 방법은 코드에 스프링 API가 직접 등장한다는 단점이 있다.

`ObjectFactory, ObjectFactoryCreatingFactoryBean`

- 직접 애플리케이션 컨텍스트를 사용하지 않으려면 중간에 `ApplicationContext` 의 `getBean()`을 호출해주는 팩토리를 두면 된다.
- 직접 구현할 수 있지만 스프링이 제공해주는 `ObjectFactory` 인터페이스를 사용하면 더 편리하게 팩토리를 사용할 수 있다.

```java
ObjectFactory<ServiceRequest> factory = ...;
ServiceRequest request = factory.getObject();
```

- `ObjectFactory`의 구현체로 `ObjectFactoryCreatingFactoryBean` 을 사용하면 된다.  `getObject()` 를 호출할 때마다 제네릭에 선언된 타입을 얻어올 수 있다.
- 중간에 팩토리를 두는 방식이기 때문에 이런 방식은 테스트에도 유용하다.

`ServiceLocatorFactoryBean`

- `ObjectFactoryCreatingFactoryBean` 사용 대신 `ServiceLocatorFactoryBean`을 사용할 수 있다.
- `ServiceLocatorFactoryBean`는 DL방식으로 가져올 빈을 리턴하는 임의의 이름을 가진 메서드가 정의된 인터페이스가 있으면 된다.

```java
public interface ServiceRequestFactory {
  ServiceRequest getServiceFactory();
}
```

- 이렇게 정의한 인터페이스를 이용해 스프링의 `ServiceLocatorFactoryBean`으로 등록해주면 된다.

```java
    
    @Bean
    public ServiceLocatorFactoryBean serviceLocatorFactoryBean() {
        ServiceLocatorFactoryBean serviceLocatorFactoryBean = new ServiceLocatorFactoryBean();
        serviceLocatorFactoryBean.setServiceLocatorInterface(ServiceRequestFactory.class);
        return serviceLocatorFactoryBean;
    }
```

- 아래와 같이 사용할 수 있다.

```java
@Autowired
ServiceRequestFactory serviceRequestFactory;

public void serviceRequestFormSubmit(HttpServletRequest request) {
  ServiceRequest serviceRequest = this.serviceRequestFactory.getServiceFactory();
  ...
}
```

`Provider<T>`

- `javax.inject.Provider`는 `ObjectFactory`와 거의 유사하지만 구현체를 따로 등록하지 않고 사용해도된다. 스프링이 자동으로 구현체를 등록해준다.

```java

@Autowired
Provider<ServiceRequest> serviceRequestProvider;

public void serviceRequestFormSubmit(HttpServletRequest request) {
  ServiceRequest serviceRequest = this.serviceRequestFactory.getServiceFactory();
  ...
}

```



### 스코프

#### 스코프의 종류

- 스프링은 싱글톤, 프로토타입외에도 요청(request), 세션(session), 글로벌 세션(global session), 애플리케이션(application)이라는 네가지 스코프를 기본적으로 제공한다. 이 스코프는 웹 환경에만 의미가 있다.

`요청(request) 스코프`

- 요청 스코프 빈은 하나의 웹 요청 안에서 만들어지고 해당 요청이 끝날 때 제거된다.
- 각 요청별로 독립적인 빈이 만들어지기 때문에 스레드 세이프하다.
- 요청 스코프빈의 주요 용도는 애플리케이션 코드에서 생성한 정보를 프레임워크 레벨의 서비스나 인터셉터 등에 전달하는 것. ex 보안 프레임워크에서 현재 요청에 관련된 권한 정보를 요청 스코프 빈에 저장해뒀다가 필요한 빈에서 참조하는 것
- 과용으로 사용하면 전역변수를 사용하는 것처럼 코드를 이해하기 힘들 수 있다.

`세션(session) 스코프, 글로벌 세션(global session) 스코프`

- HTTP 세션과 같은 존재 범위를 갖는 빈으로 만들어주는 스코프
- 서비스 계층이나 데이터 액세스 계층에서 HTTP 세션에 직접 접근하지 않고 세션 스코프 빈을 사용해서 접근하는 방법으로 사용된다.
- 웹 페이지가 바뀌고 여러 요청을 거치는 동안에도 세션 스코프빈은 계속 유지된다. 사용자별로 만들어지기 때문에 중복의 위험도 없다.
- 글로벌 세션은 포틀릿에만 존재하는 글로벌 세션에 저장되는 빈이다.

`애플리케이션(application) 스코프`

- 애플리케이션 스코프는 서블릿 컨텍스트에 저장되는 빈 오브젝트
- 애플리케이션 스코프는 싱글톤 스코프와 비슷한 존재범위를 갖는다.
- 드물게 애플리케이션 컨텍스트의 존재 범위와 웹 애플리케이션 존재범위가 달라야할 때 사용한다.
- 싱글톤과 유사하기 때문에 읽기 전용으로 만들거나, 상태가 없도록, 스레드세이프하도록 만들어야한다.

#### 스코프 빈의 사용 방법

- 스코프빈도 프로토타입 빈과 마찬가지로 `Provider`, `ObjectFactory` 같은 DL 방식으로 사용해야한다.

```java
@Scope("session")
public class LoginUser {
  String loginId;
  String name;
  Date loginTime;
}
```

```java
public class LoginService {
  @Autowired
  Provider<LoginUser> logiUserProvider;
  
  public void login(Login login) {
    LoginUser loginUser = logiUserProvider.get();
    loginUser.setLoginId(...);
    ...
  }

}
```

- 다른 방법으로 `@Scope` 애너테이션의 proxyMode 앨리먼트를 사용해서 프록시를 이용한 DI가 되도록 지정할 수 있다.
- 스코프 프록시는 각 요청에 연결된 HTTP 세션 정보를 참고해서 사용자마다 다른 `LoginUser` 오브젝트를 사용하게 해준다.
- `LoginService` 입장에서는 모두 같은 오브젝트를 사용하는것처럼 보이지만 실제로는 그 뒤에 사용자별로 만들어진 여러개의 `LoginUser`가 존재하고, 스코프 프록시는 실제 `LoginUser` 오브젝트로 클라이언트의 호출을 위임해주는 역할을 하는 방식이다.

```java
@Scope("session", proxyMode= ScopedProxyMode.TARGET_CLASS)
public class LoginUser {
  String loginId;
  String name;
  Date loginTime;
}
```

```java
public class LoginService {
  @Autowired
  LoginUser loginUser;
  
  public void login(Login login) {
    loginUser.setLoginId(...);
    ...
  }

}
```

- 프록시 방식의 DI를 적용하면 스코프 빈이지만 마치 싱글톤을 사용하는 것처럼 편하게 쓸 수 있지만 주입되는 빈의 스코프를 모르면 코드를 이해하기가 어려울 수 있다.

#### 커스텀 스코프와 상태를 저장하는 빈 사용하기

- 스프링이 기본적으로 제공하는 스코프 외에도 임의의 스코프를 만들어 사용할 수 있다. (참고)

## 기타 빈 설정 메타정보

### 빈 이름

- 일반적으로 클래스이름을 소문자로 바꿔서 빈의 이름으로 사용하는것이 관례이다.
- `@Configuration` 애너테이션이 달린 클래스의 `@Bean` 메서드를 이용해 빈을 정의하는 경우에는 메서드 이름이 그대로 빈의 이름이 된다.

```java
@Configuration
public class Config {
	  @Bean
    public UserDao userDao() {
        ...
    }
}
```

- 아래와 같이 이름을 직접 지정해줄 수 있다

```java
@Component("myUserService")
public class UserService {

}


@Named("myUserService") // JSR-330
@Component
public class UserService {

}


@Configuration
public class Config {
	@Bean(name="myUserDao")
    public UserDao userDao() {
        ...
    }
}
```



### 빈 생명주기 메서드

#### 초기화 메서드

- 초기화 메서드는 빈 오브젝트가 생성되고 DI 작업까지 마친 다음에 실행되는 메서드를 말한다.

`초기화 콜백 인터페이스`

- `InitializingBean`의 `afterPropertiesSet()` 를 구현해서 빈을 작성하는 방법

```java
@Service
public class LifeCycleTest implements InitializingBean {

  @Override
  public void afterPropertiesSet() throws Exception {
    System.out.println("afterPropertiesSet");
  }
}
```



`@PostConstruct`

- 직관적이기 때문에 가장 권장되는 방법. 

```java
@Service
public class LifeCycleTest {

  @PostConstruct
  public void postConstruct() {
    System.out.println("postConstruct");
  }
}
```



`Bean(init-method)`

- `@Bean` 의 initMethod 프로퍼티를 이용하는 방법

```java
@Bean(initMethod = "init")
public LifeCycleTest lifeCycleTest() {
  return new LifeCycleTest();
}

public class LifeCycleTest {
  public void init() {
    System.out.println("init");
  }
}

```



#### 제거 메서드

- 제거 메서드는 컨테이너가 종료될 때 호출돼서 빈이 사용한 리소스를 반환하거나 종료 전에 처리할 작업을 수행한다.

`제거 콜백 인터페이스`

- `DisposableBean` 인터페이스를 구현해서 `destory()`를 구현하는 방법

`@Bean(destroyMethod)`

- `@Bean` 애너테이션의 destroyMethod 프로퍼티를 사용하는 방법

`@PreDestroy`

- 컨테이너가 종료될 때 실행될 메서드에 `@PreDestroy`를 붙여주는 방법

### 팩토리 빈과 팩토리 메서드

- 생성자 대신 오브젝트를 생성해주는 코드의 도움을 받아서 빈 오브젝트를 생성하는 것을 팩토리빈이라 한다.

`FactoryBean 인터페이스`

- new 키워드나 리플렉션 API를 이용해 생성자를 호출하는 방식으로 만들 수 없는 JDK 다이내믹 프록시를 빈으로 등록하기 위해 `FactoryBean` 인터페이스를 구현해서 다이내믹 프록시를 생성하는 `getObject()` 를 구현하는 방법으로 사용할 수 있다.

`스태틱 팩토리 메서드`

- 스태틱 팩토리 메서드는 클래스의 스태틱 메서드를 호출해서 인스턴스를 생성하는 방식

`@Bean 메서드`

- `@Bean` 메서드를 활용해 빈을 등록하는 방법도 일종의 팩토리 형태다. 



## 스프링 3.1의 IoC 컨테이너와 DI

- 3.1이 도입되면서 xml을 이용한 빈 설정보다 자바 코드를 활용한 빈 설정 기능이 대폭 확장되었다.

### 빈의 역할과 구분

- 3.1 기능을 알기 전에 빈의 종류부터 간단하게 알아보기

#### 빈의 종류

`애플리케이션 로직 빈`

- 애플리케이션 로직을 담고 있는 주요 클래스의 오브젝트 빈

`애플리케이션 인프라 빈`

- `PlatformTransactionManager`와 같은 애플리케이션 로직을 담당하진 않지만 기능을 제공해줘서 애플리케이션 로직 빈을 지원하는 빈

`컨테이너 인프라 빈`

- 스프링 컨테이너의 기능을 확장해서 빈의 등록과 생성, 관계설정, 초기화 등의 작업에 직접 참여하는 빈



`BeanDefinition 인터페이스에 정의된 세가지 빈 타입`

```java
/**
 * Role hint indicating that a {@code BeanDefinition} is a major part
 * of the application. Typically corresponds to a user-defined bean.
 */
int ROLE_APPLICATION = 0;

/**
 * Role hint indicating that a {@code BeanDefinition} is a supporting
 * part of some larger configuration, typically an outer
 * {@link org.springframework.beans.factory.parsing.ComponentDefinition}.
 * {@code SUPPORT} beans are considered important enough to be aware
 * of when looking more closely at a particular
 * {@link org.springframework.beans.factory.parsing.ComponentDefinition},
 * but not when looking at the overall configuration of an application.
 */
int ROLE_SUPPORT = 1;

/**
 * Role hint indicating that a {@code BeanDefinition} is providing an
 * entirely background role and has no relevance to the end-user. This hint is
 * used when registering beans that are completely part of the internal workings
 * of a {@link org.springframework.beans.factory.parsing.ComponentDefinition}.
 */
int ROLE_INFRASTRUCTURE = 2;
```

- `ROLE_APPLICATION` 은 애플리케이션 로직 빈, 애플리케이션 인프라 빈을 의미하고 `ROLE_INFRASTRUCTURE` 은 컨테이너 인프라 빈을 의미한다.



#### 컨테이너 인프라 빈과 전용 태그

- 컨테이너 인프라빈은 스프링 컨테이너의 기본 기능을 확장하는 데 사용되고 주로 전용 태그를 통해 간접적으로 등록된다. `<bean>` 태그를 사용해도되지만 구별짓는게 좋다.
- xml을 사용한다면 `<context:component-scan>` 을 통해 `@Autowired`, `@PostConstruct`뿐만아니라 모든 스테레오 타입들을 빈으로 등록해준다.

### 컨테이너 인프라 빈을 위한 자바 코드 메타정보

#### IoC/DI 설정 방법의 발전

`스프링1.x`

- xml을 이용한 빈 등록 방법을 주로 사용했다.
- `<bean>` 태그만 사용할 수 있었기 때문에 위에서 언급한 세가지 타입 빈이 모두 `<bean>` 태그로 등록됐다.
- 애플리케이션의 구조가 커지면서 빈의 개수가 증가할수록 애플리케이션 구성을 파악하기 어려웠다.

`스프링2.0`

- 사용하기 까다로운 컨테이너 인프라 빈을 손쉽게 사용할 수 있도록 의미있는 스키마와 네임스페이스를 가진 전용 태그를 제공했다.
  - `<aop:config>`, `<tx:advice>`

`스프링2.5`

- 빈 스캐너와 스테레오타입 애너테이션을 이용한 빈 자동등록 방식과 애너테이션 기반의 의존관계 설정 방법이 등장했다.
- 애플리케이션 로직 빈은 `@Component` 를 사용하는 방식으로 빈으로 등록됐지만 컨테이너 인프라빈, 애플리케이션 인프라 빈은 아직 xml의 전용태그를 사용해서 등록됐다.

`스프링 3.0`

- 스프링 3.0부터 자바 코드를 이용해 빈 설정정보 또는 빈 설정 코드를 만드는 일이 가능해졌다.
- 코드를 통해 애플리케이션 인프라 빈도 코드로 등록할 수 있게 됐다.
- 하지만 여전히 컨테이너 인프라빈은 xml 전용 태그를 통해 등록하는 방식을 사용했는데 빈의 개수가 많고 어떤 빈 클래스를 설정해야하는지 정확히 알고 있어야 했기 때문이다.

`스프링 3.1`

- 3.1에서는 컨테이너 인프라 빈도 자바 코드로 등록할 수 있게 됐다. 



#### 자바 코드를 이용한 컨테이너 인프라 빈 등록

`@ComponentScan`

- `@Configuration`이 붙은 클래스에 `@ComponentScan`을 추가하면 xml에서  `<context:component-scan>` 을 사용하는 것과 같은 스테레오타입 애너테이션이 붙은 빈들을 자동으로 등록해준다

```java
@Configuration
@ComponentScan("com.example")
public class AppConfig {

}
```

-  `@ComponentScan`의 basePackageClasses 프로퍼티를 사용해서 특정 마커클래스나 인터페이스의 패키지가 빈 스캐닝의 기준 패키지가 되도록 지정할 수 있다.

```java
public interface ServiceMarker {

}


@Configuration
@ComponentScan(basePackageClasses= ServiceMarker.class)
public class AppConfig {

}

```

- 스캔을 제외하고싶다면 excludes 앨리먼트를 사용하면 된다.
- 더욱더 자세한 내용은 [docs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScan.html) 를 참고하자

`@Import`

- `@Import`는 다른 `@Configuration` 클래스를 빈 메타정보에 추가할 때 사용한다.

```java
@Configuration
@Import(DataConfig.class)
public class AppConfig {

}

@Configuration // 해당 클래스가 ComponentScan 대상이 아니라면..
public class DataConfig {

}
```



`@ImportResource`

- 3.1에서 어느정도 자바 코드 설정 방식으로 많이 바뀌었지만 스프링 시큐리티 등과 같은 서브 프로젝트, 외부 라이브러리에서 사용하는 전용 태그는 아직 xml 방식을 사용했다.
- xml 파일의 빈 설정을 가져오기 위해서 `@ImportResource`를 사용하면 된다.

```java
@Configuration
@ImportResource("/myproject/config/security.xml")
public class AppConfig {

}
```

`@EnableTransactionManagement`

- `<tx:annotation-driven/>` 태그와 동일한 기능을 수행한다.
- `@Transactional` 로 트랜잭션 속성을 지정할 수 있게 해주는 AOP 관련 빈을 등록해준다.
- 이것외에도 다양한 @EnableX 애너테이션이 존재한다.
  - `@EnableAspectJAutoProxy`
  - `@EnableAsync`
  - `@EnableCaching`
  - ...


### 런타임 환경 추상화와 프로파일

- 스프링의 빈 설정 메타정보는 대부분 바뀌지 않지만 외부 리소스나 서버 환경과 관련이 깊은 부분은 애플리케이션이 동작하는 환경에 따라서 바뀌는 경우가 있다.
- 스프링 3.0까지는 프로파일이 지원되지 않아서 환경마다 빈 메타정보를 변경하려면 새로운 xml을 만들어줘야하는 문제점이 있었는데 3.1부터 런타임 환경 추상화가 도입됐다.

#### 런타임 환경과 프로파일

- 런타임 환경은 컨텍스트 내부에 `Environment` 인터페이스를 구현한 런타임 환경 오브젝트가 만들어져서 빈을 생성하거나 의존관계를 주입할 때 사용된다. 기본 구현체로는 `ApplicationServletEnvironment` 이 사용된다.
- 런타임 환경은 프로파일과 프로퍼티 소스로 구성되는데 환경에 따라 프로파일과 프로퍼티 소스가 다르게 설정된 `Environment` 오브젝트가 사용되는 식이다.
- 프로파일을 정의하고 어떤 환경을 사용할지 결정하면 그에 맞게 알맞는 리소스가 주입된다.
- 프로파일을 이용하면 하나의 빈 설정파일로 여러 개의 다른 환경에서 각각 다른 빈 설정이 적용되도록 만들 수 있다.

#### 활성 프로파일 지정 방법

- 프로파일 관련 정보는 `Environment` 타입의 오브젝트가 갖고 있다.
- `Environment`엔 `setActiveProfiles` 메서드가 있는데 여기에 사용할 프로파일 이름을 넣어주면 된다.
- 프로파일은 xml이 로딩되거나 `@Configuration` 클래스가 적용되는 `refresh()` 메서드가 컨텍스트에서 실행되기 전에 지정해줘야 한다.
- 환경변수를 설정해서 아래와 같이 프로파일을 설정할 수 있다.

```
-Dspring.profiles.active=dev
```



#### 프로파일 활용 전략

- 프로파일은 여러개를 조합할 수 있기 때문에 n 개의 프로파일을 사용할 수 있다.
- 애너테이션으로 사용하려면 `@Profile`과 `@ActiveProfile`을 사용할 수 있다. 

```java
@Configuration
@Profile("dev")
public class AppConfig {

}
```



### 프로퍼티 소스

#### 프로퍼티

- 자바에서 말하는 프로퍼티는 기본적으로 키와 그에 대응되는 값의 쌍을 말한다.

```
db.username=spring
db.password=book
```

- 이렇게 정의된 값은 아래와 같은 방식으로 `Environment`에 주입되어 사용할 수 있다.

```java
@Configuration
@PropertySource("classpath:static/properties/common.properties")
public class AppConfig{

}
```

- Spring Boot에서는 `@ConfigurationProperties` 을 사용해서 프로퍼티 이름과 필드 이름을 같은 방식으로 조합해서 값 오브젝트로 사용하는 방법을 많이 사용한다.

```java
@Configuration
@ConfigurationProperties(prefix = "mail")
public class ConfigProperties {
    
    private String hostName;
    private int port;
    private String from;

    // standard getters and setters
}
```

```
mail.hostname=host@mail.com
mail.port=9000
mail.from=mailer@mail.com
```





#### 스프링에서 사용되는 프로퍼티 종류

`환경변수`

- OS 레벨에 적용되는 매우 넓은 범위의 프로퍼티다.
- 스프링 애플리케이션이 구동되는 OS의 환경변수도 키와 값으로 표현되는 대표적인 프로퍼티다.
- 스프링에서는 환경변수 프로퍼티를 담은 맵을 systemEnviroment 이름의 빈으로 가져올 수 있다.

`시스템 프로퍼티`

- 시스템 프로퍼티는 JVM 레벨에 정의된 프로퍼티를 말한다. (java.home, java.version ...)
- 시스템 프로퍼티에는 -D 옵션으로 지정한 커맨드라인 옵션도 포함된다.
- 스프링에서는 systemProperties 빈을 통해 접근할 수 있다.

#### 프로파일의 통합과 추상화

#### 프로퍼티 소스의 사용

#### @PropertySource와 프로퍼티 파일

#### 웹 환경에서 사용되는 프로퍼티 소스와 프로퍼티 소스 초기화 오브젝트



## 정리

- 스프링 애플리케이션은 POJO 클래스와 빈 설정 메타정보로 구성된다.
- 빈 설정 메타정보는 특정 포맷의 파일이나 리소스에 종속되지 않는다. 필요하다면 새로운 설정정보 작성 방법을 얼마든지 만들어 사용할 수 있다.
- 스프링의 빈 등록 방법은 크게 xml과 빈 자동인식, 자바코드 세 가지로 구분할 수 있다.
- 스프링의 빈 의존관계 설정 방법은 xml과 애너테이션, 자바코드로 구분할 수 있다.
- 프로퍼티 값은 빈에 주입되는 빈 오브젝트가 아닌 정보다
- 프로퍼티 값 중에서 환경에 따라 자주 바뀌는 것은 프로퍼티 파일과 같은 별도의 리소스형태로 분리해놓는것이 좋다.
- 빈의 존재 범위인 스코프는 싱글톤과 프로토타입 그리고 기타 스코프로 구분할 수 있다.
- 프로토타입과 싱글톤이 아닌 스코프 빈은 DL 방식을 이용하거나, 스코프 프록시 빈을 DI 받는 방법을 사용해야 한다.
- 스프링 3.1은 애너테이션과 자바 코드를 이용한 빈 메타정보 작성 기능을 반저시켜서 자바 코드만으로도 스프링 애플리케이션의 모든 빈 설정이 가능하게 해준다.
- 스프링 3.1의 프로파일과 프로퍼티 소스로 이뤄진 런타입 환경 추상화와 기능을 이용하면 환경에 따라 달라지는 빈 구성과 속성 지정 문제를 손쉽게 다룰 수 있다.



# #2 데이터 액세스 기술

# #3 스프링 웹 기술과 스프링 MVC

# #4 스프링 @MVC



# #5 AOP와 LTW

- 스프링은 AspectJ라는 뛰어난 AOP 프레임워크로부터 포인트컷 표현식과 함께 애너테이션을 이용해 AOP 모듈을 개발하는 방법을 도입했다.

## 애스펙트 AOP

### 프록시 기반 AOP

- AOP는 모듈화된 부가기능과 적용 대상의 조합을 통해 여러 오브젝트에 산재해서 나타나는 공통적인 기능을 손쉽게 개발하고 관리할 수 있는 기술이다.
- 스프링은 자바 JDK 에서 지원하는 다이내믹 프록시 기술을 이용해 복잡한 빌드 과정이나 바이트코드 조작 기술 없이도 유용한 AOP를 적용할 수 있는 프록시 기반 AOP 개발 기능을 제공한다.
- 프록시 방식의 AOP는 객체지향 디자인 패턴의 데코레이터 패턴 또는 프록시 패턴을 응용해서 기존 코드에 영향을 주지 않은 채로 부가기능을 타깃 오브젝트에 제공할 수 있는 객체지향 프로그래밍 모델에서 출발한다.
- 포인트컷이라는 적용 대상 선택 기법과 자동 프록시 생성이라는 적용 기법까지 적용하면 AOP라고 부를 수 있는 효과적인 부가기능 모듈화가 가능해진다.



#### 프록시 기반 AOP 개발 스타일의 종류와 특징

- 스프링에서 AOP를 구현하는 방법
  - 스프링 AOP의 어드바이저 인터페이스를 구현하는 방법
  - AspectJ의 애스펙트라는 개념을 사용해서 애너테이션을 사용하는 방법

#### 자동 프록시 생성기와 프록시 빈

- 스프링 AOP를 사용한다면 어떤 개발 방식을 적용하든 모두 프록시 방식의 AOP가 적용된다.
- 스프링의 프록시 개념은 데코레이터 패턴에서 나온것이고, 동작원리는 JDK 다이내믹 프록시와 DI를 이용한다.
- `Client` -> `Proxy` -> `Target` 순서로 의존관계를 맺게 하면 `Proxy`가 `Client`와 `Target` 호출 과정에 끼어들어서 부가기능을 제공할 수 있게 된다.
- `Proxy`도 빈이 되고 `Target`도 빈이되면 같은 타입이라는 가정 하에 `@Autowired` 를 사용할 수 없게 되지만(컨테이너에 같은 타입이 두 개 등록되기 때문에) 스프링은 자동 프록시 생성기를 이용해서 컨테이너 초기화중에 만들어진 빈을 바꿔치기해서 프록시빈을 자동으로 등록해준다.
- 빈을 선택하는 로직은 포인트컷을 이용하면 되고 xml이나 애너테이션을 통해 이미 정의된 빈의 의존관계를 바꿔치기 하는 것은 빈 후처리기를 사용한다.
- 자동 프록시 생성기가 만들어주는 프록시는 새로운 빈으로 추가되는 것이 아니라 AOP 타깃 빈을 대체한다. 

![8](./images/toby-spring-vol2/8.png)

- 등록된 빈의 관점으로 볼때 자동 프록시 생성기 방식에서는 `Target` 오브젝트가 빈으로 직접 노출되지 않는다.
- AOP 자동 프록시 생성기방식의 특징들

`AOP 적용은 @Autowired의 타입에 의한 의존관계 설정에 문제를 일으키지 않는다.`

- 직접 타겟 오브젝트의 인터페이스를 구현한 프록시를 적용한 경우에는 클라이언트에서 `@Autowired`를 사용할 수 없지만 자동 프록시 생성 AOP 방식에는 `@Autowired`를 사용할 수 있다.

`AOP 적용은 다른 빈들이 Target 오브젝트에 직접 의존하지 못하게 한다.`

- 타겟 오브젝트가 빈으로 등록되지 않기 때문에 클라이언트에서 아래와 같이 타겟 오브젝트에 직접적인 접근이 불가능해진다. (인터페이스 방식으로 자동프록시를 생성하는 경우)

```java
public class Client {
	@Autowired Target target;
}
```

- Target을 직접 참조한다면 아래와 같은 에러메시지를 확인할 수 있다.

```
The bean 'fooImpl' could not be injected because it is a JDK dynamic proxy

The bean is of type 'com.sun.proxy.$Proxy76' and implements:
	com.example.springmvcexam.service.Foo
	org.springframework.aop.SpringProxy
	org.springframework.aop.framework.Advised
	org.springframework.core.DecoratingProxy

Expected a bean of type 'com.example.springmvcexam.service.FooImpl' which implements:
	com.example.springmvcexam.service.Foo


Action:

Consider injecting the bean as one of its interfaces or forcing the use of CGLib-based proxies by setting proxyTargetClass=true on @EnableAsync and/or @EnableCaching.

Disconnected from the target VM, address: '127.0.0.1:57768', transport: 'socket'

Process finished with exit code 1
```

- 위와 같은 문제가 발생할 수 있기 때문에 가능하다면 인터페이스를 사용해서 DI 관계를 유연하게 설정해줘야한다.



#### 프록시의 종류

- 스프링에서는 클래스를 직접 참조하면서 강한 의존관계를 맺고 있더라도 cglib 라이브러리를 통해서 프록시를 적용할 수 있다.
- 타깃 클래스를 상속한 서브클래스를 만들어서 이를 프록시를 활용하는 방식이다.
- 이 방식은 final 클래스와 final 메서드는 상속이 불가능하다는 제약과 같은 클래스 타입의 빈이 두 개 만들어지기 때문에 생성자가 두번 호출된다. 추가로 cglib이라는 바이트코드 생성 라이브러리가 필요하다. 
- Spring 3.2 에서 cglib은 스프링 프로젝트에 흡수되었다. 따라서 별도의 라이브러리 설정은 필요하지 않다. [https://github.com/spring-projects/spring-framework/commit/92500ab9023ae2afd096be9c014423fcd4180c55#diff-dd90aa36a7221a652e04631ac19dcb6693a9c841e43a90d36cc3caa51121630e](https://github.com/spring-projects/spring-framework/commit/92500ab9023ae2afd096be9c014423fcd4180c55#diff-dd90aa36a7221a652e04631ac19dcb6693a9c841e43a90d36cc3caa51121630e)
- 스프링은 타깃 오브젝트에 인터페이스가 있다면 그 인터페이스를 구현한 JDK 다이내믹 프록시를 만들어주지만, 인터페이스가 없다면 cglib을 이용한 클래스 프록시를 만든다.
- 설정을 통해 강제로 프록시 클래스를 만들게 할 수도 있다.

```java
@EnableAspectJAutoProxy(proxyTargetClass = true) // proxyTargetClass 를 true로 설정
```

- 위와 같이 적용한다면 인터페이스 존재와 상관없이 클래스 프록시가 만들어진다.
- Spring Boot 2.x 기준으로 proxyTargetClass 설정은 기본값이 true로 되어있다.

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true)
public class AopAutoConfiguration {

	@Configuration(proxyBeanMethods = false)
	@ConditionalOnClass(Advice.class)
	static class AspectJAutoProxyingConfiguration {

		@Configuration(proxyBeanMethods = false)
		@EnableAspectJAutoProxy(proxyTargetClass = false)
		@ConditionalOnProperty(prefix = "spring.aop", name = "proxy-target-class", havingValue = "false")
		static class JdkDynamicAutoProxyConfiguration {

		}

		@Configuration(proxyBeanMethods = false)
		@EnableAspectJAutoProxy(proxyTargetClass = true)
		@ConditionalOnProperty(prefix = "spring.aop", name = "proxy-target-class", havingValue = "true",
				matchIfMissing = true)
		static class CglibAutoProxyConfiguration {

		}

	}
  
  ...
    
}
```

- `@ConditionalOnProperty` 을 확인해보면 `spring.aop.proxy-target-class` 값이 true인 경우 `CglibAutoProxyConfiguration` 을 빈으로 등록한다. [https://github.com/spring-projects/spring-boot/blob/main/spring-boot-project/spring-boot-autoconfigure/src/main/resources/META-INF/additional-spring-configuration-metadata.json](https://github.com/spring-projects/spring-boot/blob/main/spring-boot-project/spring-boot-autoconfigure/src/main/resources/META-INF/additional-spring-configuration-metadata.json)
-  `spring.aop.proxy-target-class`을 재정의해서 설정을 변경할 수 있다.

### @AspectJ AOP

- 애스펙트는 애플리케이션의 도메인 로직을 담은 핵심 기능은 아니지만 많은 오브젝트에 걸쳐서 필요한 부가기능을 추상화해놓은 것이다.
- 스프링의 어드바이저는 하나의 포인트컷과 하나의 어드바이스로 정의된 가장 단순한 형태의 애스펙트다.
- 독립적인 빈의 조합으로 만들어지는 어드바이저와 달리 애스펙트는 다양한 조합을 갖는 포인트컷과 어드바이스를 하나의 모듈로 정의할 수 있다.

#### @AspectJ를 이용하기 위한 준비사항

- xml방식인 경우 아래와 같이 정의해두면 클래스레벨에 `@Aspect` 가 붙은 것을 모두 애스펙트로 자동 등록해준다.

```xml
<aop:aspectj-autoproxy/>
```

- 애너테이션방식인 경우 위에서 확인했던 `@EnableAspectJAutoProxy` 를 사용하면 된다.

#### @Aspect 클래스와 구성요소

- 애스펙트는 자바 클래스에 `@Aspect`라는 애너테이션을 붙여서 만든다.

```java
@Aspejct
public class SimpleMonitoringAspect {
	...
}
```

- 이 클래스를 애스펙트로 사용하려면 빈으로 등록해야한다.
- `@Aspect` 클래스에는 포인트컷과 어드바이스를 정의할 수 있다. 두가지 모두 애너테이션이 달린 메서드를 사용해 정의한다.

`포인트컷: @Pointcut`

- 메서드에 `@Pointcut`을 사용해 포인트컷 표현식을 넣어서 정의한다.
- 하나의 `@Aspect` 클래스 안에 여러 개의 포인트컷을 선언할 수도 있다.

```java
@Pointcut("execution(* hello(..))")
private void all() { }
```



`어드바이스: @Before, @AfterReturning, @AfterThrowing, @After, @Around`

- 어드바이스도 포인트컷과 마찬가지로 애너테이션이 붙은 메서드를 이용해 정의한다.
- @AspectJ에서는 다섯가지 종류의 어드바이스를 사용할 수 있다.

- 어드바이스의 애너테이션에는 이에 적용할 포인트컷을 명시해야한다.
- 애스펙트에서는 어드바이저라는 이름을 따로 지정하지 않고 어드바이스에 포인트컷을 직접 지정해줄 수 있다.

```java
@Around("all()")
public Object around(ProceedingJoinPoint pjp) throws Throwable {
	...
	Object ret = pjp.proceed();
  ...
	return ret;
}
```

- 위 방법 대신 어드바이스 애너테이션에 직접 포인트컷 표현식을 넣어서 적용하는 방법도 있다. 이런 경우엔 포인트컷 이름이 없기 때문에 다른 어드바이스에서 참조될 수 없다.

```java
@Around("execution(* hello(..))")
public Object around(ProceedingJoinPoint pjp) throws Throwable {
	...
	Object ret = pjp.proceed();
  ...
	return ret;
}
```

- `@Aspect` 클래스에는 일반 메서드, 일반 필드도 정의할 수 있고 상속과 인터페이스 구현 또는 DI를 통한 다른 빈의 참조도 모두 가능하다.







#### 포인트컷 메서드와 애너테이션

- 포인트컷 메서드에는 구현 코드는 필요 없고 항상 void 형이어야 한다.

```java
@Pointcut("execution(* hello(..))")
private void all() { }
```

- 포인트컷은 적용할 조인 포인트를 선별하는 것이다. 여기서 조인 포인트란 어드바이스로 정의된 부가기능을 적용할 수 있는 위치다. 스프링에서는 프록시 방식의 AOP를 사용하기 떄문에 조인 포인트는 메서드 실행 지점뿐이다. 조인포인트 = 메서드
- 포인트컷 표현식은 여러 종류의 포인트컷 지시자를 이용해 정의할 수 있다.

`execution()`

- 가장 대표적인 포인트컷 지시자.
- 접근제한자, 리턴 타입, 타입, 메서드, 파라미터 타입, 예외 타입 조건을 조합해서 메서드 단위까지 선택가능한 가장 정교한 포인트컷을 만들 수 있다.

`within()`

- `within()`은 타입 패턴만을 이용해 조인 포인트 메서드를 선택한다. 
- 타깃 클래스의 타입에만 적용되며 조인 포인트는 타깃 클래스 안에서 선언된 것만 선정된다.
- 선택된 타입의 모든 메서드가 AOP의 대상이 된다.
- 패턴을 이용할 수 있기 때문에 패키지나 모듈이 잘 설계 되어 있다면 계층별 포인트컷을 만들어 사용할 수 있다.

```java
@Pointcut(within("com.example.dao..*"))
private void daoLayer() {}

@Pointcut(within("com.example.service..*"))
private void serviceLayer() {}

@Pointcut(within("com.example.web..*"))
private void webLayer() {}
```

`this, target`

- `this`와 `target`은 하나의 타입을 지정하는 방식이다.
- `this`는 프록시 오브젝트의 타입을 비교해서 선정하고, `target`은 타깃 오브젝트의 타입을 비교한다.

```java
public class HelloImpl implements Hello {
...
}

// 프록시 빈 오브젝트의 타입을 지정하는 것이므로 HelloImpl은 포인트컷의 대상이됨
this("com.example.Hello")
  
// 프록시 빈 오브젝트는 타깃 오브젝트(HelloImpl) 의 타입이 아니므로 포인트컷 대상이 아님
this("com.example.HelloImpl")

```



`args`

- `args` 지시자는 메서드의 파라미터 타입만을 이용해 포인트컷을 설정할 때 사용한다.
- `args` 는 주로 다른 지시자와 함께 사용해서 파라미터 타입 조건을 추가하기도 한다.

```java
//파라미터가 없는 메서드를 선별
args()

//파라미터가 하나이고 String인 메서드를 선별
args(String)

//첫번째 파라미터가 String이고 파라미터의 개수는 하나 이상인 메서드를 선별
args(String,...)

//첫번째 파라미터가 String이고 파라미터가 n개인 메서드를 선별
args(String, *)
```

`@target, @within`

- `@target` 지시자는 타깃 오브젝트에 특정 애너테이션이 부여된것을 선정한다.
- `@within` 도 타깃 오브젝트의 클래스에 특정 애너테이션이 부여된 것을 찾지만 선택될 조인 포인트인 메서드는 타깃 클래스에서 선언되어 있어야 한다. 슈퍼클래스의 메서드는 해당되지 않는다.

```java
@target(org.springframework.stereotype.Controller)
```

`@args`

- `args` 와 비슷하지만 파라미터 오브젝트에 지정된 애너테이션이 부여되어 있는 경우 선정 대상이 된다

```java
@args(org.springframework.web.bind.annotation.RequestParam)
```

`@annotation`

- 조인 포인트 메서드에 특정 애너테이션이 있는 것만 선정하는 지시자

```java
@annotation(org.springframework.transaction.annotation.Transactional)
```

`bean`

- 빈 이름 또는 아이디를 이용해서 선정하는 지시자
- 와일드카드를 사용할 수 있다.

```java
bean(*Service) //Service로 끝나는 모든 빈 이름을 가진 빈 오브젝트를 선정
```





----



- 조건이 복잡한 포인트컷을 만들 때는 하나의 표현식에 모든 내용을 담기보다는 의미있는 작은 단위로 분리해서 저의한 후에 이를 조합하는 것이 좋다.
- 포인트컷 표현식은 논리연산 기호를 이용해서 여러 개의 포인트컷 지시자 또는 포인트컷 자체를 조합할 수 있다. 

`&&`

- 두 개의 포인트컷 지시자를 AND 조건으로 결합한다.

```java
within(com.example.service..*) && args(java.io.Serializable)
```

- 아래와 같이 포인트컷 자체를 조합하는 조건에 추가할 수 있다.

```java
serviceLayer() && args(java.io.Serializable)
```



`||, !`

-  `||` 는 OR조건이고 `!` 는 NOT 조건이다.

```java
daoLater() || within(com.example.dao..*)
  
daoLater() && !within(com.example.exclude.dao..*)
```





#### 어드바이스 메서드와 애너테이션

- 어드바이스는 다섯 종류가있다. 메서드 실행 과정의 일부분에만 적용할 수 있도록 만들어졌다.

![9](./images/toby-spring-vol2/9.png)

- 어드바이스가 프록시 안에서 어느 단계에 적용되느냐에 따라 어드바이스의 종류가 달라진다.
- 어드바이스를 정의하는 메서드는 어드바이스 애너테이션과 어드바이스에 적용되는 포인트컷 그리고 메서드 코드로 구성된다.

```java
@Before("daoLayer()")
public void logDaoAccess(JoinPoint jp) {
	...
}
```



`@Around`

- `@Around`는 프록시를 통해서 타깃 오브젝트의 메서드가 호출되는 전 과정을 모두 담을 수 있는 어드바이스다.
- 인터페이스 구현 방식의 AOP에서 어드바이스를 만들 때 사용했던 `MethodInterceptor` 인터페이스와 유사하게 동작한다.
- 파라미터는 `ProceedingJoinPoint`를 받는데 이 오브젝트를 이용해 타깃 오브젝트의 메서드를 실행하고 그 결과를 받을 수 있다.
- 어드바이스중 가장 강력한 어드바이스이다. 타깃 오브젝트의 메서드를 여러번 호출하거나, 호출 파라미터를 바꿔치기하는 등등의 다양한 동작을 수행할 수 있다.

```java
@Around("myPointcut()")
public Object doNothing(ProceedingJoinPoint pjp) throws Throwable {
	Object ret = pjp.proceed();
  return ret;
}
```



`@Before`

- `@Before`는 타깃 오브젝트의 메서드가 실행되기 전에 사용되는 어드바이스다.
- `@Before`를 사용해서 타깃 오브젝트 메서드를 호출하는 방식을 제어하거나 파라미터를 변경할 수 없다.
- `@Before`는 `JoinPoint` 타입의 파라미터를 사용할 수 있다. 이 `JointPoint`는 `ProceedingJoinPoint` 의 상위 타입인데 `proceed()` 메서드가 없다.

```java
@Before("myPointcut()")
public void logJoinPoint(JoinPoint) {
  System.out.println(jp.getSignature().getDeclaringTypeName());
  System.out.println(jp.getSignature().getName());
}

```



`@AfterReturning`

- `@AfterReturning`은 타깃 오브젝트의 메서드가 실행을 마친 뒤에 실행되는 어드바이스이다.
- 예외가 발생하지 않고 정상적으로 종료한 경우에만 해당된다.
- 타깃 오브젝트의 메서드가 정상 종료된 후에 호출되기 때문에 메서드의 리턴 값을 참조할 수 있다.

```java
@AfterReturning(pointcut="myPointcut()", returning="ret")
public void logReturnValue(Object ret) {
  ...
}
```



`@AfterThrowing`

- `@AfterThrowing`은 타깃 오브젝트의 메서드를 호출했을 때 예외가 발생하면 실행되는 어드바이스다.
- throwing 앨리먼트를 이용해서 예외를 전달받을 메서드 파라미터 이름을 지정할 수 있다.

```java
@AfterThrowing(pointcut="myPointcut()", throwing="ex")
public void logDAException(DataAccessException ex) {
  ...
}
```



`@After`

- `@After`는 메서드 실행이 정상 종료됐을때, 예외가 발생했을 때 모두 실행되는 어드바이스다.
- finally 키워드와 비슷하지만 리턴값이나 예외를 직접 전달받을 수 없다



#### 파라미터 선언과 바인딩

- 어드바이스 메서드에는 `JoinPoint`, `ProceedingJoinPoint`를 기본적으로 사용할 수 있다. 또 종류에 따라 returning이나 throwing을 이용해서 선언된 리턴값 또는 예외 파라미터를 이용할 수 있다.
- 포인트컷 표현식의 타입 정보를 파라미터와 연결하는 방법도 있다.

```java
@Before("@target(com.example.foo.bar.BatchJob)")
public void beforeBatch() {...}


@Before("@target(batchJob)")
public void beforeBatch(BatchJob batchJob) {...}

```



#### @AspectJ를 이용한 AOP의 학습 방법과 적용 전략

- 복잡한 문법, 포인트컷 표현식 등등으로 사용법이 복잡한편인데 AOP를 적용할때는 항상 단순하고 명료하게 접근해야 한다.
- 러닝커브가 있기때문에 팀내부에서 단계를 거쳐 점진적으로 도입하는 것도 중요하다.
- 객체지향적인 방법으로 해결할 수 있는 것을 불필요하게 AOP를 사용하지 말아야한다.
- AOP는 기본적으로 애플리케이션의 핵심 로직이 아니라 부가적인 기능을 적용할 떄 사용하는 것이다.
- AOP도 POJO기반이기 때문에 충분한 테스트를 작성해야한다.
- 이것 외에도 다양한 AOP 고급 적용기술이 있기 때문에 필요한 경우 레퍼런스를 확인하면된다.

## AspectJ와 @Configurable

### AspectJ AOP

- AspectJ는 타깃 오브젝트 자체의 코드를 바꿈으로써 애스펙트를 적용하기때문에 프록시를 사용하지 않는다.
- AspectJ가 바이트코드 조작을 필요로 하는 이유는 프록시 방식으로는 어드바이스를 적용할 수 없는 조인 포인트와 포인트컷 지시자를 지원하기 위해서이다.
- AspectJ의 조인 포인트는 메서드 실행 지점 외에도 필드 읽기와 쓰기, 스태틱 초기화, 인스턴스 생성, 인스턴스 초기화 등도 지원한다.
- 자바의 프록시 기법을 이용해서는 특정 오브젝트가 생성된 시점이나 특정 필드를 읽을 지점에 어드바이스를 추가할 방법이 없다.
- 일반적인 경우에는 메서드 실행 지점을 조인포인트로 사용해서 대부분의 요구사항을 스프링의 프록시 방식 AOP로 해결할 수 있지만 위 문제를 해결하기 위해 AspectJ AOP를 적용할 수 있다.
- AspectJ AOP는 방대한 주제이므로 자세한 설명은 생략한다...

### 빈이 아닌 오브젝트에 DI 적용하기

- 스프링 빈이 아니더라도 수정자 메서드를 가진 임의의 오브젝트에는 스프링의 API의 도움을 받아서 간단히 DI를 할 수 있지만 문제는 오브젝트가 생성되는 시점은 스프링 AOP에서 지원하는 조인포인트가 아니다.
- 스프링이 직접 제공하는 AspectJ AOP 적용 기능을 활용해서 위 문제를 해결할 수 있다.

#### DI 애스펙트

- 스프링은 AspectJ 기술로 만들어진 `DependencyInjectionAspect` 라는 애스펙트를 제공한다.
- 실제 내부는 AspectJ 언어로 만들어져 있기 때문에 복잡하지만 내부에는 아래와 같은 코드가 있다.

```
public pointcut beanConstruction(Object bean) :
	initilization(ConfigurableObject+.new(..)) && this(bean);

declare parents: @Configurable * impletments ConfigurableObject;

after(Object bean) returning :
	beanConstruction(bean) && postConstructionCondition() && inConfigurableBean() {
		configureBean(bean);
	}
```

- 결론적으로 @Configurable이 붙은 오브젝트가 생성되는 조인 포인트에 이 어드바이스가 실행되도록 되어 있다.
- `DependencyInjectionAspect` 애스펙트가 적용되면 `@Configurable`이 붙은 도메인 오브젝트가 어디서든 생성될 때마다 이 어드바이스가 적용되어 자동DI 작업이 일어난다. 또 빈의 초기화 메서드가 정의되어 있다면 실행된다. DI 방식은 도메인 오브젝트를 빈으로 선언해서 명시적으로 지정할 수도 있고 자동와이어링 방식도 이용할 수 있다.

#### @Configurable

- 다음과 같이 User 도메인 안에 비지니스 로직을 넣기 위해 스프링 빈을 DI받아야한다면 아래와 같이 작성할 수 있다.

```java
public class User {

    private MyService myService;

    public void doSomething() {
        myService.something();
    }
}
```

- User는 스프링의 빈이 아니기 때문에 new 키워드를 사용해서 직접생성하기 때문에 별도의 설정이 없으면 myService는 null을 리턴하지만 DI 애스펙트를 적용하면 필요한 빈이 주입된다.

```java
@Configurable
public class User {
	...
}
```

- xml 방식, setter 방식이 있지만 가장 간단한 애너테이션 의존 방식을 사용하면 필드주입으로 간단하게 DI를 적용할 수 있다.

```java
@Configurable
public class User {

    private MyService myService;

    public void doSomething() {
        myService.something();
    }
}
```



#### 로드타임 위버와 자바 에이전트

- 추가적으로 DI 애스펙트를 적용하기 위한 작업이 필요하다. 먼저 AspectJ AOP가 동작할 수 있는 환경설정과 DI 애스펙트 자체를 등록해서 `@Configurable` 오브젝트에 어드바이스가 적용되게 해야 한다.
- AspectJ를 사용하려면 클래스를 로딩하는 시점에 바이트코드 조작이 가능하도록 로드타임 위버를 적용해줘야 한다.
- 로드타임 위버는 jvm의 javaagent 옵션을 사용해서 jvm 레벨에 적용해야한다.

```
-javaagent:/Users/a10300/.m2/repository/org/springframework/spring-instrument/5.3.10/spring-instrument-5.3.10.jar
```



## 로드타임 위버(LTW)

- 로드타임위버는 스프링에서 여러가지 기능에 활용된다.
  - `@Configurable` 지원
  - AspectJ AOP 에서 사용
  - JPA에서 필요로하는 로드타임 위버로 사용
- 하이버네이트 JPA를 제외하면 대부분의 JPA 구현 제품은 고급 기능을 위해 로드타임 위버를 사용하도록 요구한다.
- 로드타임위버의 사용에서 두가지 문제
  - JVM레벨에 적용되기 때문에 JVM을 통해 로딩되는 모든 클래스를 다 조사하고 클래스 바이트코드 조작 대상으로 삼기 때문에 서버에 적용하기 부담스럽다.
  - JVM의 자바에이전트 옵션은 한 번에 한가지만 적용할 수 있다. AspectJ와 JPA 동시 적용 불가능
- 스프링 로드타임 위버는 JPA와 AspectJ를 위한 로드타임 위버로 동작하고 JVM 레벨의 자바에이전트를 대체할 수 있는 로드타임 위버 적용 방법을 자동으로 찾아줌으로써 해결한다.
- 스프링의 로드타임 위버는 자바에이전트 방식에 종속적이지 않다.
- 스프링은 자동으로 로드타임 위버 구현 방법중에서 하나를 적용해준다.

`WAS 전용 로드타임 위버`

- 애플리케이션 서버가 WebLogic, OC4j, GlassFish, J BossAS라면 서버가 제공하는 클래스 로더를 이요하는 전용 로드타임 위버를 설정해준다.
- JVM에 자바에이전트를 설정해줄 필요가 없다.

`JVM 자바에이전트`

- 스프링이 제공하는 로드타임위버 적용하기

`관례를 따르는 클래스 로더`

- 

## 스프링 3.1의 AOP와 LTW

### AOP와 LTW를 위한 애너테이션

#### @EnableAspectJAutoProxy

- `@EnableAspectJAutoProxy` 는 @Aspect로 애스펙트를 정의할 수 있게 해주는 @AspectJ AOP 컨테이너 인프라 빈을 등록해준다.

#### @EnableLoadTimeWeaving

- `@EnableLoadTimeWeaving` 은 환경에 맞는 로드타임 위버를 등록해주는 애너테이션이다.

```java
@Configuration
@EnableSpringConfigured
@EnableLoadTimeWeaving
public class Config {

}
```



## 정리

- 스프링에서는 프록시 기반의 AOP 기술을 네 가지 접근 방법을 통해 활용할 수 있다. 각 접근 방법의 장당점을 파악하고 자신에게 맞는 방법을 선택할 수 있어야한다.
- @AspectJ는 AspectJ 스타일의 POJO 애스펙트 작성 방법이다. @AspectJ는 유연하고 강력한 기능을 가진 애스팩트를 손쉽게 만들어주지만, 복잡한 AspectJ 문법과 사용방법을 익혀야하는 부담이 있다.
- 스프링은 또한 AspectJ를 스프링 애플리케이션 내에서 간접적으로 활용할 수 있는 몇가지 방법을 제공한다. @Configurable은 스프링 빈이 아닌 오브젝트에 DI를 적용할 수 있게 해준다.
- 스프링은 다양한 선버환경에서 사용 가능한 편리한 로드타임 위버를 제공해준다.
