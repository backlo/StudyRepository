# MSA 용어 및 도구

## Eureka

- Netfilx에 서 제공하는 MSA를 위한 클라우드 오픈 소스
- LB, Middle-tier server의 에러 대응을 위한 Rest 기반 서비스
- 등록과 해지를 바로 적용 가능하게 함
- MSA의 서비스들의 목록과 위치(ip,port)가 동적으로 변화하는 환경에서 서비스들을 효율적으로 관리하기 위한 Service Discovery Server/Client

> `Discovery` : 다른 서비스의 연결 정보를 찾는 것
>
> `Registry` : 서비스의 연결 정보를 등록하는 것

![image-20221116164845812](/Users/soomcare/Library/Application Support/typora-user-images/image-20221116164845812.png)

### Eureka Server

- Service Registration : 자신을 등록하는 서버
- Service Registry : Client가 가용한 서비스 목록을 요청하는 서버

### Eureka Client

- 서비스들의 위치 정보를 Eureka Server로부터 fetch하는 서비스



## Ribbon

- Ribbon은 Load balancing을 요청 어플리케이션 단에서 수행해주는 Client-side Load balancer
- Ribbon과 같은 L/B가 필요한 이유는 **부하 분산을 적절하게 하여 서비스의 가용성을 최대화하기 위함**
- LB(Load Balancer) : 여러 대의 서버에 트래픽을 골고루 분산하기 위해 분배해주는 기술
- 각 모듈의 연결 정보를 LB에 등록해 LB는 MSA의 각 모듈에 대한 연결 정보
- LB는 ip,port,hostname를 가지고 있음.
-  CI/CD를 수행하면서 각 모듈은 업그레이드 되고 연결정보가 바뀌게 되면 새롭게 LB에 등록해야 함
- Eureka client도 내장되어있어서 유레카 클라이언트를 사용해서 주어진 url의 호출을 전달할 서버리스트를 찾음
  - 각 url에 mapping 된 서비스 명을 찾아서 Eureka server 를 통해 목록 조회
  - 조회된 서버 목록을 `Ribbon` 클라이언트에게 전달
  - Eureka + Ribbon 에 의해 결정된 Server 주소로 HTTP 요청




## Zuul

* Zuul서버는 API Gateway
* **API Gateway는 API의 요청자인 Client(웹어플리케이션 또는 모바일앱)와 API의 제공자인 backend service를 연결하는 중계자**
* API Gateway 특징
  * frontend 개발자에게 통일된 요청 정보가 필요함
  * Eureka는 단지 등록된 서비스들을 관리하는 역할만 함.
  * L/B & Routing: Client의 요청에 따라 적절한 backend service를 로드밸런싱(L/B: Load Balancing)하고 연결(라우팅)
  * 인증/인가: 부적절한 요청을 차단하여 Backend service를 보호
  * 유통되는 트래픽 로깅 관리, 제어 가능
  * Circuit Break을 통해 Backend service 장애 감지 및 대처
  * Spring 측에서 Zuul을 대체한 Spring cloud Gateway를 만듬. (netty vs tomcat)



## 예시

![image-20221117103804044](/Users/soomcare/Library/Application Support/typora-user-images/image-20221117103804044.png)



## Route

* 고유ID, 목적지 URI, Predicate, Filter로 구성된 구성요소
* GATEWAY로 요청된 URI의 조건이 참일 경우, 매핑된 해당 경로로 매칭을 시켜줌



## Predicate

* 주어진 요청이 주어진 조건을 충족하는지 테스트하는 구성요소
* 각 요청 경로에 대해 충족하게 되는 경우 하나 이상의 조건자를 정의
* 만약 Predicate에 매칭되지 않는다면 HTTP 404 not found를 응답



## Filter

* GATEWAY 기준으로 들어오는 요청 및 나가는 응답에 대하여 수정을 가능하게 해주는 구성요소



## Circuit Breaker

- 에러가 더이상 전파되지 않도록 하는 장치
- retry와 fallback, 얽혀있는 client들을 빠르게 해제
  (Netflix의 Hystrix, Resilience4j)

![image-20221117101031933](/Users/soomcare/Library/Application Support/typora-user-images/image-20221117101031933.png)

* Retry
  - 실패한 실행을 짧은 지연을 가진 후 재시도합니다.
* Circuit Breaker
  - 실패한 실행에 대해서 또 다른 시도가 들어올 때 바로 실패 처리합니다.
* Rate Limiter
  - 일정 시간동안 들어올 수 있는 요청을 제한합니다.
* Time Limiter
  - 실행 되는 시간을 특정 시간을 넘지않도록 제한합니다.
* Bulkhead
  - 동시에 실행할 수를 제한합니다.
* Cache
  - 성공적인 실행을 캐시합니다. 다음번에 동일한 요청으로 들어오면 바로 반환합니다.
* Fallback
  - 실행을 실패(Exception)하는 경우에 대신 실행되게하는 프로세스입니다.



**백엔드**

- 도메인별로 서비스가 분리돼 운영되면 좋겠다.
- 서로 다른 서비스의 통신은 gRPC를 이용해 통신 오버헤드를 줄이고 싶다.
- 이벤트(메시지)를 적극 이용해 서로 다른 도메인 간의 결합을 느슨하게 유지하면서 확장성을 가져가고 싶다.

**프론트엔드**

- 마이크로 프론트엔드를 도입해 도메인별 페이지도 따로 개발하고 싶다.
- BFF(Backend for Frontend)를 두어 프론트엔드를 그리기에 최적화된 API만 내려주고 싶다.