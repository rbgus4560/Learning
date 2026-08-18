# 03. SQL 활용

> SQLD 2과목 - SQL 기본 및 활용 / 2장 SQL 활용

---

## 1. 표준 조인

### 1.1 일반 집합 연산자와 SQL 비교

| 일반 집합 연산 | SQL 기능 | 의미 |
|---|---|---|
| UNION 연산 | `UNION` | 합집합, 시스템에 부하를 주는 작업 발생 |
| INTERSECTION 연산 | `INTERSECT` | 교집합 |
| DIFFERENCE 연산 | `EXCEPT` / Oracle `MINUS` | 차집합 |
| PRODUCT 연산 | `CROSS JOIN` | 곱집합, 조건이 없으면 가능한 모든 조합(M×N) |

### 1.2 순수 관계 연산자와 SQL 비교

- SELECT → `WHERE`
- PROJECT → `SELECT`
- NATURAL JOIN → 다양한 `JOIN`
- DIVIDE → 사용 X

### 1.3 JOIN 형태

ANSI SQL의 JOIN:

- `INNER JOIN`
  - `NATURAL JOIN`
  - `USING`
  - `ON`
- `CROSS JOIN`
- `OUTER JOIN`

#### INNER JOIN

JOIN 조건에서 동일한 값이 있는 행만 반환.

#### NATURAL JOIN

- 두 테이블 간 동일한 이름을 갖는 모든 칼럼에 대해 EQUI JOIN
- JOIN 칼럼은 ALIAS 사용 X

#### USING 조건절

같은 이름의 칼럼 중 원하는 칼럼에 대해서만 선택적으로 EQUI JOIN.

```sql
FROM USER_INFO
JOIN PROJECT USING (USER_ID)
```

#### ON 조건절

컬럼명이 다르더라도 JOIN 사용 가능.

```sql
FROM USER_INFO U
JOIN PROJECT P
ON (U.USER_ID = P.USER_ID)
```

#### CROSS JOIN

JOIN 조건이 없는 경우 생길 수 있는 모든 데이터 조합.

#### OUTER JOIN

JOIN 조건에서 동일한 값이 없는 행도 반환.

- `LEFT OUTER JOIN`
- `RIGHT OUTER JOIN`
- `FULL OUTER JOIN`

---

## 2. 집합 연산자

JOIN을 사용하지 않고 연관된 데이터를 조회하는 방법.

### 2.1 UNION

- 여러 SQL문의 결과에 대한 합집합
- 중복행은 한 개의 행으로 출력

### 2.2 UNION ALL

- 여러 SQL문의 결과에 대한 합집합
- 중복행을 그대로 출력

### 2.3 INTERSECT

- 여러 SQL문의 결과에 대한 교집합
- 중복행은 한 개의 행으로 출력
- `EXISTS`, `IN` 서브쿼리로 변경 가능

### 2.4 EXCEPT / MINUS

- 위 SQL문의 집합에서 아래 SQL문의 집합을 뺀 결과
- 차집합
- `NOT EXISTS`, `NOT IN` 서브쿼리로 변경 가능

---

## 3. 계층형 질의와 셀프 조인

### 3.1 계층형 질의

테이블에 계층형 데이터가 존재하는 경우 조회를 위해 사용.

동일 테이블 안에 계층적으로 상위/하위 데이터가 포함된 데이터.

#### 구성

1. `SELECT`: 조회할 칼럼 지정
2. `FROM TABLE`: 대상 테이블 지정
3. `WHERE`: 필터링
4. `START WITH`: 루트 데이터 지정
5. `CONNECT BY [NOCYCLE] [PRIOR] A AND B`: 다음에 전개될 자식 데이터 지정
6. `ORDER SIBLINGS BY 칼럼`: 동일 LEVEL 사이에서 정렬

#### PRIOR 방향

원문 정리 기준:

- `PRIOR 자식 = 부모`: 자식 → 부모 방향(순방향)
- `PRIOR 부모 = 자식`: 부모 → 자식 방향(역방향)

#### NOCYCLE

사이클이 발생한 이후 데이터 전개를 중단.

#### 가상칼럼

