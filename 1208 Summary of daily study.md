# 타임리프 - 기본 기능
## 타임리프 특징

- **서버 사이드 HTML 렌더링 (SSR)**
- **네츄럴 템플릿**
- **스프링 통합 지원**

### **서버 사이드 HTML 렌더링 (SSR)**

타임리프는 백엔드 서버에서 HTML을 **동적**으로 렌더링 하는 용도로 사용된다.

### **네츄럴 템플릿**

타임리프는 순수 HTML 구조를 유지하는 **네츄럴 템플릿(Natural Templates)**을 제공하여, 브라우저에서 직접 열어도 HTML 결과를 확인할 수 있고, 서버를 통해 동적으로 렌더링하면 동적 데이터가 반영된 HTML을 출력할 수 있습니다. 반면, JSP 파일은 브라우저에서 직접 열면 깨진 형태로 보입니다.

### **스프링 통합 지원**

타임리프는 스프링과 자연스럽게 통합되고, 스프링의 다양한 기능을 편리하게 사용할 수 있게 지원한다. 

타임리프를 사용하려면 다음 선언을 하면 된다.

```python
<html xmlns:th="http://www.thymeleaf.org">
```

해당 구문은 thymeleaf를 사용하기 위해서 네임스페이스를 선언한 것이다.

**네임스페이스**는 XML 기반 문서에서 동일한 이름의 태그나 속성이 사용될 경우, 이름 충돌을 방지하기 위해 도입된 식별자 체계이다.

- 이를 통해 서로 다른 기술이나 라이브러리에서 사용하는 태그와 속성을 명확히 구분할 수 있다.

## 기본 표현식

타임리프는 다음과 같은 기본 표현식들을 제공한다. 

```python
간단한 표현:
◦ 변수 표현식: ${...}
◦ 선택 변수 표현식: *{...}
◦ 메시지 표현식: #{...}
◦ 링크 URL 표현식: @{...}
◦ 조각 표현식: ~{...}
• 리터럴
◦ 텍스트: 'one text', 'Another one!',…
◦ 숫자: 0, 34, 3.0, 12.3,…
◦ 불린: true, false
◦ 널: null
◦ 리터럴 토큰: one, sometext, main,…
• 문자 연산:
◦ 문자 합치기: +
◦ 리터럴 대체: |The name is ${name}|
• 산술 연산:
◦ Binary operators: +, -, *, /, %
◦ Minus sign (unary operator): -
• 불린 연산:
◦ Binary operators: and, or◦ Boolean negation (unary operator): !, not
• 비교와 동등:
◦ 비교: >, <, >=, <= (gt, lt, ge, le)
◦ 동등 연산: ==, != (eq, ne)
• 조건 연산:
◦ If-then: (if) ? (then)
◦ If-then-else: (if) ? (then) : (else)
◦ Default: (value) ?: (defaultvalue)
• 특별한 토큰:
◦ No-Operation: _
```

# 텍스트 - text, utext
## 텍스트 출력 기능

타임리프는 HTML에서 데이터를 출력하는 두 가지 방식으로 **서버 데이터를 HTML에 반영**할 수 있습니다.

### th:text (텍스트 대체)

HTML 태그의 내용을 완전히 대체하여 서버 데이터를 출력합니다.

예시

```java
<span th:text="${data}">기존 텍스트</span>
```

랜더링 결과

```java
<span>서버에서 받은 데이터</span>
```

### [[...]] (텍스트 삽입)

- HTML 콘텐츠 영역 내에서 **텍스트와 태그를 혼합**하여 서버 데이터를 삽입합니다.

예시:

```html
<p>Welcome, [[${username}]]! Today is [[${date}]].</p>
```

렌더링 결과:

```html
<p>Welcome, Alice! Today is 2023-05-05.</p>
```

**차이점**

- th:text: 태그의 내용을 완전히 대체.
- [[...]]: HTML 콘텐츠와 데이터를 혼합하여 사용 가능.

**요약**

**th:text**는 서버 데이터를 사용해 태그 내용을 전적으로 교체합니다.
**[[...]]**는 HTML 태그와 텍스트를 조합하여 서버 데이터를 삽입하는 데 적합합니다.

