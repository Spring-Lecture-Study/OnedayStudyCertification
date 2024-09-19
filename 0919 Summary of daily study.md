# 9월 19일 정리
---
## JSP

**자바를 웹 서버에서 쉽게 사용**하기 위한 기술로, **HTML 코드와 자바 코드를 혼합**할 수 있는 파일 형식입니다.

JSP 파일은 **서버에서 실행**되며, 내부적으로 **서블릿 코드로 변환**되어 실행됩니다. 이를 통해 **동적인 웹 페이지**를 쉽게 생성할 수 있습니다.

**JSP 라이브러리 추가**
JSP를 사용하려면 먼저 다음 라이브러리를 추가해야 한다

```java
dependencies
	//JSP 추가 시작
	implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
	implementation 'jakarta.servlet:jakarta.servlet-api' //스프링부트 3.0 이상
	implementation 'jakarta.servlet.jsp.jstl:jakarta.servlet.jsp.jstl-api' //스프링부트 3.0 이상
	implementation 'org.glassfish.web:jakarta.servlet.jsp.jstl' //스프링부트 3.0 이상
	//JSP 추가 끝
```

### JSP 문법

JSP는 자바 코드를 그대로 다 사용할 수 있다

<% ~~ %>

- 이 부분에는 **자바 코드를 입력**할 수 있다.

<%= ~~ %>

- 이 부분에는 **자바 코드를 출**력할 수 있다.

<%@ page import="hello.servlet.domain.member.MemberRepository" %>

- 자바의 import 문과 같다.

JSP 문법을 보면 서블릿 코드와 비슷하지만, HTML을 중심으로 하고, 자바 코드를 부분부분 입력해주었다. <% ~ %> 를 사용해서 HTML 중간에 자바 코드를 출력하고 있다

## 서블릿과 JSP의 한계

**서블릿의 한계**:

- HTML과 자바 코드가 혼합되어 뷰(View) 생성이 복잡하고 유지보수가 어려움.

**JSP의 한계**

- 자바 코드, 비즈니스 로직, 데이터 조회 로직 등이 모두 JSP에 혼합되어, JSP가 너무 많은 역할을 하게 됨. 이로 인해 코드가 복잡해지고, 유지보수가 어려워짐.

**궁금한점**

```java
%
MemberRepository memberRepository = MemberRepository.getInstance();
List<Member> members = memberRepository.findAll();

%>
```

여기에서 private를 쓰면 안되는이유?

JSP에서 <% %> 블록 내에서는 지역 변수를 선언하거나 코드를 실행하는데, private은 클래스 필드나 메서드를 정의할 때 사용하는 접근 제어자로, 지역 변수에는 적용할 수 없기 때문에 사용하면 안 된다.

servlet이나 JSP를 사용하여 개발하면 다음과 같은 단점이 있다.

### 너무 많은 역할

하나의 서블릿이나 JSP가 **비즈니스 로직**과 **뷰 렌더링**을 모두 처리하면, 코드가 복잡해져 **유지보수**가 어렵습니다. HTML이나 자바 코드를 수정할 때, 서로 관련 없는 부분까지 함께 수정해야 하는 문제가 발생한다.

### 변경의 라이프 사이클

**UI 수정**과 **비즈니스 로직 수정**은 서로 다른 변경 주기를 가지고 있다. 그러나 서블릿이나 JSP는 이 둘을 하나의 파일로 처리하기 때문에, 수정할 때 불필요한 부분까지 영향을 미칠 가능성이 커진다.

### 기능 특화

특히 JSP 같은 뷰 템플릿은 화면을 렌더링 하는데 최적화 되어 있기 때문에 이 부분의 업무만 담당하는 것이 가장 효과적이다.

### Model View Controller

MVC 패턴은 지금까지 학습한 것 처럼 하나의 서블릿이나, JSP로 처리하던 것을 **컨트롤러(Controller)와 뷰(View)**라는 영역으로 서로 역할을 나눈 것을 말한다. 웹 애플리케이션은 보통 이 MVC 패턴을 사용한다.

