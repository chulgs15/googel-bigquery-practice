# 데이터 유형

## 데이터 유형 속성

* null 허용 데이터 유형.
  * array 도 Null 을 허용한다. 
* 정렬 불가능한 데이터 유형
  * array,   struct, geography, json
* null 정렬
  * asc : NULL 이 먼저 / desc : NULL 이 마지막
  * nulls first / nulls last 사용 가능.
* 부동 소수점 정렬
  * 가장 작은 값 순서로는...
  * null > NaN > -inf > 음수 > 0 > 양수 > ...
* 그룹화 가능한 데이터 유형
  * `partition by` 표현식은 부동 소수점 휴형을 포함할 수 없음.
* 비교 가능한 데이터 유형
  * geography, json은 비교가 안된다.
  * struct 의 비교는 필드 순서에 따라 지원.

## 데이터 유형 크기

* 유형은 기존 DB 랑 같은데...특이점은 길이를 지정하는 부분이 없네.

## 매개변수화된 데이터 유형 

* 말하자 마자, 값의 제한을 두는 방식이 있네
* 아래 데이터 유형은 매개변수를 지정할 수 있다.
* 예제 소스 코드

```sql
DECLARE x STRING(10);

-- 성공
SET x = "hello";

-- 실패
SET x = "this string is too long"

-- Declare variables with type parameters.
DECLARE x NUMERIC(10) DEFAULT 12345;
DECLARE y NUMERIC(5, 2) DEFAULT 123.45;

-- The variable x is treated as a NUMERIC value when read, so the result of this query
-- is a NUMERIC without type parameters.
SELECT x;

-- Type parameters are not propagated within expressions, so variables x and y are treated
-- as NUMERIC values when read and the result of this query is a NUMERIC without type parameters.
-- 즉, 결과가 선언한 NUMERIC 이 아닌 NUMERIC 자체로 인식.
SELECT x + y;
```

## 배열 유형

* 0개 이상의 순서가 지정된 목록.
* ARRAY 안에 ARRAY 를 허용하지 않는다.
* 소스 코드

```SQL
SELECT
  CAST(NULL AS ARRAY<INT64>) IS NULL AS array_is_null;

+---------------+
| array_is_null |
+---------------+
| TRUE          |
+---------------+

WITH
  Items AS (
  SELECT
    [] AS numbers
  UNION ALL
  SELECT
    CAST(NULL AS ARRAY<int64>) )
SELECT
  numbers
FROM
  Items;

+---------+
| numbers |
+---------+
| null    |
| null    |
+---------+

-- 아래 2문장의 차이는 잘 모르겠다.
SELECT FORMAT("%T", [1, NULL, 3]) as numbers;

+--------------+
| numbers      |
+--------------+
| [1, NULL, 3] |
+--------------+

-- error
SELECT [1, NULL, 3] as numbers;
```

* 중첩된  Array 를 사용할 경우.

```
ARRAY<STRUCT<ARRAY<INT64>>>
```

# 리터럴

## 문자 리터럴



## DATETIME 

아래 명령어로 볼 수 있다.

```SQL
SELECT
  count(d.forecast_date) as cnt
FROM
  mydataset.country_14d d 
WHERE
  1=1
  and d.forecast_date >= DATETIME '2014-09-27T12:30:00.45';
```

아래 명령어를 수행하면 오류.

```SQL
select DATETIME '2014-09-27T12:30:00.45' + 1;
```

DATETIME 은 INTERVAL 로 날짜를 더할 수 있다.

```SQL
select DATETIME '2014-09-27T12:30:00.45' + INTERVAL '1' DAY;
```



























