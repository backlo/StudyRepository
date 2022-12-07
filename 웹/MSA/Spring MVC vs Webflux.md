# Spring MVC vs Spring Webflux

* 자바 생태계에 웹 프레임워크(Web Framework)는 대부분 서블릿 위에 추상화 계층을 올리는 형태로 만들어짐
* 한데, 지금은 마이크로서비스(Microservices)를 리액티브(Reactive)라는 개념을 중심으로 자바 세상도 빠르게 변함
* 바로 서블릿과 서블릿 컨테이너를 사용하지 않고, HTTP 기반 애플리케이션을 개발을 지원하는 Ratpack과 Spring WebFlux와 같은 프레임워크

## Spring WebFlux Framework

* Spring WebFlux는 Spring Framework 5에 포함된 새로운 웹 애플리케이션 프레임워크

* 기존 Spring MVC 모델에 비동기(asynchronous)와 넌블럭킹 I/O(non-blocking I/O) 처리를 맡기려면 너무 큰 변화가 필요했기 때문에 리액티브 프로그래밍(Reactive programming)을 지원하는 새로운 웹 프레임워크를 만듬

* Spring MVC

  * MVC는 서블릿 컨테이너와 서블릿을 기반으로 웹 추상화 계층을 제공

  * 구조

    ![image-20221116170603993](/Users/soomcare/Library/Application Support/typora-user-images/image-20221116170603993.png)

* Spring Webflux

  * WebFlux는 서블릿 컨테이너(Tomcat, Jetty) 외에도 Netty, Undertow와 같이 네트워크 애플리케이션 프레임워크 위에서 HTTP와 리액티브 스트림 기반으로 웹 추상화 계층을 제공

  * 구조

    ![image-20221116170628298](/Users/soomcare/Library/Application Support/typora-user-images/image-20221116170628298.png)

## Netty란?

* 고성능 프로토콜 서버와 클라이언트를 신속히 개발하기 위한 비동기식 이벤트 기반 네트워크 애플리케이션 프레임워크
* 기존 Socket I/O 를 이용해 통신을 하면 bind - listen - accept와 같은 일들을 했어야했는데(그리고 내가 직접 해본 유일한 네트워크 통신인데) 이 소켓을 사용하려면 thread를 생성해서 관리해야 하고, 그러면 어떤 소켓에서 이벤트가 일어났는지 모르게 되고 접속이 많아질수록 자원을 낭비하게 됨
* 따라서 Client의 커넥션 수립마다 thread를 생성하지 않아도 되는 Java NIO(Non-blocking IO)가 나옴
* 그렇다고 Thread를 아예 하나만 사용하는 게 아니라 알아서 적절히 관리를 해줌
* Spring이 원래 HTTP Servlet을 다루기 위한 프레임워크였지만 Netty가 Spring의 Singleton 방식의 Bean을 지원한다고 함
* 즉 Spring Cloud Gateway가 비동기적으로 요청을 처리하기 위해 네트워크 애플리케이션까지 완전히 비동기적인걸로 사용하는 듯함

## Spring Security vs Spring Cloud Security

* Spring Security는 그 자체로 하나의 라이브러리
* Spring Cloud Security는 MSA 구조에서 Spring boot client에 Rest api로 OAuth2 SSO 인증서버 만들기에 최적화 된 라이브러리
* Spring @EnableWebFluxSecurity를 이용하면 Webflux를 이용할 Gateway 단에서도 @EnableWebSecurity로 관리했듯 Spring Security의 기능들을 사용 가능
* UserDetails의 정보 확인이나 라우팅 별 권한 설정 등을 편하게 할 수 있지만 JWT를 사용할 때는 오히려 불편

