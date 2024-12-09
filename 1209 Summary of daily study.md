# 속성 값 설정

타임리프는 주로 HTML 태그에 **th:*** 속성을 지정하는 방식으로 동작한다. th:* 로 속성을 적용하면 **기존 속성을 대체한다**. 기존 속성이 없으면 새로 만든다.

### **BasicController 추가**

```java
@GetMapping("/attribute")
    public String attribute() {
        return "basic/attribute";
    }
```

### attribute.html 파일 추가

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<h1>속성 설정</h1>
<input type="text" name="mock" th:name="userA" />

<h1>속성 추가</h1>
- th:attrappend = <input type="text" class="text" th:attrappend="class=' large'" /><br/>
- th:attrprepend = <input type="text" class="text" th:attrprepend="class='large'" /><br/>
- th:classappend = <input type="text" class="text" th:classappend="large" /><br/>

<h1>checked 처리</h1>
- checked o <input type="checkbox" name="active" th:checked="true" /><br/>
- checked x <input type="checkbox" name="active" th:checked="false" /><br/>
- checked=false <input type="checkbox" name="active" checked="false" /><br/>

</body>
</html>
```

### 속성 설정

th:* 속성을 지정하면 타임리프는 기존 속성을 th:* 로 지정한 속성으로 대체한다. 기존 속성이 없다면 새로 만든다.

```html
<input type="text" name="mock" th:name="userA" />
```

→ 타임리프 렌더링 후 <input type="text" name="userA" />

- 기존에 있었던 name 속성이 사라지고 th:name에 있었던 “userA”가 대체되었다.

### 속성 추가

**th:attrappend** : 속성 값의 뒤에 값을 추가한다.
**th:attrprepend** : 속성 값의 앞에 값을 추가한다.
**th:classappend** : class 속성에 자연스럽게 추가한다.

### checked 처리

![image (1)](https://github.com/user-attachments/assets/1c3d83c5-2e37-4355-8ce4-456297a1ede6)

![image (2)](https://github.com/user-attachments/assets/b14b6117-236d-4316-89d4-78af50f14959)

HTML에서는 **<input type="checkbox" name="active" checked="false" />** 이 경우에도
checked 속성이 있기 때문에 checked 처리가 되어버린다.

HTML에서 checked 속성은 checked 속성의 값과 상관없이 checked 라는 속성만 있어도 체크가 된다. 이런 부분이 true , false 값을 주로 사용하는 개발자 입장에서는 불편하다.

타임리프의 th:checked 는 값이 false 인 경우 checked 속성 자체를 제거한다.

```html
<input type="checkbox" name="active" th:checked="false" />
```

타임리프 렌더링 후: <input type="checkbox" name="active" />

# 반복

타임리프에서 반복은 **th:each** 를 사용한다. 추가로 반복에서 사용할 수 있는 여러 상태 값을 지원한다.

**BasicController 추가**

```java
@GetMapping("/each")
    public String each(Model model) {
        addUsers(model);
        return "basic/each";
    }

    private void addUsers(Model model) {
        List<User> list = new ArrayList<>();
        list.add(new User("userA", 10));
        list.add(new User("userB", 20));
        list.add(new User("userC", 30));

        model.addAttribute("users", list);
    }
```

해당 경로로 GET 요청이 들어오면 each 메서드가 호출됩니다. 이 메서드는 내부적으로 addUsers 메서드를 호출하여 사용자 정보를 생성하고 이를 모델 객체(Model)에 추가합니다.

addUsers 메서드는 ArrayList를 생성하고, 세 개의 User 객체를 리스트에 추가한 뒤, 해당 리스트를 "users"라는 키 값으로 Model에 저장합니다.

결과적으로, each 메서드는 "basic/each" 템플릿 파일(HTML)을 반환하며, 이 파일 내에서 **Model**에 담긴 "users" 리스트에 접근하여 데이터를 동적으로 렌더링할 수 있다.

**each.html**

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>기본 테이블</h1>
<table border="1">
    <tr>
        <th>username</th>
        <th>age</th> </tr>
    <tr th:each="user : ${users}">
        <td th:text="${user.username}">username</td>
        <td th:text="${user.age}">0</td>
    </tr>
</table>
<h1>반복 상태 유지</h1>
<table border="1">
    <tr>
        <th>count</th>
        <th>username</th>
        <th>age</th>
        <th>etc</th>
    </tr>
    <tr th:each="user, userStat : ${users}">
        <td th:text="${userStat.count}">username</td>
        <td th:text="${user.username}">username</td>
        <td th:text="${user.age}">0</td>
        <td>
            index = <span th:text="${userStat.index}"></span>
            count = <span th:text="${userStat.count}"></span>
            size = <span th:text="${userStat.size}"></span>
            even? = <span th:text="${userStat.even}"></span>
            odd? = <span th:text="${userStat.odd}"></span>
            first? = <span th:text="${userStat.first}"></span>
            last? = <span th:text="${userStat.last}"></span>
            current = <span th:text="${userStat.current}"></span>
        </td>
    </tr>
</table>
</body>
</html>
```

