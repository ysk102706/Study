### DML 성능에 영향을 미치는 요소

**요소**

- 인덱스
- 무결성 제약
- 조건절
- 서브쿼리
- Redo 로깅
- Undo 로깅
- Lock
- 커밋

**인덱스 DML 성능**

테이블 레코드를 입력하면, 인덱스에도 입력을 해야한다.

- 테이블은 Freelist를 통해 입력할 블록을 할당받는다.
    - Freelist는 테이블마다 입력이 가능한 블록 목록이다.
- 인덱스는 정렬된 자료구조이므로 수직적 탐색을 통해 입력할 블록을 찾아야 한다.
    - 인덱스에 입력하는 과정이 더 복잡하므로 DML 성능에 미치는 영향이 더 크다.

DELETE할 때도 마찬가지로 테이블 레고드 하나를 삭제하면, 인덱스 레코드를 모두 찾아서 삭제해 줘야 한다.

UPDATE할 때는 변결된 컬럼을 참조하는 인덱스만 찾아서 변경하면 되지만, 테이블에서 한 건을 변경할 때마다 인덱스에는 삭제 후 다시 삽입하는 두 개의 오퍼레이션이 일어난다.

**무결성 제약과 DML 성능**

무결성 제약의 종류

- 개체 무결성
- 참조 무결성
- 도메인 무결성
- 사용자 정의 무결성

DBMS에서 PK, FK, Check, Not Null같은 제약을 설정하면 더 완벽하게 데이터 무결성을 지켜낼 수 있지만 PK, FK같은 제약은 Check, Not Null 제약보다 성능에 더 큰 영향을 미친다.

- Check, Not Null은 정의한 제약 조건을 준수하는지만 확인하면 되지만, PK, FK 제약은 실제 데이터를 조회 해 봐야 하기 때문이다.

**Redo 로깅과 DML 성능**

오라클은 데이터파일과 컨트롤 파일에 가해지는 모든 변경사항을 Redo 로그에 기록한다.

Redo 로그는 트랜잭션 데이터가 어떤 이유에서건 유실됐을 때, 트랜젝션을 재현함으로써 유실 이전 상태로 복구하는 데 사용된다.

DML을 수행할 때마다 Redo 로그를 생성하기 때문에 DML 성능에 영향을 미친다.

**Redo 로그의 용도**

- Database Recovery
    - 물리적으로 디스크가 깨지는 등의 Media Fail 발생 시 데이터베이스를 복구하는 데 사용된다.
    - 온라인 Redo 로그를 백업해 둔 Archived Redo 로그를 이용한다.
- Cache Recovery
    - 캐시에 저장된 변경사항이 디스크 상의 데이터 블록에 아직 기록되지 않은 상태에서 정전 등이 발생해 인스턴스가 비정상적으로 종료되면, 그때까지의 작업내용을 모두 잃기 때문에 이런 트랜젝션 데이터 유실에 대비하기 위해 Redo 로그를 남긴다.
- Fast Commit
    - 변경된 메모리 버퍼블록을 디스크 상의 데이터 블록에 반영하는 작업은 랜덤 액세스 방식을 사용하기 때문에 느리다.
    - 로그는 Append 방식으로 기록하므로 상대적으로 빠르다.
        - 트랜젝션에 의한 변경사항을 Append 방식으로 빠르게 로그 파일에 기록하고, 변경된 메모리 버퍼블록과 데이터파일 블록 간 동기화는 적절한 수단을 이용해 나중에 배치 방식으로 일괄 수행한다.

**Undo 로깅과 DML 성능**

Undo는 트랜잭션을 롤백함으로써 현재를 과거 상태로 되돌리는데 사용된다.

DML을 수행할 때마다 Undo를 생성해야하므로 DML 성능에 영향을 미친다.

**Undo의 용도와 MVCC 모델**

오라클은 데이터를 입력, 수정, 삭제할 때마다 Undo 세그먼트에 기록을 남긴다.

가장 오래 전에 커밋한 Undo 공간부터 재사용하므로 Undo 데이터가 곧바로 사라지진 않지만, 언젠가 다른 트랜잭션 데이터로 덮어쓰이면서 사라질 수 밖에 없다.

