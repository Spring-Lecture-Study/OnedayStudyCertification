# ✅ 웹 서버, 웹 애플리케이션 서버

---

## 웹 - HTTP 기반

모든 웹 서비스는 **HTTP** 기반으로 동작한다.

### HTTP (HyperText Transfer Protocol)

Web에서 사용하는 **기본 통신 규약**

### 웹 서버(Web Server)

- HTTP 기반으로 동작
- **정적** 리소스 제공, 기타 부가기능
- 정적(파일) HTML, CSS, JS, 이미지, 영상
- 예) NGINX, APACHE

### 웹 애플리케이션 서버(WAS - Web Application Server)

- HTTP 기반으로 동작
- 프로그램 코드를 실행해서 **애플리케이션 로직 수행**
- **동적** HTML, HTTP API(JSON)
- 서블릿, JSP, 스프링 MVC
- 예) 톰캣(Tomcat) Jetty, Undertow

### 웹 서버, 웹 애플리케이션 서버(WAS) 차이

- 웹 서버는 **정적 리소스(파일)**, WAS는 **애플리케이션 로직**
- 사실은 둘의 용어도 경계도 모호함
    - 웹 서버도 프로그램을 실행하는 기능을 포함하기도 함
    - 웹 애플리케이션 서버도 웹 서버의 기능을 제공함
- 자바는 서블릿 컨테이너 기능을 제공하면 WAS
- WAS는 애플리케이션 코드를 실행하는데 더 특화

### 웹 시스템 구성 - WAS, DB

