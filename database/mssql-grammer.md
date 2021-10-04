# 🔗 함수

<br>

## 🚀 집계함수
* 연산을 수행하는 함수
* `NULL 컬럼은 무시`

```sql
-- NULL은 제외
SELECT COUNT([컬럼]) FROM [테이블] WHERE [조건]; -- 카운트
SELECT SUM([컬럼]) FROM [테이블] WHERE [조건]; -- 합
SELECT MAX([컬럼]) FROM [테이블] WHERE [조건]; -- 최대 값
SELECT MIN([컬럼]) FROM [테이블] WHERE [조건]; -- 최소 값
SELECT AVG([컬럼]) FROM [테이블] WHERE [조건]; -- 평균
```

<br>

## 🚀 날짜 함수

```sql
SELECT GETDATE(); -- (YYYY-MM-DD HH:MM:SS.SSS)
SELECT SYSDATETIME(); -- (YYYY-MM-DD HH:MM:SS.SSSSSSS)
-- datepart
SELECT DATEADD(datepart, number, date);
SELECT DATEPART(datepart, date);
SELECT DATEDIFF(datepart, startdate, enddate); 
```

``` sql
SELECT GETDATE(); -- 현재 날짜와 시간
SELECT DATEADD(dd, 10, GETDATE()); -- 10일 뒤
SELECT DATEADD(wk, 2, GETDATE()); -- 2주 뒤
SELECT DATEPART(yyyy,GETDATE()); -- (YYYY 형식)
SELECT DATEDIFF(mi,'2021-04-08 00:00:00',GETDATE());
```

### 👉 GETDATE()와 SYSDATETIME()의 차이

||GETDATE()|SYSDATETIME()|
|:---:|:---:|:---:|
|형식|datetime|datetime2|
|특징|초단위 아래 소수점 3자리까지 표현<br>8바이트 사용<br>1753-01-01 ~ 9999-12-31|초단위 아래 소수점 7자리까지 표현 (더 자세함)<br>6~8바이트 사용<br>0001-01-01~ 9999-12-31|

<br>

## 🚀 데이터 타입 변환 함수

``` sql
CONVERT (data_type [(length)], expression, [style]) -- style 생략 가능
CAST (expression AS data_type [(length)]);
```

``` sql
SELECT CONVERT(CHAR(8), GETDATE(), 1); -- mm/dd/yy 형식으로 변환 (style로 형식 지정)
SELECT CONVERT(INT, 29.8); -- 29
SELECT CAST(29.8 AS INT); -- 29
SELECT CAST(3 AS VARCHAR) + 'age'; -- 3age
```

## 🚀 이동

### 👉 GOTO
* GOTO를 만나면 이름에 해당하는 라벨로 이동
* 라벨 이름은 "라벨이름:" 형식으로 선언

```sql
GOTO LabelName;
LabelName:
    -- 내용
```

```sql
DECLARE @Counter int;
SET @Counter = 1;

WHILE @Counter < 10
BEGIN
    SELECT @Counter
    SET @Counter = @Counter + 1
    IF @Counter = 4 GOTO Branch_One -- Branch_One으로 이동
    IF @Counter = 5 GOTO Branch_Two -- Branch_Two로 이동
END

Branch_One:
    SELECT 'Jumping To Branch One.'
    GOTO Branch_Three; -- Branch_Three로 이동
Branch_Two:
    SELECT 'Jumping To Branch Two.';
Branch_Three:
    SELECT 'Jumping To Branch Three.';
```

## 🚀 제어문

### 👉 IF / ELSE
* 프로그래밍에서의 IF/ELSE와 같음
* IF/ELSE문에서 SQL 문이 여러 개일 경우 BEGIN/END 사용

```sql
DECLARE @Counter int;
SET @Counter = 2;
IF (SELECT @Counter) = 1
    SELECT 1;
ELSE
    SELECT 0;

-- 결과: 0
```

### 👉 CASE
* 단순 CASE문과 검색된 CASE문, 두가지 형태 존재
* 단순 CASE문: 동등성 비교만 진행
* 검색된 CASE문: WHEN 절에서 조건을 비교하여 결정


```sql
-- 예시에서 사용한 테이블
CREATE TABLE Product (
ProductNumber int,
ProductLine VARCHAR(30),
ProductName VARCHAR(30),
ListPrice int
)
INSERT INTO Product VALUES (1, 'T', 'NAME1', 100);
INSERT INTO Product VALUES (2, 'M', 'NAME2', 1000);
INSERT INTO Product VALUES (3, 'S', 'NAME3', 500);
INSERT INTO Product VALUES (4, 'R', 'NAME4', 25);
INSERT INTO Product VALUES (5, 'A', 'NAME5', 300);
INSERT INTO Product VALUES (6, 'M', 'NAME6', 700);
```