Undo의 용도

- Transaction Rollback
    - 트랜잭션에 의한 변경사항을 최종 커밋하지 않고 롤백하고자 할 때 Undo 데이터를 이용한다.
- Transaction Recovery
    - 시스템이 셧다운된 시점에 아직 커밋되지 않았던 트랜잭션들을 모두 롤백할 때 Undo 데이터를 사용한다.
- Read Consistency
    - 읽기 일관성을 위해 Consistent 모드로 데이터를 읽는 오라클에선 동시 트랜잭션이 많을수록 블록 I/O가 증가하면서 성능 저하로 이어지기 때문에 Undo를 사용한다.

MVCC(Multi-Version Concurrency Control) 모델

MVCC 모델을 사용하는 오라클은 데이터를 두 가지 모드로 읽는다.

- Current 모드
    - 디스크에서 캐시로 적재된 원본 블록을 현재 상태 그대로 읽는 방식
- Consistent 모드
    - 쿼리가 시작된 이후에 다른 트랜잭션에 의해 변경된 블록을 만나면 원본 블록으로부터 복사본 블록을 만들고, 거기에 Undo 데이터를 적용해 쿼리가 시작된 시점으로 되돌려서 읽는 방식
    - 따라서 원본 블록 하나에 여러 복사본이 캐시에 존재할 수 있다.
    - 쿼리 SCN과 블록 SCN을 비교하면서 쿼리 도중에 블록이 변경되었는지 확인하면서 데이터를 읽는 방식
        - 오라클 시스템에서 마지막 커밋이 발생한 시전정보를 SCN(System Commit Number)이라는 Global 변수값으로 관리하며, 이값은 트랜잭션이 커밋하거나, 백그라운드 프로세서에 의해 조금씩 증가한다.
        - 각 블록이 마지막으로 변경된 시점을 관리하기 위해 모든 블록 헤더에 SCN을 기록하는데 이를 블록 SCN이라고 하며, 모든 쿼리는 Global 변수인 SCN 값을 먼저 확인하고 읽기 작업을 시작하는데, 이를 쿼리 SCN이라고 한다.
        - 데이터를 읽다가 블록 SCN이 쿼리 SCN보다 큰 블록을 만나면, Undo 데이터를 사용해서 시작지점으로 되돌려서 읽는다.

**Lock과 DML 성능**

Lock은 DML 성능에 매우 크고 직접적인 영향을 미친다.

Lock을 필요 이상으로 자주 길게 사용하거나 레벨을 높일수록 DML 성능은 느려진다.

하지만 그렇다골 Lock을 너무 적게, 짧게 사용하거나 필요한 레벨 이하로 낮추면 데이터 품질이 나빠진다.

성능과 데이터 품질을 모두 잡으려면 매우 세심한 동시성 제어가 필요하다.

- 동시성 제어는 동시에 실행되는 트랜잭션 수를 최대화하면서도 입력, 수정, 삭제, 검색 시 데이터 무결성을 유지하기 위해 노력하는 것을 말한다.

**커밋과 DML 성능**

커밋은 DML과 별개로 실행하지만, DML을 끝내려면 커밋까지 완료해야하므로 서로 밀접한 관련이 있다. 특히 Lock에 의해 블로킹된 경우 커밋은 DML 성능과 직결된다. DML을 완료할 수 있게 Lock을 푸는 열쇠가 커밋이기 때문이다.

모든 DBMS가 Fast Commit을 구현하고 있다.

다음은 커밋의 내부 메커니즘이다.

- DB 버퍼캐시
    - 서버 프로세스는 버퍼캐시를 통해 데이터를 읽고 쓴다. 버퍼캐시에서 변경된 블록을 모아 주기적으로 데이터파일에 일괄 기록하는 작업은 DBWR 프로세스가 맡는다.
