# 02. SQL 기본

> SQLD 2과목 - SQL 기본 및 활용 / 1장 SQL 기본

---

## 1. 관계형 데이터베이스 개요

### 1.1 데이터베이스

특정 기업, 조직 또는 개인이 필요에 의해 데이터를 일정한 형태로 저장해 놓은 것.

#### DBMS

- 효율적인 데이터 관리
- 데이터 손상 방지
- 데이터 복구를 위한 기능 제공

### 1.2 관계형 데이터베이스

- 정규화를 통한 합리적인 테이블 모델링
- 이상 현상 제거
- 데이터 중복 회피
- 동시성 관리
- 병행 제어를 통해 여러 사용자가 동시에 데이터 공유 및 조작 가능
- 보안 기능 제공
- 데이터 무결성 보장
- 장애 발생 시 입력/수정/삭제 데이터가 정상 반영되도록 보장
- 시스템 다운/재해 상황에서도 데이터 회복 및 복구 가능

### 1.3 SQL

관계형 데이터베이스에서 데이터 정의, 조작, 제어를 위해 사용하는 언어.

| 구분 | 의미 | 명령어 |
|---|---|---|
| DML | Data Manipulation Language, 데이터 조작어 | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| DDL | Data Definition Language, 데이터 정의어 | `CREATE`, `ALTER`, `DROP`, `RENAME` |
| DCL | Data Control Language, 데이터 제어어 | `GRANT`, `REVOKE` |
| TCL | Transaction Control Language, 트랜잭션 제어어 | `COMMIT`, `ROLLBACK` |

### 1.4 테이블

- 데이터를 저장하는 객체
- 데이터베이스의 기본 단위

#### 기본 용어

- **Column**: 세로 방향의 속성
- **Row**: 가로 방향의 데이터, 튜플, 인스턴스
- **정규화**
  - 데이터 정합성 확보
  - 입력/수정/삭제 시 발생 가능한 이상현상 방지를 위해 중복 제거
- **기본키(PK)**
  - 테이블에 존재하는 각 행을 한 가지 의미로 특정할 수 있는 한 개 이상의 칼럼
- **외부키(FK)**
  - 다른 테이블의 기본키로 사용되고 있는 관계를 연결하는 칼럼
- **ERD(Entity Relationship Diagram)**
  - 테이블 간 상관관계를 도식화
  - 엔터티, 관계, 속성으로 구성

---

## 2. DDL

### 2.1 주요 데이터 타입

- **CHAR(L)**
  - 고정 길이 문자열
  - 할당된 값이 L보다 작은 경우 공백으로 채움
- **VARCHAR2(L)**
  - 가변 길이 문자열
  - L만큼의 최대 길이
  - L보다 작은 경우 해당 값만큼만 공간 차지
- **NUMBER(L, D)**
  - 정수, 실수 저장
  - L: 전체 자리 수
  - D: 소수점 자리 수
- **DATE**
  - 날짜와 시각 정보
  - 년/월/일/시/분/초

### 2.2 CREATE TABLE

#### 작성 규칙

- 테이블명은 단수형 권고
- 테이블명 중복 X
- 한 테이블 안에서 컬럼명 중복 X
- 칼럼은 `,`로 구분
- 테이블 생성문은 `;`로 종료
- 데이터 유형 반드시 지정
- 테이블명과 칼럼명은 반드시 문자로 시작
- 허용 문자: `A-Z`, `a-z`, `0-9`, `_`, `$`, `#`

### 2.3 제약조건

데이터 무결성 유지를 위해 특정 칼럼에 설정하는 제약.

- **PRIMARY KEY**
  - 하나의 테이블에 하나의 기본키
  - 자동으로 UNIQUE 인덱스 생성
  - NULL 불가
- **UNIQUE KEY**
  - 행 데이터를 고유하게 식별
  - NULL 입력 가능
- **NOT NULL**
  - NULL 입력 금지
- **CHECK**
  - 입력 가능한 값의 종류 및 범위 제한
- **FOREIGN KEY**
  - 다른 테이블의 기본키를 외래키로 지정할 때 생성
  - NULL 가능
  - 여러 속성 가능

### 2.4 CREATE 예시

