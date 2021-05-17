스프링 MVC 1편
==============

목차
----

[1. 소개](#1_소개)

[2. 웹 애플리케이션 이해](#2_웹-애플리케이션-이해)

[3. 서블릿](#3_서블릿)

[4. 서블릿, JSP, MVC 패턴](#4_서블릿-l-jsp-l-mvc패턴)

[5. MVC 프레임워크 만들기](#5_mvc-프레임워크-만들기)

[6. 스프링 MVC_구조 이해](#6_스프링-mvc_구조-이해)

---

### **1_소개**

#### 1.1 강의 소개

-	현대 애플리케이션은 대부분 웹환경에서 동작하는 웹애플리케이션임

	-	자바 백엔드 개발자는 이런 환경에서 대부분 스프링 MVC를 사용함
	-	스프링 MVC는 복잡한 웹 애플리케이션을 쉽고 빠르게 개발할 수 있게 도와주는 자바 웹 프레임워크임

-	백엔드 웹기술은 매우 방대하기 때문에 학습하기 어려움

	-	HTTP 에 대한 기반지식이 약하면 웹기술을 깊이 있게 학습하기가 어려움
	-	백엔드 역사가 오래되었는데 불편한 것을 개선하면서 너무 많은 것들이 자동화, 추상화 되었음
		-	왜 이런 방식으로 기능을 제공하는 지 이유를 모르고 단순히 사용법을 익혀 사용하게 됨
	-	스프링 MVC 떄문에 어려움
		-	수 많은 기능이 추가되었지만 스프링 MVC 의 기본구조가 탄탄하게 설계되어 있어서 기본구조는 변하지 않았음

-	이런 어려움을 해결하기 위해선?

	-	자바 웹기술의 기술의 기초라고 할 수 있는 서블릿
	-	JSP, MVC 패턴, MVC 프레임워크, 최신 스프링 사용법
	-	MVC 프레임워크를 직접 코드로 개발하고 단계적으로 개선하며 학습한다.

---

### **2_웹 애플리케이션 이해**

#### 2.1 웹 서버, 웹 애플리케이션 서버

-	웹은 HTTP 기반으로 통신함

	-	웹 브라우저에서 url를 치면 인터넷을 통해서 서버에 접근을 하고 서버에서는 HTML을 만들어서 클라이언트에 내려줌. 웹 브라우저는 받은 HTML을 가지고 보여줌

	-	HTTP 메시지에 모든 것을 전송

		-	HTML, TEXT - IMAGE, 음성, 영상, 파일

		-	JOSN, XML

		-	거의 모든 형태의 데이터 전송 가능

		-	서버간에 데이터를 주고 받을 때도 대부분 HTTP 사용

-	웹 서버

	-	HTTP 기반으로 동작
	-	정적 리소스 제공, 기타 부가 기능
	-	정적(파일) HTML, CSS, JS, 이미지, 영상
	-	ex) NGINX, APACHE

-	웹 애플리케이션 서버

	-	HTTP 기반으로 동작
	-	웹 서버 기능 포함 (정적 리소스 제공 기능)
	-	프로그램 코드를 실행해서 애플리케이션 로직 수행

		-	동적 HTML, HTTP API(JSON)

		-	서블릿, JSP, 스프링 MVC

	-	ex) 톰캣 Jetty , Undertow

-	웹 서버, 웹 애플리케이션 서버 차이

	-	**웹 서버는 정적 리소스(파일), WAS는 애플리케이션 로직**
	-	사실 둘의 용어도 경계도 모호함

		-	웹 서버로 프로그램을 실행하는 기능을 포함하기도 함
		-	WAS도 웹 서버의 기능을 제공함

	-	자바는 서블렛 컨테이너 기능을 제공하면 WAS

		-	서블릿 없이 자바코드를 실행하는 서버 프레임워크도 있음

	-	**WAS는 애플리케이션 코드를 실행하는데 더 특화**

-	웹 시스템 구성 : WAS, DB

	-	WAS, DB 만으로 시스템 구성 가능
	-	WAS는 정적 리소스, 애플리케이션 로직 모두 제공 가능

	-	하지만 WAS가 너무 많은 역할을 담당하게 되면 서버 과부하가 우려됨

	-	가장 비싼 애플리케이션 로직이 정적 리소스 때문에 수행이 어려울 수있음

	-	WAS 장애 시 오류 화면도 노출 불가능함

-	웹 시스템 구성 : WEB, WAS, DB

	-	정적 리소스는 웹 서버가 처리
	-	웹 서버는 애플리케이션 로직 같은 동적인 처리가 필요하면 WAS에 요청을 위임
	-	WAS는 중요한 애플리케이션 로직 처리 전담
	-	효율적인 리소스 관리

		-	정적 리소스가 많이 사용되면 Web 서버 증설하면 됨

		-	애플리케이션 리소스가 많이 사용되면 WAS 서버 증설하면 됨

	-	정적 리소스만 제공하는 웹 서버는 잘 죽지 않음

	-	애플리케이션 로직이 동작하는 WAS 서버는 잘 죽음

	-	WAS, DB 장애 시 WEB 서버가 오류 화면 제공 가능

#### 2.2 서블릿

-	form > submit > 서버에서 처리해야할 일은?

	-	서버 TCP/IP 연결 대기, 소켓 연결
	-	HTTP 요청 메시지를 파싱해서 읽기
	-	POST 방식, URL 인지
	-	Content-Type 확인
	-	HTTP 메시지 바디 내용 파싱

		-	받은 데이터를 사용할 수 있게 파싱

	-	저장 프로세스 실행

	-	**비즈니스 로직 실행**

		-	**데이터베이스에 저장 요청**

	-	HTTP 응답 메시지 생성 시작

		-	HTTP 시작 라인 생성

		-	Header 생성

		-	메시지 바디에 HTML 생성해서 입력

	-	TCP/IP에 응답 전달, 소켓 종료

-	서블릿을 지원하는 WAS를 사용하면 다른 일들을 지원해줌

	-	**비즈니스 로직 실행**

		-	**데이터베이스에 저장 요청**

-	서블릿

	-	urlPatterns 에 입력된 URL이 호출되면 서블릿 코드 실행 (service 가 실행됨)
	-	HTTP 요청 정보를 편리하게 사용할 수 있는 HttpServletRequest (요청 정보를 파싱 등 편하게 사용할 수 있게 해줌)
	-	HTTP 응답 정보를 편리하게 제공할 수 있는 HttpServletResponse (응답 정보를 편하게 사용할 수 있게 해줌)
	-	개발자는 HTTP 스펙을 매우 편리하게 사용할 수 있음

	```java
	@WebServlet(name = "helloServlet", urlPatterns = "/hello")
	public class HelloServlet extends HttpServlet {


	  @Override
	  protected void service(HttpServletRequest request, HttpServletResponse response){
	    // 로직
	  }
	}
	```

	-	HTTP 요청, 응답 흐름

		-	HTTP 요청 시

			-	WAS는 Request, Response 객체를 새로 만들어서 서블릭 객체 호출

			-	개발자는 Request 객체에서 HTTP 요청 정보를 편리하게 꺼내서 사용

			-	개발자는 Response 객체에 HTTP 응답 정보를 편리하게 입력

			-	WAS는 Response 객체에 담겨있는 내용으로 HTTP 응답 정보를 생성

-	서블릿 컨테이너

	-	WAS안에는 서블릿 컨테이너라고 있음

		-	서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기를 관리해줌

	-	톰캣처럼 서블릿을 지원하는 WAS를 서블릿 컨테이너라고 함

	-	서블릿 객체는 싱글톤으로 관리

		-	고객의 요청이 올 때 마다 계속 객체를 생성하는 것은 비효율적임

		-	최초 로딩 시점에 서블릿 객체를 미리 만들어두고 재활용

		-	모든 고객 요청은 동일한 서블릿 객체 인스턴스에 접근

		-	**공유 변수 사용 주의**

		-	서블릿 컨테이너 종료 시 함께 종료

	-	JPS도 서블릿으로 변환 되어서 사용

	-	동시 요청을 위한 멀티 쓰레드 처리 지원

#### 2.3 동시 요청 - 멀티 쓰레드

-	WAS에 요청이 오면 서블릿을 객체를 호출한다고 헀다. 누가 호출하는가? : 쓰레드

-	쓰레드

	-	애플리케이션 코드를 하나하나 순차적으로 실행하는 것
	-	자바 메인 메서드를 처음 실행하면 main이라는 이름의 쓰레드가 실행
	-	쓰레드가 없다면 자바 애플리케이션 실행이 불가능
	-	쓰레드는 한번에 하나의 코드 라인만 수행
	-	동시 처리가 필요하면 쓰레드를 추가로 생성

-	쓰레드가 하나라면?

	-	요청에 대한 처리가 지연되면 다른요청을 처리할 수 없다.

	-	방법은 많음

-	요청 마다 쓰레드 생성하는 방법

	-	장점

		-	동시 요청을 처리할 수 있다.

		-	리소스(CPU,메모리)가 허용할 때 까지 처리 가능

		-	하나의 쓰레드가 지연되어도, 나머지 쓰레드는 정상 동작한다.

	-	단점

		-	쓰레드는 생성 비용이 매우 비싸다.

		-	고객의 요청이 올 때 마다 쓰레드를 생성하면, 응답 속도가 늦어진다.

	-	쓰레드는 컨텍스트 스위칭 비용이 발생한다.

	-	쓰레드 생성에 제한이 없다.

		-	고객 요청이 너무 많이 오면 CPU, 메모리 임계점을 넘어서 서버가 죽을 수 있다.

-	쓰레드 풀 (요청 마다 생성하는 방법의 단점 보완)

	-	특징

		-	필요한 쓰레드를 쓰레드 풀에 보관하고 관리한다.

		-	쓰레드 풀에 생성 가능한 쓰레드의 최대치를 관리한다. (톰캣은 최대 200개 기본설정-변경가능)

	-	사용

		-	쓰레드가 필요하면 이미 생성되어 있는 쓰레드를 쓰레드 풀에서 꺼내서 사용한다.
		-	사용을 종료하면 쓰레드 풀에 해당 쓰레드를 반납한다.

		-	최대 쓰레드가 모두 사용 중이이서 쓰레드 풀에 쓰레드가 없으면?

			-	기다리는 요청은 거절하거나 특정 숫자만큼만 대기하도록 설정할 수 있음

	-	장점

		-	쓰레드가 미리 생성되어 있으므로, 쓰레드를 생성하고 종료하는 비용(CPU)이 절약되고, 응답 시간이 빠름
		-	생성 가능한 쓰레드의 최대치가 있으므로 너무 많은 요청이 들어와도 기존 요청은 안전하게 처리할 수 있다.

-	쓰레드 풀 - 실무 팁

	-	WAS의 주요 튜닝 포인트는 최대 쓰레드(max thread) 수 이다.
	-	낮게 설정하면?

		-	동시 요청이 많으면, 서버 리소스는 여유롭지만, 클라이언트는 응답 지연

	-	너무 높게?

		-	동시 요청이 많으면 CPI, 메모리 리소스 임계점 초과로 서버 다운

	-	장애 발생 시?

		-	클라우드면 일단 서버부터 늘리고, 이후에 튜닝
		-	클라우드가 아니면 열심히 튜닝

-	쓰레드 풀 - 쓰레드 풀의 적정 숫자

	-	적정 숫자는 어떻게 찾나?

	-	애플리케이션 로직의 복잡도, CPU, 메모리, IO 리소스 상황에 따라 모두 다름

	-	성능 테스트

		-	최대한 실제 서비스와 유사하게 성능 테스트 시도

		-	툴: 아파치 ab, 제이미터, nGrinder

-	WAS의 멀티 쓰레드 지원

	-	멀티 쓰레드에 대한 부분은 WAS가 처리
	-	**개발자 멀티 쓰레드 관련 코드를 신경쓰지 않아도 됨**
	-	**개발자는 마치 싱글 쓰레드 프로그래밍 하듯이 편리하게 소스 코드를 개발**
	-	멀티 쓰레드 환경이므로 싱글톤 객체(서블릿, 스프링 빈)는 주의해서 사용

#### 2.4 HTML, HTTP API, CSR, SSR

-	백엔드개발자는 어디까지 알아야하나?

-	정적 리소스

	-	고정된 HTML 파일, CSS, JS, 이미지, 영상 등을 제공

	-	주로 웹 브라우저

-	HTML 페이지

	-	동적으로 필요한 HTML 파일을 생성해서 전달
	-	웹 브라우저 : HTML 해석

-	HTTP API

	-	HTML 이 아니라 데이터를 전달
	-	주로 JSON 형식 사용
	-	다양한 시스템에서 호출
	-	데이터만 주고 받음. UI 화면이 필요하면, 클라이언트가 별도 처리
	-	앱, 웹 클라이언트, 서버 to 서버

> 백엔드 개발자는 위 3가지를 어떻게 제공할지 고민해야 함

-	서버사이드 렌더링, 클라이언트 사이드 렌더링

	-	SSR (서버 사이드 렌더링)

		-	HTML 최종 결과를 서버에서 만들어서 웹 브라우저에 전달

		-	주로 정적인 화면에 사용

		-	관련기술 : JSP, 타임리프 > 백엔드 개발자

	-	CSR (클라이언트 사이드 렌더링)

		-	HTML 결과를 자바스크립트를 사용해 웹 브라우저에서 동적으로 생성해서 적용

			-	1.HTML 요청

			-	2.자바스크립트 요청

			-	3.HTTP API - 데이터 요청

			-	4.자바스크립트로 HTML 결과 렌더링

		-	주로 동적인 화면에 사용, 웹 환경을 마치 앱 처럼 필요한 부분부분 변경할 수 있음

		-	ex_ 구글 지도, gmail

		-	관련기술 : React, Vue.js > 웹 프론트엔드 개발자

	-	참고

		-	React, Vue.js 를 CSR + SSR 동시에 지원하는 웹 프레임워크도 있음
		-	SSR을 사용하더라도, 자바스크립트를 사용해서 화면 일부를 동적으로 변경 가능

-	어디까지 알아야하나? ( 백엔드 개발자 기준 )

	-	백엔드 - 서버 사이드 렌더링 기술

		-	JSP, 타임리프
		-	화면이 정적이고, 복잡하지 않을 때 사용
		-	백엔드 개발자는 서버 사이드 렌더링 기술 학습 필수

	-	웹 프론트 엔드 - 클라이언트 사이드 렌더링 기술

		-	React, Vue.js
		-	복잡하고 동적인 UI 사용
		-	웹 프론트엔드 개발자의 전문 분야

	-	선택과 집중

		-	백엔드 개발자의 웹 프론트엔드 기술 학습은 옵션

		-	백엔드 개발자는 서버, DB 인프라 등등 수많은 백엔드 기술을 공부해야 한다.

		-	웹 프론트엔드도 깊이있게 잘하려면 숙련에 오랜 시간이 필요하다.

#### 2.5 자바 백엔드 웹 기술 역사

-	현재 사용 기술

	-	애노테이션 기반의 스프링 MVC 등장

		-	@Controller
		-	MVC 프레임워크의 춘추 전국 시대 마무리

	-	스프링 부트의 등장

	-	스프링 부트는 서버를 내장

	-	과거에는 서버에 WAS를 직접 설치하고, 소스는 War 파일을 만들어서 설치한 WAS에 배포

	-	스프링 부트는 빌드 결과(Jar)에 WAS 서버 포함 -> 빌드 배포 단순화

-	최신 기술 : 스프링 웹 기술의 분화

	-	Web Servlet - Spring MVC

		-	서블릿 기반의 스택, 멀티쓰레드 등

	-	Web Reactive - Spring WebFlux

		-	특징

			-	비동기 넌 블러킹 처리
			-	최소 쓰레드로 최대 성능 - 쓰레드 컨텍스트 스위칭 비용 효율화
			-	함수형 스타일 개발 - 동시처리 코드 효율화
			-	서블릿 기술 사용 X

		-	하지만

			-	웹 플럭스는 기술적 난이도 매우 높음

			-	아직 RDB 지원 부족

			-	일반 MVC 의 쓰레드 모델도 충분히 빠름

			-	실무에서 아직 많이 사용하지는 않음

-	뷰 템플릿 (HTML을 편리하게 생성하는 뷰 기능 )

	-	JSP

		-	속도 느림, 기능 부족

	-	프리마커, Velocity

		-	속도 문제 해결, 다양한 기능

	-	타임리프(Thymeleaf)

		-	내추럴 템플릿 : HTML의 모양을 유지하면서 뷰 템플릿 적용 가능
		-	스프링 MVC와 강력한 기능 통합

		-	최선의 선택 (단 성능은 프리마커, 벨로시티가 더 빠름)

---

### **3_서블릿**

#### 3.1 프로젝트 생성

-	https://start.spring.io/ 에서 프로젝트 생성

	-	부트를 사용하면 환경설정도 편리하고, 톰캣 서버를 내장하고 있기 때문에 편리하게 이용 가능 (하지만 스프링은 거의 사용하지 않을 예정)
	-	Packing : War 로 설정

		-	War로 해야 jsp를 쓸 수 있음

#### 3.2 Hello 서블릿

-	부트환경에서 서블릿을 등록하고 사용해본다.

-	참고

	-	서블릿은 톰캣 같은 웹 어플리케이션 서버를 직접 설치하고 그 위에 서블릿 코드를 클래스 파일로 빌드에서 올린 다음 톰캣 서버를 실행하면 됨

-	서블릿 환경 구성

	-	@ServletComponentSacn
	-	부트는 서블릿을 직접 등록해서 사용할 수 있도록 어노테이션을 지원한다.
	-	하위 패키지의 서블릿을 찾아서 자동으로 등록해서 사용할 수 있게 도와줌

-	HTTP 요청을 통해 매핑된 URL이 호출되면 서블릿 컨테이너는 다음 메서드를 실행함

	-	protected void service(HttpServletRequest request, HttpServletResponse response)

-	여러 was 서버들이 서블릿 표준 스펙을 구현하는 것임 (HttpServletRequest, HttpServletResponse)

-	환경설정 추가로 HTTP 요청 메시지 로그를 확인할 수 있다. (개발에만 적용할 것)

	-	logging.level.org.apache.coyote.http11=debug

#### 3.3 HttpServletRequest - 개요

-	HttpServletRequest 역할

	-	HTTP 요청 메시지를 개발자가 직접 파싱해서 사용해도 되지만, 불편함. 서블릿은 개발자가 HTTP 요청 메시지를 편리하게 사용할 수 있도록 개발자 대신 HTTP 요청 메시지를 파싱하고 그 결과를 HttpServletRequest 객체에 담아서 제공함

HTTP 요청 메시지

```
POST /save HTTP/1.1
Host : localhost:8080
Content-Type: application/x-www-form-urlencoded

username=kim
```

-	HTTP 메소드, URL, 쿼리스트링, 스키마,프로토콜, 헤더, form 파라미터 형식, message body 테이터

-	추가로 여러가지 부가 기능을도 함께 제공함

	-	임시저장소 기능

		-	해당 HTTP 요청이 시작부터 끝날 때까지 유지되는 임시 저장소 기능

			-	request.setAttribute(name, value)
			-	request.getAttribute(name)

	-	세션 관리 기능

		-	request.getSession(create: true)

-	중요!

	-	HttpServletRequest, HttpServletResponse 를 사용할 때 가장 중요한 점은 이 객체들이 HTTP 요청 메시지, HTTP 응답 메시지를 편리하게 사용하도록 도와주는 객체라는 점이다. 따라서 이 기능에 대해 깊이 있는 이해를 하려면 HTTP 스펙이 제공하는 요청, 응답 메시지 자체를 이해해야 한다.

#### 3.4 HttpServletRequest - 기본 사용법

-	HttpServletRequest를 통해서 HTTP 메시지의 start-line, header 정보 조회하는 방법을 알아본다 (코드)

#### 3.5 HTTP 요청 데이터 - 개요

-	HTTP 요청 메시지를 통해 클라이언트에서 서버로 데이터를 전달하는 방법을 알아본다.

-	GET - 쿼리 파라미터

	-	/url?username=hello&age=20
	-	메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함에서 전달
	-	ex_ 검색, 페이징에서 많이 사용하는 방식

-	POST - HTML Form

	-	content-type : application/x-www-form-urlencoded
	-	**메시지 바디**에 쿼리 파라미터 형식으로 전달 username=hello&age=20
	-	ex_ 회원가입, 상품 주문, HTML Form 사용

-	HTTP message body에 데이터를 직접 담아서 요청

	-	HTTP API 에서 주로 사용, JSON, XML, TEXT
	-	데이터 형식은 주로 JSON 사용
	-	POST, PUT, PATCH

#### 3.6 HTTP 요청 데이터 - GET 쿼리 파라미터

-	쿼리 파라미터는 ?를 시작으로 보낼 수 있고 추가 파라미터는 &로 구분

-	중복 파라미터는 request.getParameterValues() 사용해야함

	-	getParameter 를 사용하면 request.getParameterValues() 의 첫번째 값을 반환함

#### 3.7 HTTP 요청 데이터 - POST HTML Form

-	HTML의 Form을 사용해서 전송하는 방식

-	특징

	-	content-type : application/x-www-form-urlencoded
	-	메시지 바디에 쿼리 파라미터 형식으로 데이터를 전달함 (username=hello&age=20)

-	application/x-www-form-urlencoded 형식은 GET ㅇ에서 살펴본 쿼리 파라미터 형식과 같다.

	-	따라서 쿼리 파라미터 조회 메서드를 그대로 사용하면 된다.
	-	서버 입장에서는 둘의 형식이 동일하므로, request.getParameter()로 편리하게 구분없이 조회할 수 있다.

	-	request.getParameter() 는 GET URL 쿼리 파라미터 형식도 지원하고, POST HTML Form 형식도 둘 다 지원함

-	참고

	-	content-type은 HTTP 메시지 바디의 데이터 형식을 지정한다.
	-	GET URL 쿼리 파라미터 형식으로 클라이언트에서 서버로 데이터를 전달할 때는 HTTP 메시지 바디를 사용하지 않기 때문에 content-type이 없다.
	-	POST HTML Form 형식으로 데이터를 전달하면 HTTP 메시지 바디에 해당 데이터를 포함해서 보내기 때문에 바디에 포함된 데이터가 어떤 형식인지 content-type을 꼭 지정해야 한다.

#### 3.8 HTTP 요청 데이터 - API 메시지 바디(단순 텍스트)

-	HTTP message-body에 데이터를 직접 담아서 요청

	-	HTTP API에서 주로 사용 JSON, XML, TEXT
	-	데이터 형식은 주로 JSON 사용
	-	POST, PUT, PATCH

#### 3.9 HTTP 요청 데이터 - API 메시지 바디(JSON)

-	HTTP API에서 주로 사용하는 JSON 형식으로 데이터를 전달

	-	content-type : application/json
	-	message-body : {"username":"hello", "age":20}

-	JSON 결과를 파싱해서 사용할 수 있는 자바 객체로 변환하려면 Jackson, Gson 같은 JSON 변환 라이브러리를 추가해서 사용해야 함. 부트로 Spring MVC를 선택하면 기본으로 Jackson 라이브러리 (ObjectMapper) 를 함께 제공함

#### 3.10 HttpServletResponse - 기본 사용법

-	HttpServletResponse 역할

	-	HTTP 응답 메시지 생성

		-	HTTP 응답 코드 지정

		-	헤더 생성

		-	바디 생성

	-	편의 기능 제공

		-	Content-Type , 쿠키, Redirect

#### 3.11 HTTP 응답 데이터 - 단순 텍스트, HTML

-	HTTP 응답 메시지

	-	단순 텍스트 응답
	-	HTML 응답 (HTML을 반환할 때는 contentype을 text/html 로 지정해야함)
	-	HTTP API - MessageBody JSON 응답

#### 3.12 HTTP 응답 데이터 - API JSON

-	HTTP 응답으로 JSON을 반환할 때는 content-type을 application/json으로 지정해야함

-	Jackson 라이브러리가 제공하는 objectMapper.writeValueAsString()을 사용하면 객체를 JSON 문자로 변경할 수 있음

-	참고

	-	application/json은 스펙상 utf-8 형식을 사용하도록 정의되어 있음
	-	application/json;charset=utf-8 은 의미없는 파라미터를 추가한 것이 된다.

---

### **4_서블릿 l JSP l MVC패턴**

#### 4.1 회원 관리 웹 애플리케이션 요구사항

-	핵심 비즈니스 로직 만들고 서블릿 > JSP > MVC 패턴 을 점차 적용해서 문제들을 해결한다.

-	회원 관리 웹 애플리케이션 요구사항

	-	회원정보

		-	이름
		-	나이

	-	기능 요구사항

		-	회원 저장

		-	회원 목록 조회

#### 4.2 서블릿으로 회원 관리 웹 애플리케이션 만들기

-	먼저 서블릿으로 회원 등록 HTML 폼을 제공해보자

-	템플릿 엔진이 나온 이유

	-	자바코드로 HTML을 만들어내는 것보다 HTML 문서에 동적으로 변경해야 하는 부분만 자바 코드를 넣을 수 있다면 더 편리할 것이다.

	-	템플릿 엔진을 사용하면 HTML 문서에서 필요한 곳만 코드를 적용해서 동적으로 변경할 수 있음

	-	JSP, Thymeleaf, Freemaker, Velocity (JSP는 성능과 기능면에서 템플릿 엔진과의 경쟁에서 밀렸음)

#### 4.3 JSP로 회원 관리 웹 애플리케이션 만들기

-	<%@ page contentType="text/html;charset=UTF-8" language="java" %>

	-	JSP 문서라는 뜻

-	jsp 에서 request, response는 그냥 사용 가능함

	-	jsp도 결국 서블릿으로 바뀌기 때문에 서비스로직이 그대로 호출됨

-	서블릿과 JSP의 한계

	-	서블릿은 화면을 만들기 위한 HTML 만드는 작업이 자보코드에 섞여 지저분함
	-	JSP는 HTML은 깔끔하지고, 중간에 동적으로 변경이 필요한 부분에만 자바코드를 적용함

	-	JSP가 너무 많은 역할을 하게 됨 (비즈니스 로직 등)

-	MVC 패턴의 등장

	-	비즈니스 로직은 서블릿처럼 다른 곳에서 처리하고, JSP는 목적에 맞게 HTML로 화면을 그리는 용도로 집중

#### 4.4 MVC 패턴 - 개요

-	너무 많은 역할

	-	하나의 서블릿이나 JSP에서 비즈니스 로직과 뷰 렌더링까지 모두 처리하게 되면 너무 많은 역할을 하게되고 결과적으로 유자보수하기 어려워짐
	-	비즈니스 로직을 호출하는 부분에 변경이 발생해도 해당 코드를 손대야하고, UI를 변경할 일이 있어도 비즈니스 로직이 함께 있는 해당 파일을 수정해야함

-	변경의 라이프 사이클

	-	둘의 라이프 사이클이 다름.
	-	일부 UI를 수정하는 일 / 비즈니스 로직을 수정하는 일은 각각 다르게 발생할 가능성이 높음
	-	이렇게 변경의 라이프 사이클이 다른 부분을 하나의 코드로 관리하는 것은 유지보수하기 좋지 않다.

-	기능 특화

	-	JSP 같은 뷰 템플릿은 화면을 렌더링 하는데 최적화 되어있기 떄문에 이 부분의 업무만 담당하는 것이 가장 효과적임

-	ModelViewController

	-	컨트롤러, 뷰 라는 영역으로 서로 역할을 나누 것
	-	웹 애플리케이션은 보통 이 MVC 패턴을 사용함

	-	컨트롤러 : HTTP 요청을 받아서 검증하고, 비즈니스 로직을 실행한다. 그리고 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다.

	-	모델 : 뷰에 출력할 데이터를 담아둔다. 뷰가 필요한 데이터를 모두 모델에 담아서 전달해주는 덕분에 뷰는 비즈니스 로직이나 데이터 접근을 몰라도 되고, 화면을 렌더링하는 일에 집중할 수 있음

	-	뷰 : 모델에 담겨있는 데이터를 사용해서 화면을 그리는 일에 집중함 (HTML을 생성하는 부분)

	-	참고

		-	컨트롤러에 비즈니스로직을 둘 수 있지만, 이렇게되면 컨트롤러가 너무 많은 역할을 담당하게 됨

		-	그래서 일반적으로 비즈니스 로직은 서비스 라는 계층을 별도로 만들어서 처리함.

		-	컨트롤러는 비즈니스로직이 있는 서비스를 호출하는 담당함


#### 4.5 MVC 패턴 - 적용

-	서블릿을 컨트롤러로 사용하고, JSP를 뷰로 사용해서 MVC 패턴을 적용한다.

-	Model은 HttpServletRequest 객체 사용

	-	request는 내부에 데이터 저장소를 가지고 있는데, request.setAttribute(), request.getAttribute()를 사용해서 데이터를 보관하고, 조회할 수 있음

-	dispatcher.forward() : 다른 서블릿이나 JSP로 이동할 수 있는 기능(서버 내부에서 다시 호출이 발생함)

-	WEB-INF : 이 경로안에 JSP가 있으면 외부에서 직접 JSP를 호출할 수 없음

-	redirect vs forward

	-	리다이렉트는 실제 클라이언트(웹 브라우저)에 응답이 나갔다가, 클라이언트가 redirect 경로로 다시 요청한다.
	-	따라서 클라이언트가 인지할 수 있고, URL 경로도 실제로 변경된다.
	-	반면에 포워드는 서버 내부에서 일어나는 호출이기 때문에 클라이언트가 전혀 인지하지 못한다.

#### 4.6 MVC 패턴 - 한계

-	컨트롤러에 중복이 많고, 필요하지 않는 코드들이 많이보임

-	MVC 컨트롤러의 단점

	-	포워드 중복 : 이부분을 공통화해도 되지만, 해당 메서드도 항상 직접 호출해야함

	-	viewPath 중복

	-	사용하지 않는 코드

		-	response

	-	HttpServletRequest, HttpServletResponse를 사용하는 코드는 테스트 케이스 작성하기도 어려움

	-	공통 처리가 어려움

	-	기능이 복잡해질 수 록 컨트롤러에서 공통으로 처리해야 하는 부분이 점점 더 많이 증가될 것임

	-	공통 기능을 메서드로 뽑더라도 항상 호출해야하고, 실수로 호출하지 않으면 문제가 될 것임 (호출하는 것 자체도 중복임)

-	이 문제를 해결하려면 컨트롤러 호출 전에 먼저 공통 기능을 처리해야 함

	-	프론트 컨트롤러 패턴을 도입하면 이런 문제를 깔끔하게 해결할 수 있음
	-	스프링 MVC의 핵심도 이 프론트 컨트롤러에 있음

### **5_MVC 프레임워크 만들기**

#### 5.1 프론트 컨트롤러 패턴 소개

-	FrontController 패턴 특징

	-	프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받음
	-	프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출
	-	입구를 하나로 (공통 처리 가능)
	-	프론트 컨트롤러를 제외한 나머지 컨트롤러는 서블릿을 사용하지 않아도 됨

-	스프링 웹 MVC와 프론트 컨트롤러

	-	스프링 웹 MVC의 핵심도 FrontController
	-	스프링 웹 MVC의 DispatcherServlet 이 FrontController 패턴으로 구현되어 있음

#### 5.2 프론트 컨트롤러 도입 - v1

-	서블릿과 비슷한 모양의 컨트롤러 인터페이스를 도입한다. 각 컨트롤러들은 이 인터페이스를 구현하면 됨.

	-	프론트 컨트롤러는 이 인터페이스를 호출해서 구현과 관계 없이 로직의 일관성을 가져갈 수 있음

-	controllerMap 을 통해 매핑 URL 관리

#### 5.3 View 분리 - v2

-	모든 컨트롤러에서 중복이 있고, 깔끔하지 않음

-	MyView 생성을 통해 각 컨트롤러는 dispather.forward() 를 직접 호출하지 않아도 됨

	-	MyView 객체를 생성하고 거기에 뷰이름만 넣어서 반환하면 됨

	-	프론트컨트롤러는 view.render 호출로 일관되게 처리

#### 5.4 Model 추가 - v3

-	컨트롤러 입장에서 HttpServletRequest와 HttpServletResponse가 꼭 필요한가?

	-	컨트롤러는 현재 요청파라미터 정도만 필요함
	-	요청파라미터 정보는 자바의 Map으로 대신 넘기도록..
	-	request 객체를 model로 사용하는 대신에 별도의 Model 객체를 만들어서 반환하면 됨
	-	컨트롤러가 서블릿 기술을 전혀 사용하지 않도록 변경해 본다. (코드가 단순해지고, 테스트 코드 작성이 쉬움)

-	뷰 이름 중복제거

	-	컨트롤러는 뷰의 논리 이름을 반환, 프론트컨트롤러가 물치위치 이름 처리하도록 단순화를 하자
	-	"WEB-INF/views/new-form.jsp" -> new-form

-	ModelView

	-	서블릿의 종속성을 제거하기 위해 Model을 직접 만들고, 추가로 View 이름까지 전달하는 객체를 만든다.

#### 5.5 단순하고 실용적인 컨트롤러 - v4

-	현재 컨트롤러 인터페이스를 구현하는 개발자 입장에서 보면, 항상 ModelView 객체를 생성하고 반환해야 하는 부분이 번거로움

-	컨트롤러가 modelView를 반환하지 않고, viewName만 반환하도록 한다.

-	기존 구조에서 모델을 파라미터로 넘기고, 뷰의 논리 이름을 반환함으로 컨트롤러를 구현하는 개발자에게 편해진다.

> 프레임워크나 공통 기능이 수고로워야 사용하는 개발자가 편리해진다.

#### 5.6 유연한 컨트롤러1 - v5

-	한 프로젝트 안에서 v3,v4 방식 모두 사용하려면 어떻게 해야할까?

-	어댑터 패턴

	-	프론트 컨트롤러가 다양한 방식의 컨트롤러를 처리할 수 있도록 변경한다

#### 5.7 유연한 컨트롤러2 - v5

-	어댑터를 추가하여 v4를 추가한다.

#### 5.8 정리

-	v1 : 프론트 컨트롤러 도입

	-	기존 구조를 최대한 유지하면서 프론트 컨트롤러 도입

-	v2 : View 분류

	-	단순 반복되는 뷰 로직 분리

-	v3 : Model 추가

	-	서블릿 종속성 제거
	-	뷰 이름 중복 제거

-	v4 : 단순하고 실용적인 컨트롤러

	-	v3와 거의 비슷
	-	구현 입장에서 ModelView 를 직접 생성해서 반환하지 않도록 편리한 인터페이스 제공

-	v5 : 유연한 컨트롤러

	-	어댑터 도입
	-	어댑터를 추가해서 프레임워크를 유연하고 확장성 있게 설계

-	여기서 애노테이션을 사용해서 컨트롤러를 더 편리하게 발전시킬 수 있음

	-	애노테이션을 지원하는 어댑터를 추가하면 됨

-	지금까지 작성한 코드는 스프링 MVC 프레임워크의 핵심 코드의 축약버전이며 구조도 비슷함


### **6_스프링 MVC_구조 이해**

#### 6.1 스프링 MVC 전체 구조

-	직접 만든 프레임워크와 스프링 MVC 비교

	-	FrontController > DispatcherServlet
	-	handlerMappingMap > HandlerMapping
	-	MyHandlerAdaptor > HandlerAdaptor
	-	ModelView > ModelAndView
	-	viewResovler > ViewResolver
	-	MyView > View

-	DispatcherServlet 구조 살펴보기

	-	스프링 MVC도 프론트 컨트롤러 패턴이 구현되어 있음
	-	스프링 MVC의 프론트 컨트롤러가 DispatcherServlet 임
	-	스프링의 핵심

-	Dispatcher 서블릿 등록

	-	Dispatcher 도 부모 클래스에서 HttpServlet을 상속 받아서 사용하고, 서블릿으로 동작함
	-	부트는 DispatcherServlet을 서블릿으로 자동으로 등록하면서 모든 경우에 대해서 매핑함

-	요청 흐름

	-	서블릿이 호출되면 HttpServlet이 제공하는 service() 가 호출됨
	-	스프링 MVC는 DispatcherServlet의 부모인 FrameworkServlet에서 service()를 오버라이드 했음
	-	FrameworkServlet.service() 를 시작으로 여러 메서드가 호출되면서 DispatcherServlet.doDispatch() 가 호출됨

-	동작 순서

	-	핸들러 조회 : 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회함
	-	핸들러 어댑터 조회 : 핸들러를 실행할 수 있는 핸들러 어댑터를 조회
	-	핸들러 어댑터 실행 : 핸들러 어댑터 실행
	-	핸들러 실행 : 핸들러 어댑터가 실제 핸들러를 실행
	-	ModelAndView 반환 : 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서 반환
	-	viewResolver 호출 : 뷰 리졸버를 찾고 실행
	-	View 반환 : 뷰 리졸버는 뷰의 논리 이름을 물리이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환
	-	뷰 렌더링 : 뷰를 통해서 뷰를 렌더링함

-	인터페이스 살펴보기

	-	스프링 MVC의 가장 큰 강점은 DispatcherServlet 코드의 변경 없이, 원하는 기능을 변경하거나 확장할 수 있음 (확장가능하게 인터페이스로 제공)
	-	이 인터페이스들만 구현해서 DispatcherServlet에 등록하면 커스텀된 컨트롤러를 만들 수 있음

-	정리

	-	스프링 MVC는 분량이 많고 복잡해서 내부 구조를 모두 파악하는 것은 쉽지 않음
	-	해당 기능을 직접 확장하거나 나만의 컨트롤러를 만드는 일은 거의 없음 (대부분의 기능이 이미 구현되어 있음)
	-	하지만, 핵심 동작방식을 알아야 향후 문제가 발생했을 때 어떤 부분에서 문제가 발생했는 지 쉽게 파악하고, 문제를 해결할 수 있음




#### 6.2 핸들러 매핑과 핸들러 어댑터

-	핸들러 매핑과 어댑터가 어떤 것들이 어떻게 사용되는 지 알아본다.

-	과거 컨트롤러 : Controller 인터페이스 (이 컨트롤러가 호출되려면 다음 2 가지가 필요함)

	-	HandlerMapping

		-	핸들러 매핑에서 이 컨트롤러를 찾을 수 있어야 한다.
		-	ex_ 스프링 빈의 이름으로 핸들러를 찾을 수 있는 핸들러 매핑이 필요

	-	HandlerAdaptor

		-	핸들러 매핑을 통해서 찾은 핸들러를 실행할 수 있는 핸들러 어댑터가 필요하다.
		-	ex_ Controller 인터페이스를 실행할 수 있는 핸들러 어댑터를 찾고 실행해야 한다.

-	스프링 부트가 자동으로 등록하는 핸들러 매핑과 핸들러 어댑터 (스프링은 이미 필요한 핸들러와 핸들러 어댑터를 대부분 구현했음)

	-	HandlerMapping (일부 생략)

		-	RequestMappingHandlerMapping : 애노테이션 기반의 컨트롤러인 @RequestMapping 에서 사용
		-	BeanNameUrlHandlerMapping : 스프링 빈의 이름으로 핸들러를 찾는다.

	-	HandlerAdaptor (일부 생략)

		-	RequestMappingHandlerAdaptor : 애노네이션 기반의 컨트롤러인 @RequestMapping 에서 사용
		-	HttpRequestHandlerAdaptor : HttpRequestHandler 처리
		-	SimpleControllerHandlerAdaptor : Controller 인터페이스(애노테이션 X, 과거에 사용 처리)

-	순서

	-	1) 핸들러 매핑으로 핸들러 조회

		-	HandlerMapping을 순서대로 실행해서, 핸들러를 찾는다.
		-	이 경우 빈 이름으로 핸들러를 찾아야하기 때문에 이름 그대로 빈 이름으로 핸들러를 찾아주는 BeanNameUrlHandlerMapping 이 실행에 성공하고 핸들러인 OldController를 반환한다.

	-	2) 핸들러 어댑터 조회

		-	HandlerAdaptor의 supports()를 순서대로 호출한다.
		-	SimpleControllerHandlerAdaptor가 Controller 인터페이스를 지원하므로 대상이 된다.

	-	3) 핸들러 어댑터 실행

		-	디스패처 서블릿이 조회한 SimpleControllerHandlerAdaptor를 실행하면서 핸들러 정보도 함께 넘겨줌
		-	SimpleControllerHandlerAdaptor는 핸들러인 OldController를 내부에서 실행하고, 그 결과를 반환

-	HttpRequestHandler : 서블릿과 가장 유사한 형태의 핸들러

-	순서

	-	1) 핸들러 매핑으로 핸들러 조회

		-	HandlerMapping을 순서대로 실행해서 핸들러를 찾는다.
		-	빈이름으로 핸들러를 찾아야 하기 때문에 BeanNameUrlHandlerMapping이 실행에 성공하고 핸들러인 MyHttpRequestHandler를 반환

	-	2) 핸들러 어댑터 조회

		-	HandlerAdaptor 의 supports() 를 순서대로 호출
		-	HttpRequestHandlerAdaptor가 HttpRequestHandler 인터페이스를 지원하므로 대상이 됨

	-	3) 핸들러 어댑터 실행

		-	디스패처 서블릿이 조회한 HttpRequestHandlerAdaptor를 실행하면서 핸들러 정보도 함께 넘겨줌
		-	HttpRequestHandlerAdaptor는 핸들러인 MyHttpRequestHandler를 내부에서 실행하고, 그 결과를 반환

