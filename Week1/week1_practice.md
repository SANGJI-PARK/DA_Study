### week 1

### 실습 1

1-1. dvd 렌탈 업체의  dvd 대여가 있었던 날짜를 확인해주세요.

``` sql
select distinct rental_date
from rental
```

1-2. 영화길이가 120분 이상이면서, 대여기간이 4일 이상이 가능한, 영화제목을 알려주세요.

``` sql
select title
from film f 
where length >= 120 and rental_duration >=4
```

1-3. 직원의 id 가 2 번인  직원의  id, 이름, 성을 알려주세요

```sql
select staff_id, first_name, last_name
from staff
where staff_id = 2
```

1-4. 지불 내역 중에서,   지불 내역 번호가 17510 에 해당하는  ,  고객의 지출 내역 (amount ) 는 얼마인가요?

```sql
select amount
from payment p 
where payment_id = 17510
```

1-5. 영화 카테고리 중에서 ,Sci-Fi  카테고리의  카테고리 번호는 몇번인가요?

```sql
select category_id
from category c 
where name = 'Sci-Fi'
```

1-6. film 테이블을 활용하여, rating  등급(?) 에 대해서, 몇개의 등급이 있는지 확인해보세요.

```sql
select distinct rating 
from film
```

1-7. 대여 기간이 (회수 - 대여일) 10일 이상이였던 rental 테이블에 대한 모든 정보를 알려주세요. 단 , 대여기간은  대여일자부터 대여기간으로 포함하여 계산합니다.

```sql
select *
from rental r 
where (date(return_date) - date(rental_date) + 1) >= 10
```

1-8. 고객의 id 가  50,100,150 ..등 50번의 배수에 해당하는 고객들에 대해서, 회원 가입 감사 이벤트를 진행하려고합니다. 고객 아이디가 50번 배수인 아이디와, 고객의 이름 (성, 이름)과 이메일에 대해서 확인해주세요.

```sql
select customer_id, last_name, first_name, email
from customer c 
where (customer_id % 50) = 0
```

1-9. 영화 제목의 길이가 8글자인, 영화 제목 리스트를 나열해주세요.

```sql
select title
from film
where length(title) = 8
```

1-10. city 테이블의 city 갯수는 몇개인가요?

``` sql
select count(*)
from city
```

1-11. 영화배우의 이름 (이름+' '+성) 에 대해서,  대문자로 이름을 보여주세요.  단 고객의 이름이 동일한 사람이 있다면,  중복 제거하고, 알려주세요.

```sql
select distinct upper(first_name ||' '|| last_name) as name
from actor
```

1-12. 고객 중에서,  active 상태가 0 인 즉 현재 사용하지 않고 있는 고객의 수를 알려주세요.

```sql
select count(customer_id)
from customer c 
where active = 0
```

1-13. Customer 테이블을 활용하여,  store_id = 1 에 매핑된  고객의 수는 몇명인지 확인해보세요.

```sql
select count(customer_id)
from customer c 
where store_id = 1
```

1-14. rental 테이블을 활용하여,  고객이 return 했던 날짜가 2005년6월20일에 해당했던 rental 의 갯수가 몇개였는지 확인해보세요.

```sql
select count(rental_id)
from rental r 
where date(return_date) = '2005-06-20'
```

1-15. film 테이블을 활용하여, 2006년에 출시가 되고 rating 이 'G' 등급에 해당하며, 대여기간이 3일에 해당하는  것에 대한 film 테이블의 모든 컬럼을 알려주세요.

```sql
select *
from film f 
where release_year = '2006' 
and rating = 'G' 
and rental_duration = 3
```

1-16. langugage 테이블에 있는 id, name 컬럼을 확인해보세요 .

```sql
select language_id, name
from language l 
```

1-17. film 테이블을 활용하여,  rental_duration 이  7일 이상 대여가 가능한  film 에 대해서  film_id,   title,  description 컬럼을 확인해보세요.

```sql
select film_id, title, description
from film
where rental_duration>=7
```

1-18. film 테이블을 활용하여,  rental_duration   대여가 가능한 일자가 3일 또는 5일에 해당하는  film_id,  title, desciption 을 확인해주세요.

```sql
select film_id, title, description
from film
where rental_duration = 3 or rental_duration = 5
```

1-19. Actor 테이블을 이용하여,  이름이 Nick 이거나  성이 Hunt 인  배우의  id 와  이름, 성을 확인해주세요.

```sql
select actor_id, first_name, last_name 
from actor a 
where first_name = 'Nick' or last_name = 'Hunt'
```

1-20. Actor 테이블을 이용하여, Actor 테이블의  first_name 컬럼과 last_name 컬럼을 , firstname, lastname 으로 컬럼명을 바꿔서 보여주세요

``` sql
select first_name as first_name, last_name as lastname
from actor a 
```



### 실습 2

