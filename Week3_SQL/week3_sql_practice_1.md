### 3주차 연습문제 1

문제1번) 매출을 가장 많이 올린 dvd 고객 이름은? (subquery 활용)

```sql
select first_name , last_name 
from customer c 
where c.customer_id in(
select customer_id
from payment p 
group by customer_id 
order by sum(amount) desc
limit 1
)
```

문제2번) 대여가 한번도이라도 된 영화 카테 고리 이름을 알려주세요. (쿼리는, Exists조건을 이용하여 풀어봅시다)

```sql
select c.name
from category c 
where exists (
select 1 
from rental r 
join inventory i on r.inventory_id = i.inventory_id 
join film_category fc on i.film_id = fc.film_id 
where fc.category_id  = c.category_id 
)
```

문제3번) 대여가 한번도이라도 된 영화 카테 고리 이름을 알려주세요. (쿼리는, Any 조건을 이용하여 풀어봅시다)

```sql

```

문제4번) 대여가 가장 많이 진행된 카테고리는 무엇인가요? (Any, All 조건 중 하나를 사용하여 풀어봅시다)

문제5번) dvd 대여를 제일 많이한 고객 이름은? (subquery 활용)

문제6번) 영화 카테고리값이 존재하지 않는 영화가 있나요?