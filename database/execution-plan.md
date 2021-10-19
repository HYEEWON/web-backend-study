# 🔗 실행 계획

<br>

## 정의

* 사용자가 작성한 SQL을 실행할 때, `옵티마이저가 수립하는 작업 절차`
* 옵티마이저가 쿼리의 `최적 실행 방법`을 결정하여 나타내는 쿼리 동작 계획
* 쿼리 문의 성능을 파악하여 `튜닝`에 사용

<br>

## 용어 정리

### 👉 Table Scan 
* 테이블의 전체 데이터 모두 검색 (Full Scan)

### 👉 Index Scan 
* 데이터 페이지의 전체 데이터 모두 검색 (Range Scan)
  * Leaf 레벨의 첫 번째 페이지부터 데이터를 검색
  * Clusterd 인덱스의 경우 Leaf 레벨 인덱스 페이지는 데이터 페이지 이기 때문에 Clustered Scan 용어를 사용

### 👉 Index Seek 
* 인덱스를 사용하여 검색
* B-Tree 구조인 인덱스의 페이지의 Root부터 Leaf 레벨까지 검색

### 👉 Hash Join 
* 두 테이블에 인덱스는 없고, 동등 조인을 수행하는 경우

<br>

## 실행 계획 유의 사항
* Table Scan/Index Scan은 성능에 좋지 않으므로 Table Seek/Index Seek으로 바꾸는 것이 좋음
* Scan → Seek: WHERE 절의 컬럼에 인덱스를 걸어주면 됨
* 데이터 수가 적으면 자동적으로 Scan이 될 수 있음

<br>

## SSMS 테스트
* 동일 쿼리를 Nested Loop, Sort Merge, Hash 조인으로 실행
* 쿼리 비용을 통해 각 쿼리 문의 성능을 비교할 수 있음 → 비율이 작은 것이 성능이 좋은 것

![image](https://user-images.githubusercontent.com/38900338/135803149-25b70b09-a959-415e-8055-0d9ace5c9015.png)