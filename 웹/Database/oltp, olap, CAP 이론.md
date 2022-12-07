## Online Transaction Processing(OLTP)

- 디비에 트랜젝션 데이터를 캡쳐,가공,저장

- banking and credit card activity or retail checkout scanning에 사용됨 

- CRUD가 자주 발생해도 빠르게 처리할수 있어야함

## Online Analytical Processing(OLAP)

- 데이터 마이닝, 애널리틱스, 비즈니스 인텔리전스 프로젝트를 하기위해 오래된 데이터를 대상으로 복잡한 쿼리를 사용

- 복잡한 쿼리에 대한 응답시간이 빨라야함

## OLTP vs OLAP 

| a                    | OLTP                                     | OLAP                                                         |
| -------------------- | ---------------------------------------- | ------------------------------------------------------------ |
| 특성                 | 다량의 작은 트랜젝션을 다룸              | 대용량의 데이터를 대상으로 복잡한 쿼리를 다룸                |
| 작동방식             | Based on INSERT, UPDATE, DELETE commands | Based on SELECT commands to aggregate data for reporting     |
| 응답시간             | Milliseconds                             | Seconds, minutes, or hours depending on the amount of data to process |
| 목적                 | 실시간으로 트랜젝션 처리                 | 인사이트 발견목적                                            |
| 데이터 업데이트 주기 | 즉시 업데이트                            | 일정 주기마다 데이터를 리프레쉬                              |







# CAP



분산 데이터 스토어는 다음 세가지 성질중 2가지 이상을 모두 만족할수는 없다는 이론

- Consistency : 모든 읽기쿼리는 가장 최근에 변경된 데이터를 읽거나 만약 읽을수 없다면 에러를 수신해야함.

- Availability : 모든 요청은 에러를 수신해서는 안되며, 대신 가장 최근에 변경된 데이터를 읽으리라는 보장은 할수 없다.

- Partition tolerance : 분산시스템에서의 노드들 간에 네트워크에서 메시지가 딜레이되거나 유실됨에도 불구하고 시스템은 계속해서 동작해야한다.

네트워크 파티션 실패가 발생할경우, 다음 두가지 사항을 결정해야한다.

- 작업을 취소하고 이용성을 감소하고 일관성을 보장할것인지
- 작업을 계속하면서 이용성을 증가시키는 대신 일관성을 감소시킬것인지

CAP이론은 네트워크 파티션이 존재할때 일관성과 이용성중에서 선택해야한다는 사실을 암시한다.
CAP이론에서의 일관성은 ACID에서의 일관성과 매우 다른 의미이다.

## 상세설명

어떠한 분산시스템도 네트워크 실패에 자유로울수는 없기때문에, 네트워크 파티션은 일반적으로 수용되어야한다. 파티션상황에서 두가지 선택지가 있는데, 하나는 일관성이고 다른 하나는 이용성이다. 일관성을 이용성보다 우선할때, 네트워크 파티션닝때문에 특정 데이터가 최신데이터임을 보장할수 없다면 시스템은 에러를 반환할것이다. 이용성을 일관성보다 우선한다면, 시스템은 항상 쿼리를 처리해서 가장 최근 데이터를 리턴하려고 노력하겠지만 네트워크 파티셔닝때문에 최신데이터라는 보장은 할수가 없다.
네트워크 실패가 존재하지 않는다면 - 즉, 분산시스템이 정상적으로 동작한다면 - 이용성과 일관성은 모두 만족될수 있다.
CAP이론을 3가지 성질중 하나를 항상 포기해야한다고 오해하는 경우가 있는데, 사실 이는 네트워크 파티션이나 실패가 발생했을때만 해당한다. 다른 경우에는 아무런 트레이드오프가 발생해서는 안된다.
ACID성질을 보장하는 RDBMS와 같은 경우는 일관성을 이용성보다 우선하지만, NoSQL 디비의 경우는 대체로 이용성을 일관성보다 우선한다. 

## 데이터베이스 분류
![img](https://www.researchgate.net/profile/Dumindu_Samaraweera/publication/334554423/figure/fig1/AS:804849360855040@1568902449714/Database-Systems-according-to-the-CAP-Theorem.png)
[출처](https://www.researchgate.net/profile/Dumindu_Samaraweera/publication/334554423/figure/fig1/AS:804849360855040@1568902449714/Database-Systems-according-to-the-CAP-Theorem.png)

## 추가설명
[Availability와 Partition Tolerance](https://stackoverflow.com/a/12347673)

## 출처

[https://en.wikipedia.org/wiki/CAP_theorem](https://en.wikipedia.org/wiki/CAP_theorem)