```sql
CREATE TABLE USER_INFO (
    USER_ID CHAR(6) NOT NULL,
    USER_NAME VARCHAR2(10) NOT NULL,
    POSTCODE CHAR(5)
);
```

### 2.5 ALTER

#### ADD COLUMN

```sql
ALTER TABLE USER_INFO
ADD (ADDRESS VARCHAR2(100));
```

#### DROP COLUMN

```sql
ALTER TABLE USER_INFO
DROP COLUMN ADDRESS;
```

#### MODIFY COLUMN

```sql
ALTER TABLE USER_INFO
MODIFY (POSTCODE CHAR(5) DEFAULT '00000' NOT NULL);
```

#### RENAME COLUMN

```sql
ALTER TABLE USER_INFO
RENAME COLUMN POSTCODE TO ZIPCODE;
```

#### DROP CONSTRAINT

```sql
ALTER TABLE USER_INFO
DROP CONSTRAINT 조건명;
```

#### ADD CONSTRAINT

```sql
ALTER TABLE USER_INFO
ADD CONSTRAINT 조건명 조건(칼럼명);
```

### 2.6 TABLE 관련 명령

#### RENAME TABLE

```sql
RENAME USER_INFO TO USER_ADDRESS;
```

#### TRUNCATE TABLE

- 테이블 데이터를 비움
- ROLLBACK 불가능

```sql
TRUNCATE TABLE USER_ADDRESS;
```

#### DROP TABLE

```sql
DROP TABLE USER_ADDRESS;
```

---

## 3. DML

> 원문 정리 기준: DDL 명령어는 AUTO COMMIT, DML 명령어는 COMMIT 입력 필요.

### 3.1 INSERT

```sql
INSERT INTO USER_INFO (USER_ID, USER_NAME, ZIPCODE)
VALUES ('000001', 'MINA', '00000');

COMMIT;
```

### 3.2 UPDATE

```sql
UPDATE USER_INFO
SET ZIPCODE = '12345'
WHERE USER_ID = '000001';

COMMIT;
```

### 3.3 DELETE

```sql
DELETE FROM USER_INFO
WHERE USER_ID = '000001';

COMMIT;
```

### 3.4 SELECT

```sql
SELECT USER_NAME
FROM USER_INFO;
```

- `SELECT DISTINCT`: 중복 제거
- 와일드카드
  - `*`: 모두
  - `%`: 모두
  - `_`: 한 글자

### 3.5 ALIAS

```sql
SELECT USER_NAME AS NAME
FROM USER_INFO;
```

### 3.6 합성연산자

`||`를 이용하여 문자와 문자를 연결.

---

## 4. TCL

### 4.1 트랜잭션

밀접하게 관련되어 분리될 수 없는 1개 이상의 DB 조작을 묶은 논리적 연산 단위.

테이블 데이터 변경 수행 시 `COMMIT`, `ROLLBACK`을 통해 데이터 무결성을 보장.

### 4.2 트랜잭션의 특징

- **원자성(Atomicity)**
  - 정의된 연산들은 모두 성공하거나 모두 실패
  - All or Nothing
- **일관성(Consistency)**
  - 트랜잭션 실행 전 잘못된 내용이 없다면 실행 이후에도 잘못된 내용이 없어야 함
- **고립성(Isolation)**
  - 트랜잭션 실행 중 다른 트랜잭션의 영향을 받아 잘못된 결과를 만들면 안 됨
- **지속성(Durability)**
  - 트랜잭션이 성공적으로 수행되면 갱신한 DB 내용은 영구 저장

### 4.3 COMMIT

입력, 수정, 삭제한 자료에 문제가 없을 경우 트랜잭션 완료.

#### COMMIT 이전

- 복구 가능
- 현재 사용자만 SELECT로 결과 확인 가능
- 변경된 행은 LOCKING 설정
- 다른 사용자가 해당 행 변경 불가

#### COMMIT 이후

- 변경사항 반영
- 모든 사용자가 결과 확인 가능
- LOCKING 해제
- 모든 사용자가 데이터 조작 가능

### 4.4 ROLLBACK

COMMIT 이전의 변경사항을 취소.

- 데이터 변경사항 취소
- 데이터 복구
- LOCKING 해제
- 다른 사용자가 데이터 변경 가능

