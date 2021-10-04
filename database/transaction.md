# 🔗 트랜잭션 (Transaction)

<br>

## 🥨 정의 및 특징
* 하나의 논리적인 작업 단위
* 작업이 전부 수행되거나 전부 수행되지 않음
* 원자성 (Atomicity): 트랜잭션이 모두 반영되거나 모두 반영되지 않아야 함
  * Commit, Rollback, Save Point로 보장
* 일관성 (Consistency): 데이터베이스에 일관성이 있어야 함 (예) 도메인 유지, 기본기와 외래키 관계
  * 트리거로 보장
  * 트리거: 어떤 조건이 발생하면 자동으로 실행되는 프로시저
* 독립성 (Isolation): 두개 이상의 트랜잭션이 동시에 발생할 때 서로의 연산에 끼어들 수 없음
  * Lock으로 보장
* 지속성 (Durability): 트랜잭션의 결과는 영구적으로 반영됨

<br>

## 🥨 Commit과 Rollback

* Commit: 트랜잭션이 성공하여 작업이 DB에 영구적으로 반영되는 것
* Rollback: 트랜잭션이 실패하여 작업 내역이 취소되고 원래의 상태로 되돌아 가는 것

### 👉 SQL
* 트랜잭션 시작 후 Commit/Rollback을 하지 않으면 테이블에 Lock이 있어 외부에서 사용 불가
* SSMS에서 트랜잭션을 시작하고 Commit/Rollback을 하지 않음 → 새로운 쿼리창에서 테이블을 사용하면 해당 쿼리는 실행되지 않음 (무한 대기)

```sql
BEGIN TRAN [이름]; -- 트랜잭션 시작
ROLLBACK TRAN [이름]; -- 트랜잭션 롤백
COMMIT TRAN [이름]; -- 트랜잭션 적용
SAVE TRAN [이름]; -- Save Point 지정
dbcc opentran; -- 활성 트랜잭션 확인
```
```sql
CREATE TABLE Product (
    ProductNumber int IDENTITY (1, 1) NOT NULL,
    ProductLine VARCHAR(30),
    ProductName VARCHAR(30),
    ListPrice int
)
INSERT INTO Product VALUES ('T', 'NAME1', 100);
INSERT INTO Product VALUES ('S', 'NAME2', 100);
INSERT INTO Product VALUES ('M', 'NAME3', 100);
```

```sql
-- Commit 예시

SELECT * FROM Product WHERE ProductName='NAME1';
BEGIN TRAN;
UPDATE Product SET ListPrice = 10 WHERE ProductName='NAME1';
SELECT * FROM Product WHERE ProductName='NAME1';
COMMIT TRAN trans;
SELECT * FROM Product WHERE ProductName='NAME1';
```

```sql
-- Rollback 예시

SELECT * FROM Product WHERE ProductName='NAME1';
BEGIN TRAN;
UPDATE Product SET ListPrice = 50 WHERE ProductName='NAME1';
SELECT * FROM Product WHERE ProductName='NAME1'; --
ROLLBACK TRAN --
SELECT * FROM Product WHERE ProductName='NAME1';
```

```sql
-- Save Point와 Rollback 예시

SELECT * FROM Product;
BEGIN TRAN;
UPDATE Product SET ListPrice = 10 WHERE ProductName='NAME1';
SAVE TRAN TRAN1;
UPDATE Product SET ListPrice = 10 WHERE ProductName='NAME2';
ROLLBACK TRAN TRAN1;
UPDATE Product SET ListPrice = 10 WHERE ProductName='NAME3';
COMMIT TRAN ;
SELECT * FROM Product;
```

|Commit SQL 결과|Rollback SQL 결과|Save Point SQL 결과|
|:--:|:--:|:--:|
|![image](https://user-images.githubusercontent.com/38900338/135787483-9f508a29-85ae-4568-992d-22aa44755e77.png)|![image](https://user-images.githubusercontent.com/38900338/135787513-4146bba0-99ab-455a-b567-3e23fd4b54d9.png)|![image](https://user-images.githubusercontent.com/38900338/135788635-4186ee23-88b1-4dc2-b7b8-4a56c82621db.png)|

<br>

## 🥨 동시성 제어 (Currency Control)

* 다중 사용자 DBMS에서 반드시 지원해야 되는 기능
* 둘 이상의 트랜잭션이 동시에 발생할 때 `일관성`을 유지하도록 하는 것
* 갱신 손실, 모순성, 연쇄 복귀 등의 문제 발생 가능 → 동시성 제어를 통해 해결

### 👉 필요성
#### 갱신 손실 (Lost Update)
* 하나의 트랜잭션이 갱신한 데이터를 다른 트랜잭션이 덮어쓰면서 갱신이 무효화되는 것
한 개의 데이터를 동시에 갱신할 경우 발생
* 예시) A 트랜잭션이 x=100을 읽어 x=150이라고 갱신했는데, B 트랜잭션이 갱신 전의 x=100을 읽어 작업하는 경우
#### 모순성 (Inconsistency)
* 다른 트랜잭션이 어떤 항목을 갱신하는 동안 한 트랜잭션이 어떤 것은 갱신 전의 값을, 어떤 것은 갱신 후의 값을 읽어 발생하는 데이터 불일치
* 예시) A 트랜잭션, B 트랜잭션이 x와 y를 수정: A가 x를 수정 → B가 x와 y를 수정 → A가 y를 수정하면 데이터 불일치 발생
#### 연쇄 복귀 (Cascading Rollback) 또는 회복 불가능 (Unrecoverability)
두 트랜잭션이 동일한 데이터에 접근할 때 발생
한 트랜잭션이 데이터를 갱신하고 실패하여 Rollback을 하려고 할 때, 다른 트랜잭션이 동일 데이터를 읽어 수정하면 롤백이 불가능
예시) 한 트랜잭션이 데이터를 갱신 → 다른 트랜잭션이 동일 데이터를 갱신 → 기존 트랜잭션이 Rollback을 시도하면 Rollback 불가능