2-1. film 테이블을 활용하여,  film 테이블의  100개의 row 만 확인해보세요.

```sql
select *
from film f
limit 100
```

2-2. actor 의 성(last_name) 이  Jo 로 시작하는 사람의 id 값이 가장 낮은 사람 한사람에 대하여, 사람의  id 값과  이름, 성 을 알려주세요.

``` sql
select actor_id, first_name, last_name
from actor a 
where last_name like 'Jo%'
order by actor_id 
limit 1
```

2-3. film 테이블을 이용하여, film 테이블의 아이디값이 1~10 사이에 있는 모든 컬럼을 확인해주세요.

```sql
select *
from film
where film_id between 1 and 10
```

2-4. country 테이블을 이용하여, country 이름이 A 로 시작하는 country 를 확인해주세요.

```sql
select country
from country c 
where country like 'A%'
```

2-5. country 테이블을 이용하여, country 이름이 s 로 끝나는 country 를 확인해주세요.

```sql
select country
from country c 
where country like '%s'
```

1-6. address 테이블을 이용하여, 우편번호(postal_code) 값이 77로 시작하는  주소에 대하여, address_id, address, district ,postal_code  컬럼을 확인해주세요.

```sql
select address_id, address , district , postal_code 
from address a 
where postal_code like '77%'
```

2-7. address 테이블을 이용하여, 우편번호(postal_code) 값이  두번째글자가 1인 우편번호의  address_id, address, district ,postal_code  컬럼을 확인해주세요.

```sql
select address_id, address, district ,postal_code
from address a 
where postal_code like '_1%'
```

2-8. payment 테이블을 이용하여,  고객번호가 341에 해당 하는 사람이 결제를 2007년 2월 15~16일 사이에 한 모든 결제내역을 확인해주세요.

```sql
select *
from payment p 
where customer_id = 341
and date(payment_date) between '2007-2-15' and '2007-02-16'
```

2-9. payment 테이블을 이용하여, 고객번호가 355에 해당 하는 사람의 결제 금액이 1~3원 사이에 해당하는 모든 결제 내역을 확인해주세요.

```sql
select *
from payment p 
where customer_id =355
and amount between 1 and 3
```

2-10. customer 테이블을 이용하여, 고객의 이름이 Maria, Lisa, Mike 에 해당하는 사람의 id, 이름, 성을 확인해주세요.

```sql
select customer_id , first_name , last_name 
from customer
where first_name in ('Maria', 'Lisa', 'Mike')
```

2-11. film 테이블을 이용하여,  film의 길이가  100~120 에 해당하거나 또는 rental 대여기간이 3~5일에 해당하는 film 의 모든 정보를 확인해주세요.

```sql
select *
from film f
where length between 100 and 120
or rental_duration  between 3 and 5
```

2-12. address 테이블을 이용하여, postal_code 값이  공백('') 이거나 35200, 17886 에 해당하는 address 에 모든 정보를 확인해주세요.

```sql
select *
from address a 
where postal_code in ('''35200', '17886')
```

2-13. address 테이블을 이용하여,  address 의 상세주소(=address2) 값이  존재하지 않는 모든 데이터를 확인하여 주세요.

```sql
select *
from address a 
where address2  isnull 
```

2-14. staff 테이블을 이용하여, staff 의  picture  사진의 값이 있는  직원의  id, 이름,성을 확인해주세요.  단 이름과 성을  하나의 컬럼으로 이름, 성의형태로  새로운 컬럼 name 컬럼으로 도출해주세요.

```sql
select staff_id , first_name||', '||last_name as name
from staff s 
where picture is not null
```

2-15. rental 테이블을 이용하여,  대여는했으나 아직 반납 기록이 없는 대여건의 모든 정보를 확인해주세요.

```sql
select *
from rental r 
where return_date isnull
```

2-16. address 테이블을 이용하여, postal_code 값이  빈 값(NULL) 이거나 35200, 17886 에 해당하는 address 에 모든 정보를 확인해주세요.

```sql
select *
from address a 
where postal_code isnull 
or postal_code in ('35200', '17886')
```

2-17. 고객의 성에 John 이라는 단어가 들어가는, 고객의 이름과 성을 모두 찾아주세요.

```sql
select first_name , last_name 
from customer c 
where last_name like '%John%'
```

2-18. 주소 테이블에서, address2 값이 null 값인 row 전체를 확인해볼까요?

```sql
select * 
from address a 
where address2 isnull
```





### 오답

* 문자열 합치기 : ||

* ""를 사용하면 컬럼명, ''를 사용하면 데이터 값

* 컬럼명 변경 : as 컬럼명
* 새로운 컬럼 생성 : (식) as 컬럼명
* %word% : word를 포함하는 모든 데이터
* word% : word로 시작하는 모든 데이터
* case when ~ then ~ else ~ end as
* coalesce