- **LEVEL**
  - 루트 데이터: 1
  - 하위 데이터가 있을 때마다 1씩 증가
- **CONNECT_BY_ISLEAF**
  - 전개 과정에서 리프 데이터면 1
  - 아니면 0
- **CONNECT_BY_ISCYCLE**
  - 전개 과정에서 자식을 갖는데 해당 데이터가 조상이면 1
  - 아니면 0

### 3.2 셀프 조인(Self Join)

동일 테이블 사이의 JOIN.

```sql
SELECT WORKER.EMPNO,
       WORKER.ENAME,
       MANAGER.ENAME
FROM EMP WORKER,
     EMP MANAGER
WHERE WORKER.MGR = MANAGER.EMPNO;
```

---

## 4. 서브쿼리

### 4.1 서브쿼리란

하나의 SQL문 안에 포함되어 있는 또 다른 SQL문.

- 서브쿼리는 메인 쿼리의 칼럼을 사용할 수 있음
- 메인 쿼리는 서브쿼리의 칼럼을 사용할 수 없음

### 4.2 주의점

- 서브쿼리를 괄호로 감싸서 사용
- 단일행/복수행 비교 연산자와 함께 사용 가능
- 단일행 비교 연산자는 서브쿼리 결과가 반드시 1건 이하
- 복수행 비교 연산자는 결과 건수와 상관 없음
- 서브쿼리에서 `ORDER BY` 사용 불가
- `ORDER BY`는 메인 쿼리의 마지막 문장에 위치

### 4.3 사용 가능한 위치

- `SELECT` 절
- `FROM` 절
- `WHERE` 절
- `HAVING` 절
- `ORDER BY` 절
- `INSERT`문의 `VALUES` 절
- `UPDATE`문의 `SET` 절

### 4.4 동작방식에 따른 분류

#### 비연관 서브쿼리

- 서브쿼리가 메인 쿼리의 칼럼을 가지고 있지 않음
- 메인 쿼리에 값을 제공하기 위한 목적

#### 연관 서브쿼리

- 서브쿼리가 메인 쿼리의 값을 가지고 있음
- 메인 쿼리가 먼저 수행되어 읽힌 데이터를 서브쿼리에서 조건이 맞는지 확인할 때 사용
- `EXISTS` 서브쿼리는 항상 연관 서브쿼리로 사용
- 여러 건을 만족하더라도 1건을 찾으면 추가 검색 X

### 4.5 반환 형태에 따른 분류

#### 단일행 서브쿼리

- 실행 결과가 1건 이하인 형태로 정리
- 비교 연산자와 함께 사용
  - `=`
  - `<`
  - `>`
  - `<=`
  - `>=`
  - `<>`

#### 다중행 서브쿼리

- 실행 결과가 여러 건
- 다중행 비교 연산자와 함께 사용
  - `IN`
  - `ALL`
  - `ANY`
  - `SOME`
  - `EXISTS`

#### 다중 칼럼 서브쿼리

- 실행 결과로 여러 칼럼 반환
- 메인 쿼리 조건에서 여러 칼럼 동시 비교 가능
- 서브쿼리와 메인 쿼리의 칼럼 수와 순서가 동일해야 함

### 4.6 그 밖의 위치에서 사용하는 서브쿼리

- **스칼라 서브쿼리**
  - `SELECT` 절에서 사용
  - 한 행/한 칼럼만 반환
- **인라인 뷰**
  - `FROM` 절에서 사용
- `HAVING` 절
- `INSERT`문의 `VALUES` 절
- `UPDATE`문의 `SET` 절

### 4.7 뷰(View)

실제 데이터를 가지고 있지 않는 가상 테이블.

#### 장점

- **독립성**
  - 테이블 구조가 변경되어도 뷰를 사용하는 응용프로그램은 변경 X
- **편리성**
  - 복잡한 질의를 뷰로 생성하여 관련 질의를 단순하게 작성
- **보안성**
  - 숨기고 싶은 칼럼을 제외하고 뷰를 생성하여 정보 노출 제한

---

## 5. 그룹 함수

