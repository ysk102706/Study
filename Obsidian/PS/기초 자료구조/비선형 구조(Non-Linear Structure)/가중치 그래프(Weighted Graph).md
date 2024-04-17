### 정의
- 간선에 가중치을 적용한 그래프

### 구현
```cpp
#include <vector>

// T는 자료형, MAX는 노드의 개수 + 1
vector<T> g[MAX];

// n개의 간선에 대한 입력을 받음
for (int i = 0; i < n; i++) {
	// u는 시작노드 v는 도착노드 h는 가중치
	// g[u]에 v를 넣는다는 것은 u에서 v로 갈 수 있음을 의미한다
	int u, v, h; cin >> u >> v >> h;
	g[u].push_back({ v, h });
	g[v].push_back({ u, h });
}
```

### 활용
- 가중치로 이동 시간이나 거리로 두면 두 노드 사이의 최단 경로도 구할 수 있다