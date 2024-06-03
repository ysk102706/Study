Primitive는 더 작은 상태로 쪼개거나 분해할 수 없는 무언가 또는 기하학적인 형태를 뜻한다. 기본적인 타입으로 Point List, Line List, Line Strip, Triangle List, Triangle Strip 등이 있다.

#### Point List
- 모든 정점은 개별적인 점으로 그려진다.
#### Line List
- 정점 두 개가 개별적인 하나의 선분을 형성한다.
- 2n개의 정점으로 n개의 선분이 만들어진다.
#### Line Strip
- 정점들이 차례대로 이어져 일련의 선분들이 그려진다.
- Line List와 다르게 자동으로 선분이 이어진다.
#### Triangle List
- 정점 세 개가 하나의 개별적인 삼각형을 형성한다. 
- 3n개의 정점으로 n개의 삼각형이 만들어진다.
#### Triangle Strip
- 정점들이 연결되어 일련의 삼rkr형들을 형성한다.
- n개의 정점으로 n - 2개의 삼각형들이 만들어진다.