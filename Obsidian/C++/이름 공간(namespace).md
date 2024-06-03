어떤 정의된 객체에 대해 어디 소속인지 지정해주는 것이다.

코드의 크기가 늘어나거나, 다른 사람이 쓴 코드를 가져와 쓰는 경우 중복된 이름이 많아지게되서 C++은 이를 구분하기 위해 같은 이름이라도 소속된 **이름 공간**이 다르면 다른 것으로 취급하게 하였다.

```cpp
std::cout
```

위의 경우에는 std:: 없이 그냥 쓰게 되면 컴파일러가 cout을 찾지 못한다.

이름 공간을 정의하는 방법은 다음과 같다.

```cpp
namespace Temp {
	int Func1();
	void Func2();
}
```

자기자신이 포함되어 있는 이름 공간 안에서는 굳이 이름 공간을 명시하지 않아도 사용할 수 있지만 다른 이름 공간에 있다면 이름 공간을 명시해야한다.

```cpp
// TempNamespace.h
namespace Temp1 {
	int Func1();
	void Func2();
}

namespace Temp2 {
	int Func1();
	void Func2();
}

// TempNamespace.cpp
#include "TempNamespace.h"

namespace Temp1 {
	int func() {
		Func1();
		Temp2::Func1();
	}
}
```

또한 어떠한 이름 공간에도 속하지 않은 경우에는 명시적으로 이름 공간을 명시해야한다.

```cpp
#include "TempNamespace.h"

int main() {
	Temp1::Func1();
}
```

하지만 Func1이 반복적으로 호출되는 경우에는 매번 Temp1을 붙이는 것이 번거로울 수 있는데, 이때는 다음과 같이 할 수 있다.

```cpp
#include "TempNamespace.h"

using Temp1::Func1;

int main() {
	Func1();
}
```

Func1 뿐만아니라 이름 공간의 모든 내용을 가져오고 싶다면, 다음과 같이 할 수 있다. 몰론 이때도 Temp2에 있는 Func1함수를 사용할 수 있다.

```cpp
#include "TempNamespace.h"

using namespace Temp1

int main() {
	Func1();
	Temp2::Func1();
}
```

## 주의 사항

using namespace std; 와 같이 어떤 이름 공간을 사용하겠다라고 선언하는 것은 권장되지 않는다. 해당 이름 공간의 함수와 이름이 겹치는 함수를 만들게 되면 오류가 나기 때문이다.

게다가 C++ 표준 라이브러리는 매우 거대해 수 많은 함수들이 존재하고 있다. 그렇기 때문에 자칫 잘못해 이름을 겹치게 사용하면 고치느라 시간을 많이 잡아먹을 수 있다.

때문에 이름 공간을 직접 명시하는 것을 권장한다.

## 추가 사항

이름 공간에 이름을 지정하지 않을 수도 있다.

이 경우에는 이름 공간이 정의된 해당 파일 안에서만 접근할 수 있다.

```cpp
#include <iostream>

namespace {
	int Func1();
}

int main() {
	Func1();
}
```

아래의 경우는 불가능

```cpp
// TempNamespace.h
namespace Temp1 {
	int Func1();
}

// Tempnamespace.cpp
#include <iostream>
#include "TempNamespace.h"

namespace Temp1 {
	int Func1() { std::cout << "1"; }
}

int main() {
	Func1();
}
```