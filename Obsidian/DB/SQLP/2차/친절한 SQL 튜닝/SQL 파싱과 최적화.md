### **SQL의 특징**

- 구조적
- 집합적
- 선언적

SQL(Structed Query Language), ****말 그대로 **구조적** 질의 언어이며, 위키 피디아에 검색해보면 다음과 같은 정의 도 있다.

- SQL is designed for a specific purpose: to query data contained in a relational database.
- SQL is a **set-based(집합적)**, **declarative(선언적)** query language, not an imperative language such as C or BASIC.

오라클 PL/SQL, SQL Server T-SQL러럼 절차적(procedural)프로그래밍 기능을 구현할 수 있는 확장 언어도 제공하지만, SQL은 기본적으로 **구조적(structured)**이고 **집합적(set-based)**이고 **선언적(declarative)**인 질의 언어이다.

### SQL 최적화

DBMS 내부에서 프로시저를 작성하고 컴파일해서 실행 가능한 상태로 만드는 전 과정을 **SQL 최적화**라고 한다.

**SQL을 실행하기 전 최적화 과정**

- SQL 파싱
    - 사용자로 부터 SQL을 전달받으면 가장 먼저 **SQL 파서(Parser)**가 파싱을 진행한다.
        
        **SQL 파싱 과정**
        
        - 파싱 트리 생성 : SQL 문을 이루는 개별 구성요소를 분석해서 파싱 트리 생성
        - Syntax 체크 : 문법적 오류가 없는지 확인(사용 불가 키워드, 올바르지 않은 순서, 누락된 키워드 확인)
        - Semantic 체크 : 의미상 오류가 없는지 확인(존재하지 않는 테이블이나 컬럼의 사용, 사용한 오브젝트에 대한 권한 유무 확인)
- SQL 최적화
    - **옵티마이저**가 미리 수집한 시스템 및 오브젝트 통계정보를 바탕으로 다양한 실행 경로를 생성해서 비교한 후 **가장 효율적인** 하나를 선택한다.
    - 데이터베이스 성능을 결정하는 가장 핵심적인 엔진.
- 로우 소스 생성
    - SQL 옵티마이저가 선택한 실행경로를 **로우 소스 생성기(Row-Source Generator)**가 실제 실행 가능한 코드 또는 프로시저 형태로 포맷팅하는 단계.

### **SQL 옵티마이저**

- 원하는 결과집합을 구조적, 집합적으로 선언하지만, 그 결과집합을 만드는 과정은 절차적일 수밖에 없다. 즉, 프로시저가 필요한데, 그런 프로시저를 만들어 내는 DBMS 내부 엔진이 **SQL 옵티마이저**이다. SQL 옵티마이저가 프로그래밍을 대신해 주는 셈.
    
    ```
              **사용자 -(SQL)→ 옵티마이저 -(실행계획)→ 프로시저**
    ```
    
- 사용자가 원하는 작업을 가장 효율적으로 수행할 수 있는 최적의 데이터 액세스 경로를 선택해 주는 DBMS의 핵심 엔진.
    

**옵티마이저의 최적화 단계**

- 사용자로부터 전달받은 쿼리를 수행하는 데 후보군이 될만한 실행계획들을 찾아낸다.
- 데이터 딕셔너리(Data Dictionary)에 미리 수집해 둔 오브젝트 통계 및 시스템 통계정보를 이용해 각 실행계획의 예상비용을 산정한다.
- 최저 비용을 나타내는 실행계획을 선택한다.

### 실행계획과 비용

DBMS에는 SQL 실행경로 미리보기 기능이 있는데, 바로 실행계획(Execution Plan)이다.

실행계획은 SQL 옵티마이저가 생성한 처리절차를 사용자가 확인할 수 있게 트리 구조로 표현한 것이다.

**실행계획 확인하는 방법**

```sql
// 테이블의 통계정보 수집
exec dbms_stats.gather_table_stats(user, 't');

// 실행 정보 출력
set autotrace traceonly exp;
// 쿼리
```

### 옵티마이저 힌트

SQL 옵티마이저가 대부분은 좋은 선택을 하지만, 완벽하진 않다. SQL이 복잡할수록 실수할 가능성도 크다.

