## HDFS 2 Sink Connector

- 다양한 포맷의 HDFS 2.x 파일로 카프카 토픽 데이터를 저장할수 있는 카프카 커넥터

- HiveQL로 쿼리할수 있도록 제공

커넥터는 주기적으로 카프카로부터 데이터를 poll하고 HDFS에 쓴다. 카프카 토픽의 데이터는 제공된 partitioner의 의해 분할되고 chunk로 나눠진다. 하나의 데이터 chunk는 topic, kafka partition, start.end offset을 파일명으로 갖는 HDFS 파일로 표현된다. partioner가 명시되지 않는경우, default partitioner(카프카 파티셔닝을 보존하는)가 사용된다. 데이터 chunk 크기는 HDFS에 기록된 레코드 개수, HDFS에 기록된 시간 그리고 스키마 호환성으로 결정된다.

HDFS 커넥터는 Hive와 호환되고, 커넥터는 각각의 카프카 토픽에 대해서 external hive partitioned table을 생성한고 HDFS에 이용가능한 데이터에 따라서 테이블을 업데이트한다.



## 특징

- **Exactly Once Delivery** : 커넥터는 HDFS에 데이터를 한번만 쓸수 있도록, write-ahead log를 사용한다. 또한 HDFS 파일들에 카프카 오프셋 정보를 인코딩하므로써, 커밋된 오프셋들을 관리한다. HDFS 파일에 오프셋 정보를 저장하는것은 failover 발생시 마지막으로 커밋된 오프셋부터 데이터를 처리할수 있도록 하기 위함이다.

  > HDS에 오프셋정보를 커밋할뿐만 아니라, 오프셋 정보는 커넥터 progress 모니터링을 위해서 Kafka Connect에도 기록이 된다. 커넥터 실행 시작시, HDFS 커넥터는 HDFS 파일들로부터 오프셋 복원을 시작한다. HDFS 파일이 존재하지 않을경우, 커넥터는``` __consumer_offsets``` 에 있는 컨슈머 그룹에서 오프셋을 찾는다. 만약 거기에도 오프셋이 존재하지 않으면, 컨슈머는 ```auto.offset.reset``` 에 명시된 offset management policy를 따른다. default는 ```auto.offset.reset = earliest``` 이다.

- **Extensible Data Format** : 커넥터는 Avro와 Parquet format으로 HDFS에 쓸수 있도록 지원. Format class를 확장함으로써, 다른 포맷 데이터도 쓸수 있다.

- **Hive Integration** : Hive integration을 지원하며, 커텍터는 자동으로 HDFS에 저장된 파일들을 대상으로 , 각각의 토픽에 대한 external partitioned table을 생성한다.

- **Secure HDFS and Hive Metastore Support** : Kerberos authentication을 지원하고 secure HDFS와 Hive metastore와 동작한다.

- **Pluggable Partitioner** : default partitioner, field partitioner, time-based partitioner(daily, hourly partitioner) 지원. custom partitioner 생성가능. TimeBasedPartitioner를 확장해서 커스텀 TimeBasedPartitioner 생성가능.

- **Schema Revolution** : Schema evolution은 기본 네이밍 전략(```TopicNameStrategy```)으로 생성된 레코드들에 대해서만 동작한다. 다른 네이밍 전략들이 사용되면 에러가 발생할수 있다. 이는 레코드들이 서로 호환이 안되기 때문이다. 만약 다른 네이밍 전략이 사용된다면, ```schema.compatibility```는 ```None```으로 설정되어야한다. 이는 작은 오브젝트 파일들을 생겨나게 할수 있다, 왜냐하면 sink connectors 는 레코드에서 schema id가 변경될때마다 매번 새로운 파일을 생성하기 때문이다. 



## Storage Object Upload

커넥터가 각각의 레코드를 처리할때,  레코드를 기록할 파티션(파일 기록의 단위)을 결정하기 위해서 partitioner를 사용한다. 이 과정은 커넥터가 파티션이 충분한 레코드를 가지고 있고, hdfs로 업로드되어야 한다고 판단할때 까지 진행된다.

언제 파티션 파일을 flush해서 hdfs로 업로드 할지 결정하는 기술을 ```rotation strategy``` 라 하며, 여러 방법이 존재한다.

- **Maximum number of records** : 단일 storage object(hdfs 파일)에 기록되어야하는 최대 레코드수로 결정하는방법
- **Maximum span of record time** : ```rotation.interval.ms```  설정은 파일에 추가적인 레코드가 기록 되어야하는 최대 시간(millisecond)이다. 이때 시간값의 대상은 partitioner의 ```timestamp.extractor``` 에 정의된다. 만약 새로운 레코드가 interval내의 시간값이 아니면, hdfs파일로 기록되고 파일 데이터의 offset이 커밋된다.
- **Scheduled rotation** : ```rotate.schedule.interval.ms``` 는 ```rotation.interval.ms``` 와 유사하지만, 차이점은 파일에 기록되는 레코드의 **system time** 을 사용하는것이 차이점이다. 반드시 ```timezone``` 파라미터가 설정되어야한다. 

## 상세 configuration 설정

[https://docs.confluent.io/kafka-connect-hdfs/current/configuration_options.html](https://docs.confluent.io/kafka-connect-hdfs/current/configuration_options.html)

[https://docs.confluent.io/kafka-connect-hdfs/current/index.html#storage-object-formats](https://docs.confluent.io/kafka-connect-hdfs/current/index.html#storage-object-formats)