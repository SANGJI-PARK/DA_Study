# 서브쿼리

## 집합 연산자와 서브쿼리

### ANY 연산

- ANY(서브쿼리 or 값)
- 하나라도 만족하는 값이 있다면 true를 반환한다.
- 비교 연산자를 사용한다.

```sql
SELECT * 
FROM emp
WHERE sal = ANY(950, 3000, 1250) 
--- 위 쿼리는 'sal=950 or sal=3000 or sal=1250' 와 동일하다
```

* '>' ANY : 최솟값보다 크면
* '>=' ANY : 최소값보다 크거나 같으면
* '<' ANY : 최댓값보다 작으면
* '<=' ANY : 최대값보다 작거나 같으면
* '=' ANY : IN과 같은 효과 (그 중 하나라도 있으면)
* '!=' ANY : NOT IN 과 같은 효과 

### ALL 연산

- ALL(서브쿼리 or 값)
- 전체를 만족해야 true를 반환한다.
- ANY와 반대되는 개념
- '>' ALL : 최대값보다 크면
- '>=' ALL : 최대값보다 크거나 같으면
- '<' ALL : 최소값보다 작으면
- '<=' ALL : 최소값보다 작거나 같으면
- '=' ALL : SUBSELECT의 결과가 1건이면 상관없지만 여러 건이면 오류 발생
- '!=' ALL : 위와 마찬가지로 SUBSELECT의 결과가 여러 건이면 오류 발생

### EXISTS 연산

- EXISTS(서브쿼리 or 값)
- 행의 존재 여부를 확인하여 true를 반환하다.
- select A exist B => A 중에서 B에도 존재하는 데이터 추출 (교집합)
- id를 비교한다고 했을 때, A의 id_1이 B에 복수개 존재하는 경우, B의 첫 번째 id_1이랑 비교가 됐을 때, 이미 exist한다는 것이 확인 되었으므로 거기서 연산을 멈추고 다음 id로 넘어간다.
- EXISTS 절의 SELECT 절에는 1이나 'X'를 주로 사용하는데, 이는 실제로 데이터를 추출하는 것이 아닌 비교만이 목적이기 때문이다.

```sql
SELECT * 
FROM A
WHERE EXISTS (
SELECT 1 
FROM B
WHERE A.id = B.id
)
```

### NOT EXISTS 연산

- 반환된 행이 하나도 없어야 true를 반환한다.
- select A not exists B => A 중에서 B에 존재하지 않는 데이터만 추출 (차집합)

# 조인과 집계데이터

- GROUP BY절에 명시해서 그룹 쿼리에 사용되는 절이다.

```sql
SELECT 컬럼명,그룹함수(컬럼명), GROUPING(컬럼명)
FROM 테이블명
WHERE 조건
GROUP BY [ROLLUP | CUBE] 그룹핑하고자하는 컬럼명, ...[GROUPING SETS (컬럼명,컬럼명, ...), ...]
HAVING 그룹조건
ORDER BY 컬럼명 또는 위치번호
```

### ROLL UP

- 그룹 간의 소계를 게산한다.

### CUBE

- 그룹간의 다차원적인 소계를 계산한다
- GROUP COLUMNS이 가질 수 있는 모든 경우의 수에 대하여 소계를 생성하므로 GROUP COLUMNS의 수가 N이라고 가정하면, 2의 n승의 차원의 소계를 생성한다.

### Grouping Set

- 그룹 간의 특정 항목에 대한 소계를 계산한다.
- Grouping set절은 그룹 쿼리이긴 하나 UNION ALL개념이 섞여있다.
- 각 그룹 조건에 대해 별도로 GROUP BY한 결과를 UNION ALL한 결과와 동일하다.
- 즉, 쿼리 결과는 ((GROUP BY expr1) UNION ALL (GROUP BY expr2) UNION ALL (GROUP BY expr3))의 형태가 된다.

```sql
SELECT period, gubun, SUM(loan_jan_amt) totl_jan
FROM kor_loan_status
WHERE period LIKE '2013%'
GROUP BY GROUPING SETS(period, gubun)
```

## [분석 함수](https://logical-code.tistory.com/40)

- 테이블에 있는 행에 대해 특정 그룹별로 집계값을 산출할 때 사용한다.
- 종류 : AVG/SUM/MAX/MIN/COUNT/CUME_DIST/DENSE_RANK/PERCENT_RANK/FIRST/LAST/LAG/LEAD/ROW_NUMBER

