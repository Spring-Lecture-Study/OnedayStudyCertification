# 9월 23일 정리
---

## 단순하고 실용적인 컨트룰러 - v4

전 시간에 개발한 V3는 개발자 입장에서 보면, 항상 ModelView 객체를 생성하고 반환해야 하는 부분이조금은 번거롭다.
좋은 프레임워크는 아키텍처도 중요하지만, 그와 더불어 실제 개발하는 개발자가 단순하고 편리하게 사용할 수 있어야한다. 소위 실용성이 있어야 한다.

이번에는 v3를 조금 변경해서 실제 구현하는 개발자들이 매우 편리하게 개발할 수 있는 v4 버전을 개발해보겠습니다.

**📁 ControllerV4**

```java
public interface ControllerV4 {
    /**
     * @param paramMap
     * @param model
     * return viewName
     */
    String process(Map<String, String> paramMap, Map<String, Object> model);
}
```

이번 버전은 인터페이스에 ModelView가 없다. **model 객체는 파라미터로 전달되기 때문에** 그냥 사용하면 되고, 결과로 뷰의 이름만 반환해주면 된다

**📁 MemberFormControllerV4**

```java
public class MemberFormControllerV4 implements ControllerV4 {
    @Override
    public String process(Map<String, String> paramMap, Map<String, Object> model) {
        return "new-form";
    }
}
```

- 정말 단순하게 **new-form** 이라는 뷰의 논리 이름만 반환하면 된다.

**📁 MemberSaveControllerV4**

```java
        // V3와 비교했을 때 윗부분은 똑같기 때문에 생략 //
        model.put("member", member);
        return "save-result";
    }
}
```

 V4에서는 ModelView 객체를 따로 생성할 필요 없이, 비즈니스 로직을 처리한 후 결과 값을 바로 model에 담고, 논리적인 뷰 이름을 문자열로 반환한다.

**📁 FrontControllerServletV4**

```java
   Map<String, String> paramMap = createParamMap(request);
	 Map<String, Object> model = new HashMap<>(); // 추가
	
	 String viewName = controller.process(paramMap, model);
	
	 // /WEB-INF/views/new-form.jsp
	 MyView view = viewResolver(viewName);
	
	 view.render(model, request, response);
```

V3에서는 ModelView 객체를 생성하여, 뷰 이름과 모델 데이터를 넣고 반환하고, FrontController는 ModelView 객체에서 model 데이터를 꺼내서 뷰에 전달했다.

 하지만 V4에서는 Controller는 매개변수로 model을 받아 그 안에 필요한 데이터를 바로 추가하고, FrontController는 이 model을 그대로 뷰에 전달하므로, 코드가 더 간편해졌다.

