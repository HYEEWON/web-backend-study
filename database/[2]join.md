# Join

<br>

## 🌻 Join의 정의
* 두 개 이상의 테이블을 결합하여 데이터를 검색하는 것
* 각 테이블에 같은 속성이 존재해야 함

<br>

## 🌻 Join 종류 (논리적 조인)

### 👉 Inner Join

* 두 개의 테이블에서 공통된 요소를 통해 결합

### 👉 Left Outer Join

* 왼쪽 테이블의 모든 결과를 가져온 후, 오른쪽 테이블을 매칭 / 값이 없으면 NULL

### 👉 Right Outer Join

* 오른쪽 테이블의 모든 결과를 가져온 후, 왼쪽 테이블을 매칭 / 값이 없으면 NULL

### 👉 Full Outer Join

* 기준 테이블 없이 모든 데이터 검색

### 👉 Cross Join

* 특정 기준 없이 테이블을 결합하여 가능한 모든 경우의 수를 결과로 반환

<br>

## 🌻 Join 방식 (물리적 조인)

### 👉 Nested Loop Join (중첩 루프 조인)

#### 🔸 정의

* 두개 이상의 테이블이 있을 때, 하나의 테이블(Driving/Outer Table)을 기준으로 순차적으로 다른 테이블(Driven/Inner Table)의 행을 조인하여 원하는 데이터를 추출
* Driving 테이블의 크기가 작을수록 성능 향상

<table>
    <th rowspan="3">Driving 테이블</th>
        <td>첫번째로 접근되는 테이블</td>
        <tr>
            <td>선행 테이블의 처리 범위가 성능을 결정 → where절 조건에 의해서 출력된 레코드가 가장 적은 테이블을 사용</td>
        </tr>
        <tr>
            <td>선행 테이블은 최초 행만 액세스, 이후는 스캔</td>
        </tr>
    <th rowspan="3">Driven 테이블</th>
        <td>나중에 접근되는 테이블</td>
        <tr>
            <td>조인 컬럼은 인덱스로 구성되어야 함</td>
        </tr>
        <tr>
            <td>선행 테이블의 결과를 통해 후행 테이블을 엑세스 할 때에는 랜덤 IO 발생</td>
        </tr>
</table>

#### 🔹 특징

<table>
    <th>순차적</th>
        <td>두 테이블 간의 연결 최종 선택까지 순차적으로 진행</td>
    <tr>
    <th>선행적</th>
        <td>선행 테이블의 처리범위가 성능을 결정</td>
    </tr>
    <tr>
    <th>종속적</th>
        <td>후행 테이블은 선행 테이블의 결과 값을 받아 처리<br>(후행 테이블은 선행 테이블의 결과를 필터링하는 것)</td>
    </tr>
    <tr>
    <th>랜덤 액세스</th>
        <td>선행 테이블의 결과로 후행 테이블에 접근하는 것은 랜덤 I/O</td>
    </tr>
    <tr>
    <th>연결고리의 중요성</th>
        <td>선행 테이블의 값으로 후행 테이블에 접근하기 떄문에 인덱스가 있는 것이 좋음</td>
    </tr>        
</table>

<table>
    <th>장점</th>
        <td>하나의 테이블로 처리 범위를 줄일 수 있음선행 테이블을 잘 선택하면 처리량이 적음</td>
    <tr>
    <th>단점</th>
        <td>두 테이블을 연결할 때의 랜덤 I/O가 부담</td>
    </tr>
</table>

#### 🔸 처리 순서

```sql
-- emp 테이블의 deptno, dept  테이블의 deptno 컬럼은 인덱스 존재
-- dept 테이블이 드라이빙 테이블 (옵티마이저가 통계를 보고 선택)

Select e.ename, e.deptno, d.dname
From dept d, emp e
Where e.deptno = d.deptno
and e.job = 'CLERK'
and d.deptno = 10
and d.loc like '서울%';
```

