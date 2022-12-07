## 에어플로우

> 여러개의 테스크를 연결해서 실행시켜주는 데이터 워크 플로우 관리도구
>
> 워크플로우를 작성하여 모니터링까지 할 수 있게 해주는 오픈소스 플랫폼



### 왜 사용할까?

1. 데이터 처리를 위한 복잡한 단계를 Flow Diagram 형태로 한눈에 볼 수 있어서 사용
2. 파이썬 기반으로 비교적 쉽게 각 task를 작성 가능
3. 각 task의 실행 시간을 한눈에 파악 가능
4. 필요한 경우 특정 task만 실행 가능
5. 쉬운 job 스케줄 관리 및 전체 job의 실행 상황을 볼 수 있음
6. task들을 병렬로 실행 가능
7. 분산 환경 지원 가능



### DAG

1. 에어플로우에서 말하는 워크 플로우 단위
2. Task들의 집합으로 여러개의 Task들이 순서와 Dependency롤 가짐
   * airflow scheduler
     - 디폴트 홈 : `~/airflow`
     - DAGs 위치 : `~/airflow/dags`
     - airflow.cfg = 기본 설정값 파일 자동생성. (웹UI 상, Admin->Configuration 확인가능)
     - airflow.db = 디폴드 데이터베이스
     - 웹서버 PID 파일 = `~/airflow/webserver.pid`
3. DAG는 한개 이상의 작업들로 이루어짐
4. Directed Acyclic Graph의 약자 - **방향성 비순환 그래프**
   * Directed - 노드와 노드간의 엣지가 단방향으로 연결
   * Acyclic - 한번 지나간 노드는 다시 지나가지 않음
   * Graph - 노드와 엣지로 구성되는 구조
5. 작성된 Dag는 변경된 사항을 반영하기 위해서 airflow의 스케쥴러에 의해서 주기적으로 호출되므로 파일이 너무 크거나 복잡하게 만들면 안됌
6. Dag에서는 실제 처리 내용을 정의하기 보다는 처리될 순서가 정의가 될 수 있도록 함
   * 실제 처리내용은 별도 파일(jar, lib)에서 호출 될 수 있는 구조가 좋음
7. Task간의 데이터 전달은 xcom을 사용



### Example

* Python Operator

  ```python
  t1 = PythonOperator(
  	task_id = 'report_data',
    python_callable=report_data,
    provide_context=True,
    op_kwargs={'target_day':target_day},
    dag=dag
  )
  ```

  | parameter       | description                                                  |
  | --------------- | ------------------------------------------------------------ |
  | task_id         | Task를 구분하기 위한 이름 (유니크)                           |
  | python_callable | 실제 호출된 python 함수명                                    |
  | Provide_context | python 함수 호출시 해당 함수에서 사용될 수 있는 기본적인 args값을 넘겨줄지 여부 |
  | op_kwargs       | 기본 argument 외에 추가로 넘겨줄 파라미터 정의               |
  | dag             | Default dag 명                                               |

  

* BashOperator

  ```python
  dag = DAG(
      dag_id='hello_airflow', // DAG를 구별하는 유일한 이름
      default_args=args, // 각종 설정 데이터
      schedule_interval="@once") // DAG실행 간격
  
  # Bash Operator
  cmd = 'echo "Hello, Airflow"'
  BashOperator(task_id='t1', // 작업을 구별하는 이름
               bash_command=cmd, // 실행할 bash 명령어
               dag=dag) // 작업이 속하는 DAG
  ```



### Airflow Web 기능

1. Dag를 enable 시키고 수동으로 수행 시키기 가능
2. 수행중인 Dag를 클릭한후 Graph View에서 실행 상태 확인 가능
3. Gant View에서 각 Task의 실행에 소요된 시간을 파악 가능



### 추가 공부

#### Airflow Task 병렬 처리

* 병렬로 처리하기 위해서 sequential executor가 아닌 celery executor를 사용해야 함
* celery executor 사용을 위해서는 Broker가 필요한데 이를 위해 RabbitMQ나 Redis가 필요
* DB Lock등 조심히 사용



#### Dag 속성

- start_date: DAG 구동의 기준점이 될 시간
  - start_date는 schedule_interval에 의해 처음 구동될 시간을 정함
  - start_date 자체가 DAG의 시작시간이 되지 않는 다는 것을 기억!
- schedule_interval: 어느 주기로 실행될지 정하는 속성
  - schedule_interval은 cron expression와 (from datetime import timedelta)의 timedelta를 이용하여 구현 가능
- catchup=False or catchup=True: DAG를 구동하면서 과거데이터를 쭉 수집하면서 갈지(catchup=True) 아니면 현재 수집 되어있어야 할 데이터만 수집할지 결정하는 속성

