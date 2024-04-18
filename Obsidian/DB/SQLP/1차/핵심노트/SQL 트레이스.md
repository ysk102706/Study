**오라클에서 SQL 트레이스를 확인하는 명령어**

```sql
alter session set sql_trace = true;
```

TKProf 유틸리티를 사용하면 SQL 트레이스 파일을 분석해서 리포트 파일을 생성해준다.

- 아래처럼 유닉스 쉘(Shell)이나 도스 프롬프트에서 tkprof를 실행하면 사용법을 확인할 수 있다.

```bash
$ tkprof
```

- 첫번째 인자는 트레이스 파일명, 두번째 인자는 결과 파일명, 확장자는 자유이다.

```bash
// 가장 일반적인 사용법
$ tkprof ora10g_ora_14370_oraking.trc report.prf sys=no
```

**오라클 SQL 트레이스에서 확인할 수 있는 정보**

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

**Auto Trace의 실행통계 항목과 SQL Trace의 Call 통계 컬럼 비교**

- db block gets = current
- consistent gets = query
- physical reads = disk
- SQL*Net roundtrips to/from client = fetch count
- rows processed = fetch rows

**오라클 SGA 메모리에 기록한 SQL 트레이스 정보를 출력하는 명령어**

```sql
// 힌트 지정 SQL 트레이스 정보를 SGA 메모리에 기록
select /*+ gather_plan_statistics */ count(*) from big_table;

// 함수 사용 V$SQL_PLAN_STATISTICS_ALL 뷰에서도 조회할 수 있지만
// dbms_xplan.display_cursor 함수를 이용하면 보기 쉬운 형태로 포매팅해 줌
select * from table(dbms_xplan.display_cursor(null, null, 'allstats last'));
or
// V$SQL 컬럼명인 sql_id, child_number를 넣어줌. 
// 이게 null, null이면 바로 전에 실행한 SQL의 커서 ID와 child_number를 자동으로 선택함.
select * from table(dbms_xplan.display_cursor(sql_id, child_number, 'allstats last'));
```

**DBMS_XPLAN.DISPLAY_CURSOR 함수로 SQL 트레이스 정보를 출력하기 전에 처리해야 하는 것**

- SQL 문에 gather_plan_statistics 사용
- 'STATISTICS_LEVEL' Parameter 값을 ALL로 변경한 경우
- 'ROWSOURCE_EXECUTION_STATISTICS' Parameter 값을 TRUE로 변경한 경우
- GATHER_PLAN_STATISTICS HINT를 사용할 때, 10g부터 지원

**DBMS_XPLAN.DISPLAY_CURSOR 함수로 추출한 SQL 트레이스 정보**

- Starts : 각 오퍼레이션 단계별 실행 횟수
- E-Rows : 옵티마이저가 예상한 Rows
- A-Rows : 각 오퍼레이션 단계에서 읽거나 갱신한 로우 수
- A-Times : 각 오퍼레이션 단계별 소요시간
- Buffers : 캐시에서 읽은 버퍼 블록 수
- Reads : 디스크에서 읽은 블록 수

**DBMS_XPLAN.DISPLAY_CURSOR로 추출한 SQL 트레이스와 전통적인 SQL 트레이스**

- A-Rows = rows : 각 단계에서 읽거나 갱신한 건수
- A-Time = time : 각 단계별 소요시간
- Buffers = cr(query), current : 캐시에서 읽은 버퍼 블록 수
- Reads = pr : 디스크로부터 읽은 블록 수

**SQL 트레이스를 확인하고자 설정하는 옵션**

- set statistics profile on : 각 쿼리가 일반 결과 집합을 반환하고 그 뒤에는 커리 실행 프로필을 보여 주는 추가 결과 집합을 반환한다. 출력에는 다양한 연산자에서 처리한 행 수 및 연산자의 샐행 횟수에 대한 정보도 포함된다.
- set statistics io on : Transact-SQL 문이 실행되고 나서 해당 문에서 만들어진 디스크 동작 양에 대한 정보를 표시한다.
- set statistics time on : 각 Transact-SQL 문을 구문 분석, 컴파일 및 실행하는 데 사용한 시간을 밀리초(0.001초) 단위로 표시한다.

**SQL 트레이스에서 확인할 수 있는 정보**

```sql
use pubs
go 
set statistics profile on
set statistics io on
set statistics time on
go
select * from dbo.employee
go
```

- 구문 분석 및 컴파일 시간
- 검색 수
- 논리적/물리적 읽기 수
- 미리읽기 수
- 실행시간
- 각 오퍼레이션 단계별 실행 횟수