# 🔗 저장 프로시저 (Stored Procedure)

<br>

## 🐷 정의

* 쿼리문의 집합, 특정 동작을 `일괄 처리`하는 용도로 사용
* 쿼리문을 하나의 메서드 형식으로 관리하는 것
* 자주 실행할 쿼리문을 하나의 단위로 묶어 저장 → 필요 시 호출해서 사용

<br>

## 🐷 SQL

```sql
-- Stored Procedure 생성

CREATE PROCEDUER (또는 PROC) [프로시저 이름]
(
    @[변수] [자료 형]
)
AS
BEGIN
    -- 내용
END
```

```sql
-- Stored Procedure 수정

ALTER PROCEDUER (또는 PROC) [프로시저 이름]
(
    @[변수] [자료 형]
)
AS
BEGIN
     -- 내용
END
```

```sql
--Stored Procedure 확인

sp_helptext [이름];
```

```sql
-- 실행

EXEC ( EXECUTE) [이름] @parameter1='val1', @parameter2=val2;
EXEC ( EXECUTE) [이름] 'val1', val2;
```

```sql
-- 삭제

DROP PROCEDURE [이름];
```

### 👉 예시

```sql
CREATE TABLE Person (
    id INT IDENTITY (1, 1) NOT NULL,
    name VARCHAR(30),
    age INT
);

INSERT INTO Person VALUES('Person1', 21);
INSERT INTO Person VALUES('Person2', 20);
INSERT INTO Person VALUES('Person3', 22);
INSERT INTO Person VALUES('Person4', 21);
INSERT INTO Person VALUES('Person5', 23);
INSERT INTO Person VALUES('Person6', 22);
INSERT INTO Person VALUES('Person4', 21);
INSERT INTO Person VALUES('Person5', 23);
INSERT INTO Person VALUES('Person6', 22);
```

```sql
-- Stored Procedure 예시: Select

CREATE PROCEDURE FIND_PERSON_PROC
(
    @PERSON_NAME VARCHAR(30),
    @PERSON_AGE INT
)
AS
BEGIN
    SELECT p.name, p.age
    FROM person AS p
    WHERE p.age = @PERSON_AGE AND p.name = @PERSON_NAME;
    RETURN
END;
```

```sql
-- Stored Procedure 예시: Update

CREATE PROC UPDATE_PERSON_PROC
(
    @PERSON_NAME VARCHAR(30),
    @PERSON_AGE INT
)
AS
BEGIN
    SET NOCOUNT ON
    SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED

    Begin TRAN
        UPDATE person SET name = @PERSON_NAME WHERE age=@PERSON_AGE;
    COMMIT TRAN

    SET NOCOUNT OFF
    RETURN
END
```

```sql
-- Stored Procedure 실행

EXEC FIND_PERSON_PROC 'PERSON5', 23; -- (1)
EXEC UPDATE_PERSON_PROC @PERSON_NAME='NEW_PERSON', @PERSON_AGE = 20; -- (2)
SELECT * FROM person;
```

![image](https://user-images.githubusercontent.com/38900338/135801087-ec9984ae-58f0-4eb8-b637-faac8ea467fc.png)