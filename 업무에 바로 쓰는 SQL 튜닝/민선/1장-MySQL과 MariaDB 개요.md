# 1장 MySQL과 MariaDB 개요

## 1. MySQL & MariaDB

### MySQL
- 오픈소스 DBMS
- 장점
    - 대용량 데이터
    - 가용성
    - 안정성
- 2010년 Oracle이 인수한 후 상용 버전과 커뮤니티 버전으로 구분

### MariaDB
- 오픈소스 정책을 지향
- MySQL의 소스코드에 기반을 두고 개발
- SQL 문의 주요 뼈대는 크게 다르지 않음


#### 버전 측면
- 5.5 버전부터 MySQL 5.5와 MariaDB 10.0 으로 나뉨
- SQL 문을 개발하는 입장에서 버전에 따른 기능 차이가 존재
- 하지만, `작성하는 SQL 문법이나 실행 계획의 출력 방식은 유사` -> 버전과 관계없이 SQL문 개발 가능
- 단, 버전에 따라 `DB 엔진 레벨에서 제공하는 옵티마이저 기능의 차이는 존재`

<br>

```sql
# 버전확인
show variables like 'version';

SELECT @@Version;
```

<br>

## 1.1 현황

### 부각 배경
- **MySQL**
    - 상용 버전 (commercial edition)
        - 오라클에서 다양한 보안 패치와 개선된 기능 제공
    - 무료 버전 (community edition)
        - 라이센스: `GPL` (General Public License)
        - 제약된 기능과 서비스만 사용 가능

- **MariaDB**
    - 라이센스: `GPL v2`
    - 완전한 오픈소스 소프트웨어

MySQL, MariaDB 모두 웹 서비스 및 모바일 웹 시스템을 구축하고자 할 때 사용 가능한 `오픈소스 데이터베이스`
누구나 SQL 작성 및 튜닝 가능


### 영향력
- 오픈소스 데이터베이스로의 전환(Migration) 추세
    - 비즈니스 가격 경쟁력 향상
    - 클라우스 서비스의 급성장
- MySQL, MariaDB 둘이 합쳐 전체 시장의 `60%`의 비율을 차지

<br>

## 1.2 상용 RDBMS와의 차이점
- 오픈소스 MySQL 및 MariaDB(이하 MySQL 통칭) vs 상용 DB 오라클

### 1.2.1 구조적 차이
- 두 DBMS 모두 실제 서비스에 도입할 때는 장애 예방 효과 및 장애 발생시 가용성을 기대하며 `이중화 구조`로 구축

<br>

- 데이터가 저장되는 스토리지의 구조 측면
    - **Oracle**
        - `Shared everything`
        - `통합된 스토리지 하나를 공유`하여 사용하는 방식으로 구성
            - 어느 DB 서버에 접속하여 SQL 문을 수행하더라도 같은 결과를 출력
            - 동일한 구문(SELECT, INSERT, DELETE, UPDATE) 처리
    - **MySQL**
        - `Shared nothing`
        - `물리적인 DB 서버마다 독립적으로 스토리지를 할당`하여 구성
            - 이중화를 위한 클러스터나 복제 구성으로 운영
            - 보통 `Master-Slave 구조`
                - Master: 읽기/쓰기 모두 가능
                - Slave: 읽기만 가능
            - 즉, 동일한 구문(SELECT, INSERT, DELETE, UPDATE)이 처리되지 않을 수 있음
            - DB마다 각자의 역할이 부여될 수 있음

        -> 쿼리문이 수행하는 서버의 위치를 파악하고 튜닝을 진행하면, 물리적인 위치 특성이 내포된 쿼리 튜닝 수행 가능
    
    <br>

    - `쿼리 오프로딩 (Query offloading)`
        - DB 서버의 트랜잭션에서 `write 와 read를 분리하여 DB 처리량을 증가`시키는 성능 향상 기법
        - Read Transaction
            - SELECT
        - Write Transaction
            - UPDATE, INSERT, DELETE

### 1.2.2 지원 기능 차이
- `조인 알고리즘 (Join Algorithm)` 기능의 차이
    - **Oracle**
        - 중접 루프 조인 방식 (nested loop join)
        - 정렬 병합 조인 (sort merge join)
        - 해시 조인 (hash join)
    - **MySQL**
        - 중첩 루프 조인 (nested loop join)

<br>

