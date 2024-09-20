# 9월 20일 정리
---
## 프런트 컨트룰러 패턴 소개

![image](https://github.com/user-attachments/assets/008f8808-de23-43da-a276-d1732ae330f0)

기존에는 클라이언트가 요청을 보내면, 요청에 매핑되는 각각의 서블릿이 요청을 처리했다.

![image (1)](https://github.com/user-attachments/assets/61f1b317-b0da-43e0-b9f8-dad0d55d7e52)

**프론트 컨트롤러 패턴**에서는 **하나의 프론트 컨트롤러(Front Controller)**라는 서블릿이 모든 요청을 일괄적으로 받는다. 이 프론트 컨트롤러가 요청을 받아, 해당 요청을 처리할 적절한 개별 컨트롤러(Controller)를 찾아 호출하는 역할을 한다.

이 패턴을 사용하면 공통 처리(예: 인증, 로깅 등)를 프론트 컨트롤러에서 한 번에 처리할 수 있어 코드 중복을 줄이고 유지보수가 쉬워진다.

**FrontController 패턴 특징**

- 프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받음
- 프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출
- 입구를 하나로!
- 공통 처리 가능
- 프론트 컨트롤러를 제외한 나머지 컨트롤러는 서블릿을 사용하지 않아도 됨

**스프링 웹 MVC와 프론트 컨트롤러**
스프링 웹 MVC의 핵심도 바로 **FrontController**
스프링 웹 MVC의 **DispatcherServlet**이 FrontController 패턴으로 구현되어 있음

## 프런트 컨트룰러 도입 -v1

프런트 컨트룰러를 단계적으로 도입해보겠습니다.

**📁 web.frontcontroller.v1.ControllerV1**

```java
public interface ControllerV1 {
    void process(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException;
}
```

서블릿과 비슷한 모양의 컨트롤러 인터페이스를 도입한다. 각 컨트롤러들은 이 인터페이스를 구현하면 된다. 프론트 컨트롤러는 이 인터페이스를 호출해서 구현과 관계없이 **로직의 일관성**을 가져갈 수 있다.

내부 로직은 기존 서블릿과 거의 같다.

**📁 FrontControllerServletV1 - 프론트 컨트롤러**

```java
@WebServlet(name = "frontControllerServlet1", urlPatterns = "/front-controller/v1/*")
public class FrontControllerServlet1 extends HttpServlet {

    private Map<String, ControllerV1> controllerMap = new HashMap<>();

    public FrontControllerServlet1() {
        controllerMap.put("/front-controller/v1/members/new-form", new MemberFormControllerV1());
        controllerMap.put("/front-controller/v1/members/save", new MemberSaveControllerV1());
        controllerMap.put("/front-controller/v1/members", new MemberListControllerV1());
    }
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("FrontControllerServlet1.service");
        String requestURI = request.getRequestURI();

        ControllerV1 controller = controllerMap.get(requestURI);
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }
        controller.process(request, response);
    }
}

```

### **프론트 컨트롤러 분석**

**urlPatterns**

- **urlPatterns = "/front-controller/v1/*"** : /front-controller/v1 를 포함한 하위 모든 요청은 이 서블릿에서 받아들인다.
    - 이로 인해, 개별 서블릿마다 URL을 매핑할 필요 없이, **FrontControllerServlet**이 모든 요청을 받아 해당 컨트롤러에 전달해준다.
- 예) /front-controller/v1 , /front-controller/v1/a , /front-controller/v1/a/b

**controllerMap**

- key: 매핑 URL
- value: 호출될 컨트롤러
- 이런 키 값 구조를 저장함으로써, request.getRequestURI()를 사용해 요청된 URL을 가져오고, 그 URL을 controllerMap에서 찾은 후, 일치하는 컨트롤러가 있으면 그 컨트롤러의 process() 메서드를 호출하게 됩니다.

**service()**
먼저 requestURI 를 조회해서 실제 호출할 컨트롤러를 controllerMap 에서 찾는다. 만약 없다면
404(SC_NOT_FOUND) 상태 코드를 반환한다.
컨트롤러를 찾고 controller.process(request, response); 을 호출해서 해당 컨트롤러를 실행한다.
**JSP**
JSP는 이전 MVC에서 사용했던 것을 그대로 사용한다

### ⭐정리

![image (2)](https://github.com/user-attachments/assets/2642bdbb-05e2-4c54-bc85-31f078cea04f)

- 클라이언트가 **HTTP 요청**을 보내면, **FrontControllerV1**이 그 요청을 받습니다.
- **FrontControllerV1**은 내부의 **HashMap**에서 요청된 **URL**에 해당하는 컨트롤러를 조회합니다.
- 조회된 컨트롤러가 있으면, 해당 컨트롤러의 **비즈니스 로직을 처리**하고, 필요한 데이터를 가공합니다.
- 그런 다음, 컨트롤러는 **JSP로 forward**를 통해 **HTML 페이지를 생성**하여, 최종적으로 클라이언트에게 **응답**을 보냅니다.

## View 분리 - v2

모든 컨트롤러에서 뷰로 이동하는 부분에 중복이 있고, 깔끔하지 않다

```java
String viewPath = "/WEB-INF/views/new-form.jsp";
RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
dispatcher.forward(request, response);
```

이 부분을 깔끔하게 분리하기 위해 별도로 뷰를 처리하는 객체를 만들어보겠습니다.

**MyView**

뷰 객체는 이후 다른 버전에서도 함께 사용하므로 패키지 위치를 frontcontroller 에 두었다

**📁 web.frontcontroller.MyView**

```java
public class MyView {
    private String viewPath;

    public MyView(String viewPath) {
        this.viewPath = viewPath;
    }
    public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

V1에서는 각 컨트롤러마다 request.getRequestDispatcher().forward()를 직접 호출해야 했지만, V2에서는 MyView 객체가 이를 대신 처리해 주므로, 컨트롤러는 MyView의 render() 메서드만 호출하면 JSP로의 포워딩이 이루어집니다.

**📁 ControllerV2**

```java
public interface ControllerV2 {
    MyView process(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException;
}
```

ControllerV1과 다르게 MyView를 반환하도록 설계하였다.

**📁 MemberFormControllerV2 - 회원 등록 폼**

```java
public class MemberFormControllerV2 implements ControllerV2 {
    @Override
    public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        return new MyView("/WEB-INF/views/new-form.jsp");
    }
}
```

V1과의 차이점은, ControllerV2에서는 getRequestDispatcher를 직접 호출하지 않고, JSP 경로를 매개변수로 가진 MyView 객체를 반환한다. 이로 인해 컨트롤러는 뷰 렌더링과 관련된 로직을 직접 처리하지 않고, MyView 객체에 위임한다.

회원 저장과 회원 목록도 위와 같은 방법을 적용한다.

**📁 FrontControllerServletV2**

```java
WebServlet(name = "frontControllerServletV2", urlPatterns = "/front-controller/v2/*")
public class FrontControllerServletV2 extends HttpServlet {

    private Map<String, ControllerV2> controllerMap = new HashMap<>();

    public FrontControllerServletV2() {
        controllerMap.put("/front-controller/v2/members/new-form", new MemberFormControllerV2());
        controllerMap.put("/front-controller/v2/members/save", new MemberSaveControllerV2());
        controllerMap.put("/front-controller/v2/members", new MemberListControllerV2());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("FrontControllerServletV2.service");

        String requestURI = request.getRequestURI();

        ControllerV2 controller = controllerMap.get(requestURI);
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }
        MyView view = controller.process(request, response);
        view.render(request, response);
    }
}
```

ControllerV2의 반환 타입이 **MyView** 이므로 프론트 컨트롤러는 컨트롤러의 호출 결과로 MyView 를 반환 받는다.
그리고 view.render() 를 호출하면 forward 로직을 수행해서 JSP가 실행된다.

### ⭐ 정리

![image (3)](https://github.com/user-attachments/assets/2a32a799-17dd-4206-acb2-4f3c1e5a9174)

클라이언트가 HTTP 요청을 보내면, FrontController가 요청에 맞는 컨트롤러를 호출합니다. 각 컨트롤러는 비즈니스 로직을 수행하고, 결과값을 request에 저장한 후, JSP 경로와 함께 결과값을 담은 MyView 객체를 반환합니다. FrontController는 이 MyView 객체의 render() 메서드를 호출하여 JSP로 포워딩하고, 클라이언트에게 응답을 전달합니다. 이 과정에서 request에 저장된 데이터는 JSP에서 활용됩니다.

## Model 추가 - V3

### **서블릿 종속성 제거**

컨트롤러 입장에서 HttpServletRequest, HttpServletResponse이 꼭 필요할까?
요청 파라미터 정보는 자바의 Map으로 대신 넘기도록 하면 지금 구조에서는 컨트롤러가 서블릿 기술을 몰라도 동작할 수 있다.
그리고 request 객체를 Model로 사용하는 대신에 별도의 Model 객체를 만들어서 반환하면 된다.

### **뷰 이름 중복 제거**

컨트롤러에서 지정하는 뷰 이름에 중복이 있는 것을 확인할 수 있다.
컨트롤러는 뷰의 **논리 이름**을 반환하고, 실제 **물리 위치**의 이름은 **프론트 컨트롤러에서 처리**하도록 단순화 하자.
이렇게 해두면 향후 뷰의 폴더 위치가 함께 이동해도 프론트 컨트롤러만 고치면 된다.

**논리 이름**: **뷰의 실제 경로와 무관하게** 컨트롤러가 반환하는 이름

 ex/ new-form, save-result

- **/WEB-INF/views/new-form.jsp → new-form**
- **/WEB-INF/views/save-result.jsp → save-result**
- **/WEB-INF/views/members.jsp → members**

### **ModelView(Model)**

지금까지 컨트롤러에서 서블릿에 종속적인 **HttpServletRequest**를 사용했다. 그리고 Model도
**request.setAttribute()** 를 통해 데이터를 저장하고 뷰에 전달했다.
서블릿의 종속성을 제거하기 위해 Model을 직접 만들고, 추가로 View 이름까지 전달하는 객체를 만들어보겠습니다.

참고로 ModelView 객체는 다른 버전에서도 사용하므로 패키지를 frontcontroller 에 둔다

**📁 ModelView**

```java
@Getter @Setter
public class ModelView {
    private String viewName;
    private Map<String, Object> model = new HashMap<>();

    public ModelView(String viewName) {
        this.viewName = viewName;
    }
}
```

뷰의 이름과 뷰를 렌더링할 때 필요한 model 객체를 가지고 있다. model은 단순히 map으로 되어 있으므로 컨트롤러에서 뷰에 필요한 데이터를 **key, value**로 넣어주면 된다.

- Object 타입은 자바의 모든 클래스의 최상위 클래스이므로, 어떤 객체라도 저장할 수 있다.

**📁 ControllerV3**

```java
public interface ControllerV3 {
    ModelView process(Map<String, String> paramMap);
}
```

V1, V2와 달리 서블릿 기술(HttpServletRequest, HttpServletResponse)에 의존하지 않고, 정의한 **ModelView 객체를 반환**합니다.

HttpServletRequest가 제공하는 파라미터는 프론트 컨트롤러가 **paramMap**에 담아서 호출해주면 된다.
응답 결과로 뷰 이름과 뷰에 전달할 Model 데이터를 포함하는 ModelView 객체를 반환하면 된다.

**📁 FrontControllerServletV3**

```java
        Map<String, String> paramMap = createParamMap(request);
        ModelView mv = controller.process(paramMap);

        String viewName = mv.getViewName(); // 논리 이름 new-form
        // /WEB-INF/views/new-form.jsp
        MyView view = viewResolver(viewName);

        view.render(mv.getModel(), request, response);
    }

    private static MyView viewResolver(String viewName) {
        return new MyView("/WEB-INF/views/" + viewName + ".jsp");
    }

    private  Map<String, String> createParamMap(HttpServletRequest request) {
        Map<String, String> paramMap = new HashMap<>();
        request.getParameterNames().asIterator()
                        .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
        return paramMap;
    }
}
```

**createParamMap()**
HttpServletRequest에서 파라미터 정보를 꺼내서 Map으로 변환한다. 그리고 해당 Map( paramMap )을 컨트롤러에 전달하면서 호출한다.

즉, parmMap에는 클라이언트 요청에 대한 모든 파라미터 이름과 값이 key-value 형태로 저장되어있다.

**ModelView mv = controller.process(paramMap);**

URL에 해당하는 컨트롤러의 process 메서드를 호출한다.

컨트롤러는 **논리적인 뷰 이름을 담은 ModelView** 객체를 반환하거나, 비**즈니스 로직을 수행한 후 결과값을 Map**에 저장하여 해당 값을 가진 ModelView를 반환한다.

**viewResolver**
MyView view = viewResolver(viewName)
컨트롤러가 반환한 논리 뷰 이름을 실제 물리 뷰 경로로 변경한다. 그리고 실제 물리 경로가 있는 MyView 객체를 반환한다.

- **논리 뷰 이름**: members
- 물**리 뷰 경로**: /WEB-INF/views/members.jsp

**view.render(mv.getModel(), request, response)**

- 뷰 객체를 통해서 HTML 화면을 렌더링 한다.
- 뷰 객체의 render() 는 모델 정보도 함께 받는다.
- JSP는 request.getAttribute() 로 데이터를 조회하기 때문에, 모델의 데이터를 꺼내서
request.setAttribute() 로 담아둔다.
- JSP로 포워드 해서 JSP를 렌더링 한다.

**📁 MyView**

```java
public class MyView {
    private String viewPath;