(1) dept 테이블의 deptno 인덱스에서 "deptno = 10"인 인덱스 범위를 추출<br>
(2) dept 테이블에서 튜플에 Access<br>
(3) 튜플의 "d.loc like '서울%'" 조건을 확인 후 아니면 (2)로 돌아감<br>
(4) "e.deptno = d.deptno"에서 d.deptno는 상수가 되어 "e.deptno = 10"이 됨<br>
(5) emp 테이블은 랜덤 Access<br>
(6) emp 테이블의 튜플에서 "e.job = 'CLERK'" 조건 확인 → 맞으면 Select<br>
(7) (2)부터 다시 시작

### 👉 Sort Merge Join (정렬 병합)

#### 🔹 정의

* 두 테이블의 처리 범위에 각자 접근하여 정렬 → 스캔 → Merge하여 처리
* 두 입력의 Join 열이 정렬되어 있고 ON절에 동등 연산(=)일 경우 수행, 정렬되어 있지 않다면 쿼리 옵티마이저가 Merge Join 아래에 정렬 연산자를 배치
* MSSQL에서는 테이블에서의 필터링 결과가 Unique할 때, 이 조인을 사용

#### 🔸 특징

<table>
    <th>동시적</th>
        <td>두 테이블을 동시에 읽고 조인을 수행</td>
    <tr>
    <th>독립적</th>
        <td>각 테이블의 Where 조건으로 처리 범위 감소 가능</td>
    </tr>
    <tr>
    <th>전체 범위 처리</th>
        <td>정렬 후 조인을 진행해 부분 범위 처리가 되지 않음</td>
    </tr>
    <tr>
    <th>연결고리의 중요성 없음</th>
        <td>정렬 결과를 스캔하여 인덱스는 중요하지 않음</td>
    </tr>    
</table>

<table>
    <th rowspan="3">장점</th>
        <td>정렬 후 스캔을 하기 때문에 인덱스가 없을 때 사용할 수 있음</td>
        <tr>
            <td>넓은 범위의 데이터도 처리 가능</td>
        </tr>
        <tr>
            <td>비동등 조인(>, <)도 가능</td>
        </tr>
    <th>단점</th>
        <td>정렬로 인한 부담</td>
</table>

#### 🔹 처리 순서

```sql
-- A.KEY, B.KEY가 인덱스 
SELECT COL1, COL2 
FROM TAB1 A INNER JOIN TAB2 B ON A.KEY = B.KEY
WHERE A.KEY = '111' 
AND A.COL1 LIKE '222%' 
AND B.COL2 = '333';
```
(1) 아래 두 가지가 동시에 발생
* "A.KEY = '111'"인 것을 찾고 "A.COL1 LIKE '222%'" 조건을 만족하는 튜플을 연결고리인 A.KEY로 정렬
* "B.COL2 = '333'"를 만족하는 튜플을 찾아 B.KEY로 정렬

(2) 정렬된 결과에서 A.KEY = B.KEY를 만족하는 결과를 병합(Merge)을 하면서 Select
* 행을 비교하다가 한쪽이 커지면 정지, 반대쪽 행의 컬럼값을 키워서 다시 비교 → 랜덤 액세스가 아닌 스캔 사용

### 👉 Hash (Match) Join (해시 매치)

#### 🔸 정의

* Hash Table을 만들어 Hash 함수로 탐색을 하여 조인 (해시 충돌 → Hash Chain = Linked List)
* 대용량 데이터를 사용할 때 사용되고 Nested Loop, Sorted Merge보다 빠름
* Inner/Outer join, 차/교/합집합, 그룹화(Group By), 중복 제거 등에서 사용

<table>
    <th rowspan="2">Build Input (Outer Table)</th>
        <td>해시 테이블을 만드는데 사용되는 테이블</td>
    <tr>
        <td>옵티마이저가 작은 입력을 Build Input으로 설정</td>
    </tr>
    <tr>
    <th>Probe Input (Inner Table)</th>
        <td>해시 테이블을 탐색하는 입력</td>
    </tr>  
</table>

#### 🔹 특징

