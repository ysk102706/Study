### 정의
- 자식 노드의 수가 2개 이상을 가지는 트리
- 자식 노드의 숫자가 n개인 B-tree를 B-tree of order n이라고 한다.

### 조건
- 모든 노드가 가질 수 있는 자식 노드의 최대 수는 m이다.
- 루트 노드를 제외한 내부 노드 들은 적어도 ${m\over2}$개의 자식 노드를 가진다.
- 루트 노드는 최소한 두 개의 자식 노드를 가진다.
- k개의 자식 노드를 가진 내부 노드들은 k-1개의 값을 가지고 있다.
- 모든 리프 노드들의 높이는 같다.

### 활용
- 2-3-4 tree : B-tree of order 4의 일종. 구조가 이진 트리인 red-black tree와 유사하며 변환이 가능하다. 노드 내부의 키값은 1~3개를 가질 수 있으며, 삽입과 삭제시 오버플로우나 언더플로우가 발생하면 각각 분할과 병합 연산을 한다.
- B+ tree : 루트 노드와 중간의 노드는 키를 이용하여 위치를 찾아가는 인덱스 역할만을 하며 데이터 자체는 모두 리프 노드에 저장한다(데이터를 정렬해 리프 노드에 저장했고, 그 위에 B-tree의 규칙으로 키를 저장해 트리를 구성).