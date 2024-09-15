# 서블릿
---

[spring.start.io](http://spring.start.io) 에서 다음과 같은 설정으로 프로젝트를 생성한다.

**프로젝트 선택**

- Project: Gradle - Groovy Project
- Language: Java
- Spring Boot: 3.x.x

**Project Metadata**

- Group: hello
- Artifact: servlet
- Name: servlet
- Package name: hello.servlet
- Packaging: War (주의!)
- Java: 17 또는 21

**Dependencies**: Spring Web, Lombok

**war를 선택한 이유?**
JSP를 사용하거나 외부 톰캣 같은 애플리케이션 서버에 배포하기 위해서 선택했다.
이 경우 별도로 내장 서버를 설치하지 않고 기존 서버에 애플리케이션을 배포할 수 있다.

**Lombok 라이브러리를 사용할 때, 정상적으로 사용하려면 세 가지 단계가 필요**
dependencies 추가
플러그인 설치
Annotation Processors -> Enable annotation processing활성화

스프링 부트 환경에서 서블릿 등록하고 사용해보자

### ✅ 서블릿이란?

클라이언트의 요청을 처리하고, 그 결과를 반환하는 자바 웹 프로그래밍 기술을 의미한다.

서블릿은 톰캣 같은 웹 애플리케이션 서버를 직접 설치하고,그 위에 서블릿 코드를 클래스 파일로 빌드해서 올린다음, 톰캣 서버를 실행하면 된다. 하지만 이 과정은 매우 번거롭다.
**스프링 부트는 톰캣 서버를 내장하고 있으므로, 톰캣 서버 설치 없이 편리하게 서블릿 코드를 실행할 수 있다.**

### ✅ 스프링 부트 서블릿 환경 구성

**@ServletComponentScan**

메인 코드 클래스 위에 명시하면, 현재 패키지를 포함해서 하위 패키지에 **@WebServlet**, **@WebFilter**, **@WebListener**가 붙은 서블릿 컴포넌트들을 **자동으로 스캔하고 등록**해준다.

- 어찌보면 @ComponentScan이랑 비슷하다. 다만. 대상이 다를 뿐이다.

**📁 HelloServlet.java**

```java
// 서블릿을 정의하는 애노테이션
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {

    @Override // 상속 메서드는 Ctrl + o 단축키를 통해 해당 메서드를 가져올 수 있다.(열쇠키)
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("HelloServlet.service");
        System.out.println("req = " + req);
        System.out.println("resp = " + resp);
    }
}
```

해당 URL을 호출하면, 웹 브라우저에서 HTTP 요청 메시지를 생성하여 서버로 보냅니다. 서버의 서블릿 컨테이너는 이 요청을 받아 **HttpServletRequest**와 **HttpServletResponse** 객체를 생성하여 HelloServlet의 service 메서드에 전달합니다.

**💬 결과 로그**

```java
HelloServlet.service
req = org.apache.catalina.connector.RequestFacade@fa783f6
resp = org.apache.catalina.connector.ResponseFacade@43986399
```

실행 결과에 나타난 **org.apache.catalina.connector**는 **Tomcat 라이브러리**를 의미하며, **RequestFacade**와 **ResponseFacade**는 **HttpServletRequest**와 **HttpServletResponse** 인터페이스의 **Tomcat 구현체**를 나타냅니다. 

HttpServletRequest와 HttpServletResponse는 여러 WAS(Web Application Server)에서 구현하는 **표준 인터페이스**이며, 각 서버는 이를 구현한 구체적인 객체(RequestFacade, ResponseFacade 등)를 사용하여 요청과 응답을 처리합니다.

**HttpServletResponse 사용하여 응답 설정**

```java
resp.setContentType("text/plain"); // MIME 타입을 설정, 서버가 보내는 데이터 형식의 종류
resp.setCharacterEncoding("utf-8"); // 문자 인코딩 방식
resp.getWriter().write("hello " + username); // getWriter는 출력 스트림을 열어주는 역할,
												// .write는 PrintWriter 객체의 메서드로써 클라이언트에게 보낸 데이터 작성
```

MIME 타입의 종류

- text/plain: 일반 텍스트 파일
- text/html: HTML 파일
- application/json: JSON 데이터
- application/xml: XML 데이터

![image](https://github.com/user-attachments/assets/e07bb2be-ea44-4502-8892-23bb47429b75)

Spring Boot에서 애플리케이션을 실행하면, 내장된 Tomcat 서버가 함께 실행된다. Tomcat은 **Servlet 컨테이너 기능을 제공**하며, 애플리케이션 내에서 **Servlet을 처리할 수 있는 환경을 구성**해준다

사용자가 브라우저에서 특정 URL을 호출하면, 브라우저는 이 URL을 기반으로 HTTP 요청 메시지를 만들어 서버에 전송한다.

서버(Tomcat)는 이 요청을 받으면, **HttpServletRequest**와 **HttpServletResponse** 객체를 생성하여 해당 요청을 처리할 준비를 한다. 그 후, 요청된 URL에 매핑된 서블릿 객체를 찾아서, 그 서블릿의 service() 메서드를 호출한다. 이 메서드 내에서 request와 response 객체를 사용하여 요청을 처리하고, 응답을 클라이언트(브라우저)에 전달하게 된다.

![image (1)](https://github.com/user-attachments/assets/460484f1-8c99-4795-a9d1-f96d8717b5dd)

## ✅ HttpServletRequest - 개요

**HttpServletRequest 역할**
HTTP 요청 메시지를 개발자가 직접 파싱해서 사용해도 되지만, 매우 불편하다. 서블릿은 개발자가 HTTP 요청 메시지를 편리하게 사용할 수 있도록 **개발자 대신에 HTTP 요청 메시지를 파싱한다**. 그리고 그 결과를 **HttpServletRequest** 객체에 담아서 제공한다

HTTP 요청 메시지를 통해 클라이언트에서 서버로 데이터를 전달하는 방법 총 3가지 있다.

### ✔️ GET - 쿼리 파라미터

- /url**?username=hello&age=20**
- 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
- 예) 검색, 필터, 페이징등에서 많이 사용하는 방식

### ✔️ POST - HTML Form

- content-type: application/x-www-form-urlencoded
- 메시지 바디에 쿼리 파리미터 형식으로 전달 username=hello&age=20
- 예) 회원 가입, 상품 주문, HTML Form 사용

### ✔️ HTTP message body에 데이터를 직접 담아서 요청

- HTTP API에서 주로 사용, JSON, XML, TEXT
- 데이터 형식은 주로 **JSON** 사용
    - POST, PUT, PATCH

## ✅ HTTP 요청 데이터 - GET 쿼리 파라미터

쿼리 파라미터는 URL에 다음과 같이 ? 를 시작으로 보낼 수 있다. 추가 파라미터는 & 로 구분하면 된다.
**http://localhost:8080/request-param?username=hello&age=20**

**쿼리 파라미터 조회 메서드**

```java
String username = request.getParameter("username"); //단일 파라미터 조회
Enumeration<String> parameterNames = request.getParameterNames(); //파라미터 이름들
모두 조회
Map<String, String[]> parameterMap = request.getParameterMap(); //파라미터를 Map으로
조회
String[] usernames = request.getParameterValues("username"); //복수 파라미터 조회
```

**복수 파라미터에서 단일 파라미터 조회**
username=hello&username=kim 과 같이 파라미터 이름은 하나인데, 값이 중복이면 어떻게 될까?
request.getParameter() 는 하나의 파라미터 이름에 대해서 단 하나의 값만 있을 때 사용해야 한다. 지금처럼 중복일 때는 **request.getParameterValues()** 를 사용해야 한다.
참고로 이렇게 중복일 때 request.getParameter() 를 사용하면 request.getParameterValues() 의 **첫
번째 값을 반환**한다.

## ✅ HTTP 요청 데이터 - POST HTML Form

**특징**

- **content-type**: application/x-www-form-urlencoded
- 메시지 바디에 **쿼리 파리미터 형식**으로 데이터를 전달한다. username=hello&age=20

POST의 HTML Form을 전송하면 웹 브라우저는 다음 형식으로 HTTP 메시지를 만든다. (웹 브라우저 개발자 모드 확인)

- **요청 URL**: http://localhost:8081/request-param
- **content-type**: application/x-www-form-urlencoded
- **message body**: username=hello&age=20

application/x-www-form-urlencoded 형식은 앞서 GET에서 살펴본 쿼리 파라미터 형식과 같다. 따라서 쿼리 파라미터 조회 메서드를 그대로 사용하면 된다.

**content-type**은 HTTP 메시지 바디의 **데이터 형식**을 지정한다.
GET URL 쿼리 파라미터 형식으로 클라이언트에서 서버로 데이터를 전달할 때는 H**TTP 메시지 바디를 사용하지 않기 때문에 content-type이 없다.**
POST HTML Form 형식으로 데이터를 전달하면 HTTP 메시지 바디에 해당 데이터를 포함해서 보내기 때문에 바디에 포함된 데이터가 어떤 형식인지 content-type을 꼭 지정해야 한다. 이렇게 폼으로 데이터를 전송하는 형식을 **application/x-www-form-urlencoded** 라 한다.

## ✅ HTTP 요청 데이터 - API 메시지 바디 - 단순 텍스트

- HTTP message body에 데이터를 직접 담아서 요청
    - HTTP API에서 주로 사용, JSON, XML, TEXT
    - 데이터 형식은 **주로 JSON 사용**
    - POST, PUT, PATCH

📁 **RequestBodyStringServlet.java**

```java
@WebServlet(name = "requestBodyStringServlet", urlPatterns = "/request-body-string")
public class RequestBodyStringServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        System.out.println("messageBody = " + messageBody);
        response.getWriter().write("ok");
    }
```

**request.getInputStream();**

- 요청에는 헤더와 본문(body)이 포함될 수 있는데, 요청 본문 데이터를 읽기 위해 사용되는 스트림

**StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);**

