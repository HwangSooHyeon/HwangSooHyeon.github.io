---
title: "SQL 스터디 3일차: Oracle DML2"
excerpt: "COUNT, CALCULATION, GROUP, LIMIT"

categories:
    - 웹프로그래밍
tags:
    - SQL
    - 스터디
date: 2021-01-16 20:12:00 +0900
---

2일차 SELECT를 이어서 진행한다.

### 1. 데이터의 열 갯수 구하기

```SQL
-- COUNT
SELECT COUNT(*) FROM STUDENTTBL;
-- STUDENTTBL 내의 모든 컬럼 데이터의 갯수를 구함

SELECT COUNT(STD_NO) FROM STUDENTTBL;
-- STUDENTTBL 내의 STD_NO 컬럼 데이터의 갯수를 구함

SELECT COUNT(DISTINCT STD_CLASS) FROM STUDENTTBL;
-- STUDENTTBL 내의 STD_CLASS 컬럼 데이터의 갯수를 구하되, 중복은 제외
```

### 2. 데이터의 사칙 연산

```SQL
-- MAX, MIN
SELECT MAX(STD_SCORE_KOR), MIN(STD_SCORE_KOR) FROM STUDENTTBL;
-- STUDENTTBL 내의 STD_SCORE_KOR의 최댓값과 STD_SCORE_KOR의 최솟값을 구함

-- SUM
SELECT SUM(STD_SCORE_KOR) FROM STUDENTTBL;
-- STUDENTTBL 내의 STD_SCORE_KOR의 총 합을 구함

-- AVG
SELECT AVG(STD_SCORE_KOR) FROM STUDENTTBL;
-- STUDENTTBL 내의 STD_SCORE_KOR의 평균을 구함

-- STDDEV
SELECT STDDEV(STD_SCORE_KOR) FROM STUDENTTBL;
-- STUDENTTBL 내의 STD_SCORE_KOR의 표준편차를 구함
```

### 3. 데이터의 그룹화

``` SQL
-- GROUP BY 
SELECT STD_CLASS, SUM(STD_SCORE_MATH) FROM STUDENTTBL WHERE STD_SCORE_MATH>=90 GROUP BY STD_CLASS;
/*
STUDENTTBL 내의 STD_CLASS와 STD_SCORE_MATH의 합을 출력하되, 
STD_SCORE_MATH>=90을 만족하는 데이터만 진행하고 연산이 끝나면 STD_CLASS를 기준으로 그룹화
*/

-- HAVING
SELECT STD_CLASS, SUM(STD_SCORE_KOR), COUNT(*), AVG(STD_SCORE_KOR) 
FROM STUDENTTBL GROUP BY STD_CLASS HAVING SUM(STD_SCORE_KOR) >= 90;
/*
STUDENTTBL 내의 STD_CLASS, STD_CORE_KOR의 합, 전체 데이터 갯수, STD_SCORE_KOR의 평균을 출력하되, 
연산이 끝나면 STD_CLASS를 기준으로 그룹화한 후 SUM(STD_SCORE_KOR)>=90을 만족하는 데이터만 출력
*/
```

* WHERE과 HAVING의 차이점
WHERE의 조건은 데이터 연산이 진행되기 전 우선적으로 필터링되고 HAVING은 연산이 진행된 후 그룹화 된 데이터를 필터링한다.  

### 3. 데이터의 제한

```SQL
-- LIMIT
SELECT ITM_NO, ITM_NAME, ITM_PRICE, ROW_NUMBER() OVER (ORDER BY ITM_NO DESC) ITM_ROWN 
FROM ITEMTBL;
/*
ITEMTBL 내의 ITM_NO, ITM_NAME, ITM_PRICE, 
ITM_NO을 기준으로 내림차순 순번을 매긴 열 번호 ITM_ROWN를 출력
*/

SELECT * FROM 
    (SELECT ITM_NO, ITM_NAME, ITM_PRICE, ROW_NUMBER() OVER (ORDER BY ITM_NO DESC) ITM_ROWN 
    FROM ITEMTBL)
WHERE ITM_ROWN BETWEEN  1 AND 3;
/*
ITEMTBL 내의 ITM_NO, ITM_NAME, ITM_PRICE, 
ITM_NO을 기준으로 내림차순 순번을 매긴 열 번호 ITM_ROWN에 대한 가상의 테이블을 만든 뒤
1<=ITM_ROWN<=3을 만족하는 데이터만 출력
*/
```

두 번째 예문은 첫 번째 예문을 가상의 테이블로 만들어 사용한다. 특정 값을 만족하는 데이터를 출력하고 싶을 때는 WHERE 또는 HAVING을 사용하지만 원하는 갯수만큼 데이터를 출력해야할 경우 ROW_NUMBER()를 사용한 LIMIT기법을 이용한다.