-	@RequestMapping

	-	가장 우선순위가 높은 핸들러 매핑과 핸들러 어댑터는

		-	RequestMappingHandlerMapping
		-	RequestMappingAdaptor

	-	@RequestMapping의 앞글자를 따서 만들 이름이며 스프링에서 주로 사용하는 애노테이션 기반의 컨트롤러를 지원하는 매핑과 어댑터임. (실무에서는 99.9 이 방식의 컨트롤러를 사용함)

#### 6.3 뷰 리졸버

-	뷰 리졸버 : InternalResourceViewResolver

	-	스프링 부트는 InternalResourceViewResolver 라는 뷰리졸버를 자동으로 등록하는데, 이때 application.properties에 등록한 spring.mvc.view.prefix, spring.mvc.view.suffix 설정 정보를 사용해서 등록함

-	스프링 부트가 자동 등록하는 뷰 리졸버 (일부 생략)

	-	BeanNameViewResolver : 빈 이름으로 뷰를 찾아서 반환(ex_엑셀 파일생성 기능에 사용)
	-	InternalResourceViewResolver : JSP 를 처리할 수 있는 뷰를 반환

-	순서

	-	1) 핸들러 어댑터 호출

		-	핸들러 어댑터를 통해 new-form 이라는 논리 뷰 이름 획득

	-	2) ViewResolver 호출

		-	new-form 이라는 뷰 이름으로 viewResolver를 순서대로 호출
		-	BeanNameViewResolver는 new-form 이라는 이름의 스프링 빈으로 등록된 뷰를 찾아야하는 데 없다
		-	InternalResourceViewResolver 가 호출됨

	-	3) InternalResourceViewResolver

		-	이 뷰 리졸버는 InternalReosuceView 를 반환

	-	4) 뷰_InternalReosurceView

		-	InternalResoucreView는 JSP처럼 forward()를 호출해서 처리할 수 있는 경우에 사용한다.

	-	5) view.render()

		-	view.render() 가 호출되고 InternalResourceView는 forward()를 사용해서 JSP를 실행함

