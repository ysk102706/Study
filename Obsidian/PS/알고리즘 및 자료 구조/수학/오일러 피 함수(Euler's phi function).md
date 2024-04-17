### 정의
- 오일러 피 함수 ${\phi(x)}$는 ${x}$와 서로소이고 ${x}$보다 작은 자연수(음이 아닌 정수)의 개수이다.
- 수식
    - ${\phi(x) = |\{k\in Z^+ : gcd(x, k) = 1\}|}$
    - ex) ${\phi(4)}$를 구한다고 할 때, 1, 2, 3 중에서 4와 서로소인 수는 1, 3 이므로 ${\phi(4) = 2}$이다.

### 동작 과정
1. phi를 1로 초기화한다.
    - 모든 수와 1은 서로소기 때문이다.
2. 2 부터 N까지 순회한다.
3. gcd(N, k)가 1이라면 phi에 1을 더해준다.

### 성질
- 만약 ${\alpha}$와 ${\beta}$가 서로소라면 아래 식이 항상 성립한다.
    - ${\phi(\alpha\beta) = \phi(\alpha)\phi(\beta)}$
- 소수 ${p}$의 거듭제곱에 대한 성질
    - ${\phi(p^\alpha) = p^\alpha - p^{\alpha - 1}}$
- 소수 ${p}$에 대한 오일러 피 함수 값
    - ${\phi(p) = p - 1}$

### 구현

- 오일러 피 함수
    - 2부터 ${x}$${N}$${k}$${N}$각 ${k}$마다 ${gcd}$를 확인하기 때문에 시간복잡도는 ${O(NlogN)}$이다.
    ```cpp
    // 가장 간한단 구현
    #include <iostream>
    #include <cstring>
    using namespace std;
    
    int phi;
    
    // 유클리드 호제법
    int gcd(int x, int k) {
    	int r = x % k;
    	while (r != 0) {
    		x = k;
    		k = r;
    		r = x % k;
    	}
    	return k;
    }
    
    int main() {
    	// 모든 수는 1과 서로소이기 때문에 1로 초기화
    	phi = 1;
    
    	// 오일러 피 함수
    	// φ(100)
    	for (int k = 2; k < 100; k++) {
    		if (gcd(100, k) == 1) phi++;
    	}
    }
    ```
- 최적화
    오일러 피 함수의 성질을 이용해서 최적화를 할 수 있다.
    핵심은 ${x}$를 소인수 분해하는 것이다.
    ${x}$를 소인수 분해하면 ${x}$를 다음과 같이 표기할 수 있다.
    ${x = {p_1}^{a_1}{p_2}^{a_2}\dots{p_k}^{a_k}}$
    여기서 각각의 소수는 서로소이다.
    그러므로
    ${\phi(\alpha\beta) = \phi(\alpha)\phi(\beta)}$를 이용해서
    ${\phi(x) = \phi({p_1}^{a_1})\phi({p_2}^{a_2})\dots\phi({p_k}^{a_k})}$와 같이 쓸수 있고,
    여기서 각각의 피함수 값은
    ${\phi({p}^{a}) = {p}^{a} - {p}^{a-1}}$
    ${\phi(p) = p - 1}$
    로 구할 수 있다.
    따라서 소수에 대한 전처리(에라토스테네스의 체)가 되있다고 가정하면 시간 복잡도는 ${O(\sqrt{N})}$이다.
    만약 폴라드-로 같이 더 빠른 소인수 분해 알고리즘을 사용하면 시간 복잡도는 ${O(\sqrt[4]N)}$이다.
    - 에라토스테네스의 체
        ```cpp
        #include <iostream>
        #include <cmath>
        using namespace std;
        typedef long long ll;
        
        ll n, sq, phi, era[1000001];
        
        void eratos(ll x) {
        	sq = sqrt(x);
        	era[0] = 1; era[1] = 1;
        	for (ll i = 2; i <= sq; i++) {
        		if (era[i]) continue;
        		for (ll j = i * 2; j <= sq; j += i) era[j] = 1;
        	}
        }
        
        int main() {
        	cin >> n;
        	eratos(n);
        	phi = 1;
        	for (ll i = 2; i <= sq; i++) {
        		if (era[i]) continue;
        		ll cnt = 0;
        		while (!(n % i)) {
        			cnt++;
        			n /= i;
        		}
        		if (!cnt) continue;
        		else if (cnt == 1) phi *= i - 1;
        		else phi *= pow(i, cnt) - pow(i, cnt - 1);
        	}
        	if (n != 1) phi *= n - 1;
        	cout << phi;
        }
        ```
    - 플라드 로, 밀러-라빈 소수 판별법
        ```cpp
        #include <bits/stdc++.h>
        using namespace std;
        typedef long long ll;
        
        ll n, phi = 1, sq;
        
        namespace _miller {
        	ll mul(ll x, ll y, ll mod) { return (__int128)(x) * y % mod; }
        	ll _pow(ll a, ll b, ll mod) {
        		ll ret = 1;
        		a %= mod;
        		while (b) {
        			if (b & 1) ret = mul(ret, a, mod);
        			a = mul(a, a, mod);
        			b >>= 1;
        		}
        		return ret;
        	}
        	bool miller(ll n, ll a) {
        		if (!(n % a)) return 0;
        		ll d = n - 1;
        		while (1) {
        			ll tmp = _pow(a, d, n);
        			if (tmp == n - 1) return 1;
        			if (d & 1) return tmp == 1;
        			d >>= 1;
        		}
        	}
        	bool isPrime(ll n) {
        		for (auto& a : { 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37 }) {
        			if (n == a) return 1;
        			if (n > 40 && !miller(n, a)) return 0;
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
        		if (_miller::isPrime(n)) {
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
        	vector<ll> v = _pollard::factorize(n);
        	v.erase(unique(v.begin(), v.end()), v.end());
        	for (auto a : v) {
        		int cnt = 0; 
        		while (!(n % a)) {
        			cnt++;
        			n /= a;
        		}
        		if (cnt == 1) phi *= a - 1;
        		else phi *= pow(a, cnt) - pow(a, cnt - 1);
        	}
        	if (n != 1) phi *= n - 1;
        	cout << phi;
        }
        ```