# 9월13일 정리
---

## ✅ 프로토타입 스코프 - 싱글톤 빈과 함께 사용시 문제점

---

클라이언트 빈에는 count 필드가 있어 addCount 메서드를 사용하면 count 값을 +1씩 증가시킨다. 

만약, 클라리언트 A, B가 클라이언트 빈을 직접 요청하고 addcount 메서드를 사용하면, 싱글톤 빈이기 때문에 두 클라이언트 모두 count 값이 1이 된다.

스프링 컨테이너에 프로토타입 스코프의 빈을 요청하면 **항상 새로운 객체 인스턴스를 생성해서 반환**한다. 하지만 싱글톤 빈과 함께 사용할 때는 의도한 대로 잘 동작하지 않으므로 주의해야 한다. 

## ✅ 프로토타입 스코프 - 싱글톤 빈과 함께 사용시 Provider로 문제 해결

---

싱글톤 빈과 프로토타입 빈을 함께 사용할 때, 어떻게 하면 사용할 때 마다 항상 새로운 프로토타입 빈을 생성할 수 있을까?

**ObjectProvider**를 사용하는 방법과 **JSR-330 Provider** 사용하는 방법이 있다.

### ObjectProvider

지정한 빈을 컨테이너에서 대신 찾아주는 DL 서비스를 제공하는 것이 바로 ObjectProvider 이다

**📁 SingletonWithPrototypeTest1.java**

```java
@Autowired
private ObjectProvider<PrototypeBean> prototypeBeanProvider;

public int logic() {
    PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```

- 실행해보면 prototypeBeanProvider**.getObject()** 을 통해서 항상 **새로운 프로토타입 빈이 생성된다.**
- ObjectProvider 의 getObject() 를 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환한다. **(DL)**
- 스프링이 제공하는 기능을 사용하지만, 기능이 단순하므로 단위테스트를 만들거나 mock 코드를 만들기는 훨씬 쉬워진다.
- ObjectProvider 는 지금 딱 필요한 DL 정도의 기능만 제공한다.
- **ObjectProvider**는 스프링 컨테이너를 대신해 특정 스프링 빈을 지연해서 조회하거나, 필요할 때마다 요청하는 역할을 한다.

**특징**

- ObjectFactory: 기능이 단순, 별도의 라이브러리 필요 없음, 스프링에 의존
- ObjectProvider: ObjectFactory 상속, 옵션, 스트림 처리등 편의 기능이 많고, 별도의 라이브러리 필요 없음, 스프링에 의존

### JSR-330 Provider

마지막 방법은 javax.inject.Provider 라는 **JSR-330 자바 표준**을 사용하는 방법이다.
스프링 부트 3.0은 **jakarta.inject.Provider** 사용한다.

이 방법을 사용하려면 다음 라이브러리를 **gradle**에 추가해야 한다.

**🧑‍💻 Provider 사용**

```java
@Autowired
private Provider<PrototypeBean> prototypeBeanProvider;

public int logic() {
    PrototypeBean prototypeBean = prototypeBeanProvider.get();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```

- 실행해보면 provider**.get()** 을 통해서 **항상 새로운 프로토타입 빈이 생성된다.**
- provider 의 get() 을 호출하면 내부에서는 스프링 컨테이너를 통해 해당 빈을 찾아서 반환한다. (DL)
- 자바 표준이고, 기능이 단순하므로 단위테스트를 만들거나 mock 코드를 만들기는 훨씬 쉬워진다.
- **Provider** 는 지금 딱 필요한 DL 정도의 기능만 제공한다.

**특징**

- get() 메서드 하나로 기능이 매우 단순하다.
- 별도의 라이브러리가 필요하다.
- 자바 표준이므로 스프링이 아닌 **다른 컨테이너에서도 사용할 수 있다.**

**정리**

- 그러면 프로토타입 빈을 언제 사용할까? 매번 사용할 때 마다 의존관계 주입이 완료된 새로운 객체가 필요하면 사용하면 된다. 그런데 실무에서 웹 애플리케이션을 개발해보면, 싱글톤 빈으로 대부분의 문제를 해결할 수 있기 때문에 프로토타입 빈을 직접적으로 사용하는 일은 매우 드물다.
- ObjectProvider , JSR330 Provider 등은 프로토타입 뿐만 아니라 DL이 필요한 경우는 언제든지 사용할수 있다.

## ✅ 웹 스코프

---

웹 애플리케이션에서 사용되는 스프링 빈의 생명주기를 정의하는 스코프이다.

### 웹 스코프의 특징