    public MyView(String viewPath) {
        this.viewPath = viewPath;
    }

    public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }

    public void render(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        modelToRequestAttribute(model, request);
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }

    private static void modelToRequestAttribute(Map<String, Object> model, HttpServletRequest request) {
        model.forEach((key, value) -> request.setAttribute(key, value));
    }
}
```

**modelToRequestAttribute()**

model에 담긴 데이터를 request 객체에 속성으로 저장하여, JSP에서 해당 데이터를 사용할 수 있게 만드는 기능을 수행합니다.

### ⭐ 정리

![image (4)](https://github.com/user-attachments/assets/24d3a670-0b74-4322-9056-dbbb149e013d)

V3에서는 FrontController에서 모든 요청 파라미터를 받아서 Map에 저장하고, 각 컨트롤러는 HttpServletRequest를 사용하지 않고 이 Map을 통해 비즈니스 로직을 처리합니다. 그 후, 비즈니스 로직 결과와 JSP 논리 경로를 담은 ModelView 객체를 반환합니다.

FrontController는 반환된 ModelView에서 논리적인 뷰 이름을 추출하고, viewResolver를 사용하여 논리 경로와 물리 경로를 합쳐 실제 JSP 파일 경로를 생성합니다. 생성된 경로와 컨트롤러가 반환한 Model 데이터를 MyView 객체로 전달하고, MyView는 이 데이터를 HttpServletRequest에 저장한 후 JSP로 포워딩합니다.