- Redo 로그버퍼
    - 버퍼캐시는 휘발성이므로 DBWR 프로세스가 Dirty 블록들을 데이터파일에 반영할 때까지 불완전한 상태이지만 변경사항을 Redo 로그에도 기록해 놨으므로 안심해도 된다.
    - 하지만 Redo 로그도 파일이기 때문에 Append 방식으로 기록하더라도 디스크 I/O는 느리다. Redo 로깅 성능 문제를 해결하기 위해 오라클은 로그버퍼를 이용한다.
        - 로그버퍼에 기록한 내용은 나중에 LGWR 프로세스가 Redo 로그 파일에 일괄 기록한다.
- 트랜잭션 데이터 저장 과정
    - DML 문을 실행하면 Redo 로그버퍼에 변경사항을 기록한다.
    - 버퍼블록에서 데이터를 변경한다. 몰론, 버퍼캐시에서 블록을 찾지 못하면, 데이터파일에서 읽는 작업부터 한다.
    - 커밋한다.
    - LGWR 프로세스가 Redo 로그버퍼 내용을 로그파일에 일괄 저장한다.
    - DBWR 프로세스가 변경된 버퍼블록들은 데이터파일에 일괄 저장한다.
- 커밋 = 저장 버튼
    - 디스크에 기록하는 것이 Sync 방식이고, LGWR 프로세스가 Redo 로그를 기록하는 작업은 디스크 I/O 작업이기 때문에 커밋이 느리다.
    - 따라서 트랜잭션을 너무 오래 커밋을 안하는 것도 문제지만, 너무 자주하는 것도 문제다.

### 데이터베이스 Call과 성능

**데이터베이스 Call**

SQL은 아래 세 단계로 나누어 실행된다.

- Parse Call
    - SQL 파싱과 최적화를 수행하는 단계이다.
    - SQL과 실행계획을 라이브러리 캐시에서 찾으면, 최적화 단계는 생략할 수 있다.
- Execute Call
    - SQL을 실행하는 단계다.
    - DML은 이 단계에서 모든 과정이 끝나지만, SELECT 문은 Fetch 단계를 거친다.
- Fetch Call
    - 데이터를 읽어서 사용자에게 결과집합을 전송하는 과정이다.
    - SELECT 문에서만 나타난다.

Call이 어디서 발생하느냐에 따라 2가지로 나눌 수 있다.

- User Call
    - 네트워크를 경유해 DBMS 외부로부터 인입되는 Call이다.
- Recursive Call
    - DBMS 내부에서 발생하는 Call이다.
    - SQL 파싱과 최적화 과정에서 발생하는 데이터 딕셔너리 조회, PL/SQL로 작성한 사용자 정의 함수/프로시저/트리거에 내장된 SQL을 실행할 때 발생하는 Call이 여기에 해당한다.

User Call이든 Recursive Call이든 SQL을 실행할 때마다 Parse, Execute, Fetch Call 단계를 거친다.

데이터베이스 Call이 많으면 성능은 느릴 수 밖에 없다. 특히 User Call이 성능이 미치는 영향이 크다.

**One SQL의 중요성**

절차적으로 처리하지 않아도 되는 쿼리의 경우에는 One SQL을 통해 1번의 Call로 처리함으로써 성능을 크게 줄일 수 있다.

One SQL은 다음 구문을 활용해 구현할 수 있다.

- Insert Into Select
- 수정가능한 조인 뷰
- Merge 문

### Array Processing 활용

실무에서 절차적 프로그램을 One SQL로 구현하는 것은 쉽지 않은데, 바로 복잡한 업무 로직을 포함하는 경우가 많기 때문이다.

이때 Array Processing 기능을 활용하면 One SQL로 구현하지 않고도 Call 부하를 크게 줄일 수 있다.

```sql
// PL/SQL
declare
	cursor c is select * from source;
	type typ_source is table of c%rowtype;
	l_source typ_source;

	l_array_size number default 10000;

	procedure insert_target(p_source in typ_source) is 
	begin
		forall i in p_source.first..p_source.last
		insert into target values p_source(i);
	end insert_target;

begin
	open c;
	loop
		fetch c bulk collect into l_source limit l_array_size;

		insert_target(l_source);

		exit when c%notfound
	end loop;

	close c;

	commit;
end;
/
```

### 인덱스 및 제약 해제를 통한 대량 DML 튜닝