- StreamUtils.copyToString는 스프링 프레임워크에서 제공하는 유틸리티 메서드로, **스트림 데이터를 간편하게 문자열로 변환**할 수 있게 해줍니다.

**response.getWriter().write("ok");**

- response.getWriter()**는 PrintWriter 객체를 반환합니다.
    - 서블릿에서 **텍스트 형식의 데이터를 응답으로 클라이언트에게 전송**할 때 사용됩니다.
- **.write() 클라이언트에게 전송할 데이터를 작성**합니다.

정리하면, 서블릿(Servlet)에서 클라이언트가 보낸 요청의 본문(body)을 읽고, 이를 콘솔에 출력한 후, 클라이언트에게 "ok"라는 응답을 보내는 과정을 처리한다.

## ✅ HTTP 요청 데이터 - API 메시지 바디 - JSON

이번에는 HTTP API에서 주로 사용하는 JSON 형식으로 데이터를 전달해보자

**📁 RequestBodyJsonServlet** 

```java
@WebServlet(name = "requestBodyJsonServlet", urlPatterns = "/request-body-json")
public class RequestBodyJsonServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        System.out.println("messageBody = " + messageBody);
    }
```

**JSON 형식 전송**

- **POST** http://localhost:8080/request-body-json
- **content-type**: application/json
- **message body**: {"username": "hello", "age": 20}
- **결과**: messageBody = {"username": "hello", "age": 20}