## Escape

HTML 문서는 < , > 같은 특수 문자를 기반으로 정의된다. 따라서 뷰 템플릿으로 HTML 화면을 생성할 때는 출력하는 데이터에 이러한 특수 문자가 있는 것을 주의해서 사용해야 한다.

예시

<b> 테그를 사용해서 Spring!이라는 단어가 진하게 나오도록 해보자.

```html
"Hello <b>Spring!</b>"

웹 브라우저: Hello <b>Spring!</b>
소스보기: Hello &lt;b&gt;Spring!&lt;/b&gt;
```

개발자가 의도한 것은 <b> 가 있으면 해당 부분을 강조하는 것이 목적이었다. 그런데 <b> 테그가 그대로 나온다. 소스보기를 하면 < 부분이 &lt;로 변경된 것을 확인할 수 있다.

- 이는 브라우저가 <br> 태그를 단순히 문자열로 처리하게 만들기 때문에 화면 상에서 <br> 자체가 출력되는 것이다.

### **HTML 엔티티**

웹 브라우저는 < 를 HTML 테그의 시작으로 인식한다. 따라서 < 를 테그의 시작이 아니라 문자로 표현할 수 있는 방법이 필요한데, 이것을 HTML 엔티티라 한다. 그리고 이렇게 HTML에서 사용하는 특수 문자를 HTML 엔티티로 변경하는 것을 **이스케이프(escape)**라 한다. 그리고 타임리프가 제공하는 th:text , [[...]] 는 기본적으로 이스케이프 (escape)를 제공한다.

```html
< → &lt;
> → &gt;
```

### Unescape

타임리프는 다음 두 기능을 제공한다.

**th:utext**

**이스케이프 해제** 기능을 수행합니다. 즉, HTML 코드가 포함된 문자열이 있으면, 그 문자열을 **HTML 태그로 해석**하여 실제 HTML로 렌더링합니다.

```html
<span th:utext="${data}"></span>
```

data 값이 "<b>Hello</b>"라면, 출력은 다음과 같다.

```html
<span><b>Hello</b></span>
브라우저에서 보면,
**Hello**
```

**[[...]]**

**동적 텍스트를 HTML 문서 내에 삽입**하는 데 사용됩니다. 만약 해당 값에 HTML 태그가 포함되어 있으면, 이를 **HTML 태그로 해석**하여 실제로 렌더링합니다.

```html
<div>
  컨텐츠 안에서 직접 출력하기 = [[${data}]]
</div>
data 값이 "<b>Hello</b>"라면, 출력은 다음과 같다,
<div>컨텐츠 안에서 직접 출력하기 = <b>Hello</b></div>
브라우저에서 보면,
컨텐츠 안에서 직접 출력하기 = **Hello**
```

### 정리

HTML 태그를 동작시키고 싶다면 Unescape를, 태그나 특수 문자를 문자 그대로 출력하고 싶다면 Escape를 사용합니다.

# 변수 - SpringEL
타임리프에서 변수를 사용할 때는 변수 표현식을 사용한다

```html
변수 표현식 : ${...}
```

그리고 이 변수 표현식에는 스프링 EL이라는 스프링이 제공하는 표현식을 사용할 수 있다.

**BasicController 추가**

```java
@GetMapping("/variable")
    public String variable(Model model) {
        User userA = new User("userA", 10);
        User userB = new User("userB", 20);

        List<User> list = new ArrayList<>();
        list.add(userA);
        list.add(userB);

        Map<String, User> map = new HashMap<>();
        map.put("userA", userA);
        map.put("userB", userB);

        model.addAttribute("user", userA);
        model.addAttribute("users", list);
        model.addAttribute("userMap", map);

        return "basic/variable";
    }

    @Data
    static class User {
        private String username;
        private int age;

        public User(String username, int age) {
            this.username = username;
            this.age = age;
        }
    }
```

**basic/variable.html** 추가

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>SpringEL 표현식</h1>
<ul>Object
    <li>${user.username} = <span th:text="${user.username}"></span></li>
    <li>${user['username']} = <span th:text="${user['username']}"></span></li>
    <li>${user.getUsername()} = <span th:text="${user.getUsername()}"></span></li>
