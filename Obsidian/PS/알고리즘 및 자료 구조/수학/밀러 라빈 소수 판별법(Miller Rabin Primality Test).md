### 정의
- 가장 빠르게 소수를 판별할 수 있는 알고리즘이다.
- 어떤 수가 소수인지 확률적으로 판단할 수 있다.
    - long long 범위 안에서는 정확하게 판별할 수 있다.

### 원리
- 설명
    먼저 ${N}$이 2가 아닌 소수라고 가정하자. 그러면 ${N-1}$은 짝수가 되고, ${N - 1}$을 홀수가 될 때까지 2로 나누면 다음과 같은 쓸 수 있다.
    ${N - 1 = d \times 2^r}$
    ${N}$이 소수이기 때문에 양의 정수 ${a}$에 대해서 페르마의 소정리를 만족하게 된다.
    따라서 다음 식을 만족한다.
    ${a^{N-1} \equiv 1 (mod \ \ N), \ \ a^{d\times2^r}\equiv1(mod \ \ N)}$
    즉, ${(a^{d\times2^{r-1}}) ^ 2 \equiv 1 (mod \ \ N)}$이다.
    따라서 ${a^{d\times2^{r-1}}\equiv-1(mod \ \ N) \ \ or \ \ a^{d\times2^{r-1}}\equiv 1(mod \ \ N)}$
    계속해서 1이 나오는 경우에는 결국 ${a^{d\times2}\equiv1(mod \ \ N)}$까지 나와서 마지막으로
    ${a^d\equiv-1(mod \ \ N) \ \ or \ \ a^d\equiv 1(mod \ \ N)}$의 결과가 나오게 된다.
	
    위 과정을 통해서 ${N}$이 소수라면 ${N}$보다 작은 양의 정수 ${a}$에 대해서 다음 둘중 하나를 만족한다는 걸 알 수 있다.
    - ${a^d\equiv 1 (mod \ \ N)}$
    - ${a^{d\times2^r}\equiv -1(mod \ \ N)\ \ \ \ for \ \ some \ \ r(0≤r<s)}$
    위 결과를 통해 어떤 수 ${N}$이 소수인지 아닌지 빠르게 판별 가능하다.
	
    int와 long long에서는 다음과 같은 ${a}$에 대한 테스트를 통과하면 소수라고 결정론적으로 말할 수 있다.
    - int : 2, 7, 61
    - long long : 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37
- 보조 정리
    - 페르마의 소정리(Fermat’s Little Theorem)
        소수 ${p}$와 정수 ${a}$에 대해 다음을 만족한다.
        ${a^p \equiv a(mod \ \ p)}$
        ${a^{p-1} \equiv 1 (mod \ \ p) \ \ if \ \ a\%p ≠ 0}$
    - 소수 ${p}$에 대해서 ${x^2\equiv 1(mod \ \ p)}$이면,
        ${x \equiv -1(mod \ \ p) \ \ or \ \ x \equiv 1(mod \ \ p)}$이다.

### 구현
- 밀러 라빈 소수 판별법
    시간 복잡도는 일반적으로 ${O(k log^3 n)}$이다.(${k}$는 ${a}$의 개수)
    ```cpp
    #include <iostream>
    #include <vector>
    using namespace std;
    typedef long long ll;
    
    vector<int> miller_base = { 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37 }; // long long 기준
    //int miller_base[] = { 2, 7, 61 }; // int 기준
    
    // x^y (mod)
    ll _pow(ll x, ll y, ll mod) {
    	ll res = 1;
    	x %= mod;
    	while (y) {
    		if (y % 2) res = (res * x) % mod;
    		y /= 2;
    		x = (x * x) % mod;
    	}
    	return res;
    }
    
    bool miller_rabin(ll n, ll a) {
    	if (n <= 1) return false;
    	if (a % n == 0) return true; // 판별하는 a가 모두 소수이므로 n도 소수이다.
    	ll k = n - 1;
    	while (1) {
    		ll t = _pow(a, k, n); // a^k
    		if (t == n - 1) return true; // a^k = -1 (mod n)
    		if (k % 2) return (t == 1); // 더 이상 루트를 씌울 수 없기 때문에 1 또는 -1이어야 한다.
    		k /= 2;
    	}
    }
    
    int main() {
    	ll n; cin >> n;
    	for (int i = 0; i < miller_base.size(); i++) {
    		if (miller_rabin(n, miller_base[i]) == false) {
    			cout << "Composite!";
    			return 0;
    		}
    	}
    	cout << "Prime!";
    }
    ```
- 고속 푸리에 변환 사용
    고속 푸리에 변환으로 곱셈의 횟수를 줄였기 때문에 시간 복잡도는 ${O(klog^2n)}$이다.