```sql
분석함수(매개함수) OVER (PARTITION BY EXPR1, EXPR2 ...
									ORDER BY EXPR3, EXPR4, ...
										WINDOW ...)
```

- 절 설명
  - PARTITION BY : 분석 함수로 계산될 대상 그룹(파티션)을 지정한다.
  - ORDER BY : 파티션 내의 순서를 지정한다.
  - WINDOW : 파티션으로 분할된 그룹에서 더 상세한 그룹으로 분할할 때 사용한다.

### ROW_NUMBER

- 파티션으로 분할된 그룹별로 각 행에 대한 순번을 반환한다.

```sql
SELECT DEPARTMENT_ID, EMP_NAME,
			ROW_NUMBER() OVER (PARTITION BY DEPARTMENT_ID
										ORDER BY DEPARTMENT_ID, EMP_NAME) DEP_ROWS
FROM EMPLOYEES
```

### RANK

- 파티션별 순위를 반환한다.

```sql
SELECT DEPARTMENT_ID, EMP_NAME,SALARY
			,RANK() OVER(PARTITION BY DEPARTMENT_ID 
								ORDER BY SALARY) DEP_RANK
FROM EMPLOYEES
```

### DENSE_RANK

- RANK와 비슷하나, 같은 순위가 나오면 다음 순위가 한 번 건너뛰지 않고 매겨진다.

```sql
SELECT *
FROM (SELECT DEPARTMENT_ID, EMP_NAME, SALARY
			,DENSE_RANK() OVER (PARTITION BY DEPARTMENT_ID
											ORDER BY SALARY) DEP_RANK
			 FROM EMPLOYEES
		)
WHERE DEP_RANK <= 3
```

### LAG/LEAD(EXPR, OFFSET, DEFAULT_VALUE)

- LAG : OFFSET의 수 만큼의 선행 로우의 값을 반환한다.
- LEAD : OFFSET의 수 만큼의 후행 로우의 값을 반환한다.

### 그 외

- CUME_DIST : 주어진 그룹에 대한 상대적인 누적분포도 값을 반환한다. 0초과 1이하의 값을 반환
- PERCENT_RANK : 해당 그룹 내의 백분위 순위를 반환하며, 0 이상 1 이하의 값을 반환한다.
- NTILE(EXPR) : 파티션별로 EXPR에 병시된 값만큼  분할한 결과를 반환한다.
  - NTILE(N) OVER (...)

# 조건연산자/WITH문/트랜잭션

### CASE

- IF/ELSE문과 같은 로직을 구사할 수 있다.

```sql
SELECT
			CASE WHEN 조건식1 THEN 결과1
					 WHEN 조건식2 THEN 결과2
					 ELSE 조건식 3
      END
```

### COALESCE

- 입력한 인자값 중에 NULL값이 아닌 첫번째 값을 반환한다.
- NULL값을 처리할 때 유용하게 사용된다.

```sql
COALESCE(DISCOUNT, 0)
--- DISCOUNT가 NULL이라면 0을, 아니라면 DISCOUNT값을 반환한다.
```

### NULLIF

- 입력한 두개의 인자 값이 동일하면 NULL을, 그렇지 않으면 첫번째 인자값을 반환한다.

```sql
NULLIF(1,0) -- 1
NULLIF(0,0) -- NULL
```

### CAST

- 데이터의 형변환이 가능하도록 한다.

```sql
SELECT CAST('100' AS INTEGER) -- INTEGER로 변환
SELECT CAST('10C' AS INTEGER) -- 문자가 포함되어 있어서 INTEGER로 변환 불가
SELECT CAST('2015-01-01' AS DATE) -- DATE형으로 변환
SELECT CAST('10.2' AS DOUBLE PRECISION)
```

### WITH문 - CTE(Common Table Expression)

- SELECT문의 결과를 임시 집합으로 정해두고 SQL문에서 마치 테이블처럼 해당 집합을 불러올 수 있다.
- CTE는 서브쿼리로 쓰이는 파생테이블(derived table)과 비슷한 개념이다.
- 다만, CTE는 권한이 필요없고 하나의 쿼리문이 끝날때까지만 지속되는 일회성 테이블이다.
- CTE는 주로 복잡한 쿼리문에서 코드의 가독성과 재사용성을 위해 파생테이블 대신 사용하기에 유용하다.
- CTE에는 재귀적 CTE와 비재귀적 CTE가 있다.
- 구문 : WITH 테이블명 AS (SELECT ...)

