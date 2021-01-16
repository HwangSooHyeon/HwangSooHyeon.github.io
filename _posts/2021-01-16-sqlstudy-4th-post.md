---
title: "SQL 스터디 4일차: Oracle DML3"
excerpt: "JOIN"

categories:
    - webprogramming
tags:
    - SQL
    - 스터디
date: 2021-01-16 23:10:00 +0900
---

4일차에선 SELECT의 JOIN에 대해 공부한다.

### 1. 테이블 JOIN

```sql
-- 기본 JOIN 문법
SELECT * FROM 테이블1 INNER JOIN 테이블2 ON 테이블1.컬럼 = 테이블2.컬럼;

-- 조건을 포함한 예문
SELECT * FROM ORDERTBL INNER JOIN ITEMTBL ON ORDERTBL.ITM_NO = ITEMTBL.ITM_NO 
WHERE ORDERTBL.ORD_CNT > 20;
/*
ORDERTBL.ITM_NO = ITEMTBL.ITM_NO를 만족하는
ORDERTBL에 ITEMTBL을 INNER JOIN한 가상의 테이블의 데이터를 출력하되, 
ORDERTBL.ORD_CNT > 20을 만족하는 데이터만 진행
*/
```

JOIN을 진행할 경우 외래키 관계로 묶인 컬럼을 기준으로 두 개의 테이블을 묶는다. 세 개 이상을 묶으려면 괄호를 이용해 먼저 2개를 묶어 가상의 테이블을 만든 뒤 나머지 하나와 묶어 완성한다.

```sql
-- OUTER JOIN
SELECT * FROM SHOP_ORDER FULL OUTER JOIN SHOP_ITEM 
ON SHOP_ORDER.ORD_ITEM = SHOP_ITEM.ITM_CODE;
/* 
SHOP_ORDER.ORD_ITEM = SHOP_ITEM.ITM_CODE를 만족하는
SHOP_ORDER와 SHOP_ITEM을 FULL OUTER JOIN한 가상의 테이블의 데이터를 출력
*/
```
INNER JOIN과 OUTER JOIN의 차이점은 다음과 같다.
* INNER JOIN은 두 테이블의 겹치는 데이터, 즉 교집합이 되도록 JOIN을 진행한다.
* OUTER JOIN은 두 테이블이 겹치지 않는 데이터까지, 즉 합집합이 되도록 JOIN을 진행한다.

다시 말하면, INNER JOIN은 ON 뒤에 나오는 조건을 만족하는 경우에 대해서만 데이터 출력을 진행하므로, 만약 두 테이블 중 하나의 테이블의 데이터 갯수가 적을 경우 작은 테이블에 맞춰 데이터가 잘리게 된다. 그러나 OUTER JOIN의 경우 REFT, RIGHT로 기준을 잡아주는 테이블에 맞춰 출력되는 데이터 갯수가 결정되므로 기준이 되는 테이블의 데이터 갯수가 많다고 해서 잘리지 않는다. 오히려 갯수가 부족한 테이블의 데이터가 NULL로 출력되어 기준이 되는 테이블의 데이터 갯수에 맞춰진다.
