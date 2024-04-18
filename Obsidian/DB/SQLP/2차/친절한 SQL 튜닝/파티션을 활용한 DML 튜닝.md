### 테이블 파티션

파티셔닝(Partitioning)은 테이블 또는 인덱스 데이터를 특정 컬럼 값에 따라 별도 세그먼트에 나눠서 저장하는 것을 말한다.

**파티션이 필요한 이유**

- 관리적 측면 : 파티션 단위 백업, 추가, 변경 → 가용성 향상
- 성능적 측면 : 파티션 단위 조회 및 DML, 경합 또는 부하 분산

**파티션 종류**

- Range 파티션
    
    가장 기초적인 방식으로 주로 날짜 컬럼을 기준으로 파티셔닝한다.
    
    ```sql
    create table 주문 (주문번호 number, 주문일자 varchar2(8), 고객ID varchar2(5), 
    									 배송일자 varchar2(8), 주문금액 number, ...)
    partition by range(주문일자) (
    	partition P2017_Q1 values less than ('20170401')
    , partition P2017_Q2 values less than ('20170701')
    , partition P2017_Q3 values less than ('20171001')
    , partition P2017_Q4 values less than ('20180101')
    , partition P2018_Q1 values less than ('20180401')
    , partition P9999_MX values less than ( MAXVALUE )  -> 주문일자 >= '20180401'
    );
    ```
    
    읽을 때 검색 조건을 만족하는 파티션만 골라 읽을 수 있어 이력성 데이터를 Full Scan 방식으로 조회할 때 성능을 크게 향상시킨다.
    
    데이터 관리 작업을 효율적이고 빠르게 수행할 수 있는 장점도 있다.
    
    파티션 테이블에 대한 SQL 성능 향상 원리는 파티션 Pruning에 있다.
    
    파티션 Pruning은 SQL 하드파싱이나 실행 시점에 조건절을 분석해서 읽지 않아도 될 파티션 세그먼트를 액세스 대상에서 제외하는 기능이다.
    
    파티션도 인덱스로 액세스할 수 있지만, 파티션 Pruning을 이용한 것보다 느리다.
    
    파티션도 클러스터, IOT와 마찬가지로 관련 있는 데이터가 흩어지지 않고 물리적으로 인접하도록 저장하는 클러스터링 기술에 속한다.
    
    클러스터와 다른 점은 세그먼트 단위로 모아서 저장한다는 것이다.
    
- 해시 파티션
    
    파티션 키 값을 해시 함수에 입력해서 반환받은 값이 같은 데이터를 같은 세그먼트에 저장하는 방식이다.
    
    파티션 개수만 사용자가 결정하고 데이터를 분산하는 알고리즘은 오라클 내부 해시 함수가 결정한다.
    
    해시 파티션은 고객ID처럼 변별력이 좋고 데이터 분포가 고른 컬럼을 파티션 기준으로 선정해야 효과적이다.
    
    ```sql
    create table 고객 (고객ID varchar2(5), 고객명 varchar2(10), ... )
    partition by hash(고객ID) partitions 4;
    ```
    
    검색할 때는 조건절 비교 값에 똑같은 해시 함수를 적용함으로써 읽을 파티션을 결정하면, 해시 알고리즘 특성상 등치(=) 조건 또는 IN-List 조건으로 검색할 때만 파티션 Pruning이 작동한다.
    
- 리스트 파티션
    
    사용자가 정의한 그룹핑 기준에 따라 데이터를 분할 저장하는 방식이다.
    
    ```sql
    create table 인터넷매물 (물건코드 varchar2(5), 지역분류 varchar2(4), ... )
    partition by list(지역분류) (
    	partition P_지역1 values ('서울')
    , partition P_지역2 values ('경기', '인천')
    , partition P_지역3 values ('부산', '대구', '대전', '광주')
    , partition P_기타 values (DEFAULT)
    );
    ```
    
    Range 파티션에선 순서에 따라 저장할 파티션이 결정되지만, 리스트 파티션에서는 순서와 상관없이 불연속적인 값의 목록에 의해 결정된다.
    
    해시 파티션과 비교하면, 해시 파티션은 오라클이 정한 해시 알고리즘에 따라 임의로 분할하는 반면, 리스트 파티션은 사용자가 정의한 논리적인 그룹에 따라 분할하기 때문에, 업무적인 친화도에 따라 그룹핑 기중을 정하되, 될 수 있으면 각 파티션에 값이 고르게 분산되도록 해야 한다.
    

### 인덱스 파티션

**테이블 파티션 구분**

- 비파티션 테이블(Non-Partitioned Table)
- 파티션 테이블(Partitioned Table)

**파티션 인덱스 구분**

