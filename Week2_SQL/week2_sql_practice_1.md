### 2주차 연습 문제 (1)

문제1번) 고객의 기본 정보인, 고객 id, 이름, 성, 이메일과 함께 고객의 주소 address, district, postal_code, phone 번호를 함께 보여주세요.

```sql
select customer_id , first_name ,last_name ,email, a.address_id, 
	address ,district , postal_code ,phone 
from customer c 
join address a 
on c.address_id = a.address_id
```

문제2번) 고객의  기본 정보인, 고객 id, 이름, 성, 이메일과 함께 고객의 주소 address, district, postal_code, phone , city 를 함께 알려주세요.

```sql
select customer_id , first_name ,last_name ,email, a.address_id, 
	address ,district , postal_code ,phone, city
from customer c 
left outer join address a on c.address_id = a.address_id
left outer join city c2 on a.city_id = c2.city_id
```

문제3번) Lima City에 사는 고객의 이름과, 성, 이메일, phonenumber에 대해서 알려주세요.

```sql
select first_name ,last_name ,email , a.phone
from customer c 
left outer join address a on c.address_id = a.address_id
left outer join city c2 on a.city_id = c2.city_id 
where c2.city = 'Lima'
```

문제4번) rental 정보에 추가로, 고객의 이름과, 직원의 이름을 함께 보여주세요.

- 고객의 이름, 직원 이름은 이름과 성을 fullname 컬럼으로만들어서 직원이름/고객이름 2개의 컬럼으로 확인해주세요.

```sql
select r.*, 
c.first_name||c.last_name as customer_fullname,
s.first_name||s.last_name as staff_fullname
from rental r
left outer join customer c on r.customer_id = c.customer_id 
left outer join staff s on r.staff_id = s.staff_id
```

문제5번) [seth.hannon@sakilacustomer.org](mailto:seth.hannon@sakilacustomer.org) 이메일 주소를 가진 고객의  주소 address, address2, postal_code, phone, city 주소를 알려주세요.

```sql
select a.address, a.address2 ,a.postal_code ,a.phone ,c2.city 
from customer c 
left outer join address a on c.address_id = a.address_id
left outer join city c2 on a.city_id = c2.city_id
where c.email = 'seth.hannon@sakilacustomer.org'
```

문제6번) Jon Stephens 직원을 통해 dvd대여를 한 payment 기록 정보를  확인하려고 합니다.

- payment_id,  고객 이름 과 성,  rental_id, amount, staff 이름과 성을 알려주세요.

```sql
select p.payment_id ,c.first_name ,c.last_name ,r.rental_id,
p.amount ,s.first_name ,s.last_name 
from customer c 
join payment p on c.customer_id = p.customer_id 
join rental r on p.rental_id = r.rental_id 
join staff s on r.staff_id = s.staff_id 
where s.first_name = 'Jon' 
and s.last_name = 'Stephens'
```

문제7번) 배우가 출연하지 않는 영화의 film_id, title, release_year, rental_rate, length 를 알려주세요.

```sql
select f.film_id ,f.title ,f.release_year ,f.rental_rate , f.length
from film f 
left outer join film_actor fa on f.film_id = fa.film_id 
left outer join actor a on fa.actor_id = a.actor_id 
where a.actor_id is null
```

문제8번) store 상점 id별 주소 (address, address2, distict) 와 해당 상점이 위치한 city 주소를 알려주세요.

```sql
select s.store_id ,a.address ,a.address2 ,a.district ,c.city 
from store s 
left outer join address a on s.address_id = a.address_id 
left outer join city c on a.city_id = c.city_id
```

문제9번) 고객의 id 별로 고객의 이름 (first_name, last_name), 이메일, 고객의 주소 (address, district), phone번호, city, country 를 알려주세요.

```sql
select c.customer_id ,c.first_name ,c.last_name ,c.email ,
a.address ,a.district ,a.phone ,
c2.city ,c3.country
from customer c
left outer join address a on c.address_id = a.address_id
left outer join city c2 on a.city_id = c2.city_id
left outer join country c3 on c2.country_id = c3.country_id
```

