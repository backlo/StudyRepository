# MSA 라이브러리

## OpenFegin

* feign : RestTemplate 호출 등을 JPA Repository 처럼 interface로 단순히 추상화 한 프로젝트

* build.gradle, service

  ```groovy
  # build.gradle 에 추가
  implementation 'org.springframework.cloud:spring-cloud-starter-openfeign'
  ```

* Service

  ```java
  // FeignServiceBRemoteService 인터페이스 추가
  // @FeignClient가 있는 interface를 찾아서 등록
  @EnableFeignClients
  @FeignClient(name = "MY-SERVICE2")
  public interface FeignServiceBRemoteService {
      @RequestMapping("info/{test}")
      String getServiceBInfo(@PathVariable("test") String test);
  }
  ```

* RequestClientController

  ```java
  @RequiredArgsConstructor
  @RestController
  public class MyController {
      private final FeignServiceBRemoteService feignServiceBRemoteService;
      @GetMapping("/test")
      public String test() {
          return feignServiceBRemoteService.getServiceBInfo("service1");
      }
  }
  
  ```

* ReponseClientController

  ```java
  @RequiredArgsConstructor
  @RestController
  public class MyController{
  	@GetMapping("/info/{test}")
    public String test(@PathVariable Stringtest) {
          return "service2에서 응답"+test;
    }
  }
  ```

