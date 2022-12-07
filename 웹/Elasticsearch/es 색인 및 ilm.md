## 색인 옵션

* store
  * 필드를 별도 인덱스 조각에 저장해서 조회를 빠르게 하며, 디스크 공간을 소모하지만 도큐먼트로부터 추출해야하는 연산은 줄임
  * default : false
* index
  * 필드를 색인할 때 사용하며 색인 필드는 검색이 안됌
  * default: true
* Null_value
  * 필드가 null 일 대 기본값을 지정함
* boost
  * 필드의 중요도를 변경하는 데 사용함
  * default: 1.0
* search_analyzer
  * 검색 시 사용할 분석기를 지정하며, 지정하지 않는 경우 부모 객체의 분석기를 사용함
  * default: null
* analyzer
  * 사용할 기본 분석기를 설정함
  * Default: null
* include_in_all
  * 현재 필드를 _all 특수 필드에 색인할 것인지를 지정함
  * Default: true
* norms
  * 더 나은 스코어 쿼리에 사용하며 필드를 오직 필터링에만 사용한다면 리소스 사용을 줄이기 위해 비활성화 하는 것이 좋음
  * Default: analyzed 필드는 true, not_analyazed 필드는 false
* copy_to
  * _all 필드와 유사한 기능으로 사용하기 위해 필드 내용을 다른 필드로 복사함
* Ignore_above
  * 지정한 값보다 더 큰 색인 문자열을 건너 뛸 수 있음
  * 정확한 필터링, 집계, 정렬을 위한 필드를 처리하는데 유용함





### 추가

* String 
  * Keyword :문장 전체를 저장하며 부분검색이 불가 하지만 리소스 비교가 적음
  * Text : tokenizer, analyzer로 인해 파싱/ 잘라서 저장. 부분 검색 가능 하지만 리소스 비용이 큼

* Numeric 
  * 숫자형 타입 -> long, integer, short, byte, double, float, half_float, scaled_float

* Range
  *  integer_range, float_range, long_range, double_range, date_range

* 그외
  * Date, date_nanos, boolean, binary, Object, Nested, Geo-point, …



* Scroll API를 통해 paging처리를 해서 window 만큼 여러번에 나눠 가져올 것
* 너무 큰 doucment를 만들지 말것 (HTTP의 Max Content Length는 기본 100mb) < -이값이 넘어가면 에러 발생, 루센자체에도 용량 제약이 있음
* 시계열 데이터의 경우 더이상 갱신이 이뤄지지 않는 인덱스는 refresh_interval 값을 끄는게 좋음
* 데이터 색인이 이뤄지는 인덱스에 대해서 refresh를 끄거나, 색인이 끝날때 까지 replica를 생성을 막음
  * 복제본을 생성하는데도 시간이 소요되므로 이를 잠시 멈췄다가 색인이 완료된 후 replica를 생성한다면 유리할 수 있음
  * 그러나 색인 도중 장애 발생으로 일부가 누락된다면 그대로 유실이 되기 때문에 정말 급하지 않다면 최소 1개의 복제본은 색인과 동시에 생성되도록 하는게 좋음



* ILM 설정시 주의사항

* lifecycle policy 는 인덱스가 어떻게 각 단계로 이동하며, 각 단계마다 어떤 작업들이 수행되는지 관장한다.
   적용 가능한 policy 는 아래와 같다.
  * 새로운 인덱스로 roll over 시 최대 size 와 age 설정
  * 인덱스가 더 이상 update 되지 않아 primary shards 를 줄여도 되는 순간 설정
  * 강제 병합을 통해 삭제 마킹된 문서를 영구적으로 삭제할 시점 설정
  * 인덱스를 저 사양 머신으로 옮겨도 되는 시점 설정
  * 가용성(Availability) 가 더 이상 중요치 않아 replica 를 줄여도 되는 시점 설정
  * 언제 인덱스를 실제로 지울 것인지 설정

