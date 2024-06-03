C 언어에서는 malloc과 free 함수를 지원해 힙 상에서의 메모리 할당을 지원하였고, C++ 에서는 new와 delete를 통해서 메모리를 할당하고, 해제한다.

```cpp
#include <iostream>

int main() {
	int*p = new int;
	*p = 10;
	
	std::cout << *p << std::endl;
	
	delete p;
	return 0;
}
```

delete를 사용하여 무리하게 해제하려고 하는 경우 Heap이 아닌 공간을 해제하려고한다는 경고가 뜬다.

배열 또한 new와 delete로 할당 및 해제 할 수 있다.

```cpp
T* p = new T[size];
delete[] p;
```