스프링에서는 **Jackson** 라이브러리를 사용하여 **JSON 데이터를 Java 객체로 변환(역직렬화)**할 수 있다.

```java
private ObjectMapper objectMapper = new ObjectMapper();
HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);

System.out.println("helloData = " + helloData.getUsername());
System.out.println("helloData.getAge() = " + helloData.getAge());
```

**ObjectMapper**: Jackson에서 제공하는 클래스이며, **JSON 데이터를 Java 객체로 변환(역직렬화)**하거나, **Java 객체를 JSON으로 변환(직렬화**)할 때 사용됩니다.
**readValue()**: 클라이언트로부터 받은 JSON 데이터를 HelloData.class라는 Java 클래스에 역직렬화하여 해당 객체로 변환합니다.
**📁 실행 결과**

```java
helloData = hello
helloData.getAge() = 20
```

## ✅ HttpServletResponse - 기본 사용법

**HttpServletResponse 역할**

**HTTP 응답 메시지 생성**

- HTTP 응답코드 지정
- 헤더 생성
- 바디 생성

**편의 기능 제공**

- Content-Type, 쿠키, Redirect

### ✔️ HttpServletResponse - 기본 사용법

**📁 ResponseHeaderServlet.java**

```java
@WebServlet(name = "responseHeaderServlet", urlPatterns = "/response-header")
public class ResponseHeaderServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // status-line
        response.setStatus(HttpServletResponse.SC_OK);

        //[response-headers]
        response.setHeader("Content-Type", "text/plain;");
        response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate");
        response.setHeader("Pragma", "no-cache");
        response.setHeader("my-header", "hello");

        PrintWriter writer = response.getWriter();
        writer.println("ok");
    }
```

