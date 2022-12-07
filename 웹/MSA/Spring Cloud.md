# 스프링 클라우드

- **스프링 클라우드는 다수의 통합된 자바 라이브러리를 제공**
- 다양한 라이브러리들은 어플리케이션 스택의 부분으로서 모든 실행시간의 고려사항들에 대해 다룸
- 결과적으로, 마이크로서비스들은 클라이언트 사이드 서비스 디스커버리, 로드 밸런싱, 설정 업데이트 등을 행하는 라이브러리와 실행시간의 행위자를 가짐
- 싱글톤과 같은 패턴과 배치 문제가 잡들은 JVM에서 관리된다.



## 스프링 클라우드 게이트웨이

* Spring Cloud Gateway는 Tomcat이 아닌 Netty를 사용
* API GATEWAY는 모든 요청이 통과하는 곳이기 때문에 성능적인 측면이 매우 중요하며
  기존의 1THREAD / 1REQUEST 방식인 SPRING MVC를 사용할 경우 성능적인 이슈가 발생
* Netty는 비동기 WAS이고 1THREAD / MANY REQUESTS 방식이기 때문에 기존 방식보다 더 많은 요청을 처리



## 스프링 클라우드 Gateway vs 스프링 클라우드 Netflix Zuul

![image-20221117104841204](/Users/soomcare/Library/Application Support/typora-user-images/image-20221117104841204.png)

*  Spring Cloud의 초창기 버전에서는 Netfilx OSS(Open Source Software)에 포함된 컴포넌트 중 하나로서 API Gateway 패턴을 구현할 수 있는 Zuul 을 사용
* 이렇게 Spring Cloud + Zuul의 형태를 Spring Cloud Zuul이라고 함
* Zuul은 서블릿 프레임워크 기반으로 만들어졌기 때문에 동기(Synchronous), 블로킹(Blocking) 방식으로 서비스를 처리
* 그러다 비동기(Asynchronous), 논블로킹(Non-Blocking) 방식이 대세가 되면서 해당 방식을 지원하는 Zuul2가 나오게 됨
* 하지만 Zuul은 Spring 생태계의 취지와 맞지 않아, Spring Cloud Gateway에서는 Zuul2를 사용하지 않고 API Gatewway 패턴을 구현할 수 있는 Spring Cloud Gateway를 새로 만듬
* Spring Cloud Gateway도 Zuul2와 마찬가지로 비동기, 논블로킹 방식을 지원
* 또한 Spring 기반으로 만들어졌기 때문에 Spring 서비스와의 호환도 좋음
* 최근에는 Spring Boot2와 Spring Cloud2가 릴리즈 된 이후에는 Spring Cloud Gateway가 성능이 더 좋다는 분석도 있음
* Spring Cloud Gateway는 **Netty** 런타임 기반으로 동작한다. 때문에 서블릿 컨테이너나 WAR로 빌드된 경우 동작하지 않음
* Spring Cloud Netflix zuul은 2018년 12월부터 더 이상의 기능 개선 없이 유지만 하는 Maintenance Mode로 변경
* 이미 Spring boot 2.4.X부터는 zuul, hystrix가 더 이상 제공되지 않음

![img](https://k.kakaocdn.net/dn/d0KboD/btqWWs8iyQW/20T2t4KzIWY6nAqlUIh1Vk/img.png)



## 스프링 클라우드를 접목 시킨 구조

![image-20221116173045754](/Users/soomcare/Library/Application Support/typora-user-images/image-20221116173045754.png)

- GATEWAY
  - 실제 라우팅 및 모니터링을 진행하는 GATEWAY의 본체
- EUREKA
  - CLIENT-SIDE 서비스 디스커버리를 구성하기 위한 구성 요소
- FALLBACK-SERVER
  - 상황에 맞는 대체 응답을 주기 위한 서버
- ADMIN
  - GATEWAY에 설정정보들을 입력하기 위한 ADMIN
- DB
  - ADMIN에서 입력된 데이터가 저장되기 위한 저장소
- CONFIG-SERVER
  - GATEWAY에 설정정보를 동적으로 변경하기 위한 구성 요소
- GIT
  - CONFIG-SERVER가 읽을 YAML 파일이 저장되는 저장소



## Spring Cloud Config

![image-20221117105325833](/Users/soomcare/Library/Application Support/typora-user-images/image-20221117105325833.png)

* config server는 git에서 config파일을 읽고, config 변경시 Message broker(예:rabbitMQ, kafka등)에 변경내용을 반영
* 그럼 각 마이크로서비스는 변경을 통지받고 config server를 통해 최신 config를 갱신
* 각 마이크로서비스는 ribbon을 이용하여 직접 다른 마이크로서비스를 load balancing하여 연결할 수 있음
* 이때 Hystrix를 통해 circuit break를 적용 가능



## Spring Cloud Config Server

* 스프링 Config Server는 각 애플리케이션에의 Config 설정을 중앙 서버에서 관리를 하는 서비스

* 중앙 저장소로 Github Repository뿐만 아니라 아래와 같은 저장소 환경을 제공

  - Git(Github)

  - JDBC

  - REDIS

  - AWS S3

  - 등등…

* Spring Config Server를 이용하면 `/actuator/refresh`, `/actuator/busrefresh`를 통해서 **서버를 재배포 없이 설정값을 변경**

* 예제 - https://cheese10yun.github.io/spring-config-server/



## Spring Cloud Config Client

* 각 서비스 애플리케이션은 해당 애플리케이션이 구동시 Config Server에 자신의 Config의 설정 파일을 읽어 오며, **애플리케이션이 구동 중에도 Config 설정을 변경해도 애플리케이션 재시작 없이 해당 변경 내용을 반영**
* 예제 - https://cheese10yun.github.io/spring-config-client/



## Spring Cloud Config 설정 파일 우선 순위

* 설정 파일은 크게 다음의 위치에 존재할 수 있으며 다음의 순서대로 읽어짐
* 나중에 읽어지는 것이 우선순위가 높음
  - 프로젝트의 application.yaml
  - 설정 저장소의 application.yaml
  - 프로젝트의 application-{profile}.yaml
  - 설정 저장소의 {application name}/{application name}-{profile}
* 설정 예제 - https://mangkyu.tistory.com/253
