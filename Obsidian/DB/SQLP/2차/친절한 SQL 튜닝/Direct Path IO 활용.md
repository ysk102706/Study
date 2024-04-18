### Direct Path I/O

일반적인 블록 I/O는 DB 버퍼캐시를 경우한다. 데이터를 변경할 때도 먼저 블록을 버퍼캐시에서 찾아보고, 찾은 버퍼블록에 변경을 가하고 나면 DBWR 프로세스가 변경된 블록을 주기적으로 찾아 데이터파일에 반영한다.

하지만 대량 데이터를 읽고 쓸 때 쓸데없이 버퍼캐시를 탐색하면 개별 프로그램 성능에 별로 좋지 않다.

- 버퍼캐시에서 찾을 가능성이 적기 때문이다.

Full Scan 위주로 가끔 수행되는 대용량 처리 프로그램이 읽어 들인 데이터는 대개 재사용성이 낮고, 그런 데이터 블록들이 버퍼캐시를 점유하면 다른 프로그램 성능에도 나쁜 영향을 미친다.

그래서 오라클은 버퍼캐시를 경유하지 않고 곹바로 데이터 블록을 읽고 쓸 수 있는 Direct Path I/O 기능을 제공한다.

아래가 Direct Path I/O가 작동하는 경우다.

- 병렬 쿼리로 Full Scan을 수행할 때
- 병렬 DML을 수행할 때
- Direct Path Insert를 수행할 때
- Temp 세그먼트 블록들을 읽고 쓸 때
- direct 옵션을 지정하고 export를 수행할 때
- nocache 옵션을 지정한 LOB 컬럼을 읽을 때

**병렬 쿼리**

쿼리문에 parallel 또는 parallel_index 힌트를 사용하면 지정한 병렬도만큼 병렬 프로세스가 떠서 동시에 작업을 진행한다.

### Direct Path Insert

일반적인 INSERT가 느린 이유

- 데이터를 입력할 수 있는 블록을 Freelist에서 찾는다. 테이블 HWM 아래쪽에 있는 블록 중 데이터 입력이 가능한 블록을 목록으로 관리하는데, 이를 Freelist라혹 한다.
- Freelist에서 할당받은 블록을 버퍼캐시에서 찾는다.
- 버퍼캐시에 없으면, 데이터 파일에서 읽어 버퍼캐시에 적재한다.
- INSERT 내용을 Undo 세그먼트에 기록한다.
- INSERT 내용을 Redo 로그에 기록한다.

Direct Path Insert 방식으로 입력하는 방법

- INSERT … SELECT 문에 append 힌트 사용
- parallel 힌트를 이용해 병렬 모드로 INSERT
- direct 옵션을 지정하고 SQL*Loader로 데이터 적재
- CTAS(create table … as select) 문 수행

Direct Path Insert 방식이 빠른 이유

- Freelist를 참조하지 않고 HWM 바깥 영역에 데이터를 순차적으로 입력한다.
- 블ㄹ고을 버퍼캐시에서 탐색하지 않는다.
- 버퍼캐시에 적재하지 않고, 데이터파일에 직접 기록한다.
- Undo 로깅을 안 한다.
- Redo 로깅을 안 하게 할 수 있다.

Direct Paht Insert 방식의 주의점

- 성능은 빨라지지만 Exclusive 모드 TM Lock이 걸린다.
    - 커밋할 때까지 다른 트랜잭션은 해당 테이블에 DML을 수행하지 못한다.
- Freelist를 조회하지 않고 HWM 바깥 영역에 입력하므로 테이블에 여유 공간이 있어도 재활용하지 않는다.

### 병렬 DML

INSERT는 append 힌트를 사용해 Direct Path Write 방식으로 유도 가능하지만, UPDATE, DELETE는 기본적으로 Direct Path Write가 불가능하다.

- 유일한 방법은 병렬 DML로 처리하는 것이다.

```sql
// 이걸 설정해야 함
alter session enable parallel dml;

// 그 다음 DML 문에 parallel 힌트 적용
```