### 4.5 SAVEPOINT

트랜잭션의 일부만 ROLLBACK 가능.

```sql
SAVEPOINT SVPT1;

-- DML 실행

ROLLBACK TO SVPT1;
```

### 4.6 COMMIT/ROLLBACK과 상관없이 트랜잭션 처리가 일어나는 상황

- DDL 문장을 실행하면 전후 시점에 자동 COMMIT
- 데이터베이스를 정상적으로 접속 종료하면 자동 COMMIT
- 애플리케이션 이상 종료로 접속 단절 시 트랜잭션 자동 ROLLBACK

---

## 5. WHERE 절

### 5.1 개요

- `FROM` 절 다음에 위치
- 구성 요소
  - 칼럼명
  - 비교 연산자
  - 문자/숫자/표현식
  - 비교 칼럼명(JOIN 사용 시)

### 5.2 연산자

#### 연산자 우선순위

```text
() → NOT → 비교/SQL 비교 연산자 → AND → OR
```

#### 비교연산자

- `=`
- `>`
- `>=`
- `<`
- `<=`

#### SQL 비교연산자

- `BETWEEN A AND B`
- `IN (LIST)`
- `LIKE '비교문자열'`
- `IS NULL`

> NULL은 `=`로 비교하지 않음.

#### 논리연산자

- `AND`
- `OR`
- `NOT`

#### 와일드카드

- `%`: 0개 이상의 문자
- `_`: 1개의 단일 문자

#### 부정 비교연산자

- `!=`
- `<>`
- `^=`
- `NOT 칼럼명 = ...`
- `NOT 칼럼명 > ...`

### 5.3 문자유형 비교방법

#### 양쪽이 CHAR

- 길이가 다르면 작은 쪽에 공백을 추가하여 길이를 같게 함
- 서로 다른 문자가 나올 때까지 비교
- 처음 달라진 값에 따라 크기 결정
- 공백의 수만 다르다면 같은 값으로 결정

#### 한쪽이 VARCHAR

- 서로 다른 문자가 나올 때까지 비교
- 공백도 문자로 판단
- 길이가 다르면 짧은 문자열이 끝날 때까지 비교 후 긴 문자열이 크다고 판단
- 길이가 같고 다른 것이 없다면 같다고 판단
- `TRIM` 함수로 공백 제거 후 비교 가능

#### 상수값과 비교

상수 쪽의 타입을 CHAR/VARCHAR로 맞추어 적용.

---

## 6. 함수

### 6.1 단일행 함수

- `SELECT`, `WHERE`, `ORDER BY` 절에서 사용 가능
- 행에 개별적으로 조작
- 여러 인자를 입력해도 하나의 결과만 반환
- 함수 인자에 상수, 변수, 표현식 사용 가능
- 함수 중첩 가능

### 6.2 문자형 함수

문자 → 문자/숫자

- `LOWER(str)`: 소문자로 변환
- `UPPER(str)`: 대문자로 변환
- `ASCII('a')`: 아스키 값 반환
- `CHR('ascii')`: ASCII 값에 해당하는 문자 출력
- `CONCAT(A, B)`: A와 B 연결
- `SUBSTR(str, m, n)`: 문자열 슬라이싱
- `LENGHT(str)`: 문자열 길이 반환
- `TRIN(str)`, `RTRIM(str)`, `LTRIM(str)`: 공백 제거

### 6.3 숫자형 함수

숫자 → 숫자

- `ABS()`: 절대값
- `SIGN()`: 양수 → 1, 0 → 0, 음수 → -1
- `MOD(A, B)`: A/B의 나머지
- `CEIL()`: 올림
- `FLOOR()`: 내림
- `ROUND(num, m)`: m자리에서 반올림
- `TRUNC(num, m)`: m자리에서 자름

### 6.4 날짜형 함수

- `SYSDATE`: 현재 날짜와 시각 출력
- `EXTRACT`: 날짜에서 데이터 출력
  - `YEAR FROM d`
  - `MONTH FROM d`
  - `DAY FROM d`
- `TO_NUMBER(TO_CHAR(d, 'YYYY'))`: 연도를 숫자로 출력
- 날짜 형식 예: `YYYY-MM-DD HH:MI:SS`
- 날짜 연산에서 `1 = 하루`

