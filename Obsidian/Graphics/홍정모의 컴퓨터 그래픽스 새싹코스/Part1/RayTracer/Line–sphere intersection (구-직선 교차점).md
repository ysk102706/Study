구와 직선이 있다고 할 때, 이들의 관계는 3가지의 경우로 나타난다.
- 충돌하지 않은 경우
- 직선이 구의 끝에서 충돌해서 교차점이 1개인 경우
- 직선이 구를 관통해서 교차점이 2개인 경우

구에서는 다음 식이 성립한다.
$$ |x - c|^2 = r^2 $$ 
- x : 구의 면 위의 한 점의 위치
- c : 구의 중심 위치
- r : 반지름

여기서 직선의 방정식을 다음과 같이 정의한다.
$$ x = o + du $$

- x : 직선 위 한 점의 위치
- o : 직선의 시작 위치
- d : 직선이 시작 점으로부터 떨어진 거리
- u : 직선의 방향

충돌 위치를 찾으려면 위 두 식의 x가 같아지는 지점을 찾으면 된다.
따라서 식은 다음과 같이 전개할 수 있다.
$$ ||o + du - c||^2 = r^2 \ \Leftrightarrow \ (o + du - c) \cdot (o + du - c) = r^2 $$
$$ o\cdot o \ + \ o\cdot du \ - \ c\cdot o \ + \ du\cdot o \ + \ d^2(u\cdot u) \ - \ du\cdot c \ - \ c\cdot o \ - \ du\cdot c \ + \ c\cdot c \ - \ r^2 \ = \ 0 $$
$$ d^2(u\cdot u) \ + \ 2d(u\cdot o) \ - \ 2d(u\cdot c) \ + \ o\cdot o \ - \ 2(c\cdot o) \ + \ c\cdot c \ - \ r^2 \ = \ 0 $$
$$ d^2(u\cdot u) \ + \ 2d[u\cdot (o - c)] \ + \ (o - c)\cdot (o - c) \ - \ r2 \ = \ 0 $$

이것을 d에 대한 이차 함수로 생각하면, 근의 공식으로 풀리므로 다음과 같이 전개된다.
$$ d \ = \ {-2[u\cdot(o-c)] \ \pm \ \sqrt{(2[u\cdot(o-c)])^2 \ - \ 4||u||^2(||o - c||^2 - r^2)}\over2||u||^2} $$

따라서 직선에 대한 식에 여기서 구한 d를 대입하면 충돌한 위치인 x를 알 수 있다.

여기서 더 간단하게 할 수 있는데,
루트 안쪽 부분을 4로 묶은 뒤에 루트 밖으로 뺄 수 있으므로 식을 다음과 같이 바꿀 수 있다.
$$ d = {-2[u\cdot(o-c)] \ \pm \ \sqrt{4([u\cdot(o-c)]^2 - \Vert u\Vert^2(\Vert o-c\Vert^2 - r^2))}\over2\Vert u\Vert^2} $$
$$ d = {-2[u\cdot(o-c)] \ \pm \ 2\sqrt{[u\cdot(o-c)]^2 - \Vert u\Vert^2(\Vert o-c\Vert^2 - r^2)}\over2\Vert u\Vert^2} $$
$$ d = {-[u\cdot(o-c)] \ \pm \ \sqrt{[u\cdot(o-c)]^2 - \Vert u\Vert^2(\Vert o-c\Vert^2 - r^2)}\over\Vert u\Vert^2} $$

여기서 u는 단위 벡터이기 때문에 길이가 1이므로 다음과 같이 바꿀 수 있다.
$$ d = -[u\cdot(o-c)] \ \pm \ \sqrt{[u\cdot(o-c)]^2 - (\Vert o-c\Vert^2 - r^2)} $$
그리고 여기서 루트의 안쪽 부분을 delta라고 할 때, 
delta < 0이면, 교차점이 없고, 
delta = 0이면, 교차점이 1개이고,
delta > 0이면, 교차점이 2개이다.