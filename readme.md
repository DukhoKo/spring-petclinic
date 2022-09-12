# 스프링 프레임워크

## IoC (Inversion of Control)
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
그럼 **컨테이너의 핵심 인터페이스**는 **ApplicationContext ( beanFactory )** 이다.
**컨테이너 내부에 만들어진 객체**를 **bean**이라고 한다. **이런한 bean들의 의존성을 관리해주는 역할을 하는게 컨테이너이다.** 오로지 bean만 관리한다.

어떤 코드를 봐도 ApplicationContext가 존재하지 않는다.

OwnerController가 IoC 컨테이너 내부의 객체로 들어오고 IoC 컨테이너 내부애서 OwnerController 객체를 만들어준다.

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