</ul>
<ul>List
    <li>${users[0].username} = <span th:text="${users[0].username}"></span></li>
    <li>${users[0]['username']} = <span th:text="${users[0]['username']}"></span></li>
    <li>${users[0].getUsername()} = <span th:text="${users[0].getUsername()}"></span></li>
</ul>
<ul>Map
    <li>${userMap['userA'].username} = <span th:text="${userMap['userA'].username}"></span></li>
    <li>${userMap['userA']['username']} = <span th:text="${userMap['userA']['username']}"></span></li>
    <li>${userMap['userA'].getUsername()} = <span th:text="${userMap['userA'].getUsername()}"></span></li>
</ul>

<h1>지역 변수 - (th:with)</h1>
<div th:with="first=${users[0]}">
    <p>처음 사람의 이름은 <span th:text="${first.username}"></span></p>
</div>

</body>
</html>
```

### **SpringEL 다양한 표현식 사용**

**Object**
user.username : user의 username을 프로퍼티 접근 user.getUsername()
user['username'] : 위와 같음 user.getUsername()
user.getUsername() : user의 getUsername() 을 직접 호출
**List**
users[0].username : List에서 첫 번째 회원을 찾고 username 프로퍼티 접근
list.get(0).getUsername()
users[0]['username'] : 위와 같음
users[0].getUsername() : List에서 첫 번째 회원을 찾고 메서드 직접 호출
**Map**
userMap['userA'].username : Map에서 userA를 찾고, username 프로퍼티 접근
map.get("userA").getUsername()
userMap['userA']['username'] : 위와 같음
userMap['userA'].getUsername() : Map에서 userA를 찾고 메서드 직접 호출

### 지역 변수 선언

**th:with** 를 사용하면 지역 변수를 선언해서 사용할 수 있다. 지역 변수는 선언한 테그 안에서만 사용할 수 있다.

```html
<h1>지역 변수 - (th:with)</h1>
<div th:with="first=${users[0]}">
		<p>처음 사람의 이름은 <span th:text="${first.username}"></span></p>
</div>
```

다음과 같이 출력된다.

![image](https://github.com/user-attachments/assets/37cf6c1d-bb1b-440a-bf20-21390d244f8e)

# 기본 객체들
타임리프는 기본 객체들을 제공한다.
${#request} - 스프링 부트 3.0부터 제공하지 않는다.
${#response} - 스프링 부트 3.0부터 제공하지 않는다.
${#session} - 스프링 부트 3.0부터 제공하지 않는다.
${#servletContext} - 스프링 부트 3.0부터 제공하지 않는다.
${#locale}

- Spring Boot 3.0 이후에는 HttpServletRequest, HttpServletResponse, HttpSession, ServletContext 등을 직접 컨트롤러에서 주입받아 처리할 수 있습니다. 예를 들어, 아래와 같이 메서드에서 직접 주입받아 사용할 수 있습니다.

**BasicController 추가**

```java
@GetMapping("/basic-objects")
    public String basicObjects(Model model, HttpServletRequest request,
                               HttpServletResponse response, HttpSession session) {
        session.setAttribute("sessionData", "Hello Session");
        model.addAttribute("request", request);
        model.addAttribute("response", response);
        model.addAttribute("servletContext", request.getServletContext());
        return "basic/basic-objects";
    }

    @Component("helloBean")
    static class HelloBean {
        public String hello(String data) {
            return "Hello " + data;
        }
    }
```

**basic-objects.html**

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>식 기본 객체 (Expression Basic Objects)</h1>
<ul>
    <li>request = <span th:text="${request}"></span></li>
    <li>response = <span th:text="${response}"></span></li>
    <li>session = <span th:text="${session}"></span></li>
    <li>servletContext = <span th:text="${servletContext}"></span></li>
    <li>locale = <span th:text="${#locale}"></span></li>
</ul>
<h1>편의 객체</h1>
<ul>
    <li>Request Parameter = <span th:text="${param.paramData}"></span></li>
    <li>session = <span th:text="${session.sessionData}"></span></li>
    <li>spring bean = <span th:text="${@helloBean.hello('Spring!')}"></span></li>
</ul>
</body>
</html>
```

