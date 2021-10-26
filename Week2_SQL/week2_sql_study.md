## 조인

### Inner조인

- Inner조인은 조인 조건(ON 뒤에 쓰는)을 만족하지 않는 행을 두 테이블에서 모두 삭제한다. == 교집합
- 두 테이블을 조인할 때에는 두 테이블 모두 동일한 identical names 컬럼을 갖고 있어야 한다. 이 때, identical naems컬럼은 한 번만 출력된다.

```sql
SELECT players.*, teams.*
FROM benn.college_football_players players
INNER JOIN benn.college_football_teams teams
ON teams.school_name = players.school_name
```

- identical names 컬럼들을 같은 내용이지만 두 개 모두 출력하고 싶을 땐, 각 컬럼에 이름을 지정해주면 된다.

```sql
SELECT players.school_name AS players_school_name,
       teams.school_name AS teams_school_name
  FROM benn.college_football_players players
  INNER JOIN benn.college_football_teams teams
    ON teams.school_name = players.school_name
```

### Outer조인

- 조인 하는 테이블 중에서 한 쪽에는 데이터가 있고, 한 쪽에는 데이터가 없는 경우 데이터가 있는 쪽 테이블의 내용을 모두 출력하는 것이다. 즉, 조건에 맞지 않아도 해당하는 행ㅇ르 출력하고 싶을 때 사용할 수 있다.
- outer조인의 종류
  - LEFT JOIN : 조인 문의 왼쪽 테이블에서 모든 결과를 가져온 후 오른쪽 테이블의 데이터를 매칭하고, 매칭되는 데이터가 없는 경우 NULL로 표시한다.
  - RIGHT JOIN : 조인문의 오른쪾에 있는 테이블의 모든 결과를 가져온 후 왼쪽 테이블의 데이터를 매칭하고, 매칭되는 데이터가 없는 경우 NULL을 표시한다.
  - FULL OUTER JOIN : 양쪽 모두 조건이 일치하지 않는 것까지 모두 결합해 출력한다. (MySQL에서는 FULL OUTER JOIN이 없으므로, LEFT OUNTER JOIN 과 RIGHT OUTER JOIN을  UNION 한다.)

### Self조인