<br>

## 🥨 잠금 (Lock)

* 동시성 제어 기법

### 👉 정의
* 트랜잭션의 실행 순서를 강제로 제어해 직렬 가능한 스케줄이 되도록 보장하는 것
* DB에서 특정 자원이 사용되고 있는 동안 `다른 트랜잭션의 접근이 제한`되는 것 (병행 접근 방지)
* 데이터의 `무결성과 일관성` 유지를 위한 것
* Lock이 있으면 속도가 느려질 수 있음

### 👉 종류
#### 공유 잠금 (Shared Lock)
* `Select 문`에 의해 설정 (트랜잭션이 데이터를 읽을 때 설정, 데이터의 변경이 없는 동안에만 설정)
* 공유 잠금이 있어도 여러 트랜잭션이 하나의 개체를 `읽을 수 있음 (쓰기 불가)` → 여러 트랜잭션이 하나의 개체에 동시에 공유 잠금 가능
* 다른 트랜잭션이 데이터에 공유 잠금을 걸어둔 경우, 공유 잠금 요청은 허용하고 배타적 잠금은 허용하지 않음

#### 배타적 잠금 (Exclusive Lock)
* `INSERT, UPDATE, DELETE 문`에 의해 설정 (트랜잭션이 데이터를 변경할 때 설정)
* 다른 트랜잭션이 자원에 접근하지 못함 → 여러 트랜잭션이 하나의 개체에 동시에 배타적 잠금 불가
* 다른 트랜잭션이 데이터에 배타적 잠금을 걸어둔 경우, 공유 잠금과 배타적 잠금 모두 허용하지 않음

### 👉 트랜잭션의 Lock
* 트랜잭션은 읽거나 쓰기 전에 Lock을 걸고, 종료 전에 Unlock을 진행
* 다른 트랜잭션의 Lock으로 인해 특정 트랜잭션이 데이터에 접근하지 못할 경우 → 대기 상태

### 👉 잠금 단위 (Locking Granularity)
* 잠금 대상인 데이터 객체의 크기
* 레코드 필드 값, 레코드, 테이블, 데이터베이스, 디스크 블록의 크기 등 모두 가능
* 잠금 단위가 클수록 동시성 수준은 낮아지고, 동시성 제어 기법은 간단
* 잠금 단위가 작을수록 동시성 수준은 높아지고, 동시성 제어 기법은 복잡

### 👉 SQL
```sql
Lock
EXEC SP_LOCK; -- Lock spid
SELECT * FROM SYS.sysprocesses WHERE blocked > 0; -- Lock 조회
DBCC inputbuffer(spid); -- lOck에 걸리게 한 쿼리 확인
EXEC SP_WHO (spid); -- 프로세스 정보 확인
EXEC SP_WHO2 (spid); -- 프로세스 정보 확인 (자세한 정보)
KILL [spid]; -- 프로세스 종료
```

### 👉 잠금의 한계
* 직렬 가능한 스케줄이 항상 보장되지 않음 -> 2단계 잠금 규약으로 해결
* `교착상태(Deadlock)` 발생 가능
  * 교착상태: 두개 이상의 트랜잭션이 다른 트랜잭션의 자원을 기다리며 무한 대기 상태에 있는 것

<br>

## 🥨 2단계 잠금 규약 (Two-Phase Locking Protocol)
### 👉 기본 2단계 잠금 규약
#### 정의
* 트랜잭션들의 Lock, Unlock 시간을 구분하여 수행하는 것
* 모든 Lock이 최초의 Unlock보다 먼저 발생 → 직렬 가능성 보장
* 확장 단계(Growing Phase) 트랜잭션은 Lock 연산만 할 수 있고, Unlock 연산은 할 수 없는 단계
* 축소 단계(Shrinking Phase) 트랜잭션은 Unlock 연산만 할 수 있고, Lock 연산은 할 수 없는 단계
#### 한계
* 일관성 보장, 직렬 가능성 보장
* 교착상태(Deadlock)에 빠질 수 있어 주의 필요
* Cascading Rollback이 발생 가능

### 👉 Strict 2 Phase Locking Protocol
* 모든 배타적 잠금은 해당 트랜잭션이 완료될 때까지 Unlock을 하지 않음
* 배타적 잠금의 Unlock을 트랜잭션이 완료될 때 수행
* Cascading Rollback이 발생하지 않음

### 👉 Rigorous 2 Phase Locking Protocol
* 모든 Lock은 트랜잭션이 완료될 때까지 Unlock을 하지 않음
* Strict 2PLP보다 제한적

### 👉 Static 2 Phase Locking Protocol
* 트랜잭션의 실행 전에 트랜잭션의 모든 읽기/쓰기 집합을 선언하고 모두에 대한 Lock을 획득
* 교착상태가 발생하지 않음
* 현실성이 없음
* 대부분의 상용 DBMS는 동시성 제어를 위해 Strict 2PLP나 Rigorous 2PLP를 사용

<br>

## 🥨 트랜잭션 스케줄링
* 직렬 스케줄: 특정 트랜잭션의 실행이 종료되어야 다른 트랜잭션을 실행
* 비직렬 스케줄: 트랜잭션을 병행해서 실행하는 것
* 직렬 가능 스케줄: 서로 영향을 주지 않는 트랜잭션들을 비직렬 스케줄링으로 실행