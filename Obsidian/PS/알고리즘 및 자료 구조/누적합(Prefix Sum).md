### 정의
- 어떤 수열 A에 대해서 처음부터 각 인덱스까지의 요소들의 합을 구하는 것

### 활용
- 구간합 구하기
    - 어떤 수열 A에서 특정 구간의 구간합을 알고싶을 떄, 누적합을 활용해서 다음과 같이 계산할 수 있다.             ${ret = P[end] - P[start - 1]}$
	    - P : 수열 A에 대한 누적합 배열
        - start : 구간의 시작 인덱스
        - end : 구간의 끝 인덱스

### 구현
- 누적합
    점화식은 다음과 같다.
    ${P[i] = P[i - 1] + value}$
	```cpp
	#include <vector>
    
    vector<int> v;
    int p[101];
    
    int main() {
    	int n; cin >> n;
    	for (int i = 1; i <= n; i++) {
    		int x; cin >> x;
    		v.push_back(x);
    		p[i] = p[i - 1] + x;
    	}
    }
	```