- 편의 객체도 제공한다.

```html
HTTP 요청 파라미터 접근: param
예) ${param.paramData}
HTTP 세션 접근: session
예) ${session.sessionData}
스프링 빈 접근: @
예) ${@helloBean.hello('Spring!')}
```

**출력 화면**

![image (1)](https://github.com/user-attachments/assets/29cc68e5-75b7-46f6-9c24-99faddbc9cf9)

#유틸리티 객채와 날짜
타임리프는 문자, 숫자, 날짜, URI등을 편리하게 다루는 다양한 유틸리티 객체들을 제공한다.

### 타임리프 유틸리티 객체들

#message : 메시지, 국제화 처리
#uris : URI 이스케이프 지원
#dates : java.util.Date 서식 지원
#calendars : java.util.Calendar 서식 지원
#temporals : 자바8 날짜 서식 지원
#numbers : 숫자 서식 지원
#strings : 문자 관련 편의 기능
#objects : 객체 관련 기능 제공
#bools : boolean 관련 기능 제공
#arrays : 배열 관련 기능 제공
#lists , #sets , #maps : 컬렉션 관련 기능 제공

#ids : 아이디 처리 관련 기능 제공

### 자바8 날짜

타임리프에서 자바8 날짜인 LocalDate , LocalDateTime , Instant 를 사용하려면 추가 라이브러리가 필요하다. 스프링 부트 타임리프를 사용하면 해당 라이브러리가 자동으로 추가되고 통합된다.
**타임리프 자바8 날짜 지원 라이브러리**

```html
thymeleaf-extras-java8time
```

- 스프링 부트 3.2 이상을 사용한다면, 타임리프 자바8 날짜 지원 라이브러리가 이미 포함되어 있다. 따라서 별도로 포함하지 않아도 된다.

**BasicController 추가**

```java
@GetMapping("/date")
    public String date(Model model) {
        model.addAttribute("localDateTime", LocalDateTime.now());
        return "basic/date";
    }
```

### data.html 추가

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>LocalDateTime</h1>
<ul>
    <li>default = <span th:text="${localDateTime}"></span></li>
    <li>yyyy-MM-dd HH:mm:ss = <span th:text="${#temporals.format(localDateTime,
'yyyy-MM-dd HH:mm:ss')}"></span></li>
</ul>