#### 단순 CASE문

```sql
SELECT ProductNumber, Category =
    CASE ProductLine
        WHEN 'R' THEN 'Road'
        WHEN 'M' THEN 'Mountain'
        WHEN 'T' THEN 'Touring'
        WHEN 'S' THEN 'Other sale items'
        ELSE 'Not for sale'
    END,
    ProductName
FROM Product;
```

#### 검색된 CASE문

```sql
SELECT ProductNumber, ProductName, "Price Range" =
CASE
WHEN ListPrice = 0 THEN 'Free'
WHEN ListPrice < 50 THEN 'Under $50'
WHEN ListPrice >= 50 and ListPrice < 250 THEN 'Under $250'
WHEN ListPrice >= 250 and ListPrice < 1000 THEN 'Under $1000'
ELSE 'Over $1000'
END
FROM Product
```

### 👉 WHILE / BREAK / CONTINUE
* WHILE: BEGIN / END 사이의 SQL문을 반복
* WHILE문 안의 IF절에서 BREAK이 사용될 경우 WHILE 문 종료
* WHILE문 안의 IF절에서 CONTINUE가 사용될 경우 WHILE 문 제일 처음으로 돌아감


```sql
WHILE
BEGIN
    IF -- 조건
        BREAK
    IF -- 조건
        CONTINUE
END
```

### 👉 BEGIN / END
* 실행할 SQL을 묶어 놓은 것
* IF/ELSE 문에서 SQL 문이 여러 개일 경우 사용
* WHILE 문에서 WHILE에 포함되는 범위를 지정할 때 사용

### 👉 RETURN
* 쿼리나 프로시저를 무조건 종료
* 정수를 반환 가능 → 설정이 없는 프로시저의 반환 0: 성공 / 그 외: 실패 / NULL: 반환 불가

## 🚀 커서

### 👉 정의
* 행 단위 작업을 효율적으로 하기 위한 방식
* 테이블의 여러 행을 조회한 후, 한 행씩 처리하는 방식
* 커서는 질의 수행 결과로 반환되는 첫번째 튜플에 대한 포인터로 생각할 수 있음
* 한 행씩 읽으면서 처리 → 데이터가 많을 때에는 사용하지 않는 것이 좋음
* 사용 예시: 데이터 마이그레이션, 일별 정리 배치 프로그램 (탈퇴 시 15일 보관 후 삭제, 1년 미 사용 계정 정리 등)

```sql
DECLARE → OPEN → FETCH → CLOSE → DEALLOCATE
CREATE TABLE MY_FRIEND(
NAME NVARCHAR(20),
AGE NUMERIC(3)
)
INSERT INTO MY_FRIEND(NAME,AGE)VALUES('',25)
INSERT INTO MY_FRIEND(NAME,AGE)VALUES('',30)
INSERT INTO MY_FRIEND(NAME,AGE)VALUES('',27)
INSERT INTO MY_FRIEND(NAME,AGE)VALUES('',32)
```

```sql
select * from MY_FRIEND;
-- ROW
DECLARE
@NAME VARCHAR(100),
@AGE INT
DECLARE CUR CURSOR -- CUR
FOR SELECT --
NAME,
AGE
FROM MY_FRIEND
OPEN CUR --
FETCH NEXT FROM CUR INTO @NAME,@AGE -- , SELECT @NAME,@AGE
-- ROW
WHILE @@FETCH_STATUS = 0 -- @@FETCH_STATUS: FETCH -> 0:
BEGIN
UPDATE MY_FRIEND
SET AGE = @AGE+1
WHERE NAME = @NAME
FETCH NEXT FROM CUR INTO @NAME,@AGE -- ROW
END
--
CLOSE CUR
DEALLOCATE CUR --
select * from MY_FRIEND;
```
실행 결과: AGE 1씩 증가

## 🚀 예외 처리 (TRY / CATCH / THROW)

```sql
BEGIN TRY
    -- 내용
END TRY
BEGIN CATCH
    -- 내용
    -- THROW 예외
END CATCH
```

```sql
BEGIN TRY
    SELECT 1/0, ' ';
END TRY
BEGIN CATCH
    THROW
END CATCH;
```

```sql
-- 예외 발생 로그 기록
BEGIN TRY
SELECT 1/0, ' ';
END TRY
BEGIN CATCH
    SELECT
        ERROR_NUMBER() AS ErrorNumber
        ,ERROR_SEVERITY() AS ErrorSeverity
        ,ERROR_STATE() AS ErrorState
        ,ERROR_PROCEDURE() AS ErrorProcedure
        ,ERROR_LINE() AS ErrorLine
        ,ERROR_MESSAGE() AS ErrorMessage;
END CATCH;
```