### 6.5 변환형 함수

- **암시적 형변환**
  - DBMS가 자동으로 데이터 유형 변환
- **명시적 형변환**
  - 데이터 변환 함수로 데이터 유형을 명시적으로 변환

### 6.6 단일행 CASE 표현

```sql
CASE
    WHEN 조건 THEN 값
    ELSE 값
END;
```

```sql
DECODE(칼럼명, 조건1, 값1, 조건2, 값2, 디폴트값)
```

### 6.7 NULL 관련 함수

- `NVL(A, B)`
  - A가 NULL이면 B 출력
  - NULL값을 대상으로 하며 공집합은 대상이 아님
- `NULLIF(A, B)`
  - A와 B가 같으면 NULL
  - 다르면 A
- `COALESCE(A, B, ...)`
  - NULL이 아닌 최초의 표현식 반환
  - 모두 NULL이면 NULL 반환

---

## 7. GROUP BY / HAVING

### 7.1 다중행 집계 함수

- 여러 행들의 그룹이 모여 그룹당 하나의 결과 반환
- `GROUP BY` 절은 행들을 소그룹화
- `SELECT`, `HAVING`, `ORDER BY` 절에 사용 가능
- `ALL`: 기본 옵션
- `DISTINCT`: 중복 제거

### 7.2 집계함수 종류

- `COUNT(*)`
  - NULL 포함 행의 수
- `COUNT(표현식)`
  - 표현식 값이 NULL이 아닌 행의 수
- `SUM`
- `AVG`
- `MAX`
- `MIN`
- `STDDEV`
- `VARIAN`

> 집계 함수는 NULL을 제외하여 계산.

### 7.3 GROUP BY 특징

- 소그룹별 기준을 정한 뒤 `SELECT` 절에 집계 함수 사용
- 집계 함수의 통계 정보는 NULL값을 가진 행을 제외하고 수행
- ALIAS 사용 불가
- 그룹을 나누기 전에 `WHERE` 절이 행을 미리 제거
- `WHERE` 절에서는 집계 함수 사용 X
- `HAVING` 절은 일반적으로 `GROUP BY` 뒤에 위치
- `HAVING`에서 기준 항목이나 소그룹 집계함수를 이용한 조건 설정 가능

---

## 8. ORDER BY

### 8.1 특징

- 조회된 데이터를 특정 칼럼 기준으로 정렬하여 출력
- ALIAS명 사용 가능
- 칼럼 순서를 나타내는 정수 사용 가능
- 기본값: `ASC`
- `DESC`: 내림차순
- SQL 문장의 가장 마지막에 위치
- `SELECT` 절에서 정의하지 않은 칼럼 사용 가능
- Oracle에서는 NULL이 가장 큰 값
- SQL Server에서는 NULL이 가장 작은 값

### 8.2 SELECT 문장 실행 순서

원문 정리 순서:

```text
SELECT ALIAS → FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY
```

### 8.3 SQL Server - WITH TIES

`WITH TIES` 조건을 추가하면 `TOP(2)`에서 공동 2위까지 모두 출력.

---

## 9. JOIN

### 9.1 JOIN

두 개 이상의 테이블을 연결 또는 결합하여 데이터를 출력.

- 일반적으로 PK/FK 값의 연관으로 JOIN
- PK/FK 관계가 없어도 논리적인 값의 연관만으로 JOIN 가능

### 9.2 EQUI JOIN

2개의 테이블 간 칼럼 값이 정확하게 일치하는 경우 사용.

대부분 PK/FK 관계 기반.

```sql
WHERE PLAYER.TEAM_ID = TEAM.TEAM_ID
```

```sql
FROM PLAYER
INNER JOIN TEAM
ON PLAYER.TEAM_ID = TEAM.TEAM_ID
```

- INNER JOIN에 참여하는 대상 테이블이 N개일 때 필요한 JOIN 조건은 `N-1개`

### 9.3 NON EQUI JOIN

칼럼 값이 정확하게 일치하지 않아 `BETWEEN`, `<` 등의 연산자 사용.

```sql
WHERE E.SAL BETWEEN S.LOSAL AND S.HSAL;
```