- 웹 스코프는 **웹 환경**에서만 동작한다.
- 웹 스코프는 프로토타입과 다르게 스프링이 **해당 스코프의 종료시점까지 관리한다**. 따라서 종료 메서드가 호출된다.

### 웹 스코프 종류

- **request**: HTTP 요청 하나가 들어오고 나갈 때 까지 유지되는 스코프, 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고, 관리된다.
- **session**: HTTP Session과 동일한 생명주기를 가지는 스코프
- **application**: 서블릿 컨텍스트( ServletContext )와 동일한 생명주기를 가지는 스코프
- **websocket**: 웹 소켓과 동일한 생명주기를 가지는 스코프

## ✅ request 스코프 예제 만들기

---

동시에 여러 HTTP 요청이 오면 정확히 어떤 요청이 남긴 로그인지 구분하기 어렵다.
이럴때 사용하기 딱 좋은것이 바로 request 스코프이다.

다음과 같이 로그가 남도록 request 스코프를 활용해서 추가 기능을 개발해보겠다.

```java
[d06b992f...] request scope bean create
[d06b992f...][http://localhost:8080/log-demo] controller test
[d06b992f...][http://localhost:8080/log-demo] service id = testId
[d06b992f...] request scope bean close
```

- 기대하는 공통 포멧: UUIDrequestURL {message}
- UUID를 사용해서 HTTP 요청을 구분하자.
- requestURL 정보도 추가로 넣어서 어떤 URL을 요청해서 남은 로그인지 확인하자.

🧑‍💻 **MyLogger.java**

```java
@Component
@Scope(value = "request")
public class MyLogger {

    private String uuid;
    private String requestURL;

    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }
    public void log(String message) {
        System.out.println("[" + uuid + "]" + "[" + requestURL + "] " + message);
    }
    @PostConstruct
    public void init() {
        uuid = UUID.randomUUID().toString();
        System.out.println("[" + uuid + "] request scope bean create:" + this);
    }
    @PreDestroy
    public void close() {
        System.out.println("[" + uuid + "] request scope bean close:" + this);
    }
}
```

- 로그를 출력하기 위한 MyLogger 클래스이다.
- @Scope(value = "request") 를 사용해서 **request 스코프로 지정**했다. 이제 이 빈은 HTTP 요청 당 하나씩 생성되고, HTTP 요청이 끝나는 시점에 소멸된다.
- 이 빈이 생성되는 시점에 자동으로 @PostConstruct 초기화 메서드를 사용해서 uuid를 생성해서 저장해둔다.
- 이 빈은 HTTP 요청 당 하나씩 생성되므로, uuid를 저장해두면 다른 HTTP 요청과 구분할 수 있다.
- 이 빈이 소멸되는 시점에 @PreDestroy 를 사용해서 종료 메시지를 남긴다.
- requestURL 은 이 빈이 생성되는 시점에는 알 수 없으므로, 외부에서 setter로 입력 받는다

**🧑‍💻 LogDemoController.java**

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final MyLogger myLogger;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURI().toString();
        myLogger.setRequestURL(requestURL);

        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}
```

- 로거가 잘 작동하는지 확인하는 테스트용 컨트롤러다.
- 여기서 HttpServletRequest를 통해서 요청 URL을 받았다.
    - requestURL 값 http://localhost:8080/log-demo
- 이렇게 받은 requestURL 값을 myLogger에 저장해둔다. myLogger는 HTTP 요청 당 각각 구분되므로 다른HTTP 요청 때문에 값이 섞이는 걱정은 하지 않아도 된다.
- 컨트롤러에서 controller test라는 로그를 남긴다.

**🧑‍💻 LogDemoService.java**

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final MyLogger myLogger;
    public void logic(String id) {
        myLogger.log("service id = " + id);
    }
}
```

- 비즈니스 로직이 있는 서비스 계층에서도 로그를 출력해보자.
- 여기서 중요한점이 있다. request scope를 사용하지 않고 파라미터로 이 모든 정보를 서비스 계층에 넘긴다면, 파라미터가 많아서 지저분해진다. 더 문제는 requestURL 같은 웹과 관련된 정보가 웹과 관련없는 서비스 계층까지 넘어가게 된다. 웹과 관련된 부분은 컨트롤러까지만 사용해야 한다. 서비스 계층은 웹 기술에 종속되지 않고, 가급적 순수하게 유지하는 것이 유지보수 관점에서 좋다.
- request scope의 MyLogger 덕분에 이런 부분을 파라미터로 넘기지 않고, MyLogger의 멤버변수에 저장해서 코드와 계층을 깔끔하게 유지할 수 있다.

