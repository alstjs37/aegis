# 0. 탄생 과정
### MySQL 
1995년 오픈소스로 배포된 무료 DBMS
+ 대용량 데이터
+ 가용성
+ 안정성

-> 학계 및 업계에서 다양한 용도로 활용
상용 버전, 커뮤니티 버전  

### MariaDB
2010년 오라클에 인수된 뒤 오픈소스 정책을 지향하는 MariaDB 탄생
5.5 버전부터 독자적으로 발전됨

개발자 입장에서 버전에 따른 기능 차이는 있지만 SQL문의 문법, 출력 방식은 유사
(단, 버전에 따라 옵티마이저 기능의 차이 존재)
설치된 DB 버전에 따라 최적화 기능을 선택적으로 활용 가능하므로 버전 정보 파악 권장

# 1. 현황
### MySQL
= 상용 버전 + 무료 버전
무료 버전 라이선스인 GPL은 소스 코드 공개의 부담과 기능,서비스 사용의 제약 존재

### MariaDB
GPL v2 라이선스를 따르는 완전한 오픈소스 SW
(영리 목적 활동 외에는 공개 의무 X)

### DB 엔진 영향력
오픈소스 RDBMS의 영향력 비율 중 58% 차지

# 1.2 상용 RDBMS와의 차이점
### 구조적 차이
- 오라클 DB : 통합된 스토리지 하나를 공유
- MySQL : 물리적인 DB 서버마다 독립적인 스토리지 할당

  -> 이중화를 위한 클러스터나 복세 구성으로 운영하더라도 master - slave 구조가 대부분

  -> master : 읽기,쓰기 모두 가능

  -> slave : 읽기만 가능

  : 여러 대의 MySQL DB에 접속하더라도 동일한 구문이 처리되지 않고 서버마다 각자의 역할이 부여될 수 있음
### 지원 기능 차이
1. Join 알고리즘
   - 오라클
     정렬 병합 조인 + 해시 조인 방식
   - MySQL
     중첩 루프 조인 방식 (최근 효율을 위해 제약적으로 해시 조인 제공)
2. 확장성
   스토리지 엔진 : 필요한 DBMS를 설정해 사용 가능(DBMS의 plug&play) => **높은 확장성**
3. 낮은 메모리 사용
   오라클은 최소 수백 mb
   MySQL은 1mb 메모리 환경에서도 활용 가능(오버헤드 작음)
### SQL 구문 차이
1. NULL 대체
   - MySQL/MariaDB

     ``` IFNULL(열명, '대쳇값')```
   - Oracle

     ``` NVL(열명 , '대쳇값') ```
2. 페이징 처리
   일부 분량만 제한적으로 가져오는 경우 사용
   - MySQL/MariaDB

     ``` LIMIT 숫자 ```
   - Oracle

     ``` ROWNUM <= 숫자 ```
     
3. 현재 날짜
   - MySQL/MariaDB
    
      ``` NOW() ```
   - Oracle
   
     ``` SYSDATE ```
     
4. 조건문
   - MySQL/MariaDB
   
      ``` IF (조건식, '참값', '거짓값') ```
   - Oracle

      ``` DECODE (열명, '값', '참값', '거짓값') ```

5. 날짜 형식
   - MySQL/MariaDB
   
      ``` DATE_FORMAT(날짜열, '형식') ```
   - Oracle

      ``` TO_CHAR(날짜열, '형식') ```

6. 자동 증갓값
   - MySQL/MariaDB
   
     ```
      AUTO_INCREMENT 
      예제)
      CREATE TABLE tab
      (seq INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
       title VARCHAR(20) NOT NULL); 
      // 사용
      CREATE SEQUENCE [시퀀스명]
     INCREMENT BY [증감숫자]
     START WITH [시작숫자]
     NOMINVALUE OR MINVALUE [최솟값]
     NOMAXVALUE OR MAXVALUE [최댓값]
     CYCLE OR NOCYCLE
     CACHE OR NOCACHE
      // 다음 값 채번
      SELECT NEXTEVAL(시퀀스명); 
     ```
  
  - Oracle
       ```
          CREATE SEQUENCE [시퀀스명]
          INCREMENT BY [증감숫자]
          START WITH [시작숫자]
          NOMINVALUE OR MINVALUE [최솟값]
          NOMAXVALUE OR MAXVALUE [최댓값]
          CYCLE OR NOCYCLE
          CACHE OR NOCACHE
          // 다음 값 채번
          SELECT 시퀀스명.NEXTVAL FROM dual;
       ```
     
  7. 문자 결합
     - MySQL/MariaDB
    
       ``` CONCAT(열값 또는 문자열, 열값 또는 문자열) ```
    
     - Oracle
        ```
        //1) 열값 또는 문자열 || 열값 또는 문자열
        // 2) CONCAT(열값 또는 문자열, 열값 또는 문자열)
        ```

  8. 문자 추출
     - MySQL/MariaDB

       ``` SUBSTRING(열값 또는 문자열, 시작 위치, 추출하려는 문자 개수) ```
     - Oracle

       ``` SUBSTR(열값 또는 문자열, 시작 위치, 추출하려는 문자 개수) ```

# 1.3 MySQL과 MariaDB 튜닝의 중요성
클라우드 서비스에 기반을 둔 하드웨어 가용성과 확장성, 지속적인 DBMS 엔진 가동을 위한 소프트웨어 안정성 등이 상용 DBMS와 크게 다르지 않음
그러나, MySQL과 MariaDB의 내부를 들여다보면 기능상 제약사항 존재

-> 대다수의 SQL문이 중첩 루프 조인 알고리즘으로 수행

-> 상용 DBMS와 다르게 수행된 쿼리 결과가 메모리에 적재되는 캐시 기능에 한계 존재

=> DBMS가 제공하는 기능을 더 자세히 알고 실행 계획(execution plan) 정보를 해석하고 문제점 인지 및 대응 능력 향상 필요 