# 테이블 간 관계를 명확히 하자

이론적으로는 관련 컬럼의 데이터 타입이 같으면 테이블 간에 어떤 관계든 맺을 수 있습니다. 하지만 뭔가 할 수 있다는 이유만으로 반드시 해야 하는 것은 아닙니다. 대표적으로 비슷한 컬럼을 가진 테이블들의 컬럼 분리입니다. 예를 들어보죠.

* Customer, Employee, Vendor 테이블 구조

```
| Customer          |      | Employee          |      | Vendor            |
---------------------      ---------------------      ---------------------
| customer_id       |      | employee_id       |      | vendor_id         |
| cust_first_name   |      | empl_first_name   |      | vend_first_name   |
| cust_last_name    |      | empl_last_name    |      | vend_last_name    |
| cust_address      |      | empl_address      |      | vend_address      |
| cust_city         |      | empl_city         |      | vend_city         |
| cust_zip_code     |      | empl_zip_code     |      | vend_zip_code     |
| cust_phone_number |      | empl_phone_number |      | vend_phone_number |
                           | empl_dob          |      | vend_fax_number   |
                           | manager_id        |      | vend_email_address|
```

다음 테이블 컬럼은 주소와 전화번호 등 비슷한 컬럼이 많이 있음을 볼 수 있습니다. 즉 우리가 앞서 봤던 'Better Way 2 - 중복으로 저장된 데이터 항목을 제거하자'에서 살펴본 중복 데이터 규칙을 위반하게 됩니다. 그럼 어떻게 하면 될까요?

1. 우선 전화번호 기준으로 첫 번째로 Contact라는 단일 테이블을 만들어 통합한다.
   * 이 방법은 문제가 있습니다. 예를 들어 employee_id, customer_id, vendor_id는 이제 contact_id로 통합되어 변경될 텐데 해당 id가 고객인지, 공급처인지, 직원인지 알 수 없습니다.
2. 또 다른 방법으로 Contacts 테이블과 일대일 관계를 맺은 Customer, Vendor, Employee 테이블을 만든다.
   * 이렇게 하면 manager_id, vend_fax_number 등 엔티티에 특화된 데이터를 다른 고객 로우와 분리해 유지하기가 쉽다는 장점이 있습니다.
   * 그러나 이 방법을 사용하면 데이터베이스 스키마를 사용하는 애플리케이션은 지금보다 훨씬 복잡해집니다. 즉 어떤 엔티티에서 특화된 데이터를 가졌는지, 어떤 엔티티가 어떻게 연결되어있는지 등 신경 쓸게 많아집니다.

그렇다면 실무에서는 어떻게 할까요? 사실 회사에서는 이런 문제를 해결하려고 시간과 비용을 쓰지 않습니다. 사실 어찌 보면 당연합니다. 3 테이블의 관계를 이해해보면 공급처나 회사 임직원이 제품을 판매하는 회사의 고객이 될 리는 없습니다. 또한 만약 될 수 있더라도 **드문 상황에서 가끔 발생하는 중복 데이터를 해결하는 것이 데이터베이스 스키마의 간소화에 비해 비용이 낮습니다.**



### 만약 해야 한다면 어떻게?

직원이 판매 지역을 할당해야 하는 시나리오를 고려해 보면, 결론적으로 판매 지역을 기준으로 고객과 직원을 연결 할 수 있습니다. 예를 들어 cust_zip_code, emp_zip_code 이 두 컬럼은 데이터 타입도 동일하고 도메인도 같기 때문에 관계를 형성해서 해결하면 될 것 같습니다. 그리고 조인을 통해 어느 고객이 어느 직원 근처에 사는지 유추해 낼 수도 있을 것 같습니다.