**애플리케이션 실행 시점에 오류 발생**

```java
Error creating bean with name 'myLogger': Scope 'request' is not active for the
current thread; consider defining a scoped proxy for this bean if you intend to
refer to it from a singleton;
```

Request 스코프 빈은 **HTTP 요청이 있을 때만 생성되기 때문에**, HTTP 요청이 없는 시점에서 Request 스코프 빈에 접근하려 하면 에러가 발생합니다.
싱글톤 빈이 request 스코프의 빈을 주입받으려고 할 때, 요청이 없으면 해당 빈을 찾을 수 없어서 스코프가 활성화되지 않았다는 에러가 발생합니다.

이러한 에러를 해결할 수 있는 방법은 다음과 같다.

## ✅ 스코프와 Provider

---

첫번째 해결방안은 앞서 배운 **Provider**를 사용하는 것이다.
간단히 **ObjectProvider**를 사용해보자.

**ObjectProvider를 적용한 코드**

**🧑‍💻 LogDemoController.java**

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final ObjectProvider<MyLogger> myLoggerProvider;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        MyLogger myLogger = myLoggerProvider.getObject();
        String requestURL = request.getRequestURI().toString();
        myLogger.setRequestURL(requestURL);

        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}
```

**🧑‍💻 LogDemoService.java**

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final ObjectProvider<MyLogger> myLoggerProvider;
    public void logic(String id) {
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.log("service id = " + id);
    }
}
```

main() 메서드로 스프링을 실행하고, 웹 브라우저에 [http://localhost:8081/log-demo](http://localhost:8080/log-demo) 를 입력하면,

잘 작동하는 것을 확인할 수 있다.

```java
[d1127a46-4295-41e8-81cc-530c71acaba1] request scope bean create:hello.core.common.MyLogger@46310c41
[d1127a46-4295-41e8-81cc-530c71acaba1][/log-demo] controller test
[d1127a46-4295-41e8-81cc-530c71acaba1][/log-demo] service id = testId
[d1127a46-4295-41e8-81cc-530c71acaba1] request scope bean close:hello.core.common.MyLogger@46310c41
```

- 기존 코드에서 request 스코프의 빈인 MyLogger를 주입하려고 하면, HTTP 요청이 존재하지 않는 시점(즉, 컨트롤러가 생성될 때)에는 request 스코프가 활성화되지 않아서 오류가 발생했었다.
- ObjectProvider 덕분에 ObjectProvider.getObject() 를 호출하는 시점까지 request scope 빈의
생성을 지연할 수 있다.
- ObjectProvider.getObject() 를 호출하시는 시점에는 HTTP 요청이 진행중이므로 request scope 빈의 생성이 정상 처리된다.
- ObjectProvider.getObject() 를 LogDemoController , LogDemoService 에서 각각 한번씩 따로
호출해도 같은 HTTP 요청이면 같은 스프링 빈이 반환된다! 내가 직접 이걸 구분하려면 얼마나 힘들까

## ✅ 스코프와 프록시

---

이번에는 프록시 방식을 사용해보자.

```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {
}
```

- 여기가 핵심이다. proxyMode = ScopedProxyMode.TARGET_CLASS 를 추가해주자.
    - 적용 대상이 인터페이스가 아닌 클래스면 TARGET_CLASS 를 선택
    - 적용 대상이 인터페이스면 INTERFACES 를 선택
- 이렇게 하면 MyLogger의 가짜 프록시 클래스를 만들어두고 HTTP request와 상관 없이 가짜 프록시 클래스를 다른 빈에 미리 주입해 둘 수 있다.

이제 나머지 코드를 Provider 사용 이전으로 돌려두자

**📁 LogDemoController.java**

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final MyLogger myLogger;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURI().toString();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}
```

**📁 LogDemoService.java**

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final MyLogger myLogger;
    public void logic(String id) {
        myLogger.log("service id = " + id);
    }
}
```

실행해보면 잘 동작하는 것을 확인할 수 있다

```java
[880fc678-cd53-46a8-9a67-78fc4ecf55a5] request scope bean create:hello.core.common.MyLogger@72553100
[880fc678-cd53-46a8-9a67-78fc4ecf55a5][/log-demo] controller test
[880fc678-cd53-46a8-9a67-78fc4ecf55a5][/log-demo] service id = testId
[880fc678-cd53-46a8-9a67-78fc4ecf55a5] request scope bean close:hello.core.common.MyLogger@72553100
16:26:15.320 [http-nio-8081-exec-1] DEBUG o.s.web.servlet.DispatcherServlet -- Completed 200 OK
```