- 동일한 테이블 사이의 조인
- FROM 절에 동일 테이블이 두 번 이상 나타난다.
- 테이블과 컬럼명이 모두 동일하기 때문에 별칭을 사용히야 한다.
- [테이블 내 동일한 값이 다른 의미를 가지는 즉, 다른 컬럼에 존재하는 경우 두 테이블을 서로 SELF JOIN 시켜서 정보를 확인할 수 있다.](http://egloos.zum.com/sweeper/v/3002332)

```sql
-- SELF JOIN을 통해 '우대리'의 manager을 출력한다.
-- WHERE 절을 없애면 전체 사원에 대한 직속상관을 쿼리할 수 있다.
SELECT E_1.Name AS name, E_2.Name AS manager
FROM Employee E_1
	INNER JOIN Emplyee E_2 
	ON E_1.Manager = E_2.Name
WHERE E_1.Name = '우대리'
```

- 위 쿼리문 해석 : inner join이니까 먼저 E1테이블 모두 불러옴→그중에서 E1의 Manager가 E_2에 있는 경우 결국 모든 테이블을 불러옴 → 그 중에서 E1의 Name이 우대리 인 행만 골라서 쿼리함. → 결국 우대리-직속상관 이런 결과만 나온다.

### Full outer조인

- 양쪽 모두 조건이 일치하지 않는 것까지 모두 결합해 출력한다. (MySQL에서는 FULL OUTER JOIN이 없으므로, LEFT OUNTER JOIN 과 RIGHT OUTER JOIN을  UNION 한다.)

```sql
SELECT a.empno
     , a.ename
     , a.job
     , a.mgr
     , a.deptno
     , b.dname
  FROM emp AS a
  LEFT OUTER JOIN dept AS b
    ON a.deptno = b.deptno
```

### [Cross조인](http://egloos.zum.com/sweeper/v/3002332)

- 한 쪽 테이블의 모든 행들과 다른 테이블의 모든 행을 조인시킨다.
- 그래서 CROSS JOIN의 결과 테이블의 행의 수는 두 테이블의 행의 개수를 곱한 값이 된다.
- 테이블 A의 첫 행을 테이블 B의 모든 행과 조인 → 테이블 A의 모든 행을 차례대로 반복
- CROSS JOIN은 모든 행에 대한 결합이 발생하는 것이므로, 위 예제처럼 ON구문을 사용할 수 없다.

```sql
SELCET *
FROM UserTable
CROSS JOIN Buy Table
```

### [Natural조인](https://keep-cool.tistory.com/41)

- 등가 조인

   하는 방법 중 하나이다.

  - 등가조인(EQui Join) : JOIN의 결과가 특정 조건값과 '일치'할 때
  - 비등가조인(Non Equi Join) : JOIN의 결과가 특정 조건 범위 내에 존재할 때

- 조인 조건을 기술하지 않아도 자동으로 공통 컬럼을 찾아서 조인한다.

- 반드시 두 테이블 간의 동일한 이름, 타입을 가진 컬럼이 **하나만** 존재해야 한다. 두개 이상이면 원하는 결과가 나오지 않을 수 있다.

- 조인에 이용되는 컬럼은 명시하지 않아도 자동으로 조인에 사용된다.

- 동일한 이름을 갖는 컬럼이 있지만 데이터 타입이 다르면 에러가 발생한다.

- SELECT 절에서 테이블 명을 기술하지 않고 검색하는 것이 가능하다.

```sql
SELECT department_id 부서번호, department_name 부서이름, 
			 location_id 지역번호, city 도시
FROM departments
NATURAL JOIN locations
WHERE city = 'Seattle'
```

## Group by

- GROUP BY는 데이터들을 그룹화 하여 각 그룹 별개로 연산이 가능하게 한다.
- 다수의 컬럼으로 그룹화를 할 때는 콤마(,)로 구분한다.

```sql
-- 연도별 합계
SELECT year, 
			 month, 
			 COUNT(*) AS count
FROM tutorial.aapl_historical_stock_price
GROUP BY year, month
ORDER BY month, year
```

## 집합 연산자와 서브 쿼리

- 두 SELECT 문의 컬럼 개수와 데이터 타입은 일치해야 한다.
- 검색 결과의 헤더는 앞쪽 SELECT문에 의해 결정된다.
- ORDER BY 절을 사용할 때는 문장의 제일 마지막에 사용한다.

```sql
SELECT ……
[UNION | UNION ALL | INTERSECT | EXCEPT]

SELECT ……

[ORDER BY 컬럼 [ASC/DESC]];
```

### [Having](https://keep-cool.tistory.com/37)

- 그룹 함수만을 포함하는 조건절이다.
- 일반 조건은 WHERE절에, 그룹 함수를 포함한 조건은 HAVING절에 기술한다.
- HAVING절은 GROUP BY절 뒤에 기술한다.

```sql
select job_id, sum(salary) 급여총액
from employees
where job_id not like '%REP%'
group by job_id
having sum(salary) > 13000
order by sum(salary)

'''
1. from 절에 기술한 테이블에서
2. where 절에 기술한 조건에 해당하는 내용들만 정리하고
3. 정리된 내용들을 가지고 group by 절에 나열된 컬럼에 대해서 그룹을 만든다.
4. 만들어진 그룹별로 having절에 기술된 그룹함수로 계산하여 having절 조건에 맞는 그룹만 추리고
5. 추려진 그룹들을 order by 절에 잇는 조건으로 정렬시킨다.
6. 마지막으로 select절에 있는 그룹화된 컬럼 또는 그룹함수를 화면에 출력한다.
'''
```

### UNION/UNIONALL

- UNION : 조회한 다수의 SELECT문을 하나로 함치고 싶을 때 사용한다.
  - 두 개의 select 결과를 합칠 수 있고, 중복되는 행은 하나만 표시한다.
  - 단, 두 테이블의 컬럼의 개수 및 데이터타입이 동일해야 한다.
- UNIONALL : 중복을 제거하지 않고 모두 합쳐서 보여준다.

```sql
SELECT COUNTRY_NAME, REGION_ID FROM COUNTRIES
UNION
SELECT REGION_NAME, REGION_ID FROM REGIONS
```

### INTERSECT

- 두 테이블에서 공통된 값을 검색한다. (교집합)

### EXCEPT

- 두 번째 테이블에는 없는 첫 번째 테이블의 고유한 값을 반환한다.