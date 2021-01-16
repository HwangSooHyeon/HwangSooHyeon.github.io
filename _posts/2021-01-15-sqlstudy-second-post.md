---
title: "SQL 스터디 2일차: Oracle DML1"
excerpt: "INSERT, UPDATE, DELETE, SELECT"

categories:
    - webprogramming
tags:
    - SQL
    - 스터디
date: 2021-01-16 01:23:00 +0900
---

### 1. 테이블 내 데이터 추가

```SQL
-- INSERT
INSERT INTO CUSOMERTBL(CST_ID, CST_NAME, CST_AGE, CST_DATE)
VALUES (1, 홍길동, 22, SYSDATE);
-- CUSTOMERTBL에 위 데이터를 삽입
```
* __주의사항__ 
데이터를 삽입할 때 __PRIMARY KEY__(CST_ID)가 중복될 경우, 무결성 제약조건(중복)에 의해 오류가 발생한다.  

테이블에 데이터를 추가할 때 자동으로 증가하는 __PRIMARY KEY__ 값을 생성해주고 싶으면 시퀀스를 사용할 수 있다.

```SQL
-- 시퀀스 생성
CREATE SEQUENCE SEQ_ITEMTBL_ITM_NO START WITH 1 INCREMENT BY 1 NOMAXVALUE NOCACHE;
-- 시작 값이 1이고 1씩 증가하는 SEQ_ITEM_ITM_NO를 생성
```
* __NOMAXVALUE__: 최대값 제한 없음
* __NOCACHE__: 메모리에 캐시를 할당하지 않는다는 뜻으로 시퀀스의 속도는 느려지지만 용량은 작아지는 역할을 한다. 또한 NEXTVAL을 사용했을 때 시퀀스의 시작 값이 바뀌지 않는다.(연속적)

### 2. 테이블 내 데이터 수정

```SQL
-- UPDATE
UPDATE ITEMTBL SET ITM_PRICE=4500 WHERE ITM_NO=5;
-- ITEMTBL 내의 ITM_PRICE=4500으로 바꾸되, ITM_NO=5인 데이터만 진행
```

### 3. 테이블 내 데이터 삭제

```SQL
-- DELETE
DELETE FROM ITEMTBL WHERE ITM_NO=6;
-- ITEMTBL에서 데이터를 삭제하되, ITM_NO=6인 데이터만 진행
```
실제 DB가 구축 되고 난 뒤 테이블 내 데이터는 함부로 삭제할 수 없다. 왜냐하면 해당 데이터와 연관된 데이터들이 있기 때문이다. 이는 실제 웹상에서도 확인할 수 있는데 탈퇴한 계정의 같은 아이디로 재가입을 못하는 경우 등이다.

### 4. 테이블 내 데이터 선택 및 조회

```SQL
-- SELECT
SELECT * FROM CUSTOMERTBL WHERE CST_AGE>=10 ORDER BY CST_AGE ASC; 
-- CUSTOMERTBL의 전체 컬럼을 선택하되, CST_AGE>=10인 경우만 출력하고 CST_AGE에 대해 오름차순 정렬
```

__SELECT__ 는 중요한 명령어 이므로 자세히 정리하고 넘어가자.  
__SELECT__ 의 구조를 풀어쓰면,
```SQL
SELECT 선택할 컬럼(들) FROM 컬럼(들)이 속한 테이블 WHERE 필터링 조건 ORDER BY 정렬 조건
```
이고, 이를 설명하면 아래와 같다.
1. 특정 테이블 내의 조회하고 싶은 컬럼들을 선택한다.
2. 해당 컬럼들의 데이터들 중 필터링 조건에 맞는 데이터들만 추려낸다.
3. 정렬 조건에 맞춰 데이터들을 정렬 후 출력한다.  
  
위 과정을 숙지하고 다른 예문을 보자.
```SQL
SELECT * FROM STUDENTTBL WHERE STD_SCORE_KOR>=50 ORDER BY STD_CLASS ASC, TD_SCORE_KOR ASC;
```
위 예문은 __STUDENTTBL__ 의 전체 컬럼을 선택하되, __STD_SCORE_KOR>=50__ 인 경우만 출력하고 __STD_CLASS__ 에 대해 오름차순 정렬한 후 <u>만약 같은 값</u>이면 __TD_SCORE_KOR__ 에 대해 오름차순 정렬한다. 라고 해석할 수 있다.  
첫 예문과 다른 점은 정렬 조건이 여러개라는 점이다. 이를 통해 정렬 순서를 결정할 수 있다.  
    
예문을 하나 더 확인해보자.

```SQL
SELECT STD_NO, STD_NAME, (KOR+ENG+MATH) STD_TOT, ROUND((KOR+ENG+MATH)/3, 2) STD_AVG
FROM STUDENTTBL ORDER BY STD_TOT ASC;
```
위 예문은 다른 예문들과 다르게 컬럼 선택 부분에서 사칙연산을 사용하고 있다. 사칙연산이 들어간 컬럼은 실제로는 존재하지 않지만 가상의 컬럼으로서 동작한다. 이때 다른 추가적인 명령어 없이 사칙연산 후 바로 출력할 수 있지만 연산이 진행된 후 정렬, 필터링과 같이 특정 명령을 수행하기 위해 연산식 뒤에 가상의 컬럼명을 할당하여 사용할 수도 있다.  
위 예문에선 (KOR+ENG+MATH)에는 STD_TOT, ROUND((KOR+ENG+MATH)/3, 2)에는 STD_AVG라는 가상의 컬럼명을 할당해주었다.