코드를 잘 보면 LogDemoController , LogDemoService 는 Provider 사용 전과 완전히 동일하다. 어떻게 된 것일까?

### 웹 스코프와 프록시 동작 원리

먼저 주입된 myLogger를 확인해보자

```java
System.out.println("myLogger = " + myLogger.getClass());
```

**출력결과**

```java
myLoger = class hello.core.common.MyLogger$$SpringCGLIB$$0
```

**CGLIB라는 라이브러리로 내 클래스를 상속 받은 가짜 프록시 객체를 만들어서 주입한다.**

- @Scope 의 proxyMode = ScopedProxyMode.TARGET_CLASS) 를 설정하면 스프링 컨테이너는 CGLIB라는 바이트코드를 조작하는 라이브러리를 사용해서, MyLogger를 상속받은 가짜 프록시 객체를 생성한다.
- 결과를 확인해보면 우리가 등록한 순수한 MyLogger 클래스가 아니라 **MyLogger$
$EnhancerBySpringCGLIB** 이라는 클래스로 만들어진 객체가 대신 등록된 것을 확인할 수 있다.
- 그리고 스프링 컨테이너에 "myLogger"라는 이름으로 진짜 대신에 이 가짜 프록시 객체를 등록한다.
- ac.getBean("myLogger", MyLogger.class) 로 조회해도 프록시 객체가 조회되는 것을 확인할 수 있
다.
- 그래서 의존관계 주입도 이 가짜 프록시 객체가 주입된다

![image](https://github.com/user-attachments/assets/f52ad966-55d4-416d-ab73-f5bee15340a4)

**가짜 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.**

- 가짜 프록시 객체는 내부에 진짜 myLogger 를 찾는 방법을 알고 있다.
- 클라이언트가 myLogger.log() 을 호출하면 사실은 가짜 프록시 객체의 메서드를 호출한 것이다.
- 가짜 프록시 객체는 request 스코프의 진짜 myLogger.log() 를 호출한다.
- 가짜 프록시 객체는 원본 클래스를 상속 받아서 만들어졌기 때문에 이 객체를 사용하는 클라이언트 입장에서는 사실 원본인지 아닌지도 모르게, 동일하게 사용할 수 있다(다형성)

**동작 정리**

- CGLIB라는 라이브러리로 내 클래스를 상속 받은 가짜 프록시 객체를 만들어서 주입한다.
- 이 가짜 프록시 객체는 실제 요청이 오면 그때 내부에서 실제 빈을 요청하는 위임 로직이 들어있다.
- 가짜 프록시 객체는 실제 request scope와는 관계가 없다. 그냥 가짜이고, 내부에 단순한 위임 로직만 있고, 싱글톤 처럼 동작한다.

**특징 정리**

- 프록시 객체 덕분에 클라이언트는 마치 싱글톤 빈을 사용하듯이 편리하게 request scope를 사용할 수 있다.
- 사실 Provider를 사용하든, 프록시를 사용하든 핵심 아이디어는 진짜 **객체 조회를 꼭 필요한 시점까지 지연처리** 한다는 점이다.
- 단지 애노테이션 설정 변경만으로 원본 객체를 프록시 객체로 대체할 수 있다. 이것이 바로 다형성과 DI 컨테이너가 가진 큰 강점이다.
- 꼭 웹 스코프가 아니어도 프록시는 사용할 수 있다.

**주의점**

- 마치 싱글톤을 사용하는 것 같지만 다르게 동작하기 때문에 결국 주의해서 사용해야 한다.
- 이런 특별한 scope는 꼭 필요한 곳에만 최소화해서 사용하자, 무분별하게 사용하면 유지보수하기 어려워진다.

### 그 외 알게된 점

- AnnotationConfigApplicationContext는 스프링 컨테이너 중 하나로, 주로 자바 기반 설정에서 애플리케이션 컨텍스트를 설정하고 관리하는 데 사용됩니다.
- ApplicationContext는 스프링의 최상위 인터페이스로, 다양한 컨텍스트 구현체들이 있습니다. 이 인터페이스는 스프링의 기본 기능을 제공하며, 빈 조회, 이벤트 관리, 리소스 로딩 등의 공통 기능을 제공합니다.
- AnnotationConfigApplicationContext를 타입으로 변수를 선언한 이유는 자바 기반 설정을 통해 클래스를 사용하여 스프링 컨테이너를 생성하기 위함이다.