그룹 함수를 이용하여 특정 집합의 소계, 중계, 합계, 총합계를 계산.

### 5.1 ROLLUP

- 소그룹 간 소계 계산
- 인자 순서가 바뀌면 결과가 바뀜
- 계층 구조

```sql
GROUP BY ROLLUP (DNAME, JOB);
```

결과 구조 예:

```text
DNAME1 - JOB1, JOB2
DNAME2 - JOB1, JOB2
```

### 5.2 CUBE

- 결합 가능한 모든 값에 대해 다차원적인 소계 계산
- 원문 정리: `2^N`만큼의 SUBTOTAL 생성

```sql
GROUP BY CUBE (DNAME, JOB);
```

### 5.3 GROUPING SETS

- 특정 항목에 대한 소계 계산
- 인자 순서가 바뀌어도 결과 동일
- 평등 관계

```sql
GROUP BY GROUPING SETS (DNAME, JOB);
```

예시 구조:

```text
DNAME1, DNAME2 - ALL JOB
ALL DNAME - JOB1, JOB2
```

---

## 6. 윈도우 함수

행과 행 간 관계에서 다양한 연산 처리 가능.

- 중첩 호출 X

기본 형태:

```sql
SELECT 윈도우함수(인자)
       OVER (
           PARTITION BY 칼럼
           ORDER BY 칼럼
           윈도우절
       )
FROM 테이블명;
```

### 6.1 순위 관련 함수

- **RANK**
  - 특정 항목(칼럼)에 대한 우선순위
  - 동일 값은 동일 순위
- **DENSE_RANK**
  - 동일 순위를 하나의 건으로 취급
- **ROW_NUMBER**
  - 동일 값이라도 고유 순위 부여

### 6.2 집계 관련 함수

- `SUM`
- `MAX`
- `MIN`
- `AVG`
- `COUNT`

> SQL Server의 경우 집계함수는 `OVER` 절 내부의 `ORDER BY` 지원 X.

### 6.3 행 순서 관련 함수

원문 기준: Oracle에서 지원.

- **FIRST_VALUE**
  - 파티션별 윈도우에서 가장 먼저 나온 값
  - 공동 등수 인정 없이 처음 나온 행만 처리
- **LAST_VALUE**
  - 파티션별 윈도우에서 가장 나중에 나온 값
  - 공동 등수 인정하지 않음
- **LAG**
  - 파티션별 윈도우에서 이전 몇 번째 행의 값

```sql
SELECT ENAME,
       HIREDATE,
       SAL,
       LAG(SAL, 2, 0) OVER (ORDER BY HIREDATE) AS PREV_SAL
FROM EMP
WHERE JOB = 'SALESMAN';
```

- **LEAD**
  - 파티션별 윈도우에서 이후 몇 번째 행의 값

```sql
SELECT ENAME,
       HIREDATE,
       LEAD(HIREDATE, 1) OVER (ORDER BY HIREDATE) AS NEXTHIRED
FROM EMP;
```

### 6.4 그룹 내 비율 관련 함수

- **CUME_DIST**
  - 현재 행보다 작거나 같은 건수에 대한 누적 백분율
- **PERCENT_RANK**
  - 파티션별 윈도우에서 처음 값을 0, 마지막 값을 1로 하여 순서별 백분율
- **NTILE**
  - 파티션별 전체 건수를 인수 값으로 N등분한 결과
- **RATIO_TO_REPORT**
  - 파티션 내 전체 `SUM(칼럼)` 값에 대한 행별 칼럼 값의 백분율을 소수점으로 구함

---

## 7. DCL

### 7.1 DCL

유저를 생성하고 권한을 제어할 수 있는 명령어.

#### Oracle 사용자 예시

- **SCOTT**: 테스트용 샘플 유저
- **SYS**: DBA 권한을 부여받은 최상위 유저
- **SYSTEM**: SYSTEM 데이터베이스의 모든 시스템 권한을 부여받은 SYS 바로 밑 유저

### 7.2 Oracle과 SQL Server 사용자 아키텍처 차이

#### Oracle

- 유저를 통해 DB에 접속
- ID/PW 방식으로 인스턴스 접속
- 해당 스키마 오브젝트 생성 등의 권한 부여

