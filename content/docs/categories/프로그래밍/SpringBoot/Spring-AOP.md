# 스프링 AOP를 구현하는 방법 기술.

AOP 개념

Aspect : 공통 기능을 말합니다.

Advice : Aspect의 기능 자체를 말합니다.

Jointpoint : Advice를 적용해야 하는 부분입니다. 필드나 메소드이고, 스프링에서는 메소드만

해당됩니다.

Pointcut : Jointpoint의 부분,  실제로 Advice가 적용된 부분

Weaving : Advice를 핵심 기능에 적용하는 행위를 말합니다.

* RTW (Runtime Weaving)
  스프링 AOP에서 사용하는 위빙 방식. Proxy를 생성하여 실제 타깃 오브젝트의 변형없이 위빙을 수행.
  실제 런타임 시, 메소드 호출과 동시에 위빙이 이루어 지는 방식이다.

  장점 : 소스파일, 클래스파일의 변형이 없다.
  단점 : 포인트 컷에 대한 어드바이스 적용 갯수가 늘어 날수록 성능이 떨어진다.

* CTW (Compile time Weaving)
  AspectJ에는 AJC (AspectJ Compiler)라는 컴파일러가 있는데 Java Compiler를 확장한 형태의 컴파일러이다. AJC를 통해 java파일을 컴파일 하며, 컴파일 과정에서 바이트 코드 조작을 통해 Advisor 코드를 직접 삽입하여 위빙을 수행

  장점 : 위빙중 가장 빠른 퍼포먼스 (JVM상에 올라 갈때 메소드 내에 이미 advice 코드가 삽입 되어있기 때문에..)
  단점 : 컴파일 과정에서 lombok과 같이 컴파일 과정에서 코드를 조작하는 플러그인과 충돌이 발생할 가능성이 아주 높다. (거의 같이 사용 불가)

* LTW (Load time Weaving)
  ClassLoader를 통해 JVM에 로드 될 때 바이트 코드 조작을 통해 위빙이 되는 방식
  RTW와 마찬가지로 소스코드와 클래스파일에 조작이 없다.
   하지만 오브젝트가 메모리에 올라가는 과정에서 위빙이 일어나기 때문에 런타임 시, 시간은 CTW보다 상대적으로 느리다

  장점 : 소스파일, 클래스파일의 변형이 없다.
  단점 : performance가 저하, 설정의 복잡.



스프링 프레임워크에서 지원하는 3가지 AOP 기술

* JDK dynamic proxy, CGLIB, AspectJ



AOP 적용 방식에 따른 분류

* 프록시 기반 : JDK dynamic proxy, CGLIB
* 타깃 오브젝트 조작 : AspectJ



**JDK Dynamic Proxy & CGLIB**

Aspect 프레임워크와는 달리 스프링에서는 간단한 설정만으로 JDK Dynamic Proxy와 CGLIB 방식을 사용할 수 있도록 되어 있습니다. 두 방식의 차이는 인터페이스의 유무로서, AOP의 타깃이 되는 클래스가 인터페이스를 구현했다면 JDK Dynamic Proxy를 사용하고, 구현하지 않았다면 CGLIB 방식을 사용합니다

**[출처]** [Spring AOP에 관하여 - [2\] JDK Dynamic Proxy & CGLIB](http://blog.naver.com/tmondev/220558804255)





Aspect란?

도메인 로직에 필요한 다양한 부가기능을 추상화 시킨것.

어노테이션 기반으로 AOP를 구현은 런타임이 아닌 `Compile` 시점에 Aspect를 적용하는 것이다.

```java
@Aspect
@Component
public class TestAspect {

  private static final Logger logger = LoggerFactory.getLogger(TestAspect.class);

  @Before("execution(* com.example.aop_example.service.*.test(..))")
  public void onBeforeHandler(JoinPoint joinPoint) {
    logger.info("=============== onBeforeThing");
  }

  @After("execution(* com.example.aop_example.service.*.test(..))")
  public void onAfterHandler(JoinPoint joinPoint) {
    logger.info("=============== onAfterHandler");
  }

  @AfterReturning(pointcut = "execution(* com.example.aop_example.service.*.test(..))",
      returning = "str")
  public void onAfterReturningHandler(JoinPoint joinPoint, Object str) {
    logger.info("@AfterReturning : " + str);
    logger.info("=============== onAfterReturningHandler");
  }

  @Pointcut("execution(* com.example.aop_example.service.*.test(..))")
  public void onPointcut(JoinPoint joinPoint) {
    logger.info("=============== onPointcut");
  }
}
```