-	참고

	-	InternalResourceViewResolver는 만약 JSTL 라이브러리가 있으면 InternalResourceView 를 상속받은 JstlView 를 반환한다. (JSTL 태그 사용 시 약간의 부가기능 추가됨)

	-	다른 뷰는 실제 뷰를 렌더링하지만, JSP의 경우 forward()를 통해서 해당 JSP로 이동해야 렌더링이 된다. JSP를 제외한 나머지 뷰 템플릿은 forward() 과정 없이 바로 렌더링 됨

	-	Thymeleaf 뷰 템플릿을 사용하면 ThymeleafViewResolver를 등록해야 함. 최근에는 라이브러리만 추가하면 부트가 모두 자동화해줌

#### 6.4 스프링 MVC - 시작하기

-	스프링이 제공하는 컨트롤러는 애노테이션 기반으로 동작해서 매우 유연하고 실용적임

-	@RequestMapping

	-	스프링은 애노테이션을 활용한 매우 유연하고, 실용적인 컨트롤러를 만들었는데 이것이 @RequestMapping

	-	RequestMappingHandlerMapping

	-	RequestMappingHandlerAdaptor

	-	스프링에서 주로 사용하는 애노테이션 기반의 컨트롤러를 지원하는 핸들러 매핑과 어댑터임

