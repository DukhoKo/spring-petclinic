# 스프링 프레임워크

## IoC (Inversion of Control)
- 의존 관계 주입(Dependency Injection)이라고도 하며, 어떤 객체가 사용하는 의존 객체를 직접 만들어 사용하는게 아니라, 주입 받아 사용하는 방법을 말함

컨트롤의 뒤바뀜.. 무엇에 대해 뒤바뀐거냐? 일반적으로 의존성에 대해서..

원래는 내가 쓸놈은 내가 만들어서 쓸게..
```c
class OwnerController {
    private OwnerRepository repository = new OwnerRepository();
}
```
이렇게 OwnerRepository는 OwnerController의 의존성이다. OwnerController는 OwnerRepository를 필요로 한다.

```c
class OwnerController { //OwnerController는
    private OwnerRepository repo; //OwnerRepository타입의 래퍼런스 변수 repo를 들고만 있고
    public OwnerController(OwnerRepository repo) { //누군가가 생성자로 OwnerRepository를 주겠지라고 생각하고
        this.repo = repo;
    }
    // repo를 사용합니다.
}
//코드 밖의 OwnerControllerTest에서
class OwnerControllerTest {
    @Test
    public void create() {
        OwnerRepository repo = new OwnerRepository();
        OwnerController controller = new OwnerController(repo); //생성자를 통해 repo를 준다. ->이게 의존성 주입이다.
    }
}
```
이런 형태가 IoC이다. 제어권이 나 자신이 아닌 나 의외의 누군가에게로 제어권이 뒤바뀐것이다.

## IoC (Inversion of Control) 컨테이너
Spring Framework은 Inversion of Control용 컨테이너를 제공해준다.
Spring IoC 컨테이너
 * [BeanFactory](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/javadoc-api/org/springframework/beans/factory/BeanFactory.html)
 * 애플리케이션 컴포넌트의 중앙 저장소
 * 빈 인스턴스 생성
 * 빈 설정 소스로 부터 빈 정의를 읽어들이고, 빈을 구성하고 제공한다.

그럼 **컨테이너의 핵심 인터페이스**는 **ApplicationContext ( beanFactory )** 이다.
**컨테이너 내부에 만들어진 객체**를 **bean**이라고 한다. **이런한 bean들의 의존성을 관리해주는 역할을 하는게 컨테이너이다.** 오로지 bean만 관리한다.

어떤 코드를 봐도 ApplicationContext가 존재하지 않는다.
OwnerController가 IoC 컨테이너 내부의 객체로 들어오고 IoC 컨테이너 내부애서 OwnerController 객체를 만들어준다.

[ApplicationContext](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/javadoc-api/org/springframework/context/ApplicationContext.html)
 * [BeanFactory](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/javadoc-api/org/springframework/beans/factory/BeanFactory.html)
 * 메시지 소스처리 기능 (i18n)
 * 이벤트 발행 기능
 * 리소스 로딩 기능

## Bean(빈)
**스프링 IoC 컨테이너가 관리하는 객체**
어떻게 등록이 되는가?
* Component Scanning
  * @Component
    * @Controller
    * @Service
    * @Repository
* 또는 직접 일일히 XML이나 자바 설정 파일에서 등록하는 방법이 있다.

어떻게 꺼내쓰나?
* @Autowired 또는 @Inject
* 또는 ApplicationContext에서 getBean()으로 직접 꺼내쓴다.

- 빈 장점
  * 스코프
      * 싱글톤 : 하나 (빈으로 등록시 어떠한 annotation도 안붙였다면 그 빈들은 싱글톤)
      * 프로토타입 : 매번 다른 객체를 사용하는것
  * 라이프사이클 인터페이스
    cf) @PostConstruct ...
  
## @Autowired
* required : 기본값은 true (못찾으면 애플리케이션 구동 실패) cf) @Autowired(requried = false) false를 쓰려면 이렇게 쓰면 된다.
* 사용할수 있는 위치
  * 생성자 (스프링 4.3부터는 생략 가능)
  * Setter
  * 필드

같은 타입의 빈이 여러개일 경우
* @Primary
* 해당 타입의 빈 모두 주입 받기
* @Qualifier("빈 이름으로 주입")

## @Component 와 @ComponentScan
* @ComponentScan의 주요 기능
  * 스캔 위치 기능
  * 필터 : 어떤 애노테이션을 스캔할지 또는 하지 않을지

* @Component
  * @Repository
  * @Service
  * @Controller
  * @Configuration

## ApplicationEventPublisher
이벤트 프로그래밍에 필요한 인터페이스 제공, 옵저버 패턴 구현체
- 스프링 4.2부터는 이 클래스를 상속받지 않아도 이벤트로 사용할 수 있다.
이벤트 처리하는 방법
  - ApplicationListener<Event> 구현한 클래스 만들어서 빈으로 등록하기
  - 스프링 4.2부터는 @EventListener를 사용해서 빈의 메소드에 사용할 수 있다.
  - 기본적으로는 synchronized
  - 순서를 정하고 싶다면 @Order(...)와 함께 사용
  - 비동기적으로 실행하고 싶다면 @Async와 함께 사용

스프링이 제공하는 기본 이벤트
* ContextRefreshedEvent: ApplicationContext를 초기화 했더나 리프래시 했을 때 발생
* ContextStartedEvent: ApplicationContext를 start()하여 라이프사이클 빈들이 시작 신호를 받은 시점에 발생
* ContextStoppedEvent: ApplicationContext를 stop()하여 라이프사이클 빈들이 정지 신호를 받은 시점에 발생
* ContextClosedEvent: ApplicationContext를 close()하여 싱글톤 빈 소멸되는 시점에 발생
* RequestHandledEvent: HTTP 요청을 처리했을 때 발생

## ResourceLoader
리소스를 읽어오는 기능을 제공하는 인터페이스
ApplicationContext extends ResourceLoader
리소스 읽어오기
* 파일 시스템에서 읽어오기
* 클래스패스에서 읽어오기
* URL로 읽어오기
* 상대/절대 경로로 읽어오기

Resource getResource(java.lang.String location)

## 의존성 주입 (Dependency Injection)
필요한 의존성을 어떻게 받아올것인가..
@Autowired / @Inject 를 어떻게 받아 올것인가?
* 생성자
* 필드
* Setter
어디에 붙여야하는가?
=> 생성자를 쓸수있다면 or 해당 클래스에 반드시 필요한 객체다 라고 생각하면 첫번째로 생성자, 
   필드와 Setter중에 클래스가 Setter를 가지고 있다면 Setter, 그게 아니라면 필드에 붙이는게 좋다.
 
## AOP (Aspect Oriented Programming)
*핵심! 흩어져 있는 코드를 한곳으로 모으는게 AOP이다.*
이렇게 구현하는 기법 
1. byte 코드를 조작한다. (compile하면 나오는 doc.class 파일을 조작하는것이다.)
2. proxy pattern을 조작한다.

@Around 말고 추가적으로 찾아보면 더 있다. 

## PSA (Portable Service Abstraction)
환경의 변화와 관계없이 일관된 방식의 기술로의 접근 환경을 제공하려는 추상화 구조

## Transactional
Platform TransactionManager중에 
JpaTransacionManager | DatasourceTransactionManager | HibernateTransactionManager를 사용한다.

## Cache
@EnableCaching - spring framework : cache관련 기능 활성화  @Cacheable | @CacheEvict | .. 사용가능
CacheManager중에
JCacheManager | ConcurrentMapCacheManager | EhCacheCacheManager | .. 사용가능

## 웹 MVC
@Controller | @RequestMapping





