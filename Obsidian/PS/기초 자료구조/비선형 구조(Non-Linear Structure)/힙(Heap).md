### 정의
- 데이터에서 최댓값과 최솟값을 빠르게 찾기 위해 고안된 완전 이진 트리
- 항상 완전 이진 트리이며, 최대힙과 최소힙으로 나눠짐 ⇒ 최대힙은 큰 값이 부모노드, 최소힙은 작은 값이 부모노드 ⇒ 최댓값 혹은 최솟값을 ${O(1)}$에 찾을 수 있다.
- 완전 이진트리를 사용하는 이유는 삽입/삭제 속도 때문이다. ⇒ 삽입과 삭제는 모두 ${O(logN)}$이다.

### 연산
- 삽입
    - 가장 끝자리에 노드를 삽입한다.
    - 삽입된 노드와 부모 노드를 서로 비교한다.
    - 조건에 맞으면 그대로 두고, 아니면 부모와 교환한다.
    - 더 이상 바뀌지 않을 때까지 반복한다.
- 삭제
    - 루트 노드를 제거한다.
    - 루트 자리에 마지막 노드를 가져온다.
    - 올라온 노드와 자식들을 비교한다.
    - 조건에 맞으면 그대로 두고, 아니면 자식과 교환한다.
        - 없으면 그대로 둠.
        - 하나면 그 자식과 교환.
        - 두 개면 더 조건과 맞는 쪽과 교환.
    - 더 바뀌지 않을 때까지 반복한다.

### 구현
```cpp
#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;
typedef long long ll;

int min_tree[40001];
int min_l_idx;

int max_tree[40001];
int max_l_idx;

void min_Add(int x) {
	min_tree[++min_l_idx] = x;
	int cur = min_l_idx;
	while (cur > 1 && min_tree[cur >> 1] > min_tree[cur]) {
		swap(min_tree[cur >> 1], min_tree[cur]);
		cur >>= 1;
	}
}

void min_Top() {
	cout << min_tree[1] << '\\n';
}

void min_Del() {
	min_tree[1] = min_tree[min_l_idx--];
	int cur = 1;
	while (true) {
		int l = cur << 1;
		int r = cur << 1 | 1;
		if (l <= min_l_idx && r <= min_l_idx && min_tree[l] < min_tree[cur] && min_tree[r] < min_tree[cur]) {
			if (min_tree[l] < min_tree[r]) {
				swap(min_tree[cur << 1], min_tree[cur]);
				cur <<= 1;
			}
			else {
				swap(min_tree[cur << 1 | 1], min_tree[cur]);
				cur = cur << 1 | 1;
			}
		}
		else if (l <= min_l_idx && min_tree[l] < min_tree[cur]) {
			swap(min_tree[cur << 1], min_tree[cur]);
			cur <<= 1;
		}
		else if (r <= min_l_idx && min_tree[r] < min_tree[cur]) {
			swap(min_tree[cur << 1 | 1], min_tree[cur]);
			cur = cur << 1 | 1;
		}
		else break;
	}
}

void max_Add(int x) {
	max_tree[++max_l_idx] = x;
	int cur = max_l_idx;
	while (cur > 1 && max_tree[cur >> 1] < max_tree[cur]) {
		swap(max_tree[cur >> 1], max_tree[cur]);
		cur >>= 1;
	}
}

void max_Top() {
	cout << max_tree[1] << '\\n';
}

void max_Del() {
	max_tree[1] = max_tree[max_l_idx--];
	int cur = 1;
	while (true) {
		int l = cur << 1;
		int r = cur << 1 | 1;
		if (l <= max_l_idx && r <= max_l_idx && max_tree[l] > max_tree[cur] && max_tree[r] > max_tree[cur]) {
			if (max_tree[l] > max_tree[r]) {
				swap(max_tree[cur << 1], max_tree[cur]);
				cur <<= 1;
			}
			else {
				swap(max_tree[cur << 1 | 1], max_tree[cur]);
				cur = cur << 1 | 1;
			}
		}
		else if (l <= max_l_idx && max_tree[l] > max_tree[cur]) {
			swap(max_tree[cur << 1], max_tree[cur]);
			cur <<= 1;
		}
		else if (r <= max_l_idx && max_tree[r] > max_tree[cur]) {
			swap(max_tree[cur << 1 | 1], max_tree[cur]);
			cur = cur << 1 | 1;
		}
		else break;
	}
}

int main() {
	cout << "MIN Heap" << '\\n';
	min_Add(5);
	min_Add(3);
	min_Add(4);
	min_Add(2);
	min_Add(1);

	min_Top();
	min_Del();
	min_Top();
	min_Del();
	min_Top();
	min_Del();
	min_Top();
	min_Del();
	min_Top();
	min_Del();

	cout << "MAX Heap" << '\\n';

	max_Add(1);
	max_Add(2);
	max_Add(3);
	max_Add(4);
	max_Add(5);

	max_Top();
	max_Del();
	max_Top();
	max_Del();
	max_Top();
	max_Del();
	max_Top();
	max_Del();
	max_Top();
	max_Del();
}
```