개발자가 직접 더 효율적인 액세스 경로를 지정해 줄 수 있는데, 이 때 사용하는 것이 **옵티마이저 힌트**이다.

- 힌트의 사용
    
    ```sql
    select /*+ //힌트 */ *
    from 고객
    where 고객ID = '123456789';
    ```
    
- 주의 사항
    
    - 힌트 안에 인자를 나열할 땐 ,(콤마)를 사용할 수 있지만, 힌트를 나열할 때는 첫 번째 힌트만 적용된다.
    - 테이블을 지정할 때 스키마명은 표시하지 않는다.
    - From절에서 Alias를 사용했다면 힌트에서도 Alias를 사용해야한다.

**자주 쓰는** **힌트 목록**

| 분류        | 힌트               | 설명                                                    |
| --------- | ---------------- | ----------------------------------------------------- |
| 최적화 목표    | ALL_ROWS         | 전체 처리속도 최적화                                           |
|           | FIRST_ROWS(N)    | 최초 N건 응답속도 최적화                                        |
| 액세스 방식    | FULL             | Table Full Scan으로 유도                                  |
|           | INDEX            | Index Scan으로 유도                                       |
|           | INDEX_DESC       | Index를 역순으로 스캔하도록 유도                                  |
|           | INDEX_FFS        | Index Fast Full Scan으로 유도                             |
|           | INDEX_SS         | Index Skip Scan으로 유도                                  |
| 조인 순서     | ORDERED          | FROM 절에 나열된 순서대로 조인                                   |
|           | LEADING          | LEADING 힌트 괄호에 기술한 순서대로 조인                            |
|           | SWAP_JOIN_INPUTS | 해시 조인 시, BUILD INPUT을 명시적으로 선택                        |
| 조인 방식     | USE_NL           | NL 조인으로 유도                                            |
|           | USE_MERGE        | 소트 머지 조인으로 유도                                         |
|           | USE_HASH         | 해시 조인으로 유도                                            |
|           | NL_SJ            | NL 세미조인으로 유도                                          |
|           | MERGE_SJ         | 소트머지 세미조인으로 유도                                        |
|           | HASH_SJ          | 해시 세미조인으로 유도                                          |
| 서브 쿼리 팩토링 | MATERIALIZE      | WITH 문으로 정의한 집합을 물리적으로 생성하도록 유도                       |
|           | INLINE           | WITH 문으로 정의한 집합을 물리적으로 생성하지 않고 INLINE 처리하도록 유도        |
| 쿼리 변환     | MERGE            | 뷰 머징 유도                                               |
|           | NO_MERGE         | 뷰 머징 방지                                               |
|           | UNNEST           | 서브쿼리 Unnesting 유도(서브쿼리 풀어서 조인으로 변환)                   |
|           | NO_UNNEST        | 서브쿼리 Unnesting 방지                                     |
|           | PUSH_PRED        | 조인조건 Pushdown 유도                                      |
|           | NO_PUSH_PRED     | 조인조건 Pushdown 방지                                      |
|           | USE_CONCAT       | OR 또는 IN-List 조건을 OR-Expansion으로 유도                   |
|           | NO_EXPAND        | OR 또는 IN-List 조건에 대한 OR-Expansion 방지                  |
| 병렬 처리     | PARALLEL         | 테이블 스캔 또는 DML을 병렬 방식으로 처리하도록 유도                       |
|           | PARALLEL_INDEX   | 인덱스 스캔을 병렬 방식으로 처리하도록 유도                              |
|           | PQ_DISTRIBUTE    | 병렬 수행 시 데이터 분배 방식 결정                                  |
| 기타        | APPEND           | Direct-Path Insert로 유도                                |
|           | DRIVING_SITE     | DB Link Remote 쿼리에 대한 최적화 및 실행 주체 지정(Local 또는 Remote) |
|           | PUSH_SUBQ        | 서브쿼리를 가급적 빨리 필터링하도록 유도                                |
|           | NO_PUSH_SUBQ     | 서브쿼리를 가급적 늦게 필터링하도록 유도                                |