인덱스와 무결성 제약조건은 DML 성능에 큰 영향을 끼지지만, 온라인 트랜잭션 처리 시스템에서 이들 기능을 해제할 순 없다.

반대로 동시 트랜잭션 없이 대량 데이터를 적재하는 배치 프로그램에서는 이들 기능을 해제함으로써 큰 성능 개선 효과를 얻을 수 있다.

### 수정가능 조인 뷰

**전통적인 방식의 UPDATE**

```sql
update 고객 c
set 최종거래일시 = (select max(거래일시) from 거래
									 where 고객번호 = c.고객번호
									 and 거래일시 >= trunc(add_month(sysdate, -1)))
		, 최근거래횟수 = (select count(*) from 거래
										 where 고객번호 = c.고객번호
										 and 거래일시 >= trunc(add_month(sysdate, -1)))
		, 최근거래금액 = (select sum(거래금액) from 거래
										 where 고객번호 = c.고객번호
										 and 거래일시 >= trunc(add_month(sysdate, -1)))
where exists (select 'x' from 거래
							where 고객번호 = c.고객번호
							and 거래일시 >= trunc(add_month(sysdate, -1)))
// 아래 처럼 바꿀 수 있다.
update 고객 c
set (최종거래일시, 최근거래횟수, 최근거래금액) = 
		(select max(거래일시), count(*), sum(거래금액) 
		 from 거래
		 where 고객번호 = c.고객번호
		 and 거래일시 >= trunc(add_month(sysdate, -1)))
where exists (select 'x' from 거래
							where 고객번호 = c.고객번호
							and 거래일시 >= trunc(add_month(sysdate, -1)))
// 총 고객 수가 많으면 hash semi join도 고려할 수 있다.
update 고객 c
set (최종거래일시, 최근거래횟수, 최근거래금액) = 
		(select max(거래일시), count(*), sum(거래금액) 
		 from 거래
		 where 고객번호 = c.고객번호
		 and 거래일시 >= trunc(add_month(sysdate, -1)))
where exists (select /*+ unnest hash_sj */ 'x' from 거래
							where 고객번호 = c.고객번호
							and 거래일시 >= trunc(add_month(sysdate, -1)))
```

**수정가능 조인 뷰**

수정 가능 조인 뷰를 활용하면 참조 테이블과 두 번 조인하는 비효율을 없앨 수 있다.

```sql
update
(select /*+ ordered use_hash(c) no_merge(t) */
 c.최종거래일시, c.최근거래횟수, c.최근거래금액
, t.거래일시, t.거래횟수, t.거래금액
form (select 고객번호
			, max(거래일시) 거래일시, count(*) 거래횟수, sum(거래금액) 거래금액
			from 거래
			where 거래일시 >= trunc(add_month(sysdate, -1))
			group by 고객번호) t
			, 고객 c
where c.고객번호 = t.고객번호
)
set 최종거래일시 = 거래일시
	, 최근거래횟수 = 거래횟수
	, 최근거래금액 = 거래금액
```

조인 뷰는 FROM 절에 두 개 이상 테이블을 가진 뷰를 가리키며, 수정가능 조인 뷰는 말 그대로 입력, 수정, 삭제가 허용되는 조인뷰를 말한다.

**키 보존 테이블**

키 보존 테이블은 조인된 결과집합을 통해서도 중복 값 없이 Unique 하게 식별가능한 테이블을 말한다.

- 단적으로 말하면 뷰에 rowid를 제공하는 테이블을 말한다.

### MERGE 문 활용

테이블에서 발생한 변경분 데이터를 DW에 반영하는 프로세스는 다음과 같다.

이 중에서 3번 데이터 적제 작업을 효과적으로 지원하기 위해 오라클 9i에서 MERGE 문이 도입되었다.

1. 전일 발생한 변경 데이터를 기간계 시스템으로부터 추출
2. CUSTOMER_DELTA 테이블을 DW 시스템으로 전송
3. DW 시스템으로 적재

MERGE문은 source 테이블을 기준으로 Target 테이블과 LeftOuter 방식으로 조인해서 조인에 성곡하면 UPDATE, 실패하면 INSERT 한다.