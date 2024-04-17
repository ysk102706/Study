### 정의
- 선입선출(First In First Out, FIFO)의 자료구조
- 대기열이라고도하고, Queue라고도 함

![](https://i.imgur.com/SlaXOFM.png)


### 연산
- 삽입(Enqueue)
- 삭제(Dequeue)

### 구현
```cpp
#include <queue>

// T는 자료
queue<T> q;
```

### 큐의 활용
- 원형 큐(Circular Queue) : 큐를 배열을 이용할 경우 쓰다보면 배열의 앞이 비게되는 점을 이용해 마지막 부분을 채우면 다시 처음 부터 채우기 시작한다.
	![](https://i.imgur.com/uYcOKEO.png)
- 우선순위 큐(Priority Queue) : 원소들에 우선순위를 매겨서 넣을 때의 순서와 상관없이 뺄 때는 우선순위가 가장 높은 것 부터 뺀다. ⇒ 대표적인 예시로 Heap이 있는데 우선순위 큐를 Heap으로 구현하기도 한다.
    
    ```cpp
    #include <queue>
    
    // T는 자료형
    priority_queue<T, vector<T> greater<T>> pq; // 오름 차순
    priority_queue<T, vector<T> less<T>> pq; // 내림 차순
    ```
    
- 덱(Deque) : 일반적인 큐와 달리 앞과 뒤 모두에서 삽입과 삭제가 가능하다. 이중 연결리스트를 이용해 구현할 수 있다.
    ![](https://i.imgur.com/ey5fYl9.png)
### 활용 가능 문제
- 큐 구현 문제(백준 10845 큐, 백준 1966 프린터 큐)
- 큐 활용(백준 11866 요세푸스 문제 0, 백준 2164 카드2)
- 우선 순위 큐 활용(백준 1927 최소 힙, 백준 11279 최대 힙, 11286 절댓값)
- 덱 활용(백준 10866 덱)