- MySQL (Oracle 대비)
    - 확장성
        - 데이터를 저장하는 `스토리지 엔진`이라는 개념을 포함
        - 오픈소스 DBMS를 바로 꽂아서 사용 가능
            - DBMS의 `플러그 앤 플레이 (plug & play)`
    - 저사양 환경
        - 오라클 대비 메모리 사용률이 상대적으로 낮음
        - 사양이 낮은 컴퓨팅 환경에서도 설치 및 서비스 가능

<br>

### 1.2.3 SQL 구문의 차이

##### 1. Null 값 대체하기
- 해당 열에 `Null이 포함되는 경우 다른 값으로 대체`할 때

**MySQL/MariaDB**

```sql
# 문법
IFNULL(열명, '대체값')

# 예제
SELECT IFNULL(Col1, 'N/A') AS coll FROM tab;
```

**Oracle**

```sql
# 문법
NVL(열명, '대체값')

# 예제
SELECT NVL(Col1, 'N/A') AS coll FROM tab;
```

---

##### 2. 페이징 처리
- 테이블에서 데이터를 불러올 경우 `전체가 아닌 일부 분량만 제한적으로 가져오는 경우`
- 페이징 처리 뿐만 아니라 새 일련번호를 받는 순번을 생성할 때도 응용하여 사용 가능

**MySQL/MariaDB**

```sql
# 문법
LIMIT 숫자

# 예제
SELECT col1 FROM tab LIMIT 5;
```

**Oracle**

```sql
# 문법
ROWNUM < 숫자

# 예제
SELECT col1 FROM tab WHERE ROWNUM <= 5;
```

--- 

##### 3. 현재 날짜 조회
- `DBMS의 현재 시스템 날짜를 조회`
- MySQL은 FROM 절 없이 SELECT 문만으로도 현재 날짜 출력 가능
- Oracle은 가상 테이블을 명시해야 현재 날짜 출력 가능

**MySQL/MariaDB**

```sql
# 문법
NOW()

# 예제
SELECT NOW() AS date;
```

**Oracle**

```sql
# 문법
SYSDATE

# 예제
SELECT SYSDATE AS date FROM dual;
```

---

##### 4. 조건문
- `특정한 조건을 만족할 때와 만족하지 않을 때 각각 수행할 방향`을 정해주는 구문

**MySQL/MariaDB**

```sql
# 문법
IF(조건식, '참값', '거짓값')

# 예제
SELECT IF(col1='A', 'apple', '-') AS coll FROM tab;
```

**Oracle**

```sql
# 문법
DECODE(열명, '값', '참값', '거짓값')

# 예제
SELECT DECODE(col1, 'A', 'apple', '-') AS coll FROM tab;
```

---

##### 5. 날짜 형식 변환
- `날짜 데이터를 원하는 형태로 변경`하는 구문 작성

**MySQL/MariaDB**

```sql
# 문법
DATE_FORMAT(날짜열, '형식')

# 예제
SELECT DATE_FORMAT(NOW(),'%Y%m%d %H%i%s') AS date;
```

**Oracle**

```sql
# 문법
TO_CHAR(날짜열, '형식')

# 예제
SELECT TO_CHAR(SYSDATE, 'YYYYMMDD HH24MISS') AS date FROM dual;
```

---

##### 6. 자동 증가 값
- 신규 데이터가 지속적으로 생성되는 경우, `증가하는 순번을 자동으로 매기는 숫자형 값`
- 이미 저장된 다른 데이터와 값이 중복되지 않도록 기존에 저장된 순번보다 더 큰 숫자를 생성하여 데이터를 저장하는 방식
- `MySQL - Sequence / Oracle - object` 활용

<br>

- MySQL의 자동 증가 값 저장 2가지 방법
    - [1] 특정 열의 속성으로 자동 증가하는 값을 설정하는 auto_increment 명시
        - 즉, 테이블마다 특정한 하나의 열에만 해당 속성을 정의하여 자동 증가하는 숫자를 저장
    - [2] 오라클과 마찬가디로 시퀀스라는 오브젝트를 생성한 뒤 호출하여 활용하는 방법
        - CREATE SEQUENCE 문으로 시퀀스 생성한 뒤 SELECT nextval(시퀀스 명); 구문으로 신규 순번을 매기는 기능 활용


**MySQL/MariaDB**

```sql
# 문법
AUTO_INCREMENT

# 예제
CREATE TABLE tab (
  seq INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(20) NOT NULL
);

# 문법 (MariaDB 10.3 이상)
CREATE SEQUENCE [시퀀스명] INCREMENT BY [증감숫자] START WITH [시작숫자] NOMINVALUE OR MINVALUE [최솟값] NOMAXVALUE OR MAXVALUE [최댓값] CYCLE OR NOCYCLE CACHE OR NOCACHE;

# 예제
CREATE SEQUENCE MARIA_SEQ_SAMPLE INCREMENT BY 1 START WITH 1 MINVALUE 1 MAXVALUE 99999999999 CYCLE CACHE;

# 문법
SELECT NEXTVAL(시퀀스 명);

# 예제
SELECT NEXTVAL(MARIA_SEQ_SAMPLE);

```

