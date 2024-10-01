프로그램에서 정의된 함수는 프로그램이 실행될 때 모두 메인 메모리에 올라간다.
이때 함수의 이름은 메모리에 올라간 함수의 시작 주소를 가리키는 포인터 상수가 된다.
그리고 함수의 시작 주소를 가리키는 포인터 상수를 함수 포인터라고 한다.

```cpp
// 함수 원형
void Func(int, int);

// 함수 포인터
void (*ptr_func)(int, int);
```

함수 포인터는 함수를 또 다른 함수의 인수로 전달할 때 유용하게 사용된다.
```cpp
#include <iostream>
using namespace std;

int add(int a, int b) {
	return a + b;
}

int sub(int a, int b) {
	return a - b;
}

int FP(int a, int b, int (*func)(int, int)) {
	return func(a, b);
}

int main() {
	int (*func)(int, int) = NULL;

	func = add;

	cout << FP(1, 2, func) << '\n';

	func = sub;

	cout << FP(4, 3, func) << '\n';
}
```

이렇게 사용하면 표기법이 상당히 복잡해지는데 이를 해결하기 위해서 2가지 방법을 사용할 수 있다.
```cpp
// typedef 키워드 이용
typedef int (*Func)(int, int);
Func ptr_func = Function;

// auto 키워드 이용 
auto ptr_func = Function;
```