#### SQL Server

- 인스턴스 접속을 위해 로그인 생성
- 인스턴스 내 여러 DB에 연결해 작업하기 위해 유저 생성
- 로그인과 유저 매핑
- Windows 인증 방식 / 혼합 모드 방식 존재

### 7.3 시스템 권한

사용자가 SQL문을 실행하기 위해 필요한 적절한 권한.

#### GRANT

권한 부여.

```sql
GRANT CREATE USER TO SCOTT;
```

#### REVOKE

권한 취소.

```sql
REVOKE CREATE TABLE FROM USR;
```

### 7.4 ROLE

유저에게 알맞은 여러 권한을 한 번에 부여하기 위해 사용.

- **CONNECT**
  - SESSION
- **RESOURCE**
  - CLUSTER
  - PROCEDURE
  - TYPE
  - SEQUENCE
  - TRIGGER
  - OPERATOR
  - TABLE
  - INDEXTYPE

```sql
DROP ROLE TEST;

CREATE ROLE TEST;

GRANT CREATE TABLE TO TEST;

GRANT TEST TO TEST_USR;
```

---

## 8. 절차형 SQL

### 8.1 절차형 SQL

SQL문의 연속적인 실행이나 조건에 따른 분기처리를 이용하여 특정 기능을 수행하는 저장모듈을 만들 수 있음.

- `PROCEDURE`
- `TRIGGER`
- `USER DEFINED FUNCTION`

#### 저장모듈

- PL/SQL 문장을 DB 서버에 저장
- 사용자와 애플리케이션 사이에서 공유할 수 있도록 만든 SQL 컴포넌트 프로그램
- 독립적으로 실행되거나 다른 프로그램에서 실행될 수 있는 완전한 실행 프로그램

#### T-SQL

SQL Server를 제어하기 위한 언어.

### 8.2 PL/SQL

Block 구조이며 SQL문, IF, LOOP 등이 포함.

#### 특징

- Block 구조로 되어 있어 기능별 모듈화 가능
- 변수/상수 선언 가능
- IF/LOOP문 사용 가능
- DBML 에러나 사용자 에러 정의 가능
- Oracle에 내장되어 호환성이 좋음
- Block 단위로 묶어 한 번에 서버로 보내므로 네트워크 패킷 수 감소

#### 구조

- **DECLARE(선언부, 필수)**
  - `BEGIN~END`에서 사용할 변수/인수 정의 및 데이터 타입 선언
- **BEGIN(실행부, 필수)**
  - 개발자가 처리할 SQL문과 LOGIC이 정의되는 실행부
- **EXCEPTION(예외처리부, 선택)**
  - SQL문에서 발생한 에러를 처리하는 예외처리부
- **END(필수)**

### 8.3 사용자 정의 함수

프로시저처럼 SQL문을 IF/LOOP 등의 LOGIC과 함께 데이터베이스에 저장한 명령 집합.

- `SUM`, `AVG`, `NVL` 등의 함수처럼 호출 가능
- 프로시저와의 차이점
  - 반드시 한 건을 RETURN해야 함

### 8.4 TRIGGER

특정 테이블에 DML문이 수행되었을 때 DB에서 자동으로 동작하도록 작성한 프로그램.

- 사용자가 호출하지 않고 DBMS가 자동 수행
- 이벤트 발생 대상
  - 테이블
  - 뷰
  - 데이터베이스
- 발생 범위
  - 전체 트랜잭션 작업
  - 각 행에 대해서 발생

```sql
CREATE OR REPLACE TRIGGER TRIGER_TEST
```

### 8.5 프로시저와 트리거 비교

| 프로시저 | 트리거 |
|---|---|
| `CREATE PROCEDURE` 문법 사용 | `CREATE TRIGGER` 문법 사용 |
| `EXECUTE`/`EXEC` 명령어로 실행 | 생성 후 자동으로 실행 |
| 내부에서 `COMMIT`, `ROLLBACK` 실행 가능 | 내부에서 `COMMIT`, `ROLLBACK` 실행 안 됨 |