**Oracle**

```sql
# 문법
CREATE SEQUENCE [시퀀스명] INCREMENT BY [증감숫자] START WITH [시작숫자] NOMINVALUE OR MINVALUE [최솟값] NOMAXVALUE OR MAXVALUE [최댓값] CYCLE OR NOCYCLE CACHE OR NOCACHE;

# 예제
CREATE SEQUENCE ORACLE_SEQ_SAMPLE INCREMENT BY 1 START WITH 1 MINVALUE 1 MAXVALUE 99999999999 CYCLE CACHE;

# 문법
SELECT 시퀀스명.NEXTVAL FROM dual;

# 예제
SELECT ORACLE_SEQ_SAMPLE.NEXTVAL FROM dual;

```

---

##### 7. 문자열 결합
- `여러 개의 문자를 하나로 결합`하여 조회

**MySQL/MariaDB**

```sql
# 문법
CONCAT(열값 또는 문자열, 열값 또는 문자열)

# 예제
SELECT CONCAT('A', 'B') AS TEXT;
```

**Oracle**

```sql
# 문법
열값 또는 문자열 || 열값 또는 문자열
CONCAT(열값 또는 문자열, 열값 또는 문자열)

# 예제
SELECT 'A' || 'B' AS TEXT FROM dual;
SELECT CONCAT('A', 'B') AS TEXT FROM dual;
```

---

##### 8. 문자열 추출
- `문자열에서 특정 구간 및 특정 위치의 문자열을 추출`하는 경우

**MySQL/MariaDB**

```sql
# 문법
SUBSTRING(열값 또는 문자열, 시작 위치, 추출하려는 문자 개수)

# 예제
SELECT SUBSTRING('ABCDE', 2, 3) AS sub_string;
```

**Oracle**

```sql
# 문법
SUBSTR(열값 또는 문자열, 시작 위치, 추출하려는 문자 개수)

# 예제
SELECT SUBSTR('ABCDE', 2, 3) AS sub_string FROM dual;
```

<br>

### 1.3 MySQL과 MariaDB 튜닝의 중요성
- 최근 MySQL과 MariaDB에 기반을 둔 안정적인 운영 서비스 사례가 흔함
- 나아가 클라우드 환경과의 접목으로 다양한 방식의 안정적인 아키텍처를 서비스에 활용
- 이러한 사례들을 보면 클라우드 서비스에 기반을 둔 하드웨어 가용상과 확장성, 지속적인 DBMS 엔진 가동을 위한 소프트웨어 안정성 등이 상용 DBMS와 크게 다르지 않음

<br>

- 하지만, `MySQL과 MariaDB 내부를 보면 기능적인 제약사항`이 존재
    - 대다수의 SQL 문이 `중첩 루프 조인 알고리즘`으로 수행
    - 상용 DBMS와는 다르게 수행된 쿼리 결과가 메모리에 적재되는 `캐시 기능에 한계`가 존재
        데이터가 변경되면 캐시된 내용 모두 삭제되어 일반적인 쿼리 작성 및 튜닝이 통하지 않을 수 있음

- `즉, DBMS가 제공하는기능을 더 자세히 알아야함`
    - 제공되는 실행 계획 (Execution plan) 정보를 해석하고 문제점을 인지하거나 대응할 수 있는 능력을 갖춘 뒤 쿼리 튜닝 진행

##### SWOT
- Strength
    - 무료
    - 경량화된 소프트웨어이므로 활용하기 편리하고 유용함
- Weakness
    - 수행 가능한 알고리즘이 적어 성능적으로 불리
- Opportunity
    - 다양한 스토리지 엔진을 적극적으로 사용할 수 있는 기회
- Threat
    - 오라클에 인수된 MySQL은 라이선스의 유료화
    - 오픈소스 정책의 지원범위 변동에 대한 위협성 존재

<br>

### 1.4 마치며
- MySQL과 MariaDB 탄생 배경
- 세계적으로 상당한 수준의 점유율 차지
- Oracle과의 기본적인 차이점 및 상대적으로 부족한 기능과 정보, 알고리즘에 따른 쿼리 튜닝의 중요성 인지