# 🔗 사용자 정의 함수

<br>

## 🍑 종류

### 👉 스칼라 함수
* 단일 데이터를 리턴하는 함수

```sql
CREATE (ALTER) FUNCTION [이름]
(
    @[파라미터] [타입]
)
RETURNS @[반환 타입]
AS
BEGIN
    DECLARE @[변수] [타입];
    -- 내용
RETURN @[변수];
END
```

### 👉 테이블 반환 함수
* 테이블을 리턴하는 함수

#### 인 라인 테이블 반환 함수
* 간단히 SELECT만 사용해 테이블 반환
* BEGIN/END 사용하지 않음

```sql
CREATE (ALTER) FUNCTION [이름]
(
    @[파라미터] [타입]
)
RETURNS TABLE
AS
RETURN ( 
    -- 단일 SELECT 문
    SELECT * FROM [테이블];
)
```

#### 다중 문 테이블 반환 함수

* INSERT 문이 포함
* BEGIN/END 사용

```sql
CREATE (ALTER) FUNCTION [이름]
(
    @[파라미터] [타입]
)
RETURNS @[테이블 변수] TABLE
(
    [열 이름] [타입]
)
AS
BEGIN
    -- INSERT INTO @[테이블 변수] VALUES(값1, 값2);
    RETURN
END
```

### 👉 함수 호출

```sql
SELECT dbo.[함수 이름]([파라미터]); -- DBO (DB Owner)
SELECT * FROM [함수 이름]([파라미터]);
```

### 👉 함수 삭제

```sql
DROP FUNCTION [함수 이름];
```

<br>

## 🍑 프로시저와 함수의 차이
|프로시저|함수|
|:--:|:--:|
|리턴 값이 있을 수도 없을 수도 있음|리턴 값 필수|
|서버에서 실행 → 빠름|클라이언트에서 실행 → 느림|

<br>

## 🍑 SQL 예시

```sql
-- 스칼라 함수 예시

CREATE FUNCTION CALCULATE_BIRTH_YEAR_AGE
(
    @AGE INT
)
RETURNS INT
AS
BEGIN
    DECLARE @BIRTH_YEAR INT;
    SET @BIRTH_YEAR = YEAR(GETDATE())-@AGE+1;
    RETURN @BIRTH_YEAR
END
```

```sql
-- 인 라인 테이블 반환 함수

CREATE FUNCTION SELECT_PERSON
(
    @AGE INT
)
RETURNS TABLE
AS
RETURN (
    SELECT * FROM Person WHERE AGE >= @AGE;
)
```

```sql
-- 다중 문 테이블 반환 함수

CREATE FUNCTION INSERT_DATA()
RETURNS @RETURN_TABLE TABLE
(
    name VARCHAR(30),
    age INT
)
BEGIN
    INSERT INTO @RETURN_TABLE VALUES ('이름1' , 35);
    INSERT INTO @RETURN_TABLE VALUES ('이름2' , 40);
    RETURN
END
```

```sql
-- 함수 실행

SELECT DBO.CALCULATE_BIRTH_YEAR_AGE(10); -- (1)
SELECT * FROM SELECT_PERSON(23); -- (2)
SELECT * FROM INSERT_DATA(); -- (3)
```

![image](https://user-images.githubusercontent.com/38900338/135802882-5db2b2e6-38b7-4972-ae6a-119fd4b94ad8.png)