-	@Controller

	-	스프링이 자동으로 스프링 빈으로 등록한다 (내부에 @Component 가 있어 컴포넌트 스캔의 대상이 됨)
	-	스프링 MVC에서 애노테이션 기반 컨트롤러로 인식이 됨 (RequestMappingHadlerMapping에서 꺼낼 수 있는 대상이 됨)
	-	다른 방식으로 스프링 빈 등록이 가능하지만 이 어노테이션이 제일 깔끔함

-	@RequqeatMapping

	-	요청 정보를 매핑
	-	해당 URL이 호출되면 이 메서드가 호출됨
	-	애노테이션 기반 동작하기 때문에, 메서드 이름은 임의로 지으면 됨

-	ModelAndView : 모델과 뷰 정볼르 담아서 반환하면 됨

#### 6.5 스프링 MVC - 컨트롤러 통합

-	@RequestMapping 을 보면 메서드 단위에 적용된 것을 볼 수 있음.

	-	컨트롤러 클래스를 유연하게 하나로 통합할 수 있음

#### 6.6 스프링 MVC - 실용적인 방식

-	v2 에서 ModelAndView를 직접 생성하고 있다. 스프링 MVC는 수 많은 편의 기능을 제공함

-	Model 파라미터

	-	Model을 파라미터로 받을 수 있다.

-	ViewName 직접 반환

	-	뷰의 논리 이름을 반환할 수 있음

-	@RequestParam 사용

	-	스프링은 HTTP 요청 파라미터를 @RequestParam 으로 받을 수 있음
	-	request.getParameter("~") 와 거의 같다고 생각하면 됨
	-	GET 쿼리파라미터, POST Form 방식 모두 지원

-	@RequestMapping > @GetMapping, @PostMapping

	-	get,post,put,delete,patch 모두 가능