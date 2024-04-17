### 정의
- 몬테카를로적으로 진약수를 찾아주는 인수분해 방법
- 10^18정도의 매우 큰 수도 빠르게 소인수분해 할 수 있다.
    - 1 또는 소수가 나올 때까지 인수분해를 재귀적으로 해서 소인수분해를 할 수 있다.
    - 소수를 판별하기 위해 가장 빠른 소수 판별 알고리즘인 밀러-라빈 소수판별법을 함께 쓴다.

### 원리
- 설명
    큰 홀수 ${n}$을 생각해 보면
    ${n}$이 짝수라면 ${n = 2}$일 때 그 자체로 소수이고
    ${n > 2}$라면 진약수 ${2}$를 가진다.
		
    다음과 같은 단순한 이차 다항식을 생각해보면
    ${f(x) = x^2 + a \ \ (a≠0 \ \ and \ \ a ≠ -2)}$
    어떤 초기값 ${x_0}$으로 시작해서 귀납적으로
    ${x_{k + 1} \equiv f(x_k)(mod \ \ n)}$
    을 얻어가고, 이로 인해 임의 수열 ${x_0, x_1, x_2\dots}$를 얻는다.
		
    ${n}$의 진약수 ${d}$를 생각해보자
    ${d < n}$이므로
    ${x_j \equiv x_k(mod \ \ d)}$
    ${x_j \not\equiv x_k(mod \ \ n)}$
    인 ${x_j, x_k}$가 아마도 존재할 것이다.
	
    ${d\mid x_j-x_k}$이지만 ${n\nmid x_j - x_k}$이기 때문에
    - 보조 지식
        ${a\mid b}$는 ${b \div a}$의 나머지가 ${0}$임을 말한다.
        - ${b}$가 ${a}$의 배수임을 뜻함
        ${a\nmid b}$는 ${b \div a}$의 나머지가 ${0}$이 아님을 말한다.
    ${gcd(x_j-x_k, n)}$은 ${n}$의 진약수가 된다.
    ${∵ n = dr, x_j - x_k = ds}$
    ${\Rightarrow gcd(x_j - x_k, n) \ge d, \ \ gcd(x_j-x_k, n) \ne n}$
	
    이렇게 무작위의 ${x_j, x_k}$ 쌍들을 이용해 위 조건을 만족하는 ${x_j, x_k}$를 찾아 인수분해를 할 수 있을 것이다.
    이 과정에서 무작위로 얻은 ${x_j, x_k}$를 이용한 ${gcd(x_j - x_k, n)}$은 ${n}$이거나 ${1}$이되어 인수분해에 도움이 안될 가능성도 있다.
	
    ${j < k}$ 일 때, 만약 ${x_k \equiv x_j(mod \ \ d)}$라면 ${f(x)}$에 의해
    ${x_{j + 1} = f(x_j) \equiv f(x_k) = x_{k + 1} (mod \ \ d)}$
    이므로, 수열 ${x_k}$가 아직 모르는 진약수 ${d}$에 대해 모두 나머지를 취해주면 ${k-j}$ 길이의 무한히 반복되는 구간이 생긴다.
	
    즉, ${r \ge j, s \ge j}$일 때,
    ${r \equiv s(mod \ \ k-j) \Leftrightarrow x_r\equiv x_s(mod \ \ d)}$
	
    어떤 ${t}$를 ${j}$보다 큰 ${k - j}$의 배수라 할 때,
    ${x_{2t} \equiv x_t(mod \ \ d) \Rightarrow d\mid x_{2t} - x_t}$가 된다.
	
    따라서 ${1 < gcd(x_{2t} - x_t, n) < n}$이 되는 정수 ${t}$가 존재할 것으로 예상하는 것은 적절하다.
    ${j, k}$는 아무거나에서 시작해도 되지만 두 값이 처음에는 같아야한다.
	
    다음은 ret 배열에서 찾아낸 소인수들을 모두 push_back하는 재귀함수이다.
    ```cpp
    void fn(ll n, vector<ll> &ret) {
    	if (n == 1) return;
    	if (!(n & 1)) {
    		ret.push_back(2);
    		fn(n >> 1, ret);
    		return;
    	}
    	if (primell(n)) {
    		ret.push_back(n);
    		return;
    	}
    	ll g = 1, j, k, a = nextInt<ll>(1, 20);
    	j = k = nextInt<ll>(2, n - 1);
    	auto f = [&](ll x) { return (mul(x, x, n) + a) % n; };
    	while (g == 1) j = f(j), k = f(f(k)), g = gcd(j - k, n);
    	fn(g, ret), fn(n / g, ret);
    }
    ```
	
    첫 항 j와 k는 대충 랜덤으로 잡고 다항식의 상수항인 a는 무조건 랜덤으로 잡는다.
	
    while에서는 j는 한번 이동, k는 두번 이동 시킨다.
    - 1, 2번이 아닌 1, k번 이동해도 되지만 이러면 쓸데없이 계산시간이 늘어난다.
    이 반복문은 결국 j = k이거나 gcd(j - k, n)이 유의미한 결과를 냈을 때문 탈출한다.
    - 실제로 돌려보면 j = k인 경우가 나와 n이 인수분해가 안된채 그대로 fn(n), fn(1)을 호출하게 할 수도 있다.
	
    결국 이 방법의 시간복잡도를 결정하는 것은 일단 n이 합성수임을 판단한 이상 얼마나 효율적으로 진약수를 뽑아내느냐인데, 이는 n 개의 수 중 임의의 두 수를 ${n^{1\over2}}$번 정도 뽑는 독립시행을 했을 때 생일 역설에 의해 같은 수가 나올 확률이 상당히 높다는 것에 의거해 유의미한 진약수 gcd(j - k, n)이 탐지되기까지 걸리는 시간이 충분히 빠르다.