**url 호출 시 Http 헤더 정보**

![image (2)](https://github.com/user-attachments/assets/1a8959c3-02c6-4d79-ba91-4236ceb7239a)

**Content 편의 메서드**

```java
private void content(HttpServletResponse response) {
 //Content-Type: text/plain;charset=utf-8
 //Content-Length: 2
 //response.setHeader("Content-Type", "text/plain;charset=utf-8");
 response.setContentType("text/plain");
 response.setCharacterEncoding("utf-8");
 //response.setContentLength(2); //(생략시 자동 생성)
}
```

**쿠키 편의 메서드**

```java
private void cookie(HttpServletResponse response) {
 //Set-Cookie: myCookie=good; Max-Age=600;
 //response.setHeader("Set-Cookie", "myCookie=good; Max-Age=600");
 Cookie cookie = new Cookie("myCookie", "good");
 cookie.setMaxAge(600); //600초
 response.addCookie(cookie);
}
```

**redirect 편의 메서드**

```java
private void redirect(HttpServletResponse response) throws IOException {
 //Status Code 302
 //Location: /basic/hello-form.html
 //response.setStatus(HttpServletResponse.SC_FOUND); //302
 //response.setHeader("Location", "/basic/hello-form.html");
 response.sendRedirect("/basic/hello-form.html");
 }
```

## ✅ HTTP 응답 데이터 - 단순 텍스트, HTML

HTTP 응답 메시지는 주로 다음 내용을 담아서 전달한다.

- 단순 텍스트 응답
    - 앞에서 살펴봄 ( writer.println("ok"); )
- HTML 응답
- HTTP API - MessageBody JSON 응답

**HttpServletResponse - HTML 응답**

**📁 ResponseHtmlServlet.java**

```java
@WebServlet(name = "responseHtmlServlet", urlPatterns = "./response-html")
public class ResponseHtmlServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // Content-Type: text/html;charset=utf-8
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");

        PrintWriter writer = response.getWriter();
        writer.println("<html>");
        writer.println("<body>");
        writer.println("    <div>안녕?<div>");
        writer.println("</body>");
        writer.println("<html>");
    }
```

- HTTP 응답으로 HTML을 반환할 때는 content-type을 text/html 로 지정해야 한다.

**페이지 소스 보기**

```java
<html>
<body>
    <div>안녕?<div>
</body>
<html>
```

## ✅ HTTP 응답 데이터 - API JSON

**📁 ResponseJsonServlet.java**

```java
@WebServlet(name = "responseJsonServlet", urlPatterns = "/response-json")
public class ResponseJsonServlet extends HttpServlet {

    private ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // Content-Type: application/json
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");

        HelloData helloData = new HelloData();
        helloData.setUsername("kim");
        helloData.setAge(20);

        // {"username" : "kim", "age":20}
        String result = objectMapper.writeValueAsString(helloData);
        response.getWriter().write(result);
    }
```

HTTP 응답으로 JSON을 반환할 때는 content-type을 application/json 로 지정해야 한다.
Jackson 라이브러리가 제공하는 objectMapper.writeValueAsString() 를 사용하면 객체를 JSON 문자로 변경할 수 있다.

**url 호출 결과**

```java
{"username":"kim","age":20}
```

![image (3)](https://github.com/user-attachments/assets/a9910aff-7961-46e3-acfa-b7d8c977f47b)
