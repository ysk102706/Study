객체의 private 멤버는 해당 객체의 public 멤버 함수를 통해서만 접근할 수 있지만, 경우에 따라서는 해당 객체의 멤버 함수가 아닌 함수도 private 멤버에 접근해야만 할 경우가 있다. 

이럴 때마다 매번 새로운 public 함수를 만드는 것은 매우 비효율적이기 때문에 이러한 경우에 사용하는 것이 friend 라는 접근 제어 키워드이다. 

프렌드는 지정한 대상에 한해 해당 객체의 모든 멤버에 접근할 수 있는 권한을 부여해 준다. 
- friend 키워드는 전역 함수, 클래스, 멤버 함수의 세 가지 형태로 사용할 수 있다. 
```cpp 
// friend 메서드 
class Test 
{
private: 
	int x;
public: 
	friend void Func1(Test& t);
};

void Func1(Test& t) 
{
	std::cout << t.x << '\n';
} 

int main()
{
	Test t;
	Func1(t);
}

// friend 클래스 
class Test1
{
private: 
	int x;
public: 
	friend class Test;
};

class Test
{ 
public: 
	void Func1(Test1& t);
};

void Test::Func1(Test1& t) 
{
	std::cout << t.x << '\n';
} 

int main()
{
	Test1 t1;
	Test t;
	t.Func1(t1);
} 

// friend 멤버 함수
class Test1;

class Test
{ 
public: 
	void Func1(Test1& t);
};

class Test1
{
private: 
	int x;
public: 
	friend void Test::Func1(Test1& t);
};

void Test::Func1(Test1& t) 
{
	std::cout << t.x << '\n';
} 

int main()
{
	Test1 t1;
	Test t;
	t.Func1(t1);
} 
```