하지만 단순히 Customer 테이블에 employee_id라는 외래키를 생성하면 가능할 것 같지만, 이 방법은 실제로 더 많은 문제를 일으킵니다. 예를 들어 만약 고객이 이사를 한다면 어떻게 될까요? 해당 고객이 고객의 정보를 수정할 수는 있지만, 이 고객과의 연결된 직원을 갱신해야 한다는 것을 알아차리지 못하기 때문에 새로운 오류가 발생시킬 수 있습니다. 차라리 SalesTerritory라는 테이블을 만들어 unique 속성을 가진 zip 컬럼으로 고객과 직원의 관계를 형성시키면 좋을 것 같습니다.

반대로 판매 지역이 아닌 다른 기준으로 Customer 테이블에 employee_id 외래키를 설정하는 것도 나쁘지 않습니다. 고객과 직원 간 연결에 실질적인 유동성을 반영 할 수 있기 때문입니다. 즉 상황을 봐가면서 연결을 하는 것이 좋은 방법이라 생각합니다.



### Attribute 컬럼

만약 가끔 사용하는 컬럼 때문에 컬럼을 추가하는 게 좋을까요?? 제가 생각하기엔 별로 좋은 선택으로 보이지는 않습니다. 그렇다고 데이터를 저장을 안 할 수는 없는 노릇입니다. 이런 상황에서는 XML이나 JSON 문서를 저장할 수 있는 Attribute 컬럼을 생성해서 관리하는 것이 더 나을 수 있습니다. 

* Product

```mysql
CREATE TABLE Product {
	product_number int NOT NULL PRIMARY KEY,
	product_description varchar(255) NOT NULL
};
```

* ProductAttributes

```mysql
CREATE TABLE ProductAttirbute {
	product_number int NOT NULL,
	attribute_name varchar(255) NOT NULL,
	attribute_value varchar(255) NOT NULL,
	CONSTRAINT PK_ProductAttribute
		PRIMARY KEY (product_number, attribute_name)
};

ALTER TABLE ProductAttribute
	ADD CONSTRAINT FK_ProductAttributes_ProductNumber
		FOREIGN KEY (product_number)
			REFFERENCES Product (product_number);
```

위에 예제는 EAV(Entity-Attribute-Value) 모델이라고 합니다. 속성이 있다면 추가로 ProductAttribute 테이블에 저장을 시켜 관리하는 구조로 되어 있습니다. 하지만 속성값을 컬럼이 아닌 로우로 저장해 문제를 해결한 것처럼 보이지만, 특정 속성의 특정 제품을 추출하는 쿼리는 훨씬 복잡해졌습니다. 특히 다뤄야 할 속성이 여러 개라면 더욱더 그렇습니다. 이 속성들이 바로 반정형 데이터입니다.

사실 설계자에게는 정형 데이터와 반정형 데이터를 구별할 수 있는 능력을 요구합니다. RDB에서는 입력하기 전 가능한 모든 컬럼과 데이터 타입을 나열해 데이터를 제대로 정의해야 합니다. ***따라서 관계를 정의하는 데 어려움을 겪고 있다면 반정형 데이터를 다루고 있는지, 관계형 모델에서 직접 반정형 데이터를 사용할 필요는 없는지 자문해 보는 것이 좋습니다.***



### 결론은?

사실 데이터 모델 설계부터 맞춰 애플리케이션을 구현해야 하지만, 그러기엔 어렵습니다. 그렇기 때문에 종종 어려운 일이 되곤 합니다. 실제로 여러 데이터 모델 중 하나를 선택하면 데이터베이스를 사용할 애플리케이션을 설계하는 방법에 상당한 수준의 변경이 뒤따르고 시간에 영향을 미치기도 합니다. 하지만 중요한 것은 데이터 모델의 정확성 유무를 규정하는 것은 비즈니스 규칙이며, 우리는 애플리케이션을 설계할 때 이 점을 감안 해야 한다는 것입니다.

다음에는 제 3정규화로도 부족하다면 더 정규화 하자에 대해 알아보겠습니다.