- 로컬 파티션 인덱스(Local Partitioned Index)
    
    각 테이블 파티션과 인덱스 파티션이 서로 1:1 대응 관계가 되도록 오라클이 자동으로 관리하는 파티션 인덱스를 말한다.
    
    ```sql
    create index 주문_x01 on 주문 (주문일자, 주문금액) LOCAL;
    // 인덱스 생성문 뒤에  LOCAL을 붙이면 됨.
    ```
    
    각 인덱스 파티션은 테이블 파티션 속성을 그대로 상속받는다. 로컬 인덱스의 장점은 관리의 편의성에 있다.
    
- 글로벌 파티션 인덱스(Global Partitioned Index)
    
    파티션을 테이블과 다르게 구성한 인덱스다.
    
    - 파티션 유형이 다르거나, 파티션 키가 다르거나, 파티션 기준값 정의가 다른 경우이다.
    - 비파티션 테이블이어도 인덱스는 파티셔닝할 수 있다.
    
    ```sql
    create index 주문_x03 on 주문 (주문금액, 주문일자) GLOBAL
    partition by range(주문금액) (
    	partition P_01 values less than (100000)
    , partition P_MX values less than (MAXVALUE) 
    );
    ```
    
    테이블 파티션 구성을 변경하는 순간 Unusable 상태로 바뀌므로 곧바로 인덱스를 재생성해 줘야 한다.
    
- 비파티션 인덱스(Non-Partitioned Index)
    
    파티셔닝하지 않은 인덱스이다.
    
    ```sql
    create index 주문_x04 on 주문 (고객ID, 배송일자);
    ```
    
    테이블 파티션 구성을 변경하는 순간 Unusable 상태로 바뀌므로 곧바로 인덱스를 재생성해 줘야 한다.
    

**Prefixed vs Nonprefixed**

- Prefixed
    - 인덱스 파티션 키 컬럼이 인덱스 키 컬럼 왼쪽 선두에 위치한다.
- Nonprefixed
    - 인덱스 파티션 키 컬럼이 인덱스 키 컬럼 왼쪽 선두에 위치하지 않는다.
    - 파티션 키가 인덱스 컬럼에 아예 속하지 않을 때도 여기에 속한다.

네가지 유형으로 나온다.

- 로컬 Prefixed 파티션 인덱스
- 로컬 Nonprefixed 파티션 인덱스
- 글로벌 Prefixed 파티션 인덱스
- 비파티션 인덱스

**중요한 인덱스 파티션 제약**

Unique 인덱스를 파티셔닝하려면, 파티션 키가 모두 인덱스 구성 컬럼이어야 한다.

서비스 중단 없이 파티션 구조를 변경하려면 PK를 포함한 모든 인덱스가 로컬 파티션이어야 한다.

### 파티션을 활용한 대량 UPDATE 튜닝

**파티션 Exchange를 이용한 대량 데이터 변경**

테이블이 파티셔닝 되어 있고, 인덱스도 로컬 파티션이라면, 수정된 값을 가지는 임시 세그먼트를 만들어 원본 파티션과 바꿔치기 하는 방식이다.

작업 순서는 다음과 같다.

1. 임시 테이블을 생성한다. ⇒ 할 수 있으면 nologging 모드로 생성한다.
    
    ```sql
    create table 거래_t
    nologging
    as 
    select * from 거래 where 1 = 2;
    ```
    
2. 거래 데이터를 읽어 임시 테이블에 입력하면서 상태코드 값을 수정한다.
    
    ```sql
    insert /*+ append */ into 거래_t
    select 고객번호, 거래일자, 거래순번, ... , 
    			 (case when 상태코드 <> 'ZZZ' then 'ZZZ' else 상태코드 end) 상태코드
    from 거래
    where 거래일자 < '20150101';
    ```
    
3. 임시 테이블에 원본 테이블과 같은 구조로 인덱스를 생성한다. ⇒ 할 수 있으면 nologging모드로 생성한다.
    
    ```sql
    create unique index 거래_t_pk on 거래_t (고객번호, 거래일자, 거래순번) nologging;
    create index 거래_t_x1 on 거래_t (거래일자, 고객번호) nologging;
    create index 거래_t_x2 on 거래_t (상태코드, 거래일자) nologging;
    ```
    
4. 2014년 12월 파티션과 임시 테이블을 Exchange 한다.
    
    ```sql
    alter table 거래
    exchange partition p201412 with table 거래_t
    including indexes without validation;
    ```
    
5. 임시 테이블을 Drop 한다.
    
    ```sql
    drop table 거래_t
    ```
    
6. (nologging 모드로 작업했다면) 파티션을 logging 모드로 전환한다.
    
    ```sql
    alter table 거래 modify partition p201412 logging;
    alter index 거래_pk modify partition p201412 logging;
    alter index 거래_x1 modify partition p201412 logging;
    alter index 거래_x2 modify partition p201412 logging;
    ```
    

### 파티션을 활용한 대량 DELETE 튜닝

