많은 STL 알고리즘이 데이터를 처리하기 위해 매개 변수로 함수 객체를 받아들인다. 
functor라고도 불리는 함수 객체는 ()(호출 연산자)와 함께 사용할 수 있는 객체를 의미한다. 
- 함수 객체는 타입을 선언하고, 해당 클래스에서 호출 연산자를 오버로딩하여 구현하게 된다. 

함수 객체의 장점은 다음과 같다. 
- 함수 객체는 상태를 포함할 수 있다. 
- 함수 객체는 타입이므로, 템플릿 인수로 사용할 수 있다. 

STL은 자주 사용할만한 연산에 대해 몇 가지 기본적인 함수 객체를 미리 정의하여 제공한다. 
- functional 헤더 파일에 정의되어 있다. 
- plus : + 
- minus : -
- multiplies : * 
- divides : /
- modulus : % 
- negate : - 
- equal_to : == 
- not_equal_to : != 
- greater : > 
- greater_equal : >= 
- less : < 
- less_equal : <= 
- logical_and : && 
- logical_or : || 
- logical_not : ! 

