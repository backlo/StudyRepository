# Apache Kafka의 장점

- Low Latency : 10 millisecond까지 low latency value를 제공한다.

- High Throughput : low latency 덕분에, Kafka는 메시지들을 더 많이, 빠르게 전달할수 있다. 

- Fault Tolerance : Kafka는 cluster 내에있는 node나 machine의 실패에 대처하는 방법을 갖고 있음.

- Durability : Kafka는 replication 기능을 제공하는데, cluster내에 다른 노드에게 데이터를 복사하는 기능을 갖고 있다.

- Reduces the need for multiple integrations : producer들은 Kafka에게 데이터를 쓰고, consumer는 Kafka로 데이터를 읽으면 되므로 producing,cumsuming system을 통합할수 있다.

- Easily accessible : 데이터가 Kafka에 저장되므로, 모두가 쉽게 데이터에 접근할수 있다.

- Distributed System (Scalability) : distributed architecture를 포함하고 있으므로 scalable하다. Partitioning과 replication이 distributed system 하에서 동작한다.

- Real-Time Handling : 실시간 데이터 파이프라인을 다룰수 있다. 실시간 데이터 파이프라인을 구축하는 것은 processor, analytics, storage등을 포함한다.

# Apache Kafka의 단점

- No Complete Set of Monitoring Tools : 관리와 모니터링 툴들이 부족하다. 장기적으로 신뢰하기 어렵다는 시각이 있다.

- Message tweaking issues : 카프카는 byte를 받고 보내기만 하는데, 메시지가 수정이 필요하다면 카프카의 퍼포먼스는 급격히 감소한다. 따라서 메시지가 바뀔필요가 없을때 카프카는 가장 잘 동작한다.

- wild card topic selection을 지원하지 않는다. 즉, 한번에 하나의 topic만 선택이 가능하다.

- Reduce Performance : producer가 데이터를 압축하고 consumer가 데이터를 decompress 하는 과정에서 성능 저하가 생길수 있다.