문제10번) country 가 china 가 아닌 지역에 사는, 고객의 이름(first_name, last_name)과 , email, phonenumber, country, city 를 알려주세요

```sql
select c.first_name ,c.last_name ,c.email ,
a.phone ,c2.city ,c3.country 
from customer c 
left outer join address a on c.address_id = a.address_id 
left outer join city c2 on a.city_id = c2.city_id 
left outer join country c3 on c2.country_id = c3.country_id 
where c3.country != 'China' -- not in ('China') 써도 됨
```

문제11번) Horror 카테고리 장르에 해당하는 영화의 이름과 description 에 대해서 알려주세요

```sql
select f.title ,f.description 
from film f 
join film_category fc on f.film_id = fc.film_id
join category c on fc.category_id = c.category_id 
where c.name = 'Horror'
```

문제12번) Music 장르이면서, 영화길이가 60~180분 사이에 해당하는 영화의 title, description, length 를 알려주세요.

- 영화 길이가 짧은 순으로 정렬해서 알려주세요.

```sql
select f.title ,f.description ,f.length
from film f 
join film_category fc on f.film_id = fc.film_id
join category c on fc.category_id = c.category_id 
where c.name = 'Music' 
and f.length between 60 and 180
order by f.length
```

문제13번) actor 테이블을 이용하여,  배우의 ID, 이름, 성 컬럼에 추가로    'Angels Life' 영화에 나온 영화 배우 여부를 Y , N 으로 컬럼을 추가 표기해주세요.  해당 컬럼은 angelslife_flag로 만들어주세요.

```sql
select a.actor_id ,a.first_name ,a.last_name, case when a.actor_id in(
select fa.actor_id 
from film f
inner join film_actor fa on f.film_id = fa.film_id
where f.title = 'Angels Life'
) then 'Y'
else 'N'
end as angelslife_flag
from actor a
'''
서브쿼리를 통해서 Angels Life에 출현한 배우들의 id 리스트를 만든다. 
이 리스트 안에 id가 있는 배우들은 Y, 아닌 배우들은 N값을 갖는 컬럼(angelslife_flag)를 새로 만든다.
해당 서브쿼리에서 inner join(교집합)이 쓰인 이유는??
'''
```

문제14번) 대여일자가 2005-06-01~ 14일에 해당하는 주문 중에서 , 직원의 이름(이름 성) = 'Mike Hillyer' 이거나  고객의 이름이 (이름 성) ='Gloria Cook'  에 해당 하는 rental 의 모든 정보를 알려주세요.

- 추가로 직원이름과, 고객이름에 대해서도 fullname 으로 구성해서 알려주세요.

```sql
select r.*, s.first_name ,s.last_name ,c.first_name ,c.last_name 
from customer c
join rental r on c.customer_id = r.customer_id 
join staff s on r.staff_id = s.staff_id 
where date(r.rental_date) between '2005-06-01' and '2005-06-14' 
and (
(s.first_name = 'Mike' and s.last_name = 'Hillyer')
or (c.first_name = 'Gloria' and c.last_name = 'Cook')
)
```

문제15번) 대여일자가 2005-06-01~ 14일에 해당하는 주문 중에서 , 직원의 이름(이름 성) = 'Mike Hillyer' 에 해당 하는 직원에게  구매하지 않은  rental 의 모든 정보를 알려주세요.

```sql
select r.*, s.first_name ,s.last_name ,c.first_name ,c.last_name 
from customer c
join rental r on c.customer_id = r.customer_id 
join staff s on r.staff_id = s.staff_id 
where date(r.rental_date) between '2005-06-01' and '2005-06-14' 
and s.first_name||' '||s.last_name not in ('Mike Hillyer')
```



### Review

- 두 테이블을 조인했을 때 맨 처음 테이블의 변수들만 select절에서 테이블명 생략 가능??
- left join과 그냥 join을 쓰는 경우 차이점
- 13번의 서브쿼리에서 inner join(교집합)이 쓰인 이유는??