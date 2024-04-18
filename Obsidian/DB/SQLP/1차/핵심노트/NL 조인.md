**NL 조인 유도**

- Oracle : use_nl(table name)
- SQL Server : Option(force order, loop join)
- SQL Server(from절) : Option(force order) from inner loop join tablename on (조건)

**NL 조인 특징**

- 랜덤 액세스 위주의 조인 방식
- 대량 데이터 조인 시 불리
- 한 레코드씩 순차적으로 진행
- 부분범위 처리가 가능하면 레코드가 얼마든지 빠름
- 인덱스 전략이 중요함(인덱스가 있느냐 없느냐, 있으면 컬럼이 어떻게 구성됐는지)