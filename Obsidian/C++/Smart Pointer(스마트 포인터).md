C++에서는 메모리 누수(memory leak)로부터 프로그램의 안전성을 보장하기 위해 스마트 포인터를 제공하고 있다. 

스마트 포인터란 포인터처럼 동작하는 클래스 템플릿으로, 사용이 끝난 메모리를 자동으로 해제해 준다. 

##### 스마트 포인터의 동작 
보통 new 키워드를 사용하여 기본 포인터(raw pointer)가 실제 메모리를 가리키도록 초기화한 후에, 기본 포인터를 스마트 포인터에 대입하여 사용한다. 

이랗게 정의된 스마트 포인터의 수명이 다하면, 소멸자는 delete 키워드를 사용하여 할당된 메모리를 자동으로 해제한다. 
- 스마트 포인터를 사용하면 메모리를 해제할 필요가 없다. 

### 스마트 포인터의 종류 
C++11 이전에는 auto_ptr이라는 스마트 포인터를 사용하였지만, C++11부터는 다음과 같은 스마트 포인터를 사용한다. 
- unique_ptr 
- shared_ptr 
- weak_ptr 

스마트 포인터는 memory 헤더 파일에 정의되어 있다. 

##### unique_ptr 
unique_ptr은 하나의 스마트 포인터만이 특정 객체를 소유할 수 있도록, 객체에 소유권 개념을 도입한 스마트 포인터이다. 

이 스마트 포인터는 해당 객체의 소유권을 가지고 있을 때만, 소멸자가 해당 객체를 삭제할 수 있으며, move() 멤버 함수를 통해 소유권을 이전할 수는 있지만, 복사는 할 수 없다. 
```cpp 
unique_ptr<int> ptr(new int(5));
```

C++14 이후부터 제공되는 make_unique() 함수를 사용하면 unique_ptr 인스턴스를 안전하게 생성할 수 있다. 
make_unique()는 전달받은 인수를 사용해 지정된 타입의 객체를 생성하고, 생성된 객체를 가리키는 unique_ptr을 반환한다. 
```cpp 
unique_ptr<int> ptr = make_unique<int>(5); 

// 객체에 대한 unique_ptr 
class Test
{ 
public:
	Test(int x, double y);
private: 
	int _x;
	double _y;
};

unique_ptr<Test> test = make_unique<Test>(1, 3.0f);
``` 

##### shared_ptr 
shared_ptr은 하나의 객체를 참조하는 스마트 포인터가 총 몇 개인지를 참조하는 스마트 포인터이다. 
- 이렇게 참조하고 있는 스마트 포인터의 개수를 참조 횟수라고 한다. 

참조 횟수는 특정 객체에 새로운 shared_ptr이 추가될 때마다 1씩 증가하며, 수명이 다할 때마다 1씩 감소한다. 
- 마지막 shared_ptr의 수명이 다해서, 참조회수가 0이 되면, delete 키워드를 사용해서 메모리를 자동으로 해제한다. 

```cpp 
shared_ptr<int> ptr01(new int(5)); 
cout << ptr01.use_count() << endl; // 1
auto ptr02(ptr01); 
cout << ptr01.use_count() << endl; // 2
auto ptr03 = ptr01;
cout << ptr01.use_count() << endl; // 3
``` 

shared_ptr은 make_share() 함수를 통해서 shared_ptr 인스턴스를 안전하게 생성할 수 있다. 

```cpp 
shared_ptr<int> ptr = make_shared<int>(5); 

// 객체에 대한 shared_ptr 
class Test
{ 
public:
	Test(int x, double y);
private: 
	int _x;
	double _y;
};

shared_ptr<Test> test = make_shared<Test>(1, 3.0f); 
``` 

##### weak_ptr 
weak_ptr은 하나 이상의 shared_ptr 인스턴스가 소유하는 객체에 대한 접근을 제공하지만, 소유자의 수에는 포함되지 않는 스마트 포인터이다. 

shared_ptr은 참조 횟수를 기반으로 동작하는 스마트 포인터이다. 
여기서 만약 서로가 상대방을 가리키는 shared_ptr을 가지고 있다면, 참조 횟수는 절대 0이 되지 않으므로 메모리는 영원히 해제되지 않는다. 

이렇게 서로가 상대방을 참조하고 있는 상황을 순환 참조라고 한다. 

weak_ptr은 shared_ptr 인스턴스 사이의 순환 참조를 제거하기 위해서 사용된다. 

