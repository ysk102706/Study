템플릿이란 매개변수의 타입에 따라 함수나 클래스를 생성하는 메커니즘을 의미하며, 템플릿은 타입이 매개변수에 의해 표현되므로, 매개변수화 타입이라고도 불린다. 

템플릿을 사용하면, 타입마다 별도의 함수나 클래스를 만들지 않고, 여러 타입에서 동작할 수 있는 단 하나의 함수나 클래스를 작성하는 것이 가능하다. 
```cpp
// 함수 템플릿
template <typename T> 
void Func(T t)
{
	std::cout << t;
}

int a = 1;
Func<int>(a);

// 클래스 템플릿
template <typename T> 
class Test 
{
public:
	T x;
};

Test<int> t;
```

##### Explicit Specialization(명시적 특수화) 
함수 템플릿은 특정 타입에 대한 명시적 특수화를 제공해서, 해당 타입에 대해 특별한 동작을 정의할 수 있다. 

컴파일러는 호출된 함수에 정확히 대응하는 특수화된 정의를 발견하면, 더는 템플릿을 찾지 않고 해당 정의를 사용한다. 
```cpp
// 함수 템플릿
template<>
void Func<int>(int a) 
{

}

// 클래스 템플릿
template<> class Test<int>
{

};

// 부분 특수화
template<typename T> class Test<T, int>
{

};

template<> class Test<int, int>
{

};
```

##### Nested Class template(중첩 클래스 템플릿)
클래스나 클래스 내에 또 다른 템플릿을 중첩해서 정의할 수 있으며, 이런 템플릿을 멤버 템플릿이라고 한다. 
멤버 템플릿 중에서도 클래스 템플릿을 중첨 클래스 템플릿이라고 한다. 
```cpp 
template <typename T>
class X
{
public:
	template <typename U>
	class Y
	{
	public:
		T Func(U x);
	};
}; 

template <typename T>
template <typename U>
T X<T>::Y<U>::Func(U x) 
{

}
``` 

##### 클래스 템플릿의 특징
- 하나 이상의 템플릿 인수를 가지는 클래스 템플릿을 선언할 수 있다. 
```cpp
template<typename T, int i> 
class X
{

};
```
- 클래스 템플릿에 디폴트 템플릿 인수를 명시할 수 있다. 
```cpp
template <typename T = int>
class X
{

};
```
- 클래스 템플릿을 기초 클래스로 해서 상속할 수 있다. 
```cpp 
template <typename T>
class Y : public X<T> 
{

};
```