**컨트롤러**: HTTP 요청을 받아서 파라미터를 검증하고, 비즈니스 로직을 실행한다. 그리고 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다.
**모델**: 뷰에 출력할 데이터를 담아둔다. 뷰가 필요한 데이터를 모두 모델에 담아서 전달해주는 덕분에 뷰는 비즈니스 로직이나 데이터 접근을 몰라도 되고, 화면을 렌더링 하는 일에 집중할 수 있다.
**뷰**: 모델에 담겨있는 데이터를 사용해서 화면을 그리는 일에 집중한다. 여기서는 HTML을 생성하는 부분을 말한다.

**참고**

> 컨트롤러에 비즈니스 로직을 둘 수도 있지만, 이렇게 되면 컨트롤러가 너무 많은 역할을 담당한다. 그래서 일반적으로 비즈니스 로직은 **서비스(Service)**라는 계층을 별도로 만들어서 처리한다. 그리고 컨트롤러는 비즈니스 로직이 있는 **서비스를 호출하는 역할을 담당**한다. 참고로 비즈니스 로직을 변경하면 비즈니스 로직을 호출하는 컨트롤러의 코드도 변경될 수 있다.
> 

MVC 패턴 이전, MVC 1, MVC 2 패턴은 다음 그림과 같다.

![image](https://github.com/user-attachments/assets/31e15ea8-79a9-4f44-a8ea-c0663a874cbb)

![image (1)](https://github.com/user-attachments/assets/ce62af45-f407-4bae-896c-9cde66f68d70)

- **MVC1**: 컨트롤러에서 비즈니스 로직을 직접 처리.

![image (2)](https://github.com/user-attachments/assets/96f7da5e-505b-40ce-abcb-fbd052a9252b)

- **MVC2**: 컨트롤러는 비즈니스 로직을 **서비스 계층**에서 처리하고, 호출만 함으로써 역할 분리를 더 명확히 함.

### MVC

**dispatcher.forward()** 

- 서버 내부에서 다른 서블릿이나 JSP로 요청을 **포워딩(전달)**하는 기능입니다.
- **클라이언트에게 새로운 요청을 보내지 않고**, 서버 내(다른 서블릿이나 JSP)에서 **다시 호출**이 발생하며, **요청과 응답 객체**가 그대로 전달됩니다.

**/WEB-INF**

- **/WEB-INF** 폴더에 있는 JSP는 외부에서 직접 접근할 수 없다.
- JSP는 항상 **컨트롤러를 통해서만 호출**되도록 설정하여, **보안**과 **구조적 관리**를 강화

**redirect vs forward**

**Redirect**:
서버가 클라이언트(브라우저)에 응답을 보내고, 클라이언트가 새로운 요청을 보냅니다.
URL이 변경되며, 클라이언트는 이를 인지할 수 있습니다.

**Forward**:

- **서버 내부에서** 다른 서블릿이나 JSP로 **요청을 전달**합니다.
- **URL이 변경되지 않으며**, 클라이언트는 전혀 이를 **인지하지 못합니다**.

⭐ **MVC 덕분에 컨트롤러 로직과 뷰 로직을 확실하게 분리한 것을 확인할 수 있다. 향후 화면에 수정이 발생하면 뷰 로직만 변경하면 된다.**

## MVC 패턴 - 한계

## MVC 컨트롤러의 단점

### 포워드 중복

View로 이동하는 코드가 항상 중복 호출되어야 한다. 물론 이 부분을 메서드로 공통화해도 되지만, 해당 메서드도 항상직접 호출해야 한다. 

```java
RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
dispatcher.forward(request, response);
```

### **ViewPath에 중복**

```java
String viewPath = "/WEB-INF/views/new-form.jsp";
```

**prefix**: /WEB-INF/views/

- **preifx**:뷰 파일 경로의 **앞부분**

**suffix:** .jsp

- 뷰 파일 경로의 **확장자**

그리고 만약 jsp가 아닌 thymeleaf 같은 다른 뷰로 변경한다면 전체 코드를 다 변경해야 한다.

### 사용하지 않는 코드

response는 현재 코드에서 사용되지 않는다.

### 공통 처리가 어렵다.

기능이 복잡해질 수 록 컨트롤러에서 공통으로 처리해야 하는 부분이 점점 더 많이 증가할 것이다. 단순히 공통 기능을 메서드로 뽑으면 될 것 같지만, 결과적으로 해당 메서드를 항상 호출해야 하고, 실수로 호출하지 않으면 문제가 될 것이다. 그리고 호출하는 것 자체도 중복이다.
