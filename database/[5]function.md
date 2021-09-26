# 함수

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
SELECT DATEADD(datepart, number, date); --
SELECT DATEPART(datepart, date); --
SELECT DATEDIFF(datepart, startdate, enddate); --
```

``` sql
SELECT GETDATE(); --
SELECT DATEADD(dd, 10, GETDATE()); -- 10
SELECT DATEADD(wk, 2, GETDATE()); -- 2
SELECT DATEPART(yyyy,GETDATE()); -- (YYYY 형식)
SELECT DATEDIFF(mi,'2021-04-08 00:00:00',GETDATE()); --
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