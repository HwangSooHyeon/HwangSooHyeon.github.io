---
title: "SQL 스터디 1일차: Oracle DDL"
excerpt: "테이블 생성, 삭제, 수정"

categories:
    - 웹프로그래밍
tags:
    - SQL
    - 스터디
last_modified_at: 2021-01-15 22:01:00
---

SQL의 정의와 주로 사용하게 되는 문법들은 다음과 같다.  

### SQL(Structured Query Language)의 종류
* 데이터 정의(DDL)  
* 데이터 조작(DML)  
* 데이터 제어(DCL)  


### 데이터 정의어(Data Definition Language)
__: 데이터의 구조를 정의하기 위한 테이블 생성, 수정, 삭제 명령어__

```sql
CREATE -- 테이블 생성
DROP -- 테이블 삭제
ALTER -- 테이블 수정
TRUNCATE -- 테이블에 있는 모든 데이터 삭제
```

### 데이터 조작어(Data Manipulation Language)  
__: 데이터 추가, 조회,  수정 및 삭제를 위한 명령어__  

```sql
SELECT -- 데이터 조회
INSERT -- 데이터 입력
UPDATE -- 데이터 수정
DELETE -- 데이터 삭제
```

### 데이터 제어어(Data Control Language) 
__: 사용자에게 권한 생성 혹은 권한 삭제 명령어__

```sql
GRANT -- 권한 생성
REVOKE -- 권한 삭제
```

---

### 1. 테이블 생성

SQL의 정의와 SQL의 여러 핵심어들을 알아보았으니 다음으로 CREATE를 이용해 테이블을 생성해보자.  
  
테이블을 생성하는 방법으로는  

1. Oracle sqldeveloper에서 직접 타이핑 
2. eclipse ermaster를 통해 테이블을 만든 후 DDL 내보내기를 이용  

이 있다. oracle에서 타이핑하는 방법은 다음과 같다.

```sql
-- MEMBERTBL 생성
CREATE TABLE MEMBERTBL(
    USER_ID VARCHAR2(30) PRIMARY KEY,
    USER_PW VARCHAR2(200),
    USER_AGE NUMBER,
    USER_TEL VARCHAR2(15)
);
COMMIT;
```

새로운 데이터 타입과 __PRIMARTU KEY__ 라는 것이 눈에 띈다.  
한 번 짚고 넘어가도록 하자.

* __PRIMARY KEY__(기본키): 테이블의 유일성을 보장하기 위한 제약조건이다. 하나의 테이블에는 하나의 기본키만 가질 수 있다.  
  
* __VARCHAR2__: 가변형 __CHAR__ 로, 들어오는 데이터의 크기에 따라 저장 공간의 크기가 바뀐다.
예) 
__CHAR(4)__='A'
__VARCHAR2(4)__='A'
위와 같이 저장했을 때 __CHAR__ 는 고정형이므로 'A---' 처럼 빈 공간은 공백으로 저장되지만 __VARCHAR2__ 는 'A---'이 아닌 데이터가 없는 뒤 3칸은 사라지고 실제 데이터는 'A'만 저장된다.  
  
* __CLOB__: 사이즈가 큰 문자열 데이터를 DB 외부에 저장하기 위한 타입이다.  

코드를 입력하고 나면 __COMMIT__ 을 실행하여 코드를 적용시킨다. 이때 __COMMIT__ 대신 __ROLLBACK__ 을 실행하면 되돌리기가 가능하다. 그러나 __COMMIT__ 을 입력하지 않고 약 15분 정도 지나면 자동으로 __COMMIT__ 이 적용된다.  
  

### 2. 테이블 조정

테이블을 생성했으니 다음으로 테이블 내의 컬럼을 조정하는 명령어들을 알아보자.

```sql
-- 컬럼 추가
ALTER TABLE MEMBERTBL ADD USER_ADDR VARCHAR2(200);
-- MEMBERTBL에 데이터타입 VARCHAR2(200)인 USER_ADDR 컬럼을 추가

-- 컬럼 수정
ALTER TABLE MEMBERTBL MODIFY USER_ADDR VARCHAR2(300);
-- MEMBERTBL의 컬럼 USER_ADDR의 데이터타입 크기를 300으로 수정

-- 컬럼 제거
ALTER TABLE MEMBERTBL DROP COLUMN USER_ADDR;
-- MEMBERTBL의 컬럼 USER_ADDR을 테이블에서 제거
```

### 3. 테이블 관계 생성

CREATE를 통해 테이블을 생성하면 처음엔 모두 독립적인 상태이다. 이때 각 테이블끼리 관계를 형성하려면(외래키) 다음과 같이 작성한다.

```sql
-- ORDERTBL이 존재하면 제거
DROP TABLE ORDERTBL CASCADE CONSTRAINTS;

-- ORDERTBL 생성
CREATE TABLE ORDERTBL
(
	ORD_NO number NOT NULL,
	ORD_CNT number,
	ORD_DATE date,
	ITM_NO number NOT NULL,
	CST_ID number NOT NULL,
	PRIMARY KEY (ORD_NO)
);

-- ORDERTBL 내의 CST_ID를 CUSTOMERTBL에서 받아온 외래키로 설정
ALTER TABLE ORDERTBL
	ADD FOREIGN KEY (CST_ID)
	REFERENCES CUSTOMERTBL (CST_ID)
;

-- ORDERTBL 내의 ITM_NO를 ITEMTBL에서 받아온 외래키로 설정
ALTER TABLE ORDERTBL
	ADD FOREIGN KEY (ITM_NO)
	REFERENCES ITEMTBL (ITM_NO)
```

위와 같이 작성하면 서로 독립적이었던 ORDERTBL, CUSTOMTBL, ITEMTBL이 외래키로 엮이면서 관계를 갖게 된다. 이때 가져오는 데이터를 최소로 할 수 있도록 __PRIMARY KEY__(기본키) 정보만 받아와야 한다. 왜냐하면 추후 수정이 필요할 때 데이터 갯수가 많을 경우 수정이 힘들어지기 때문이다.

