# 인덱스를 만들 때는 널을 고려하자

RDB에서 NULL은 데이터가 들어가지 않은 특별한 값입니다. 따라서 NULL은 동등이나 연산자로 비교가 불가능하고 IS NULL 키워드를 통해 널 값이 있는지 없는지만 확인 할 수 있습니다.

인덱스는 보통 ``WHERE`` 조건 절엥 자주 사용되는 컬럼, 분포도가 높은 컬럼을 선정합니다. 그리고 인덱스를 통해 쿼리의 성능을 향상 시킵니다. 따라서 개발자는 인덱스를 설정할 때 해당 컬럼이 NULL을 포함하는지 혹은 NULL값을 포함한 인덱스를 시스템이 어떻게 처리하는지 등 고려해야합니다. 



## 인덱스의 특별한 기능

데이터베이스 시스템에서 인덱스를 걸때 ``NULL``를 제외해주고 인덱스를 생성할 수 있는 기능을 제공합니다. 만약 이런 기능이 없으면 어떻게 될까요? 

>  ``NULL``도 인덱스가 되버려 저장 공간이 심각하게 낭비 될 것입니다.

또한 일부 데이터베이스 시스템은 빈 문자열도 ``NULL``로 처리해버리기 때문에 인덱스 컬럼 설정도 어려울 것입니다. 따라서 모든 데이터베이스는 인덱스 처리할 때 ``NULL`` 에 대한 처리 기능을 제공해줍니다. 그렇다면 각각의 데이터베이스 시스템에서 어떻게 처리하는지 확인해보겠습니다.



## DB2

> 1. 인덱스에 NULL 값 허용 O - (기본키는 NULL 허용 X)
> 2. NULL 값 끼리 동일하게 판단 O
> 3. 빈 문자열 NULL 변환 X

DB2는 위왁 같은 성질을 가지고 있습니다. 따라서 Unique index를 생성할 경우 NULL을 따로 처리해주지 않으면 중복으로 인한 오류가 발생합니다. 따라서 ``EXCLUDE NULL KEYS``를 사용하여 NULL을 Index에 포함시키지 않게 처리합니다.

* Unique Index에서 NULL 처리 방법

```sql
CREATE UNIQUE INDEX StudentNumber_IDX
	ON Students (Number ASC)
	EXCLUDE NULL KEYS;
```

그렇다면 중복이 가능한 Non-Unique Index에서는 ``NULL``을 어떻게 처리 할까요? Unique Index와 마찬가지로 ``EXCLUDE NULL KEYS``통해 처리합니다.

* Non-Unique Index에서 NULL 처리 방법

```sql
	CREATE INDEX StudentPhone_IDX
		ON Students (PhoneNumber)
		EXCLUDE NULL KEYS;
```

그럼 왜 위와 같은 처리를 할까요? 저는 다음과 같은 장점이 있다고 생각합니다.

1. 인덱스로 인한 저장 공간을 절약할 수 있다.
2. 대다수의 컬럼 값이 NULL일 경우 유용하다.

하지만 조건절에 IS NULL을 검사하는 경우 테이블을 Full Scan 합니다.



## 액세스

>1. 인덱스에 NULL 값 허용 O - (기본키는 NULL 허용 X)
>2. NULL 값 끼리 동일하게 판단 X
>3. 빈 문자열 NULL 변환 O

DB2와 마찬가지로 인덱스에 NULL 값을 허용합니다. 하지만 NULL 값 끼리는 동일하게 처리하지 않기 때문에 유일 인덱스 컬럼에도 NULL가진 로우를 여러개 저장할 수 있습니다. 따라서 비유일 인덱스와 유일 인덱스는 똑같다고 볼 수 있습니다. 그럼 어떻게 처리하는지 한번 보겠습니다.

* Index에서 NULL 처리 방법

```sql
CREATE INDEX StudentNumber_IDX
	ON Students (Number)
	WITH IGNORE NULL;
```

바로 ``WITH IGNORE NULL`` 키워드를 통해 처리를 해줍니다.