### 구현
- 플라드 로
    시간복잡도는 ${O(n^{1\over4})}$이다.
    ```cpp
    #include <bits/stdc++.h>
    using namespace std;
    typedef long long ll;
    
    ll n;
    
    namespace _miller {
    	ll mul(ll x, ll y, ll mod) { return (__int128)(x) * y % mod; }
    	ll poww(ll a, ll b, ll mod) {
    		ll ret = 1;
    		a %= mod;
    		while (b) {
    			if (b & 1) ret = mul(ret, a, mod);
    			a = mul(a, a, mod);
    			b >>= 1;
    		}
    		return ret;
    	}
    	bool miller_rabin(ll n, ll a) {
    		if (!(n % a)) return 0;
    		ll d = n - 1;
    		while (1) {
    			ll tmp = poww(a, d, n);
    			if (tmp == n - 1) return 1;
    			if (d & 1) return tmp == 1;
    			d >>= 1;
    		}
    	}
    	bool primei(ll n) {
    		if (n <= 1) return 0;
    		for (auto& a : { 2, 7, 61 }) {
    			if (n == a) return 1;
    			if (!miller_rabin(n, a)) return 0;
    		}
    	}
    	bool primell(ll n) {
    		for (auto& a : { 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37 }) {
    			if (n == a) return 1;
    			if (n > 40 && !miller_rabin(n, a)) return 0;
    		}
    		if (n <= 40) return 0;
    		return 1;
    	}
    }
    
    namespace _pollard {
    	void fn(ll n, vector<ll>& ret) {
    		if (n == 1) return;
    		if (!(n & 1)) {
    			ret.push_back(2);
    			fn(n >> 1, ret);
    			return;
    		}
    		if (_miller::primell(n)) {
    			ret.push_back(n);
    			return;
    		}
    		ll g = n, j, k, a;
    		auto f = [&](ll x) { return (_miller::mul(x, x, n) + a) % n; };
    		do {
    			if (g == n) {
    				j = k = rand() % (n - 2) + 2;
    				a = rand() % 20 + 1;
    			}
    			j = f(j), k = f(f(k)), g = gcd(abs(k - j), n);
    		} while (g == 1);
    		fn(g, ret), fn(n / g, ret);
    	}
    	vector<ll> factorize(ll n) {
    		vector<ll> ret;
    		fn(n, ret);
    		sort(ret.begin(), ret.end());
    		return ret;
    	}
    }
    
    int main() {
    	ios_base::sync_with_stdio(false);
    	cin.tie(NULL);
    	cout.tie(NULL);
    	cin >> n;
    	for (auto i : _pollard::factorize(n)) cout << i << '\\n';
    }
    ```