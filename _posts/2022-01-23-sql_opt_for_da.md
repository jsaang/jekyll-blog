---
title: 당신의 쿼리가 느린 이유
author:
  name: Jsang
  link: https://github.com/jsaang
date: 2022-01-23 00:30:00
categories: [Data, SQL]
tags: [SQL, Study, Tip]
---

<br />

는 정확히 모르겠지만, 적어도 내 쿼리가 느린 이유는 알아가고 있다. 몇몇 데이터 분석가님들과 **보다 효율적인 쿼리를 짜는 것**을 목표로 SQL 스터디를 진행하고 있다. 그동안 무지성으로 짜왔던 내 쿼리들을 돌아보며 좀 더 나은(빠르고 효율적인) 쿼리를 작성하고 싶다는 욕심이 생겼다. SQL은 R, Python 보다도 자주 사용하게 되는 분석가의 깐부가 아니던가. 하지만 대충 적당히 짜면 짜지긴 하니 소홀했던 게 팩트다.

쿼리를 효율적으로 짜는 것은 분석가에게 꼭 필요한 스킬이 아닐 수 있다. ‘쿼리 최적화’ 키워드로 구글링을 해보면 개발 관점의 최적화가 대부분이다. 또 그 내용을 들여다보면 분석을 위한 쿼리 작성에서 적용하기 어려운 내용도 많다. 그럼에도 분석가가 쿼리 최적화를 공부해보는 것은 두 가지 측면에서 의미가 있다고 생각한다.

1. **SQL에 대한 이해와 관심이 높아진다 :** SQL에 대해 처음부터 파고들자면 밑도 끝도 없고 엄두도 안 난다. 하지만 “더 빠른 쿼리를 작성해보자”는 명확한 목표로 시작하게 되면 인덱스, 옵티마이저, 실행 계획 등 좁혀진 범위를 공부하게 된다. 이렇게 수줍게 알아가다가 더 궁금해지면 더 깊게 공부해보면 될 일이다.
2. **비효율적인 쿼리로 인해 발생하는 비용을 줄일 수 있다 :** 무쟈게 큰 게임 로그 데이터를 다루던 전 회사에선, 쿼리 잘못 날리면 집 한 채 태우는 거라는 넝담같은 위협을 듣기도 했었다. 분석을 위해 한두번 비효율적인 쿼리를 날리는 거라면 큰 문제가 아닐 수 있겠지만(?), 배치를 걸어 정기적으로 동작하게 될 쿼리라면 이야기가 달라지지 않을까? 회삿돈이 남의 돈이긴 하지만 아껴 쓰면 좋으니까...

<br />

![](/assets/posts/sql_opt_for_da/fig_1.png)
_쿼리 최적화를 공부하면 좋은 점을 어거지로 써봤지만 그냥 궁금해서 공부하고 있다. SQL로 먹고 살고 있는데 궁금할 수 있잖아요!_

<br />

사실 분석가의 쿼리에서 중요한 것은 **효율성보단 작성 속도와 가독성**이라고 생각한다. 실제로 최적화를 염두에 두고 분석 쿼리를 작성하니 분석 속도가 느려지는 것을 느꼈다. (물론 익숙해지면 빨라지겠지만) 또한 최적화된 쿼리가 가독성도 좋으리란 보장은 없다. 쿼리로 협업을 해야하는 경우는 말할 필요도 없고, 오롯이 혼자 관리하는 쿼리라 할지라도 한 달 후의 나는 내가 아니라는 것을 다들 아실 것이다. 그럼에도 **분석가로서 최소한 이 정도는 알아두면 좋지 않을까?** 싶은 것들을 한번 정리해보기로 했다.

(참고로 최적화된 쿼리는 DBMS 종류와 버전에 따라 다를 수 있으므로, 항상 사용 중인 DBMS 종류에 최적화된 방법을 공부하는 것이 중요하다! 예컨데 MySQL 5.5 버전과 5.6 버전의 서브쿼리 성능은 천지차이로 다르다.)

<br />
<br />
<br />
<br />

## 1. 무지성 SELECT 멈춰!

---

### SELECT * 사용을 지양하자