### 반복 기능

![image (3)](https://github.com/user-attachments/assets/229c3f1d-6590-4f90-928a-7cad5c3434ec)

```html
<tr>
    <th>username</th>
    <th>age</th> </tr>
<tr th:each="user : ${users}">
    <td th:text="${user.username}">username</td>
    <td th:text="${user.age}">0</td>
</tr>
```

- 반복시 오른쪽 컬렉션**( ${users} )**의 값을 하나씩 꺼내서 왼쪽 변수( user )에 담아서 태그를 반복 실행합니다.
- **th:each** 는 List 뿐만 아니라 배열, java.util.Iterable , java.util.Enumeration 을 구현한 모든 객체를 반복에 사용할 수 있습니다. **Map** 도 사용할 수 있는데 이 경우 변수에 담기는 값은 Map.Entry 입니다.

### 반복 상태 유지

![image (4)](https://github.com/user-attachments/assets/dafdef92-9b74-4fa0-a8e6-7a182c390e4f)

```html
<tr th:each="user, userStat : ${users}">
    <td th:text="${userStat.count}">username</td>
    <td th:text="${user.username}">username</td>
    <td th:text="${user.age}">0</td>
    <td>
        index = <span th:text="${userStat.index}"></span>
        count = <span th:text="${userStat.count}"></span>
        size = <span th:text="${userStat.size}"></span>
        even? = <span th:text="${userStat.even}"></span>
        odd? = <span th:text="${userStat.odd}"></span>
        first? = <span th:text="${userStat.first}"></span>
        last? = <span th:text="${userStat.last}"></span>
        current = <span th:text="${userStat.current}"></span>
    </td>
</tr>
```

반복의 두번째 파라미터를 설정해서 **반복의 상태**를 확인 할 수 있습니다.
두번째 파라미터는 생략 가능한데, 생략하면 지정한 변수명**( user ) + Stat** 가 됩니다.
여기서는 **user + Stat = userStat** 이므로 생략 가능합니다.

### 반복 상태 유지 기능

- **index** : 0부터 시작하는 값
- **count** : 1부터 시작하는 값
- **size** : 전체 사이즈
- **even** , **odd** : 홀수, 짝수 여부( boolean )
- **first** , **last** :처음, 마지막 여부( boolean )
- **current** : 현재 객체

# 조건부 평가

### 타임리프의 조건식

**if , unless** ( if 의 반대)

**BasicController 추가**

```java
@GetMapping("/condition")
    public String condition(Model model) {
        addUsers(model);
        return "basic/condition";
    }
```

**condition.html추가**

```java
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head><body>
<h1>if, unless</h1>
<table border="1">
    <tr>
        <th>count</th>
        <th>username</th>
        <th>age</th>
    </tr>
    <tr th:each="user, userStat : ${users}">
        <td th:text="${userStat.count}">1</td>
        <td th:text="${user.username}">username</td>
        <td>
            <span th:text="${user.age}">0</span>
            <span th:text="'미성년자'" th:if="${user.age lt 20}"></span>
            <span th:text="'미성년자'" th:unless="${user.age ge 20}"></span>
        </td>
    </tr>
</table>
<h1>switch</h1>
<table border="1">
    <tr>
        <th>count</th>
        <th>username</th>
        <th>age</th>
    </tr>
    <tr th:each="user, userStat : ${users}">
        <td th:text="${userStat.count}">1</td>
        <td th:text="${user.username}">username</td>
        <td th:switch="${user.age}">
            <span th:case="10">10살</span>
            <span th:case="20">20살</span>
            <span th:case="*">기타</span>
        </td>
    </tr>
</table>
</body>
</html>
```

### if, unless

타임리프는 해당 조건이 맞지 않으면 태그 자체를 렌더링하지 않는다.

- 만약 조건이 맞지 않는다면, <td th:text="${userStat.count}">1</td> 이 구문에서는 1이 출력된다.

만약 다음 조건이 **false** 인 경우 **<span>...<span>** 부분 자체가 렌더링 되지 않고 사라진다.

```java
<span th:text="'미성년자'" th:if="${user.age lt 20}"></span>
```

![image](https://github.com/user-attachments/assets/52076801-81a1-421b-a412-c72c8a89d63f)

th:if="${user.age lt 20}": user.age가 20보다 작을 때 조건이 참이고, th:unless="${user.age ge 20}": user.age가 20보다 크거나 같지 않을 때도 조건이 참이므로 userA에만 “미성년자 미성년자” 가 출력되고 나머지는 해당이 되지 않기 때문에 나이만 출력된다.

### switch

“*”은 만족하는 조건이 없을 때 사용하는 디폴트이다.

![image (1)](https://github.com/user-attachments/assets/6dd0e177-a24d-446f-b6e1-a899cc0b0d79)

# 주석

**BasicController 추가**

```java
@GetMapping("comments")
    public String comments(Model model) {
        model.addAttribute("data", "Spring!");
        return "basic/comments";
    }
```

**comments.html**

```java
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
<h1>예시</h1>
<span th:text="${data}">html data</span>
<h1>1. 표준 HTML 주석</h1>
<!--
<span th:text="${data}">html data</span>
-->
<h1>2. 타임리프 파서 주석</h1>
<!--/* [[${data}]] */-->
<!--/*-->
<span th:text="${data}">html data</span>
<!--*/--><h1>3. 타임리프 프로토타입 주석</h1>
<!--/*/
<span th:text="${data}">html data</span>
/*/-->
</body>
</html>
```

**결과**

![image](https://github.com/user-attachments/assets/138d3ba9-75d7-46a2-a51d-7aed037c11ba)

1. **표준 HTML 주석**
    
    ```java
    <!--
    <span th:text="${data}">html data</span>
    -->
    ```
    
    자바스크립트의 표준 HTML 주석은 타임리프가 렌더링 하지 않고, 그대로 남겨둔다.
    
2. **타임리프 파서 주석**
    
    ```java
    <h1>2. 타임리프 파서 주석</h1>
    <!--/* [[${data}]] */-->
    <!--/*-->
    <span th:text="${data}">html data</span>
    ```
    
    타임리프 파서 주석은 타임리프의 진짜 주석이다. 렌더링에서 주석 부분을 제거한다
    
3. **타임리프 프로토타입 주석**
    
    ```java
    <!--*/--><h1>3. 타임리프 프로토타입 주석</h1>
    <!--/*/
    <span th:text="${data}">html data</span>
    /*/-->
    ```
    
    타임리프 프로토타입은 약간 특이한데, HTML 주석에 약간의 구문을 더했다.
    HTML 파일을 웹 브라우저에서 그대로 열어보면 HTML 주석이기 때문에 이 부분이 웹 브라우저가 렌더링하지 않는다. 타임리프 렌더링을 거치면 이 부분이 정상 렌더링 된다.
    쉽게 이야기해서 HTML 파일을 그대로 열어보면 주석처리가 되지만, 타임리프를 렌더링 한 경우에만 보이는 기능이다.

# 블럭

**th:block** 은 HTML 태그가 아닌 타임리프의 유일한 자체 태그다.

**BasicController 추가**

```java
@GetMapping("/block")
    public String block(Model model) {
        addUsers(model);
        return "basic/block";
    }
```

**block.html**

```java
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<th:block th:each="user : ${users}">
    <div>
        사용자 이름1 <span th:text="${user.username}"></span>
        사용자 나이1 <span th:text="${user.age}"></span>
    </div>
    <div>
        요약 <span th:text="${user.username} + ' / ' + ${user.age}"></span>
    </div>
</th:block>
</body>
</html>
```

th:each는 특정 태그 하나에만 반복을 적용하므로, 두 개 이상의 태그를 동시에 반복하려면 th:block을 사용하면 된다.
th:block은 그룹 전체를 반복하도록 도와주는 Thymeleaf 전용 컨테이너로, HTML 구조에 영향을 주지 않아 깔끔하고 효율적인 반복 처리가 가능하다.

**결과**

```java
사용자 이름1 <span>userB</span>
사용자 나이1 <span>20</span>
</div>
<div>
요약 <span>userB / 20</span>
</div>
<div>
사용자 이름1 <span>userC</span>
사용자 나이1 <span>30</span>
</div>
<div>
요약 <span>userC / 30</span>
</div>
```

타임리프는 HTML 태그에 속성을 추가하여 동적으로 데이터를 렌더링하는 특성을 가지고 있습니다. 그러나 HTML 구조에 영향을 주지 않고 여러 태그를 한 번에 반복하거나 조건을 처리해야 할 경우, th:block과 같은 Thymeleaf 전용 태그를 사용할 수 있다.
