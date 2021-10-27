### 2주차 연습 문제 (2)

- 2주차 연습 문제 2

  문제1번) store 별로 staff는 몇명이 있는지 확인해주세요.

  ```sql
  select s.store_id , count(s.staff_id) as staffs
  from staff s 
  group by s.store_id
  ```

  문제2번) 영화등급(rating) 별로 몇개 영화film을 가지고 있는지 확인해주세요.

  ```sql
  select rating , count(film_id) as films
  from film f 
  group by rating
  ```

  문제3번) 출현한 영화배우(actor)가  10명 초과한 영화명은 무엇인가요?

  ```sql
  select f.title, new_table.actors
  from
  	(select fa.film_id , count(fa.actor_id) actors
  	from film_actor fa 
  	group by fa.film_id 
  	having count(fa.actor_id) > 10) new_table
  join film f on new_table.film_id = f.film_id
  ```

  문제4번) 영화 배우(actor)들이 출연한 영화는 각각 몇 편인가요?

  - 영화 배우의 이름 , 성 과 함께 출연 영화 수를 알려주세요.

  ```sql
  select a.first_name ,a.last_name ,af.films
  from
  	(select fa.actor_id, count(fa.film_id) as films
  	from film_actor fa 
  	group by fa.actor_id) as af 
  join actor a on af.actor_id = a.actor_id
  ```

  문제5번) 국가(country)별 고객(customer) 는 몇명인가요?

  ```sql
  select country.country , count(c.customer_id)
  from customer c 
  join address a on c.address_id = a.address_id 
  join city on a.city_id = city.city_id 
  join country on city.country_id = country.country_id 
  group by country.country
  ```

  문제6번) 영화 재고 (inventory) 수량이 3개 이상인 영화(film) 는?

  - store는 상관 없이 확인해주세요.

  ```sql
  select f.title, i_f.cnt
  from 
  	(select i.film_id ,count(*) cnt
  	from inventory i 
  	group by i.film_id 
  	having count(*) > 3) i_f
  join film f on i_f.film_id = f.film_id
  ```

  문제7번) dvd 대여를 제일 많이한 고객 이름은?

  ```sql
  select c.first_name ,c.last_name ,rentals.cnt
  from
  	(select r.customer_id , count(*) cnt
  	from rental r 
  	group by r.customer_id) rentals
  join customer c on rentals.customer_id = c.customer_id 
  order by cnt desc 
  limit 1
  ```

  문제8번) rental 테이블을  기준으로,   2005년 5월26일에 대여를 기록한 고객 중, 하루에 2번 이상 대여를 한 고객의 ID 값을 확인해주세요.

  ```sql
  select customer_id , count(distinct r.rental_id) rentals
  from rental r 
  where date(r.rental_date) = '2005-05-26'
  group by r.customer_id 
  having count(distinct r.rental_id) >= 2
  ```

  문제9번) film_actor 테이블을 기준으로, 출현한 영화의 수가 많은  5명의 actor_id 와 , 출현한 영화 수 를 알려주세요.

  ```sql
  select fa.actor_id , count(fa.film_id) films
  from film_actor fa
  group by fa.actor_id 
  order by films desc
  limit 5
  ```

  문제10번) payment 테이블을 기준으로,  결제일자가 2007년2월15일에 해당 하는 주문 중에서  ,  하루에 2건 이상 주문한 고객의  총 결제 금액이 10달러 이상인 고객에 대해서 알려주세요. (고객의 id,  주문건수 , 총 결제 금액까지 알려주세요)

  ```sql
  select p.customer_id , count(distinct p.payment_id) cnt, sum(p.amount) total_amount
  from payment p 
  where date(p.payment_date) = '2007-02-15'
  group by p.customer_id 
  having count(distinct p.payment_id) >= 2 and sum(p.amount) >=10
  ```

  문제11번) 사용되는 언어별 영화 수는?

  ```sql
  select l."name" , count(*)
  from film f
  join "language" l on f.language_id = l.language_id 
  group by l."name"
  ```

  문제12번) 40편 이상 출연한 영화 배우(actor) 는 누구인가요?

  ```sql
  select a.first_name ,a.last_name ,fa_cnt.cnt
  from
  	(select fa.actor_id , count(fa.film_id) as cnt
  	from film_actor fa 
  	group by actor_id 
  	having count(fa.film_id) >= 40) fa_cnt
  join actor a on fa_cnt.actor_id = a.actor_id
  ```

  문제13번) 고객 등급별 고객 수를 구하세요. (대여 금액 혹은 매출액  에 따라 고객 등급을 나누고 조건은 아래와 같습니다.) /* A 등급은 151 이상 B 등급은 101 이상 150 이하 C 등급은   51 이상 100 이하 D 등급은   50 이하

  - 대여 금액의 소수점은 반올림 하세요.

  HINT 반올림 하는 함수는 ROUND 입니다.	 */

  ```sql
  select case when total_amount <= 50 then 'D'
  when total_amount between 51 and 100 then 'C'
  when total_amount between 101 and 150 then 'B'
  when total_amount >=151 then 'A' end customer_level, count(r.customer_id)
  from
  (
  	select c.customer_id , round(sum(p.amount),0) total_amount
  	from customer c 
  	join payment p on c.customer_id = p.customer_id 
  	group by c.customer_id 
  ) r
  group by case when total_amount <= 50 then 'D'
  when total_amount between 51 and 100 then 'C'
  when total_amount between 101 and 150 then 'B'
  when total_amount >=151 then 'A' end
  order by customer_level
  ```



### Review

* Group by 의 조건은 Having