**DELETE가 느린 이유**

- 테이블 레코드 삭제
- 테이블 레코드 삭제에 대한 Undo Logging
- 테이블 레코드 삭제에 대한 Redo Logging
- 인덱스 레코드 삭제
- 인덱스 레코드 삭제에 대한 Undo Logging
- 인덱스 레코드 삭제에 대한 Redo Logging
- Undo에 대한 Redo Logging

특히 각 인덱스 레코드를 찾아서 삭제해 주는 작업에 대한 부담이 크다.

**파티션 Drop을 이용한 대량 데이터 삭제**

테이블이 삭제 조건절 컬럼 기준으로 파티셔닝 되어 있고, 인덱스도 로컬 파티션이라면, 빠르게 삭제할 수 있다.

```sql
alter table 거래 drop partition p201412;
```

**파티션 Truncate를 이용한 대량 데이터 삭제**

거래일자 조건에 해당하는 데이터를 일괄 삭제하지 않고 또 다른 삭제 조건이 있는 경우가 있는다.

- 조건을 만족하는 데이터가 소수일 경우
    
    ```sql
    delete from 거래
    where 거래일자 < '20150101'
    and (상태코드 <> 'ZZZ' or 상태코드 is null);
    ```
    
- 조건을 만족하는 데이터가 대량일 경우
    
    남길 데이터만 백업했다가 재입력하는 방식이 빠르다.
    
    1. 임시 테이블을 생성하고 남길 데이터만 복제한다.
        
        ```sql
        create table 거래_t
        as 
        select * 
        from 거래
        where 거래일자 < '20150101'
        and 상태코드 = 'ZZZ';
        ```
        
    2. 삭제 대상 테이블 파티션을 Truncate 한다.
        
        ```sql
        alter table 거래 truncate partition p201412;
        ```
        
    3. 임시 테이블에 복제해 둔 데이터를 원본 테이블에 입력한다.
        
        ```sql
        insert into 거래 
        select * from 거래_t;
        ```
        
    4. 임시 테이블을 Drop 한다.
        
        ```sql
        drop table 거래_t;
        ```
        
    
    서비스 중단없이 파티션을 Drop 또는 Truncate 하려면 아래 조건을 모두 만족해야 한다.
    
    - 파티션 키와 커팅 기준 컬럼이 일치해야 한다.
    - 파티션 단위와 커팅 주기가 일치해야 한다.
    - 모든 인덱스가 로컬 파티션 인덱스여야 한다.

### 파티션을 활용한 대량 INSERT 튜닝

**비파티션 테이블일 때**

비파티션 테이블에 손익분기점을 넘는 대량 데이터를 INSERT 하려면, 인덱스를 Unusable 시켰다가 재생성하는 방식이 더 빠를 수 있다.

1. (할 수 있다면) 테이블을 nologging 모드로 전환한다.
    
    ```sql
    alter table target_t nologging;
    ```
    
2. 인덱스를 Unusable 상태로 전환한다.
    
    ```sql
    alter index target_t_x01 unusable;
    ```
    
3. (할 수 있다면 Direct Path Insert 방식으로) 대량 데이터를 입력한다.
    
    ```sql
    insert /*+ append */ into target_t
    select * from source_t;
    ```
    
4. (할 수 있다면, nologging 모드로) 인덱스를 재생성한다.
    
    ```sql
    alter index target_t_x01 rebuild nologging;
    ```
    
5. (nologging 모드로 작업했다면) logging 모드로 전환한다.
    
    ```sql
    alter table target_t logging;
    alter index target_t_x01 logging;
    ```
    

**파티션 테이블일 때**

테이블이 파티셔닝되어 있고, 인덱스도 로컬 파티션이라면 초대용량 인덱스도 재생성하는 부담이 줄어든다.

1. (할 수 있다면) 작업 대상 테이블 파티션을 nologging 모드로 전환한다.
    
    ```sql
    alter table target_t modify partition p_201712 nologging;
    ```
    
2. 작업 대상 테이블 파티션과 매칭되는 인덱스 파티션을 Unusable 상태로 전환한다.
    
    ```sql
    alter index target_t_x01 modify partition p_201712 unusable;
    ```
    
3. (할 수 있다면 Direct Path Insert 방식으로) 대량 데이터를 입력한다.
    
    ```sql
    insert /*+ append */ into target_t
    select * from source_t where dt between '20171201' and '20171231';
    ```
    
4. (할 수 있다면, nologging 모드로) 인덱스를 재생성한다.
    
    ```sql
    alter index target_t_x01 rebuild partition p_201712 nologging;
    ```
    
5. (nologging 모드로 작업했다면) logging 모드로 전환한다.
    
    ```sql
    alter table target_t modify partition p_201712 logging;
    alter index target_t_x01 modify partition p_201712 logging;
    ```