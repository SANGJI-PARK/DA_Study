### SELECT, FROM

- SELECT :
  - 조회하고 싶은 컬럼을 지정한다.
  - 다수의 컬럼을 지정할 때는 콤마(,)로 구분한다.
  - 모든 컬럼을 지정할 땐 *를 입력한다.
- FROM : 컬럼이 속해있는 테이블을 나타낸다.
- 한번에 하나의 SELECT만 실행할 수 있다.

```sql
SELECT year,
       month,
       west
  FROM tutorial.us_housing_units
SELECT *
  FROM tutorial.us_housing_units
```

- 서로 다른 테이블에서의 컬럼들을 조회할 때?

### ORDER BY

(1) 정렬

- 조회한 데이터를 하나 혹은 그 이상의 컬럼을 기준으로 정렬한다.
- 기본으로 오름차순(ascending)으로 정렬된다.
- 내림차순(descending)으로 정렬할 땐 컬럼 뒤에 'DESC'을 추가한다.

```sql
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year = 2013
ORDER BY year_rank DESC
```

- 다수의 컬럼으로 정렬하고 싶을 땐 각 컬럼을 콤마(,)로 구분한다.
- DESC는 내림차순을 적용하고 싶은 각 컬럼의 뒤에 쓴다.

```sql
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE year_rank <= 3
ORDER BY year_rank DESC, year
```

(2) 컬럼을 숫자로 대체하기 (Mode에서만 가능)

- SELECT에서 조회되는 컬럼들을 순서에 따라 1부터 숫자로 대체할 수 있다.
- 위 쿼리와 아래의 쿼리는 결과가 같다.

```sql
SELECT * 
FROM tutorial.billboard_top_100_year_end
WHERE year_rank <= 3
ORDER BY 2 DESC, 1
```

(3) 정렬에서 LIMIT을 사용할 때

- LIMIT은 정렬이된 후 적용된다.

### SELECT DISTINCT

- Viewing unique values
- 각 컬럼의 유니크값을 확인하는 것은 데이터를 어떻게 그룹핑 혹은 필터링하고 싶은지를 알 수도 있게 해주는 좋은 방법이다.
- 두 개(혹은 그 이상)의 컬럼을 SELECT DISTINCT 절에 넣는다면, 두 개의 컬럼 가능한 모든 pairs의 unique한 결과가 나온다. (컬름은 콤마로 구분한다.)

```sql
SELECT DISTINCT year, month
FROM tutorial.aapl_historical_stock_price
```

- Using DISTINCT in aggregations
  - DISTINCT는 aggregation에서도 활용 가능하다.
  - COUNT 함수가 가장 많이 사용된다.

```sql
SELECT COUNT(DISTINCT month) AS unique_months
FROM tutorial.aapl_historical_stock_price
```

### WHERE

- SELECT한 데이터를 filtering 한다.
- 쿼리문은 항상 SELECT - FROM - WHERE 의 순서대로 작성되어야 한다.
- WHERE문을 사용하면 모든 컬럼 중 조건을 만족하는 행이 추출된다. 이는 각 행이 하나의 객체임을 의미한다. 또한 해당 행에 포함된 모든 정보는 함께 속한다.

```sql
SELECT *
FROM tutorial.us_housing_units
WHERE month = 1
```

### LIMIT

- 쿼리 문이 반환하는 행의 수를 제한한다.

```sql
SELECT *
FROM tutorial.us_housing_units
LIMIT 100
```

### FETCH/OFFSET

- 쿼리문이 반환하는 행의 수를 제한한다. ⇒ FETCH FIRST N ROWS ONLY
- OFFSET으로 시작 위치를 지정할 수 있다. ⇒ OFFSET N ROWS
- OFFSET의 디폴트 값은 0이다.
- 주로 ORDER BY와 사용하여 상위/하위 N개의 행을 추출할 때 사용된다.

```sql
SELECT *
FROM tutorial.aapl_historical_stock_price
ORDER BY high DESC
OFFSET 2 ROWS
FETCH FIRST 3 ROWS ONLY
-- 상위 2개의 행을 스킵하고 그 다음의 3개의 행을 반환한다.
```

### IN

- 결과에 포함할 값들의 리스트를 지정할 수 있는 논리 연산자이다.
- 숫자형이 아닌 데이터도 사용될 수 있으며 타입에 상관없이 모든 값들은 콤마(,)로 구분된다.

```sql
SELECT *
FROM tutorial.billboard_top_100_year_end
WHERE artist IN('Taylor Swift', 'Usher', 'Ludacris')
```

### BETWEEN

- 특정 구간 안에 속한 데이터들만 선택할 수 있는 논리 연산자이다.
- 'AND' 와 같이 쓰인다.
- 범위의 양 끝을 포함한다.

```sql
SELECT *
  FROM tutorial.billboard_top_100_year_end
 WHERE year_rank BETWEEN 5 AND 10

-- 위와 아래의 쿼리는 똑같은 결과를 반환한다.

SELECT *
  FROM tutorial.billboard_top_100_year_end
 WHERE year_rank >= 5 AND year_rank <= 10
```

### LIKE

- 특정 값과 비슷한 데이터를 선택하는 논리 연산자이다.
- % : 모든 문자
- _ : 한 글자

```sql
SELECT *
 FROM tutorial.billboard_top_100_year_end
 WHERE "group" LIKE 'Snoop%' 
-- Snoop으로 시작하는 모든 데이터
-- SQL의 연산자인 GROUP과 혼용되지 않도록 컬럼명인 group에 ""를 사용하였다.
```

### ISNULL

- 데이터가 없는 행을 조회할 수 있는 논리 연산자이다.

```sql
SELECT *
 FROM tutorial.billboard_top_100_year_end
 WHERE artist IS NULL
```