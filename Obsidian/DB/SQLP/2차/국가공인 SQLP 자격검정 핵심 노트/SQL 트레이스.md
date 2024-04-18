오라클에서 SQL 트레이스를 확인하려면 다음 명령어를 실행해야한다.

```sql
alter session set sql_trace = true;
/* 쿼리 */
```

### TKProf

SQL 트레이스 파일을 분석해서 리포트 파일을 생성해 준다.

유닉스 쉘이나 도스 프롬프트에서 tkprof를 실행하면 사용법을 확인할 수 있다.

```bash
$ tkprof
Usage: tkprof tracefile outputfile [explain= ] [table= ] [print= ] [insert= ] [sys= ] [sort= ] ...
```

가장 일반적인 사용법

```bash
// 첫 번째 인자는 트레이스 파일 명이다.
// 두 번째 인자는 결과 파일명이며 확장자는 자유다.
// 마지막 옵션은 SQL을 파싱하는 과정에서 내부적으로 수행되는 SQL문장을 제외시킨다.
$ tkprof ora10g_ora_14370_oraking.trc report.prf sys=no
```

### 오라클 SQL 트레이스

- call : 커서 상태에 따라 Parse, Execute, Fetch 세 개의 Call로 나누어 각각에 대한 통계정보를 보여줌.
    - Parse : 커서를 파싱하고 실행계획을 생성하는 것에 대한 통계.
    - Execute : 커서의 실행 단계에 대한 통계.
    - Fetch : 레코드를 실제로 Fetch하는 것에 대한 통계.
- count : Parse, Execute, Fetch 각 단계가 수행된 횟수.
- cpu : 현재 커서가 각 단계에서 사용한 cpu time이다.
- elapsed : 현재 커서가 각 단계를 수행하는데 소요된 시간이다(Call 단위로 측정).
    - Elapsed Time = CPU Time + Wait Time = Response 시점 - Call 시점
- disk : 디스크로부터 읽은 블록 수
- query : Consistent 모드에서 읽은 버퍼 블록 수
- current : Current 모드에서 읽은 버퍼 블록
- rows : 각 단계에서 읽거나 갱신한 처리건
- Misses in library cache during parse : 하드 파싱 횟수
- Optimizer mode : Access 경로를 추출하는 방법(RULE, CHOOSE, FIRST_ROWS, ALL_ROWS)
- Parsing user id : 파싱하는 동안의 사용자 ID

**AutoTrace와 비교**

- db block gets = current
- consistent gets = query
- physical reads = disk
- SQL*Net roundtrips to/from client = fetch count
- rows processed = fetch rows

**오라클 SGA 메모리에 기록한 SQL 트레이스 정보를 출력**

SQL에 gather_plan_statistics 힌트를 지정하면, SQL 트레이스 정보를 서버 파일이 아닌 SGA 메모리에 기록한다.

SGA 메모리에 저장된 트레이스 정보를 V$SQL_PLAN_STATISTICS_ALL 뷰에서 직접 조회할 수도 있지만, dbms_xplan.display_cursor 함수를 이용하면 분석하기 쉬운 형태로 포매팅해 준다.

- 첫 번째 인자에 SQL 커서의 ID, 두 번째 인자에는 CHILD_NUMBER를 입력해야한다. 이 정보는 SQL 문장의 앞쪽 일부 문자열로 V$SQL 뷰의 SQL_TEXT 컬럼을 조회해서 얻은 SQL_ID와 CHILD_NUMBER 컬럼 값에서 얻는다.
- 첫 번째와 두 번째 인자에 null을 입력하면 바로 직전에 수행한 SQL의 커서 ID와 CHILD_NUMBER를 내부에서 자동으로 선택한다.
- 이 함수를 사용하기 전에 다음과 같은 것들을 처리해야 한다.
    - SQL에 gather_plan_statistics 힌트 사용
    - statistics_level 파라미터를 all로 설정
    - _rowsource_execution_statistics 파라미터를 true로 설정
- 추출 가능한 정보는 다음과 같다. (괄호 안의 내용은 SQL 트레이스의 정보)
    - Starts
        - 각 오퍼레이션 단계별 실행 횟수
    - E-Rows
        - 옵티마이저가 예상한 Rows
    - A-Rows(= rows)
        - 각 오퍼레이션 단계에서 읽거나 갱신한 로우 수
    - A-Times(= time)
        - 각 오퍼레이션 단계별 소요시간
    - Buffers(= cr(=query), current)
        - 캐시에서 읽은 버퍼 블록 수
    - Reads(= pr)
        - 디스크에서 읽은 블록 수

### SQL 트레이스를 확인하고자 설정하는 옵션

- set statistics profile on
    - 각 쿼리가 일반 결과 집합을 반환하고 그 뒤에는 쿼리 실행 프로필을 보여 주는 추가 결과 집합을 반환한다.
    - 출력에는 다양한 연산자에서 처리한 행수 및 연산자의 실행 횟수에 대한 정보도 포함된다.
- set statistics io on
    - Transact-SQL 문이 실행되고 나서 해당 문에서 만들어진 디스크 동작 양에 대한 정보를 표시한다.
- set statistics time on
    - 각 Transact-SQL문을 구문 분석, 컴파일 및 실행하는 데 사용한 시간을 밀리초(0.001초) 단위로 표시한다.

### SQL Server 트레이스에서 확인할 수 있는 정보

- 구문 분석 및 컴파일 시간
- 검색 수
- 논리적 읽기 수
- 물리적 읽기 수
- 미리 읽기 수
- 실행 시간