* 동등 조인(=)에서만 사용 가능
* 조인 결과가 정렬되지 않고, 각 테이블의 인덱스를 사용하지 않음
* 해시 충돌, 해시 체인의 크기 증가 등을 막기위해 중복 데이터가 적을 때 사용하는 것이 좋음 (키 중복이 거의 없을 때 사용)
* Outer Table이 Hash Area에 담길 정도로 충분히 작을 때 사용
* Nested Loop 조인의 랜덤 Access 부하가 크거나 비효율적일 떄 사용
* Sort Merge 조인의 정렬 부하가 클 때 사용
* Hash Table은 일회성으로 수행 빈도가 낮고 수행 시간이 긴 조인에 사용하는 것이 좋음

#### 🔸 종류

<table>
    <th rowspan="2">In-Memory Hash Join<br>(인 메모리 해쉬 조인)</th>
        <td>해시 테이블을 메모리에 유지할 수 있을 경우 사용</td>
        <tr>
            <td>1. Build Input을 읽어 해시 테이블을 생성 (키와 행을 저장)<br>2. Probe Input을 스캔하여 해시 테이블을 탐색 → 해시 값으로 버킷을 찾아 해시 체인을 스캔하며 데이터를 검색</td>
        </tr>
    <th rowspan="3">Grace Hash Join<br>(유예 해쉬 조인)</th>
        <td>해시 테이블을 저장할 메모리가 부족해 디스크를 사용하는 경우 사용</td>
        <tr>
            <td>해시 테이블을 메모리에 유지할 수 있을 경우 사용</td>
        </tr>
        <tr>
            <td>1. 파티션 단계<br>&nbsp;&nbsp;▪&nbsp;&nbsp;Where 조건절로 테이블 필터링<br>&nbsp;&nbsp;▪&nbsp;&nbsp;해시 함수를 적용해서 해시 값에 따라 파티셔닝 → 파티션 별로 TempDB에 임시 파일로 저장<br>&nbsp;&nbsp;▪&nbsp;&nbsp;두 테이블은 같은 해시 함수를 사용하여 같은 해시 키를 가짐 → 같은 해시 키를 가진 두 파티션을 파티션 Pair라고 함<br>
            2. 조인 단계<br>&nbsp;&nbsp;▪&nbsp;&nbsp;파티션 Pair에서 한쪽 파티션 파일을 해시 테이블로 생성<br>&nbsp;&nbsp;&nbsp;&nbsp;(어떤 파티션 Pair가 Build Input이 될지는 독립적으로 결정)<br>&nbsp;&nbsp;▪&nbsp;&nbsp;파티션 파일의 행을 하나씩 읽어 해시 테이블을 탬색</td>
        </tr>
</table>

<br>

## 🌻 MSSQL 사용 방법
```sql
-- INNER JOIN

SELECT *
FROM a INNER JOIN b ON a.cul = b.cul;

-- LEFT OUTER JOIN

SELECT *
FROM a LEFT OUTER JOIN b ON a.cul = b.cul;

-- RIGHT OUTER JOIN

SELECT *
FROM a RIGHT OUTER JOIN b ON a.cul = b.cul;

-- FULL OUTER JOIN

SELECT *
FROM a FULL OUTER JOIN b ON a.cul = b.cul;

-- CROSS JOIN

SELECT *
FROM a CROSS JOIN b ON a.cul = b.cul;
```

```sql
-- Nested Loop Join

SELECT *
FROM a INNER LOOP JOIN b ON a.cul = b.cul;

SELECT *
FROM a INNER JOIN b ON a.cul = b.cul
OPTION (LOOP join);

-- Merge Join

SELECT *
FROM a INNER MERGE JOIN b ON a.cul = b.cul;

SELECT *
FROM a INNER JOIN b ON a.cul = b.cul
OPTION (MERGE join);

-- Hash Join

SELECT *
FROM a INNER HASH JOIN b ON a.cul = b.cul;

SELECT *
FROM a INNER JOIN b ON a.cul = b.cul
OPTION (HASH join);
```