### 정의
- 소수를 찾는 방법
- ${2 ≤ k ≤ \sqrt{n}}$을 만족하는 ${n}$이하 모든 ${k}$의 배수들을 제외시킴

### 동작 과정
1. 2 부터 ${\sqrt N}$까지 순회한다.
    - ${\sqrt N * \sqrt N = N}$이기 때문에 ${\sqrt N}$까지 오는 과정에서 그 뒤의 ${N}$이하의 수들은 이미 모든 소수 판별이 끝나있다.
2. 해당 수(${x}$)가 소수인지 아닌지 판단한다.
    2-1. 소수라면 ${x^2}$부터 ${x}$를 더하면서 더해진 수를 제외시킨다.
    - ${x}$이전의 수와 ${x}$를 곱한 수들은 이미 처리가 끝나있기 때문에 ${x^2}$부터 시작한다.
    2-2. 소수가 아니라면 이어서 순회한다.

### 구현
- 에라토스테네스의 체
    시간복잡도는 ${O(Nlog(logN))}$이다.
    
    ```cpp
    #include <iostream>
    #include <cmath>
    using namespace std;
    
    int era[101];
    
    int main() {
    	int sq = sqrt(100);
    	era[0] = era[1] = 1;
    	for (int i = 2; i <= sq; i++) {
    		if (era[i]) continue;
    		for (int j = i * i; j <= 100; j += i) era[j] = 1;
    	}
    }
    ```