분석을 하다보면 이 컬럼도 쓰게 되지 않을까?라는 이유로 `SELECT *`를 난사하곤 한다. (사실 그냥 귀찮아서...) 당연한 말이지만 테이블에서 몇 개의 컬럼만 필요한 상황이라면 `SELECT *`를 사용할 이유가 하등없다. 분석에 쓰일 컬럼이 예측 가능한 상황이라면, 해당 컬럼만 선택해서 불러오는 습관을 들여보도록 하자.

```sql
-- BAD
SELECT * FROM commerce.order_hist

-- GOOD
SELECT order_id, order_date, order_amt FROM commerce.order_hist
```

<br />

### 불필요한 SELECT DISTINCT를 남발하지 말자

부끄럽지만 위와 비슷한 맥락으로 `SELECT DISTINCT`를 애용하곤 했다. 특히 Primary Key가 걸려 있기 때문에 중복이 있을 염려가 없음에도 무지성 DISTINCT를 걸던 만행을 반성한다. DISTINCT는 중복된 행을 찾기 위해 데이터를 정렬하는 과정이 포함되기 때문에 효율이 좋지 않다. 나는 가독성이 떨어져서 선호하지 않는 방법이지만, [GROUP BY를 사용하면](https://stackoverflow.com/questions/6598778/solution-for-speeding-up-a-slow-select-distinct-query-in-postgres/6598989) 대체로 더 좋은 효율을 낼 수 있다고 한다.

```sql
-- BAD
SELECT DISTINCT order_id, order_date, order_amt FROM commerce.order_hist

-- GOOD
SELECT order_id, order_date, order_amt FROM commerce.order_hist
```

<br />
<br />
<br />
<br />

## 2. 그렇게 할거면 Index 왜 만듬?

---

쿼리 최적화를 공부하다보니 가장 많이 보게 되는 단어가 바로 **Index**였다. Index를 얼마나 잘 활용하느냐가 쿼리의 효율에 가장 큰 영향을 미친다. Index는 단어 그대로, 책의 색인처럼 원하는 위치를 빠르게 탐색하고 이동할 수 있게 도와주는데, 원리와 작동방식에 대한 내용은 생략한다. 혹시 Index에 대해 더 궁금하신 분들께서는 아래 글들을 추천한다.

- [https://jojoldu.tistory.com/243?category=761883](https://jojoldu.tistory.com/243?category=761883)
- [https://towardsdatascience.com/indexing-for-sql-query-optimization-139b57db9fc6](https://towardsdatascience.com/indexing-for-sql-query-optimization-139b57db9fc6)

<br />

Index 활용이 가능한 쿼리를 작성하는 것을 [**SARGABLE**(Search ARGument ABLE)](https://en.wikipedia.org/wiki/Sargable)이라고 부른다고 한다. **제발 WHERE에서 Index 컬럼을 변형시키지 마라!!** 라고 나는 나이브하게 이해했다. 아래 케이스들을 보며 SARGABLE한 쿼리가 어떤 것인지 알아보자.

<br />

### Index 컬럼에 함수 씌우거나 연산하지 말자

`order_date`가 Index다. [CASE 1]은2021년 데이터만 가져오고 싶은 상황이고, [CASE 2]는 오늘 기준으로 30일 이전 데이터만 가져오고 싶은 상황이다. Index에 함수를 씌워버리게 되면 Index 활용을 못하게 된다. (함수의 종류에 따라 그렇지 않을 수도 있긴하다.) 이를 해결하기 위해선 원하는 함수의 **역함수**를 우변에 적용하자.

```sql
-- CASE 1

-- BAD
SELECT order_id, order_date, order_amt 
FROM commerce.order_hist
WHERE YEAR(order_date) = '2021'

-- GOOD
SELECT order_id, order_date, order_amt 
FROM commerce.order_hist
WHERE order_date >= '2021-01-01' AND order_date < '2022-01-01'


-- CASE 2

-- BAD
SELECT order_id, order_date, order_amt 
FROM commerce.order_hist
WHERE DATE_DIFF(order_date, CURRENT_DATE()) >= 30

-- GOOD
SELECT order_id, order_date, order_amt 
FROM commerce.order_hist
WHERE order_date <= DATE_ADD(CURRENT_DATE(), INTERVAL -30 DAY)
```

<br />

이번엔 `order_amt`가 Index고 order_amt가 3만 이상인 데이터만 가져오고 싶은 상황이다. 분석을 하다 보면 종종 이런 쿼리를 작성하게 된다. 예를 들어, 1만원이 구매 건당 평균 구매금액이라고 한다면, 구매 금액(order_amt)이 평균 구매 금액보다 2만원 이상 큰 구매를 뽑기 위해 BAD 케이스와 같은 쿼리를 작성하게 된다. 쿼리의 의미상 이렇게 작성하는 것이 더 직관적으로 느껴지기 때문이다. 하지만 만약 해당 컬럼이 Index라면 활용을 못하게 된다는 사실!

```sql
-- BAD
SELECT order_id, order_date, order_amt 
FROM commerce.order_hist
WHERE order_amt - 10000 >= 20000

-- GOOD
SELECT order_id, order_date, order_amt 
FROM commerce.order_hist
WHERE order_amt >= 30000
```

<br />

### Index 컬럼과 비교하는 컬럼 타입을 일치시키자

`order_id`가 Index고 VARCHAR형이다. 이 때 숫자형과 대조하게 되면 Index를 활용하지 못한다.

```sql
-- BAD
SELECT order_id, order_date, order_amt 
FROM commerce.order_hist
WHERE order_id = 12345

-- GOOD
SELECT order_id, order_date, order_amt 
FROM commerce.order_hist
WHERE order_id = '12345'
```

<br />

### LIKE 문에서 %keyword% 막 쓰지 말자

`order_id`가 Index일때, LIKE를 통한 필터링에서 %를 앞뒤로 넣게 되면 Index 활용을 못하게 된다. “123으로 시작하는”이라는 표현을 하기 위해 %를 앞뒤로 넣을 필요는 없다. %123%가 비효율적인 이유가 궁금하다면 [이 글](https://www.brentozar.com/archive/2010/06/sargable-why-string-is-slow/)을 읽어보시길!

```sql
-- BAD
SELECT order_id, order_date, order_amt 
FROM commerce.order_hist
WHERE order_id LIKE '%123%'

-- GOOD
SELECT order_id, order_date, order_amt 
FROM commerce.order_hist
WHERE order_id LIKE '123%'
```

<br />
<br />
<br />
<br />

## 글을 쓰다보니 자신감이 쪼그라들었다

---

이외에도 쿼리 최적화의 핵심 중 하나인 **서브쿼리** 등을 다뤄보고 싶었으나, 글을 작성하다 보니 여전히 SQL에 대한 이해가 많이 부족하다고 느꼈다. 부정확한 정보를 전달하게 될까 두려워 이번 글은 이 정도에서 마무리하고자 한다. SQL 공부를 계속 꾸준히 해서 어느 정도 이해했다고 느껴지면 더 질 좋은 컨텐츠를 만들어 보고 싶다.

어찌 됐든 이번 SQL 스터디를 통해 상대적으로 소홀했던 SQL에 관심을 가지고, 더 깊게 공부해볼 수 있는 기틀을 마련할 수 있어 좋았다. 분석가는 쿼리를 손발처럼 자유롭게 다룰 수 있어야 한다고 생각한다. 잘 설계된 DB 구조와 Index들이 쿼리 최적화의 전부라고도 하지만, 주어진 환경 내에서 좋은 쿼리를 작성하는 연습은 분명 큰 도움이 될 것이다. 꾸준히 공부하고 적용을 시도해봐야겠다.

<br />
<br />
<br />
<br />

## References

---

- [http://www.mysqlkorea.com/sub.html?mcode=manual&scode=01&lang=k&cat1=7](http://www.mysqlkorea.com/sub.html?mcode=manual&scode=01&lang=k&cat1=7)
- [https://www.sisense.com/blog/8-ways-fine-tune-sql-queries-production-databases/](https://www.sisense.com/blog/8-ways-fine-tune-sql-queries-production-databases/)
- [https://mangkyu.tistory.com/52?category=761304](https://mangkyu.tistory.com/52?category=761304)
- [https://yulsfamily.tistory.com/233](https://yulsfamily.tistory.com/233)
- [https://jojoldu.tistory.com/243?category=761883](https://jojoldu.tistory.com/243?category=761883)
- [https://jojoldu.tistory.com/520](https://jojoldu.tistory.com/520)