## SQL Server

>1. 인덱스에 NULL 값 허용 O - (기본키는 NULL 허용 X)
>2. NULL 값 끼리 동일하게 판단 O - (Unique Column에는 NULL값 하나만 지정 가능)
>3. 빈 문자열 NULL 변환 X

SQL Server는 DB2와 비슷한 성질을 가지고 있습니다. 따라서 인덱스에서 널 값을 제외하려면 필터링된 인덱스를 만들어야 합니다. 그 방법은 다음과 같습니다.

* Index에서 NULL 처리 방법

```sql
CREATE INDEX StudentNumber_IDX
	ON Students (Number)
	WHERE Number IS NOT NULL;
```

``IS NULL`` 조건을 통해 Index를 필터링 시킵니다. 또한 빈 문자열은 NULL처리를 하지 않기 때문에 인덱스를 만들 수 있습니다.



## MySQL

>1. 인덱스에 NULL 값 허용 X - (기본키는 NULL 허용 X)
>2. NULL 값 끼리 동일하게 판단 X
>3. 빈 문자열 NULL 변환 X

인덱스를 만들 때 모든 널 값이 동일하지 않게 처리하기 때문에 널 값이 있는 컬럼에 유일 인덱스를 만들 수 있습니다. 또한 이 컬럼을 포함한 여러 개의 로우도 저장할 수 있습니다. 

하지만 MySQL은 인덱스에 NULL 값을 허용하기 때문에 ``제거하는 옵션``이 없습니다. ``IS NULL``과 ``IS NOT NULL``를 사용해 인덱스가 있으면 사용합니다. 또한 빈 문자열은 NULL로 변환하지 않고 길이는 0인 문자열로 처리합니다.



## Oracle

> 1. 인덱스에 NULL 값 허용 X - (기본키는 NULL 허용 X)
>    * 하지만 복합키에 NULL이 아닌 값이 하나라도 있으면 인덱스로 만들 수 있다.
> 2. NULL 값 끼리 동일하게 판단 X
> 3. 빈 문자열 NULL 변환 O

오라클은 특이하게 인덱스에 NULL을 허용하지 않지만, 복합키인 경우 값이 하나라도 NULL이 아니면 인덱스로 만들 수 있습니다. 즉 하나의 상수 값을 포함하거나 NULL을 처리 할 수 있는 ``NVL()``함수를 통해 인덱스를 사용하도록 강제할 수 있습니다. 

* 복합키로 Index에 NULL 허용하는 방법

```sql
CREATE INDEX StudentNumber_IDX
	ON Students (StudentNumber ASC, 1);
```

``NVL()``함수를 사용해서 처리하는 방법은 다음과 같습니다.

* ``NVL()`` 함수로 변환해 인덱스로 생성하는 방법

```sql
CREATE INDEX StudentNumber_IDX
	ON Students (NVL(StudentNumber, 'unknown'));
```

이 방법은 다음과 같은 단점이 있습니다. 밑에 예제와 같이 반드시 ``NVL()`` 함수를 사용해야 한다는 것입니다. 

> WHERE NVL(StudentNumber, 'unknown') = 'unknown' )

오라클은 빈 문자열이 들어올 경우 NULL 과 동일하개 인식합니다. 즉 빈 문자열이 올경우 NULL값으로 저장 됩니다.



## PostgreSQL

>1. 인덱스에 NULL 값 허용 O - (기본키는 NULL 허용 X)
>2. NULL 값 끼리 동일하게 판단 X
>3. 빈 문자열 NULL 변환 X

PostgreSQL은 인덱스에 널 값을 포함할 수 있지만 ``WHERE`` 절을 통해 조건을 정의하여 제외합니다.

* Index에서 NULL 처리 방법

```sql
CREATE INDEX StudentNumber_IDX
	ON Students (Number)
	WHERE Number IS NOT NULL;
```

또한 길이가 0인 문자열을 NULL로 변환하지 않으며 빈 문자열과 NULL을 다른 값으로 인식합니다.