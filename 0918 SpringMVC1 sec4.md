# 서블릿, JSP, MVC 패턴
---

서블릿, JSP, MVC 패턴을 사용하여 아래와 같은 요구사항을 구현해보겠습니다.

**회원 정보**

- 이름: username
- 나이: age

**기능 요구사항**

- 회원 저장
- 회원 목록 조회

**📁 MemberRepository.java**

```java
private static Map<Long, Member> store = new HashMap<>();
    private static long sequence = 0L;
    private static final MemberRepository instance = new MemberRepository();

    public static MemberRepository getInstance() {
        return instance;
    }
```

현재 스프링으로 내장 톰캣만 사용할 것이기 때문에, 싱글톤으로 사용하기 위해 **static**과 **private**으로 
변수를 선언하여 **클래스 차원에서 하나의 인스턴스**만 생성되도록 한다.

static 으로 선언하면 해당 변수는 **클래스 차원에서 하나만** 만들어지며, 모든 인스턴스에서 **공유된다.**

**instance** 변수는 **final**로 선언하여 초기화된 이후 다시 변경되지 않도록 하여 불변성을 보장한다.

- static으로 선언 했어도 값이 공유 되기 때문에 final로 선언하여 불변성 보장

getInstance() 메서드를 통해서만 외부에서 이 인스턴스에 접근할 수 있게 하여 단일 인스턴스만 사용하도록 강제한다.

🧪 **Test**

```java
@Test
    void findAll() {
        // given
        Member member = new Member("hello", 20);
        Member member1 = new Member("hi", 18);
        memberRepository.save(member);
        memberRepository.save(member1);
        // when
        List<Member> result = memberRepository.findAll();
        // that
        assertThat(result.size()).isEqualTo(2);
        assertThat(result).contains(member, member1);
    }
```

- .size() 를 통해 리스트나 컬렉션의 **크기(원소 개수)를 반환한다.**
- contains() 를 통해 리스트나 컬렉션에 **해당 객체가 포함되어 있는지** 확인한다.

이제 배웠던 서블릿을 활용하여 애플리케이션을 개발해보겠습니다.

**🧑‍💻 MemberFormServlet.java**

```java
@WebServlet(name = "memberFormServlet", urlPatterns = "/servlet/members/new-form")
public class MemberFormServlet extends HttpServlet {
    private MemberRepository memberRepository = MemberRepository.getInstance();
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");
        PrintWriter w = response.getWriter();
        // .write는 PrintWriter 클래스의 메서드로, 출력 스트림에 데이터를 작성하는 역할
        w.write("<!DOCTYPE html>\n" +
                "<html>\n" +
                "<head>\n" +
                " <meta charset=\"UTF-8\">\n" +
                " <title>Title</title>\n" +
                "</head>\n" +
                "<body>\n" +
                "<form action=\"/servlet/members/save\" method=\"post\">\n" +
                " username: <input type=\"text\" name=\"username\" />\n" +
                " age: <input type=\"text\" name=\"age\" />\n" +
                " <button type=\"submit\">전송</button>\n" +
                "</form>\n" +
                "</body>\n" +
                "</html>\n");
    }
```

이렇게 서블릿을 사용하면 .writer를 통해 자바 코드로 다 작성해야 하기 때문에 번거롭다

**결과 화면**

![image](https://github.com/user-attachments/assets/5e7f0441-253c-4e69-8cae-2019b0fd081f)

**🧑‍💻 MemberSaveServlet.java**

```java
String username = request.getParameter("username");
// age같은 경우 int형이기 때문에 Integer.parseInt를 통해 request.getParameter의 
		결과값인 String 타입을 int 타입으로 변경한다.	
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");
        PrintWriter w = response.getWriter();
        w.write("<html>\n" +
                "<head>\n" +
                " <meta charset=\"UTF-8\">\n" +
                "</head>\n" +
                "<body>\n" +
                "성공\n" +
                "<ul>\n" +
                " <li>id="+member.getId()+"</li>\n" +
                " <li>username="+member.getUsername()+"</li>\n" +
                " <li>age="+member.getAge()+"</li>\n" +
                "</ul>\n" +
                "<a href=\"/index.html\">메인</a>\n" +
                "</body>\n" +
                "</html>");
    }
```

request.getParameter()를 사용하여 클라이언트가 HTTP 요청으로 보낸 쿼리 파라미터 값을 받아오고, 그 값을 이용하여 동적인 HTML 페이지를 생성해서 반환한다.

**결과 화면**

![image (1)](https://github.com/user-attachments/assets/ac5bafd4-c840-4666-8808-42053d7c0cb9)

**🧑‍💻 MemberListServlet.java**

```java
List<Member> members = memberRepository.findAll();
        PrintWriter w = response.getWriter();
        w.write("<html>");
        w.write("<head>");
        w.write(" <meta charset=\"UTF-8\">");
        w.write(" <title>Title</title>");
        w.write("</head>");
        w.write("<body>");
        w.write("<a href=\"/index.html\">메인</a>");
        w.write("<table>");
        w.write(" <thead>");
        w.write(" <th>id</th>");
        w.write(" <th>username</th>");
        w.write(" <th>age</th>");
        w.write(" </thead>");
        w.write(" <tbody>");

        for (Member member : members) {
            w.write(" <tr>");
            w.write(" <td>" + member.getId() + "</td>");
            w.write(" <td>" + member.getUsername() + "</td>");
            w.write(" <td>" + member.getAge() + "</td>");
            w.write(" </tr>");
        }
        w.write(" </tbody>");
        w.write("</table>");
        w.write("</body>");
        w.write("</html>");
    }
```

**실행 결과**

![image (2)](https://github.com/user-attachments/assets/c1135ea1-3ccd-4985-bd7d-55b388df620a)

폼에서 등록한 멤버들이 list형태로 출력되는 걸 볼 수 있다.

다만 이렇게 자바코드 안에 html을 넣는 방식은 매우 번거롭다.
