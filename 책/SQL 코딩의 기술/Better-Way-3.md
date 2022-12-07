# 반복 그룹을 제거 하자

스프레드 시트에서는 비슷한 데이터가 반복적으로 그룹을 이루는 것을 흔히 볼 수 있습니다. 또한 이런 반복적인 데이터들을 정규화를 시키지 않고 새 데이터베이스에 밀어 넣는 일도 빈번히 일어난다고 합니다. 여기 Home 테이블의 예를 들어보죠.

```
Home Table

|id| delivery_task1 | delivery_task2 | delivery_task3 | delivery_task4 | delivery_task5 | name  |
-------------------------------------------------------------------------------------------------
|1 | LQ25432        | LQ12345        | LA90584        | LS39473        | LQ06958        | Home1 |
|2 | LQ42315        | LQ55555        | LQ23457        | LA12356        | LB49038        | Home2 |
|3 | LQ39843        | LQ25432        | LQ39843        |                |                | Home3 |
|4 | LQ21222        | LA22126        | LB89872        | LA22126        |                | Home4 |
|5 | LQ25665        | LQ29724        |                |                |                | Home5 |
|6 | LQ25349        | LQ24776        | LQ90484        |                |                | Home6 |
```

테이블은 집과 배달 업무간의 연결 테이블입니다. 그리고 OneToMany 관계를 가지고 있죠. 하지만 여기서 문제점이 보입니다. 어떤 문제일까요? 

1. 우선 첫번째로 delivery라는 단일 속성의 반복 그룹임이 보여집니다.
2. 두번째로는 Id가 3인 로우에서 의도하지 않은 중복 값이 들어 있습니다. 

해당 예제 뿐만 아니라 1월, 2월, 3월 등 month 컬럼만 가지고 있는 반복 그룹, 혹은 위예제나 월 예제 처럼 단일 속성이 아닌 qunity1, description1, price1, qunity2, description2, price2, .... 등 이렇게 반복되는 그룹을 가지고 있을 수 있습니다. 여기서 중요한건 **우리는 반복 그룹 패턴임을 알아 챌 수 있어야 합니다.**



### 반복 그룹 처리 쿼리 UNION

반복 그룹은 우리에게 다양한 영향을 줍니다. 반복 그룹은 쿼리를 만들어 속성별로 묶은 보고서를 생성하기가 어렵습니다. 이 말이 무엇이냐 하면 위에 예제에서 delivery_task6를 추가하거나 delivery_task5를 제거 할 일이 발생 하면 컬럼은 추가하거나 제거해야 합니다. 또한 해당 테이블의 데이터를 기준으로 만든 모든 쿼리, 보고서의 설계도 수정해야합니다. 

***즉 영향도 측면에서 컬럼은 비싸고 로우는 저렴하다.***

이 말은 꼭 기억해야 할 유용한 공식입니다. 향후 입력될 유사한 데이터로 컬럼을 추가하거나 제거하도록 테이블을 설계 했다면 마음속으로 적색 경보를 울려야합니다. 컬럼을 추가하는 것이 아닌 로우를 추가하는게 훨씬 바람직한 설계입니다. 위의 예제는 Home id를 외래키로 설정해 Delivery라는 테이블을 만들어주는 것이 좋습니다. 

* Home

```
|home_id| name  |
-----------------
|1      | Home1 |
|2      | Home2 |
|3      | Home3 |
|4      | Home4 |
|5      | Home5 |
|6      | Home6 |
```

* Delivery

```
|delivery_id| task    | home_id |
---------------------------------
|1          | LQ25432 | 1       |
|2          | LQ42315 | 2       |
|3          | LQ39843 | 3       |
|4          | LQ21222 | 4       |
...
```

그리고 나서 반복 그룹을 처리할 때는 UNION 쿼리를 사용합니다. 만약에 이런저런 이유로 적절하게 정규화를 적용한 설계를 할 수 없다면, UNION을 사용해 읽기 전용 뷰를 만들어 데이터를 '정규화'할 수도 있습니다. 다음 쿼리문을 보면 쉽게 이해하실 겁니다.

```sql
SELECT id AS home_id, delivery_task1 AS task
FROM Home WHERE delivery_task1 IS NOT NULL 
UNION
SELECT id AS home_id, delivery_task2 AS task
FROM Home WHERE delivery_task2 IS NOT NULL 
UNION
SELECT id AS home_id, delivery_task3 AS task
FROM Home WHERE delivery_task3 IS NOT NULL 
UNION
SELECT id AS home_id, delivery_task4 AS task
FROM Home WHERE delivery_task4 IS NOT NULL 
UNION
SELECT id AS home_id, delivery_task1 AS task
FROM Home WHERE delivery_task1 IS NOT NULL 
ORDER BY home_id, task;
```

 UNION을 사용하려면 각 SELECT 문에서 사용되는 컬럼의 데이터 타입이 동일해야 하며 나열 순서도 같아야 합니다. 즉 나머지 AS home_id나 AS_task등 첫번째 SELECT 이후에는 또다시 선언을 안해도 됩니다. 즉 UNION은 맨 앞에 명시된 컬럼 이름을 취합니다.

각 SELECT 문의 WHERE 절에 다른 조건을 붙이는 것도 가능합니다. 데이터에 따라 다양한 조건을 걸 수 있다는 거죠. 또한 UNION 쿼리에서의 ORDER BY는 맨 마지막 SELECT절에서만 가능합니다. 

이렇게 UNION을 통해 반복 그룹에 대한 처리에 대해 살펴봤습니다. 다음에는 컬럼당 하나의 특성만 저장하자를 공부해보겠습니다.