```sql
WITH TMP1 AS(
SELECT FILM_ID, TITLE, 
			,(CASE
						WHEN LENGTH <30 THEN 'SHORT'
						WHEN LENGTH >= 30	AND LENGTH < 90 THEN 'MEDIUM'
						WHEN LENGTH > 90 THEN 'LONG'
				END) LENGTH
FROM FLIM
)
SELECT * FROM TMP1; 
```

### WITH문을 활용한 재귀

```sql
WITH RECURSIVE TMP1 AS (
SELECT EMPLOYEE_ID, MANAGER_ID, FULL_NAME, 0 LVL
  FROM TB_EMP_RECURSIVE_TEST
 WHERE MANAGER_ID IS NULL -- MANAGER_ID IS NULL인 조건부터 재귀 시작
UNION 
SELECT E.EMPLOYEE_ID, E.MANAGER_ID, E.FULL_NAME, S.LVL + 1
  FROM TB_EMP_RECURSIVE_TEST E, TMP1 S 
 WHERE S.EMPLOYEE_ID = E.MANAGER_ID
) 
SELECT EMPLOYEE_ID, MANAGER_ID, LPAD(' ', 4 * (LVL)) || FULL_NAME AS FULL_NAME FROM TMP1; -- LPAD를 이용해서 깊이 만큼 들여쓰기해서 출력
```

### 트랜잭션

- 작업을 하고 난 후 DBMS에 반영을 할지 말지를 정해야 하는 명령어
- COMMIT(저장)/ROLLBACK(불러오기)/BEGIN(시작)
- BEGIN은 생략가능

# [WINDOW Function](https://velog.io/@yewon-july/Window-Function)

- 행과 행 간의 관계를 정의하기 위해서 제공되는 함수
- 윈도우 함수를 사용해서 순위, 합계, 평균, 행 위치 등을 조작할  수 있다.
- 윈도우 함수는 GROUP BY문과 병행하여 사용할 수 없다.
- 윈도우 함수로 인해 결과 건수가 줄어들지는 않는다.
- SUM, MAX, MIN 등과 같은 집계 윈도우 함수를 사용할 때 윈도우 절과 함께 사용하면 집계 대상이 되는 레코드 범위를 지정할 수 있다.

```sql
--- 윈도우 함수의 구조
SELECT WINDOW_FUNCTION(ARGUMENTS)
	OVER (PARTITION BY 컬럼
			ORDER BY WINDOWING절) 
FROM 테이블명
```

- WINDOWING : 행 기준 범위를 정한다.
  - ROWS : 부분집합인 윈도우 크기를 **물리적 단위로 행의 집합을 지정**한다
  - RANGE : **논리적 주소**에 의해 **행 집합을 지정**한다.
  - BETWEEN~AND : 윈도우의 시작과 끝 위치를 지정한다.
  - UNBOUNDED PRECEDING : 윈도우 **시작 위치**가 **첫 번째 행**임을 의미한다.
  - UNBOUNDED FOLLOWING : 윈도우 **마지막 위치**가 **마지막 행**임을 의미한다.
  - CURRENT ROW : 윈도우 시작 위치가 현재 행임을 의미한다. (데이터가 인출된 현재 행)

```sql
--- WINDOWING 예시 - 합계
SELECT EMPNO, ENAME, SAL
SUM(SAL) OVER(ORDER BY SAL
			ROWS BETWEEN UNBOUNDED PRECEDING
			AND UNBOUNDED FOLLOWING) TOTSAL -- 처음부터 마지막까지의 합계를 계사한 것이다.
FROM EMP
--- WNDOWING 예시 - 누적합계
SELECT EMPNO, ENAME, SAL
SUM(SAL) OVER(ORDER BY SAL
		ROWS BETWEEN UNBOUNDED PRECEDING
		AND CURRENT ROW) TOTSAL -- 처음부터 CURRENT까지의 합계 즉, 누적합계
FROM EMP
```

# CTE(Common Table Expression)

- CTE는 서브쿼리로 쓰이는 파생테이블(derived table)과 비슷한 개념이다.
- 다만, CTE는 권한이 필요없고 하나의 쿼리문이 끝날때까지만 지속되는 일회성 테이블이다.
- CTE는 주로 복잡한 쿼리문에서 코드의 가독성과 재사용성을 위해 파생테이블 대신 사용하기에 유용하다.
- CTE에는 재귀적 CTE와 비재귀적 CTE가 있다.
- 구문 : WITH 테이블명 AS (SELECT ...)