<h1>LocalDateTime - Utils</h1>
<ul>
    <li>${#temporals.day(localDateTime)} = <span th:text="${#temporals.day(localDateTime)}"></span></li>
    <li>${#temporals.month(localDateTime)} = <span th:text="${#temporals.month(localDateTime)}"></span></li>
    <li>${#temporals.monthName(localDateTime)} = <span th:text="${#temporals.monthName(localDateTime)}"></span></li>
    <li>${#temporals.monthNameShort(localDateTime)} = <span th:text="${#temporals.monthNameShort(localDateTime)}"></span></li>
    <li>${#temporals.year(localDateTime)} = <span th:text="${#temporals.year(localDateTime)}"></span></li>
    <li>${#temporals.dayOfWeek(localDateTime)} = <span th:text="${#temporals.dayOfWeek(localDateTime)}"></span></li>
    <li>${#temporals.dayOfWeekName(localDateTime)} = <span th:text="${#temporals.dayOfWeekName(localDateTime)}"></span></li>
    <li>${#temporals.dayOfWeekNameShort(localDateTime)} = <span th:text="${#temporals.dayOfWeekNameShort(localDateTime)}"></span></li>
    <li>${#temporals.hour(localDateTime)} = <span th:text="${#temporals.hour(localDateTime)}"></span></li>
    <li>${#temporals.minute(localDateTime)} = <span th:text="${#temporals.minute(localDateTime)}"></span></li>
    <li>${#temporals.second(localDateTime)} = <span th:text="${#temporals.second(localDateTime)}"></span></li>
    <li>${#temporals.nanosecond(localDateTime)} = <span th:text="${#temporals.nanosecond(localDateTime)}"></span></li>
</ul></body>
</html>
```

### 결과 화면

![image](https://github.com/user-attachments/assets/4cfb5d37-4c6f-4382-84ce-a687111a0285)

# URL 링크
타임리프에서 URL을 생성할 때는 **@{...} 문법**을 사용하면 된다.

**BasicController 추가**

```java
@GetMapping("/link")
    public String link(Model model) {
        model.addAttribute("param1", "data1");
        model.addAttribute("param2", "data2");
        return "basic/link";
    }
```

**/resources/templates/basic/link.html**

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>URL 링크</h1>
<ul>
    <li><a th:href="@{/hello}">basic url</a></li>
    <li><a th:href="@{/hello(param1=${param1}, param2=${param2})}">hello query param</a></li>
    <li><a th:href="@{/hello/{param1}/{param2}(param1=${param1}, param2=${param2})}">path variable</a></li>
    <li><a th:href="@{/hello/{param1}(param1=${param1}, param2=${param2})}">path variable + query parameter</a></li>
</ul>
</body>
</html>
```

**단순한 URL**

```java
@{/hello} 
```

- → /hello

**쿼리 파라미터**

```java
@{/hello(param1=${param1}, param2=${param2})}
```

- → /hello?param1=data1&param2=data2
- () 에 있는 부분은 쿼리 파라미터로 처리된다.

**경로 변수**

```java
@{/hello/{param1}/{param2}(param1=${param1}, param2=${param2})}
```

- → /hello/data1/data2
- URL 경로상에 변수가 있으면 () 부분은 **경로 변수**로 처리된다.
- 파라미터가 여러 개라면 자동으로 &가 추가된다.

**경로 변수 + 쿼리 파라미터**

```java
@{/hello/{param1}(param1=${param1}, param2=${param2})}
```

- → /hello/data1?param2=data2
- 경로 변수와 쿼리 파라미터를 함께 사용할 수 있다.

상대경로, 절대경로, 프로토콜 기준을 표현할 수 도 있다.

- /hello : 절대 경로
- hello : 상대 경로

# 리터럴
### Literals

리터럴은 소스 코드상에 고정된 값을 말하는 용어이다.
예를 들어서 다음 코드에서 "Hello" 는 문자 리터럴, 10 , 20 는 숫자 리터럴이다

```java
String a = "Hello"
int a = 10 * 20
```

타임리프는 다음과 같은 리터럴이 있다.

- 문자: 'hello'
- 숫자: 10
- 불린: true , false
- null: null

타임리프에서 문자 리터럴은 항상 **' (작은 따옴표)**로 감싸야 한다.

```java
<span th:text="'hello'">
```

그런데 공백 없이 쭉 이어진다면 하나의 의미있는 토큰으로 인지해 서 다음과 같이 작은 따옴표를 생략할 수 있다.

룰: A-Z , a-z , 0-9 , [] , . , - , _

```java
<span th:text="hello">
```

**오류**

```java
<span th:text="hello world!"></span>
```

문자 리터럴은 원칙상 **'** 로 감싸야 한다. 중간에 공백이 있어서 하나의 의미있는 토큰으로도 인식되지 않는다.

**수정**

```java
<span th:text="'hello world!'"></span>
```

이렇게 **'** 로 감싸면 정상 동작한다.

**BasicController 추가**

```java
@GetMapping("/literal")
    public String literal(Model model) {
        model.addAttribute("data", "Spring!");
        return "basic/literal";
    }
```

**/literal.html 추가**

```java
<!DOCTYPE html><html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>리터럴</h1>
<ul>
    <!--주의! 다음 주석을 풀면 예외가 발생함-->
    <!-- <li>"hello world!" = <span th:text="hello world!"></span></li>-->
    <li>'hello' + ' world!' = <span th:text="'hello' + ' world!'"></span></li>
    <li>'hello world!' = <span th:text="'hello world!'"></span></li>
    <li>'hello ' + ${data} = <span th:text="'hello ' + ${data}"></span></li>
    <li>리터럴 대체 |hello ${data}| = <span th:text="|hello ${data}|"></span></li>
</ul>
</body>
</html>
```

**결과 출력**

![image (1)](https://github.com/user-attachments/assets/526fa361-7f65-4c4d-93c8-cd4fabbd9e72)

**리터럴 대체(Literal substitutions)**

```java
<span th:text="|hello ${data}|">
```

- 템플릿 내에서 문자열을 결합할 때 유용하게 사용되는 기능이다.
- | | 구문을 사용하여, 템플릿에서 변수나 값을 문자열 리터럴과 쉽게 결합할 수 있다.
- data 변수를 텍스트로 삽입하면서, "hello "와 " "를 문자열 리터럴로 결합한다.

만약 name 변수에 "Spring"이라는 값이 할당되어 있으면, 출력은 다음과 같다.

```java
<span>Hello Spring!</span>
```

# 연산
타임리프 연산은 자바와 크게 다르지 않다. HTML안에서 사용하기 때문에 HTML 엔티티를 사용하는 부분만 주의해야 한다.

**BasicController 추가**

```java
@GetMapping("/operation")
    public String operation(Model model) {
        model.addAttribute("nullData", null);
        model.addAttribute("data", "Spring!");
        return "basic/operation";
    }
```

**operation.html 추가**

```java
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<ul>
    <li>산술 연산
        <ul>
            <li>10 + 2 = <span th:text="10 + 2"></span></li>
            <li>10 % 2 == 0 = <span th:text="10 % 2 == 0"></span></li>
        </ul>
    </li>
    <li>비교 연산
        <ul>
            <li>1 > 10 = <span th:text="1 &gt; 10"></span></li>
            <li>1 gt 10 = <span th:text="1 gt 10"></span></li>
            <li>1 >= 10 = <span th:text="1 >= 10"></span></li>
            <li>1 ge 10 = <span th:text="1 ge 10"></span></li>
            <li>1 == 10 = <span th:text="1 == 10"></span></li>
            <li>1 != 10 = <span th:text="1 != 10"></span></li>
        </ul>
    </li>
    <li>조건식
        <ul>
            <li>(10 % 2 == 0)? '짝수':'홀수' = <span th:text="(10 % 2 == 0)? '짝수':'홀수'"></span></li>
        </ul>
    </li>
    <li>Elvis 연산자
        <ul>
            <li>${data}?: '데이터가 없습니다.' = <span th:text="${data}?: '데이터가 없습니다.'"></span></li>
            <li>${nullData}?: '데이터가 없습니다.' = <span th:text="${nullData}?: '데이터가 없습니다.'"></span></li>
        </ul>
    </li>
    <li>No-Operation
        <ul>
            <li>${data}?: _ = <span th:text="${data}?: _">데이터가 없습니다.</span></li>
            <li>${nullData}?: _ = <span th:text="${nullData}?: _">데이터가 없습니다.</span></li>
        </ul>
    </li>
</ul>
</body>
</html>
```

- **비교연산**: HTML 엔티티를 사용해야 하는 부분을 주의하자,
    - (gt), < (lt), >= (ge), <= (le), ! (not), == (eq), != (neq, ne)
    - 이러한 것들은 thmeleaf에서 제공하는 연산자로, 실제 연산을 수행한다.
- **조건식**: 자바의 조건식과 유사하다.
- **Elvis** 연산자: 조건식의 편의 버전
    - data가 있다면 data가 동적으로 변경되면서 출력되고, 없으면 “데이터가 없습니다” 출력
    - 위와 똑같이 nullData가 있다면 nullData값이 출력 아니면 “데이터가 없습니다” 출력
- **No-Operation**: _ 인 경우 마치 타임리프가 실행되지 않는 것 처럼 동작한다. 이것을 잘 사용하면 HTML의 내용 그대로 활용할 수 있다. 마지막 예를 보면 데이터가 없습니다. 부분이 그대로 출력된다.

**결과 출력**

![image](https://github.com/user-attachments/assets/ce8cb51f-b61f-4854-8179-4374b113a0d7)

**"1 > 10” 이 false로 출력되는 이유?**

Thymeleaf에서 th:text 속성은 HTML 엔티티를 해석하고, 이를 Java 표현식으로 평가한다.
따라서, >와 같은 HTML 엔티티도 Thymeleaf에서는 단순한 문자열이 아닌 연산자로 동작한다.
