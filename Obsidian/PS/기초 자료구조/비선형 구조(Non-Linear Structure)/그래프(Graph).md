### 정의
- 정점의 집합과 간선의 집합의 순서쌍    
    ![](https://i.imgur.com/YSgVoFu.png)


### 용어
- 노드 : 그래프 상의 정점
- 간선 : 노드와 노드를 연결하는 선
- 이웃(인접) : 두 노드 사이에 간선이 존재한다면, 두 노드는 서로 이웃이다
- 차수 : 어떤 정점v와 이웃인 정점의

### 구현
```cpp
#include <vector>

// T는 자료형, MAX는 노드의 개수 + 1
vector<T> g[MAX];

// n개의 간선에 대한 입력을 받음
for (int i = 0; i < n; i++) {
	// u는 시작노드 v는 도착노드
	// g[u]에 v를 넣는다는 것은 u에서 v로 갈 수 있음을 의미한다
	int u, v; cin >> u >> v;
	g[u].push_back(v);
	g[v].push_back(u);
}
```

### 종류
[[유향 그래프(Directed Graph)]]
[[다중 그래프(Multi Graph)]]
[[가중치 그래프(Weighted Graph)]]