![image](https://github.com/user-attachments/assets/fa84f1bb-d5d9-43d3-8485-819b8fbe4af4)


- 기본적인 구조는 V3와 같다. 대신에 컨트롤러가 **ModelView 를 반환하지 않고, ViewName 만 반환한다.**

## 유연한 컨트룰러1 - V5

만약 어떤 개발자는 ControllerV3 방식으로 개발하고 싶고, 어떤 개발자는 ControllerV4 방식으로 개발하고 싶다면 어떻게 해야할까?

### 어댑터 패턴

어댑터(Adapter) 패턴은 **서로 호환되지 않는 인터페이스**를 가진 객체들이 **함께 작동할 수 있도록** 중간에서 연결해주는 디자인 패턴입니다.

지금까지 우리가 개발한 프론트 컨트롤러는 **한가지 방식**의 컨트롤러 인터페이스만 사용할 수 있다.
ControllerV3 , ControllerV4 는 완전히 다른 인터페이스이다. 따라서 호환이 불가능하다. 이럴 때 사용하는 것이 바로 어댑터이다.
어댑터 패턴을 사용해서 프론트 컨트롤러가 다양한 방식의 컨트롤러를 처리할 수 있도록 변경해보자.

**📁 MyHandlerAdapter**

```java
public interface MyHandlerAdapter {

    boolean supports(Object handler);
    ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException;
}
```

- **boolean supports(Object handler)**
    - handler는 컨트롤러를 말한다.
    - 어댑터가 해당 컨트롤러를 처리할 수 있는지 판단하는 메서드다.
        - 즉, 해당 컨틀룰러가 해당하는 Controller 인터페이스를 구현했는지 확인한다.
- **ModelView handle(HttpServletRequest request, HttpServletResponse response,
Object handler)**
    - supports 메서드로 검증된 핸들러를 해당 인터페이스로 다운 캐스팅 한 후, 모든 요청 파라미터를 paramMap으로 만들어 process 메서드에 전달합니다.
    process 메서드는 비즈니스 로직을 처리한 후, 그 결과를 담은 ModelView 객체를 반환합니다.
    - 이전에는 프론트 컨트롤러가 실제 컨트롤러를 호출했지만 이제는 이 어댑터를 통해서 실제 컨트롤러가 호출된다.

**📁 ControllerV3HandlerAdapter**

```java
public class ControllerV3HandlerAdapter implements MyHandlerAdapter {
    @Override
    public boolean supports(Object handler) {
        return (handler instanceof ControllerV3); 
    }

    @Override
    public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
        ControllerV3 controller = (ControllerV3) handler;

        Map<String, String> paramMap = createParamMap(request);
        ModelView mv = controller.process(paramMap);

        return mv;
    }

    private Map<String, String> createParamMap(HttpServletRequest request) {
        Map<String, String> paramMap = new HashMap<>();
        request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
        return paramMap;
    }
```

**MyHandlerAdapter** 인터페이스를 구현한 클래스로  **ControllerV3를 지원**하는 어댑터이다.

**supports**  

- ControllerV3 을 처리할 수 있는 어댑터인지 확인하고 true 나 flase를 반환한다,
- instanceof: 자바에서 객체가 특정 클래스 또는 인터페이스의 인스턴스인지 여부를 확인하는 연산자입니다. 결과는 true 또는 false로 반환

**handle**

handler를 컨트롤러 V3로 변환한 다음에 V3 형식에 맞도록 호출한다.
supports() 를 통해 ControllerV3 만 지원하기 때문에 타입 변환은 걱정없이 실행해도 된다.
ControllerV3는 ModelView를 반환하므로 그대로 ModelView를 반환하면 된다.

**📁 FrontControllerServletV5**

**컨트롤러(Controller) 핸들러(Handler)**
이전에는 컨트롤러를 직접 매핑해서 사용했다. 그런데 이제는 어댑터를 사용하기 때문에, 컨트롤러 뿐만 아니라 어댑터가 지원하기만 하면, 어떤 것이라도 URL에 매핑해서 사용할 수 있다. 그래서 이름을 컨트롤러에서 더 넒은 범위의 핸들러로 변경했다.

코드를 나눠서 분석해보겠습니다.

**매핑 정보**

```java
private final Map<String, Object> handlerMappingMap = new HashMap<>();
private final List<MyHandlerAdapter> handlerAdapters = new ArrayList<>();
```

매핑 정보의 값이 ControllerV3 , ControllerV4 같은 인터페이스에서 아무 값이나 받을 수 있는 **Object** 로 변경되었다.

**생성자**

```java
public FrontControllerServletV5() {
        initHandlerMappingMap();    // 핸들러 매핑 초기화
        initHandlerAdapters();      // 어댑터 초기화
    }
```

생성자는 핸들러 매핑과 어댑터를 초기화(등록)한다.

```java
private void initHandlerAdapters() {
        handlerAdapters.add(new ControllerV3HandlerAdapter());
    }
private void initHandlerMappingMap() {
    handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
    handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberSaveControllerV3());
    handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());
}
```

**핸들러 매핑**

```java
Object handler = getHandler(request);
private Object getHandler(HttpServletRequest request) {
        String requestURI = request.getRequestURI();
        return handlerMappingMap.get(requestURI);
    }
```

핸들러 매핑 정보인 handlerMappingMap 에서 URL에 매핑된 핸들러(컨트롤러) 객체를 찾아서 반환한다.

**핸들러를 처리할 수 있는 어댑터 조회**

```java
MyHandlerAdapter adapter = getHandlerAdapter(handler);
	for (MyHandlerAdapter adapter : handlerAdapters) {
	    if (adapter.supports(handler)) {
	        return adapter;
	    }
		}
	throw new IllegalArgumentException("handler adapter를 찾을 수 없습니다. handler=" + handler);
	}
```

handler 를 처리할 수 있는 어댑터를 for문을 통해 handlerAdapters Map에서 각 요소를adapter.supports(handler) 를 통해서 찾는다.
handler가 ControllerV3 인터페이스를 구현했다면, ControllerV3HandlerAdapter 객체가 반환된다.

- 현재는 ControllerV3HandlerAdapter밖에 없다.

**어댑터 호출**

```java
ModelView mv = adapter.handle(request, response, handler);
```

어댑터의 handle(request, response, handler) 메서드를 통해 실제 어댑터가 호출된다.
어댑터는 handler(컨트롤러)를 호출하고 그 결과를 어댑터에 맞추어 반환한다. ControllerV3HandlerAdapter 의 경우 어댑터의 모양과 컨트롤러의 모양이 유사해서 변환 로직이 단순하다.

### ⭐ 정리

![image (1)](https://github.com/user-attachments/assets/097b0a40-e2a2-4b93-8e24-d669133f5d26)

클라이언트가 HTTP 요청을 보내면 FrontController가 요청 URL에 맞는 핸들러를 찾고, 해당 핸들러에 맞는 핸들러 어댑터를 사용해 요청을 처리합니다. 비즈니스 로직의 결과를 ModelView로 받아서, 논리적인 뷰 이름을 실제 JSP 경로로 변환하고, JSP로 데이터를 전달하여 최종 응답을 생성합니다.

- **핸들러 어댑터**: 이 어댑터는 다양한 종류의 컨트롤러(핸들러)를 호출할 수 있도록 중간에서 변환 역할을 해줍니다.
**핸들러**: 컨트롤러의 이름을 더 넓은 범위인 핸들러로 변경했다. 그 이유는 이제 어댑터가 있기 때문에 꼭 컨트롤러의 개념 뿐만 아니라 어떠한 것이든 해당하는 종류의 어댑터만 있으면 다 처리할 수 있기 때문이다.

## 유연한 컨트롤러2 - v5

FrontControllerServletV5 에 ControllerV4 기능도 추가해보겠습니다.

**📁 FrontControllerServletV5**

```java
private void initHandlerMappingMap() {
        // V4 추가
        handlerMappingMap.put("/front-controller/v5/v4/members/new-form", new MemberFormControllerV4());
        handlerMappingMap.put("/front-controller/v5/v4/members/save", new MemberSaveControllerV4());
        handlerMappingMap.put("/front-controller/v5/v4/members", new MemberListControllerV4());

    }
    
private void initHandlerAdapters() {
    handlerAdapters.add(new ControllerV4HandlerAdapter());
}
```

핸들러 매핑( handlerMappingMap )에 ControllerV4 를 사용하는 컨트롤러를 추가하고, 해당 컨트롤러를 처리할 수 있는 어댑터인 ControllerV4HandlerAdapter 도 추가했습니다.

**📁 ControllerV4HandlerAdapter**

하나씩 분석해보겠습니다.

```java
@Override
    public boolean supports(Object handler) {
        return (handler instanceof ControllerV4);
    }
```

handler 가 ControllerV4 인 경우에만 처리하는 어댑터이다.

**실행 로직**

```java
ControllerV4 controller = (ControllerV4) handler;
	Map<String, String> paramMap = createParamMap(request);
	HashMap<String, Object> model = new HashMap<>();
	String viewName = controller.process(paramMap, model);
```

handler를 ControllerV4로 케스팅 하고, paramMap, model을 만들어서 해당 컨트롤러를 호출한다.
그리고 viewName을 반환 받는다.

**어댑터 변환**

```java
ModelView mv = new ModelView(viewName);
mv.setModel(model);
return mv;
```

어댑터가 호출하는 ControllerV4 는 뷰의 이름을 반환한다. 그런데 어댑터는 뷰의 이름이 아니라 ModelView 를 만들어서 반환해야 한다. 여기서 어댑터가 꼭 필요한 이유가 나온다.
ControllerV4 는 뷰의 이름을 반환했지만, 어댑터는 이것을 ModelView로 만들어서 형식을 맞추어 반환한다.