![image](https://github.com/user-attachments/assets/76ffbaf2-6524-4c6a-8a09-86e6789bac81)

- WAS, DB 만으로 시스템 구성 가능
- WAS는 정적 리소스, 애플리케이션 로직 모두 제공 가능
- WAS가 너무 많은 역할을 담당하면, 서버 과부하 우려
- WAS가 정적 리소스까지 제공하면, 중요한 애플리케이션 로직 실행에 필요한 자원 부족 성능 저하
- WAS 장애시 오류 화면도 노출 불가능

### 웹 시스템 구성 - WEB, WAS, DB

- **정적 리소스는 웹 서버가 처리**
- 웹 서버는 애플리케이션 로직같은 동적인 처리가 필요하면 **WAS에 요청을 위임**
- WAS는 **중요한 애플리케이션 로직 처리 전담**
- 효율적인 리소스 관리
• 정적 리소스가 많이 사용되면 Web 서버 증설
• 애플리케이션 리소스가 많이 사용되면 WAS 증설
- 정적 리소스만 제공하는 웹 서버는 잘 죽지 않음
• 애플리케이션 로직이 동작하는 WAS 서버는 잘 죽음
• WAS, DB 장애시 WEB 서버가 오류 화면 제공 가능

![image (1)](https://github.com/user-attachments/assets/eb10f4f9-2b50-4d0a-ba00-8ea64869985e)

# ✅ 서블릿

---

### 서버에서 처리해야 하는 업무

웹 애플리케이션에서 서버를 직접 구현한다고 하면 아래와 같이 많은 것들을 처리해야한다.

![image (2)](https://github.com/user-attachments/assets/428cefbd-1e08-4ecc-9e0f-e0d3fec97ac9)

그런데 서블릿을 사용하면 초록색 부분을 제외한 모든 일을 다 지원해준다.

### 서블릿

**특징**

![image (3)](https://github.com/user-attachments/assets/bac677f3-afd2-4403-b417-954ffb70c212)

- urlPatterns(/hello)의 URL이 호출되면 서블릿 코드가 실행
- HTTP 요청 정보를 편리하게 사용할 수 있는 **HttpServletRequest**
    - **클라이언트가 보낸 요청 정보**(예: 요청 URL, 헤더, 파라미터 등)를 담고 있는 객체
- HTTP 응답 정보를 편리하게 제공할 수 있는 **HttpServletResponse**
    - **서버가 클라이언트에게 보낼 응답 정보**(예: 상태 코드, 응답 데이터 등)를 담고 있는 객체
- 개발자는 HTTP 스펙을 매우 편리하게 사용

**HTTP 요청, 응답 흐름**

![image (4)](https://github.com/user-attachments/assets/7d9d1a04-4c78-4466-8f33-eb9ad7bef3a0)

HTTP 요청시

- WAS는 Request, Response 객체를 새로 만들어서 서블릿 객체 호출
- 개발자는 Request 객체에서 HTTP 요청 정보를 편리하게 꺼내서 사용
- 개발자는 Response 객체에 HTTP 응답 정보를 편리하게 입력
- WAS는 Response 객체에 담겨있는 내용으로 HTTP 응답 정보를 생성

### 서블릿 컨테이너

![image (5)](https://github.com/user-attachments/assets/6fadd0b7-d86a-42b7-a9b3-9a5ac69e4c7a)

톰캣처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라고 함

- 서블릿 컨테이너는 서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기 관리
- 서블릿 객체는 싱글톤으로 관리
- 공유 변수 사용 주의
- 서블릿 컨테이너 종료시 함께 종료
- JSP도 서블릿으로 변환 되어서 사용
- 동시 요청을 위한 **멀티 쓰레드 처리 지원**

# ✅ 동시 요청 - 멀티 쓰레드

## 쓰레드

- 프로그램에서 여러 작업을 동시에 실행하기 위한 최소 작업 단위
- 애플리케이션 코드를 하나하나 순차적으로 실행하는 것은 쓰레드
- 자바 메인 메서드를 처음 실행하면 main이라는 이름의 쓰레드가 실행
- 쓰레드가 없다면 자바 애플리케이션 실행이 불가능
- 쓰레드는 한번에 하나의 코드 라인만 수행
- 동시 처리가 필요하면 쓰레드를 추가로 생성

만약 쓰레드가 하나뿐이고 다중 요청이 들어오면, **요청 1**이 처리되는 동안 **요청 2**는 쓰레드를 기다리게 됩니다. 요청 1의 처리가 지연되면 **요청 2**는 대기 시간이 길어지고, 결국 **타임아웃**으로 오류가 발생할 수 있습니다.

이럴 경우 **요청 마다 쓰레드를 생성**해서 처리하는 방법이 있다.

## 요청 마다 쓰레드 생성

### **장단점**

장점

- 동시 요청을 처리할 수 있다.
- 리소스(CPU, 메모리)가 허용할 때 까지 처리가능
- 하나의 쓰레드가 지연 되어도, 나머지 쓰레드는 정상 동작한다.

단점

- 쓰레드는 생성 비용은 매우 비싸다.
- 고객의 요청이 올 때 마다 쓰레드를 생성하면, 응답 속도가 늦어진다.
- 쓰레드는 컨텍스트 스위칭 비용이 발생한다.
    - **CPU가 하나의 작업에서 다른 작업으로 전환될 때 발생하는 성능 비용**
- 쓰레드 생성에 제한이 없다.
- 고객 요청이 너무 많이 오면, CPU, 메모리 임계점을 넘어서 서버가 죽을 수 있다
    - 이러한 단점으로 보통 WAS는 **쓰레드 풀 방식**을 사용한다.

## 쓰레드 풀

**요청 마다 쓰레드 생성의 단점 보완**

**특징**

- 필요한 쓰레드를 쓰레드 풀에 보관하고 관리한다.
- 쓰레드 풀에 생성 가능한 쓰레드의 **최대치를 관리**한다. 톰캣은 최대 200개 기본 설정 (변경 가능)

**사용**

- 쓰레드가 필요하면, 이미 생성되어 있는 쓰레드를 쓰레드 풀에서 꺼내서 사용한다.
- 사용을 종료하면 쓰레드 풀에 해당 쓰레드를 반납한다.
- 최대 쓰레드가 모두 사용중이어서 쓰레드 풀에 쓰레드가 없으면?
    - 기다리는 요청은 거절하거나 특정 숫자만큼만 대기하도록 설정할 수 있다.

**장점**

- 쓰레드가 미리 생성되어 있으므로, 쓰레드를 생성하고 종료하는 비용(CPU)이 절약되고, 응답 시간이 빠르다.
- 생성 가능한 쓰레드의 최대치가 있으므로 너무 많은 요청이 들어와도 기존 요청은 안전하게 처리할 수 있다

### 실무 팁

WAS의 주요 튜닝 포인트는 최대 쓰레드(max thread) 수이다.

- 이 값을 너무 낮게 설정하면?
    - 동시 요청이 많으면, 서버 리소스는 여유롭지만, 클라이언트는 금방 응답 지연
- 이 값을 너무 높게 설정하면?
    - 동시 요청이 많으면, CPU, 메모리 리소스 임계점 초과로 서버 다운
- 장애 발생시?
    - 클라우드면 일단 서버부터 늘리고, 이후에 튜닝
    - 클라우드가 아니면 열심히 튜닝

### 쓰레드 풀의 적정 숫자

애플리케이션 로직의 복잡도, CPU, 메모리, IO 리소스 상황에 따라 모두 다르기 때문에 성능 테스트를 진행해 봐야 한다.

- 최대한 실제 서비스와 유사하게 성능 테스트 시도
- 툴: 아파치 ab, 제이미터, nGrinder

### 핵심

- 멀티 쓰레드에 대한 부분은 WAS가 처리
- 개발자가 멀티 쓰레드 관련 코드를 신경쓰지 않아도 됨
- 개발자는 마치 싱글 쓰레드 프로그래밍을 하듯이 편리하게 소스 코드를 개발
- 멀티 쓰레드 환경이므로 싱글톤 객체(서블릿, 스프링 빈)는 주의해서 사용

## ✅ HTML, HTTP, API, CSR, SSR

---

## 정적 리소스

- 고정된 HTML 파일, CSS, JS, 이미지, 영상 등을 제공
- 주로 웹 브라우저

![image (6)](https://github.com/user-attachments/assets/601f66c6-787b-464b-b77a-f05c601a45c1)

## HTML 페이지

- **동적**으로 필요한 HTML 파일을 생성해서 전달
- 웹 브라우저: HTML 해석

![image (7)](https://github.com/user-attachments/assets/6f73ff22-2786-461f-84d5-d9b976237e26)

## HTTP API

- HTML이 아니라 **데이터를 전달**
- 주로 **JSON** 형식 사용
- 다양한 시스템에서 호출
- 데이터만 주고 받음, UI 화면이 필요하면, 클라이언트가 별도 처리
- 앱, 웹 클라이언트, 서버 to 서버

![image (8)](https://github.com/user-attachments/assets/6256775f-4fe7-4ed0-9eee-e2e27b088535)

### 다양한 시스템 연동

- 주로 JSON 형태로 데이터 통신
- UI 클라이언트 접점
    - 앱 클라이언트(아이폰, 안드로이드, PC 앱)
    - 웹 브라우저에서 자바스크립트를 통한 HTTP API 호출
    - React, Vue.js 같은 웹 클라이언트
- 서버 to 서버
    - 주문 서버 -> 결제 서버
    - 기업간 데이터 통신

## 서버사이드 렌더링, 클라이언트 사이드 렌더링

### SSR - 서버 사이드 렌더링

HTML 최종 결과를 서버에서 만들어서 웹 브라우저에 전달

주로 정적인 화면에 사용

관련기술: JSP, 타임리프 -> 백엔드 개발자

![image (9)](https://github.com/user-attachments/assets/a51c8846-dd70-443b-ad3b-82c1c0c3a7ff)

### CSR - 클라이언트 사이드 렌더링

HTML 결과를 자바스크립트를 사용해 웹 브라우저에서 동적으로 생성해서 적용

주로 동적인 화면에 사용, 웹 환경을 마치 앱 처럼 필요한 부분부분 변경할 수 있음

예) 구글 지도, Gmail, 구글 캘린더

관련기술: React, Vue.js -> 웹 프론트엔드 개발자

![image (10)](https://github.com/user-attachments/assets/0ca76215-be48-4c72-aecf-ca945ea2103b)