## 출처
[https://www.javatpoint.com/apache-kafka-advantages-and-disadvantages](https://www.javatpoint.com/apache-kafka-advantages-and-disadvantages)







## Segment

- 토픽들은 파티션으로 구성되 있음

- 파티션들은 segment로 이루어져 있음

- 오직 기록되고 있는 segment만 활성화 되어있음

- 세그먼트는 두개의 index(파일)로 구성됨

    - offset : kafka가 읽어야하는 위치를 찾기위한 인덱스

    - timestamp : timestamp를 가진 메시지들을 찾기 위한 인덱스

    - 카프카는 constant time에 데이터를 찾음.

## Segment Setting

- ```log.segment.bytes``` : single segment의 최대 byte 사이즈(default 1GB)

    - 값이 작아지면 그만큼 partition당 segment수가 많아진다.

    - log compaction이 더 자주 일어난다.

    - 더 많은 파일들을 열고 있어야하므로 error 발생 

- ```log.segment.ms``` : segment를 commit할때 까지 기다리는 시간(segment가 가득 차지 않았을때)

    - default 1 week

    - 값이 작아지면 log compaction이 더 자주 일어난다.


## Log Cleanup Policies

- 많은 Kafka 클러스터들이 데이터를 policy에 따라서 폐기함

- 이 개념을 log cleanup 이라고 부름

- 디스크에 있는 데이터 사이즈 조절, 필요없는 데이터 삭제

- log cleanup은 partition segment에서 일어난다.

- 크키가 작은 segment들이 많으면 log cleanup이 더 자주 발생한다. (더 많은 CPU, RAM 자원을 소모한다.)

- Policy1 : ```log.cleanup.policy=delete``` (모든 유저 토픽에 대해서 default)

    - data의 age에 따라서 삭제(default 1 week)

    - log의 최대 크기에 따라서 삭제(default  -1 == infinite)

- Policy2 : ```log.cleanup.policy=compact``` (__consumer_offsets 토픽에 대한 default)

    - 메시지의 key에 기반하여 삭제

    - active segment 커밋후 중복된 키들을 삭제

    - Segment.ms (default 7 days) : active segment 닫기 까지 기다리는 최대시간

    - Segment.bytes (default 1G) : segement의 최대사이즈

    - Min.compaction.lag.ms (default 0) : message가 compact 되기까지 기다리는 시간

    - Delete.retention.ms (default 24 hours) : compaction으로 데이터가 완전히 삭제되기 전에 삭제된 데이터를 볼수 있는 시간

    - Min.Cleanable.dirty.ratio (default 0.5) : 값이 높을수록 compaction 양이 줄어듬, 더 efficient cleaning, 값이 작을수록 compaction 양은 많아짐, 느린 cleaning

- Policy3 : ```log.retention.hours```

    - data를 보관하는 시간(default 168시간 - 1주)

    - 길수록 디스크 용량을 많이 차지함

    - 짧을수록 데이터 보관량이 줄어듬

- Policy4 : ```log.retention.bytes```

    - 각각의 파티션이 가질수있는 최대 용량(bytes), default -1 - infinite

-  자주 사용되는 조합

    - One week of retention :
    ```log.retention.hours=168```, ```log.retention.bytes``` = -1

    - ```log.retentions.hours=17520, log.retention.bytes = 524288000```

- Policy5 : ```unclean.leader.election```

    - In Sync Replicas(ISR) 이 죽을경우 다음 옵션이 존재함.

        - unclean.leader.election = false : ISR이 online 될때까지 대기 (default)

        - unclean.leader.election = true : non ISR partitions에 데이터를 producing
- unclean.leader.election = true 일경우 availability는 증가하지만, ISR로 가야하는 데이터는 사라짐
    - 매우 위험한 세팅이므로, 주의해서 세팅해야함.
- Use case : metrics collection, log collection, data loss가 acceptable한 경우







# Why do we use Kafka?
## Without Kafka
![img](https://jaegukim.github.io/assets/img/post/Kafka/2020-11-06-withoutKafka.png)
## With Kafka
![img](https://jaegukim.github.io/assets/img/post/Kafka/2020-11-06-withKafka.png)

# Topic
- 특정한 data stream
  - database의 테이블과 비슷한 개념
  - 원하는 만큼 생성가능
  - 이름으로 구분됨(identified by name)
- Topic들은 partition으로 나누어짐
  - 각각의 파티션은 정렬되어있음
  - partition 내에 있는 메시지들은 offset이라 불리는  incremental한 아이디를 가진다. ( partition 0, partition 1, ... )

![img](https://jaegukim.github.io/assets/img/post/Kafka/2020-10-06-KafkaTopic.png)

- Offset은 오직 specific partition에서 의미가 있음.
- partition 내에서만 순서가 존재함(partition 간에는 순서가 보장되지 않음)
- Data가 제한된시간 (기본 1주)내에 존재함
데이터가 partition에 써지면, 바꿀수없음(immutability)

# Brokers
- Kafka cluster는 broker(servers)들로 구성되어있다.
- broker들은 id로 구분
- 각각의 브로커는 특정 topic partition들을 가지고 있음
- 아무 브로커에게 연결되면 (called a bootstrap broker), 전체 클러스터에 연결됨

![img](https://jaegukim.github.io/assets/img/post/Kafka/2020-10-06-broker.png)

- Topic-A는 3개의 파티션, Topic-B는 2개의 파티션을 가지고 있음.

# Topic replication factor

- broker가 down되어도 다른 broker가 계속 데이터를 서빙할수있음.

![img](https://jaegukim.github.io/assets/img/post/Kafka/2020-10-06-broker2.png)

- 위 경우는 replication factor가 2인경우이다.
- 특정 한 partition에는 하나의 broker만이 leader가 될수 있다.
- 그 leader만 data를 주고 받을 수 있다.
- 다른 broker들은 leader의 데이터를 동기화한다.
이때 다른 broker들을 ISR(In-Sync-Replica)라고 부른다.

# Producers

- ack=0: Producer는 ack를 기다리지 않는다.(data 손실가능)
- ack=1: Producer는 leader에대한 ack를 기다린다.(limited 데이터 손실)
- ack=all : Leader + replicas의 ack를 기다린다(data 손실 없음)
- Message Keys : key를 세팅해놓으면 특정 키에 해당하는 데이터들은 같은 partition에 저장된다
- key를 따로 세팅해두지 않으면 round robin 으로 broker 101, 102, 103 차례대로 데이터가 저장된다. (즉, 임의의 partition에 저장된다.)

# Consumers

- Consumer들은 topic에 있는 데이터들을 읽는다.
- 어느 broker를 읽어야하는지 알고있다
- 또한 broker가 down됬을시 어떻게 대처할지도 알고있음.
- Data는 partition내에서만 순서대로 읽어진다.

# Consumer Group

- 보통 하나의 application 단위를 Consumer Group이라고 한다
- Consumer Group에 있는 Consumer들은 서로 다른 partition들을 읽어들인다.
- partition 수보다 Consumer의 수가 더많으면, 몇몇 Consumer는 비활성화된다.

# Consumer Offsets

- Kafka는 Consumer group이 읽은 offset을 저장한다.
- offset은 Kafka topic에 __consumer_offsets라는 이름을 저장된다.
- consumer가 데이터를 처리하고 나면 offset을 kafka에 commit 한다.
- consumer가 down되면 읽었던 부분부터 다시 읽어들일 수 있게 하기 위함이다.

# Delivery Semantics for consumers
- Consumer들은 언제 offset을 커밋할지 선택할수있다.
  - At most once 
    - message가 수신되자 마자 커밋
    - processing이 잘못되면 message가 유실됨(다시 읽을 수 없음)
  - At least Once (보통 선호됨)
    - 데이터가 처리되고나서 커밋
    - 처리가 잘못되면, 다시 읽음
    - 같은 메시지를 여러번 처리할수 있기 때문에, processing이 idempotent(연산을 여러 번 적용하더라도 결과가 달라지지 않는 성질)하도록 해야함
  - Exactly Once

# Kafka Broker Discovery

- Kafka broker들은 bootstrap server라고 불리는데, 오직 하나의 broker에만 연결되면 된다는 의미이다. 하나의 broker에 연결되면 전체 클러스터에 연결된다.

- 개별 broker는 모든 broker들에 대한 정보와 topic, partition들에 대한 정보를 가지고 있다.

# Zookeeper

- broker들을 관리함
- partition들에 대한 leader 선출을 수행하는데 도움을 줌
- new topic, broker dies, broker comes up, delete topics 등과 같은 정보들을 Kafka에게 알려줌
- Kafka는 Zookeeper없이는 작동할수 없음.
- Zookeeper는 홀수개의 서버로 동작함.
- Zookeeper에는 leader와 follower가 있는데, leader는 write를 담당하고 follower들은 read를 담당함.
- consumer offset은 Kafka topic에 저장되고 Zookeeper에는 더이상 저장되어있지 않음.