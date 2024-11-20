Eular Angle(오일러 각)을 이용한 회전을 사용하다보면, Y축의 회전값이 90도 일 때, 나머지 두 축의 회전 방향이 똑같아지는 현상이 발생하며, 이 현상을 Gimbal Rock(짐벌락)이라고 한다. 

최근에는 사원수를 사용해서 회전을 표현하는 것이 일반적이다. 사원수를 사용하면 하나의 회전축에 대한 회전으로 3차원 회전을 정의할 수 있기 때문에 짐벌락 현상이 발생하지 않는다. 

사원수는 3차원 회전을 4개의 숫자로 표현하며, 절대값이 1이 되어야 한다. 또한 회전 행렬과 사원수 간의 변환을 이용하는 것이 보편적이며, 사원수를 사용하면 회전의 보간에 유리하다. 

### 사원수의 구성 
허수 3개와 실수 1개로 구성되어 있다. 
$$ q = xi + yj + zk + w = (u, w) $$
$$ i^2 = j^2 = k^2 = ijk = -1 $$
$$ ij = k = -ji $$
$$ jk = i = -kj $$
$$ ki = j = -ik $$

##### 사원수의 곱셈 
$$ q_aq_b = (a, w_a)(b, w_b) = ((x_a, y_a, z_a), w_a)((x_b, y_b, z_b), w_b) $$
$$ = (x_ai + y_aj + z_ak + w_a)(x_bi + y_bj + z_bk + w_b) $$
$$ = (w_aw_b - x_ax_b - y_ay_b - z_az_b) + (w_ax_b + w_bx_a + y_az_b - y_bz_a)i + (w_ay_b + w_by_a + z_ax_b - z_bx_a)j + (w_az_b + w_bz_a + x_ay_b - x_by_a)k $$
$$ = (w_ab + w_ba + a \times b, w_aw_b - a \cdot b) $$

##### 절대값 
$$ \|q\| = \|xi + yj + zk + w\| = \|(u, w)\| $$
$$ = \sqrt{x^2 + y^2 + z^2 + w^2} = \sqrt{\|u\|^2 + w^2} $$

##### Inverse Quaternion 
**Conjugate** 
$q = (u, w)$ 의 Conjugate는 $q^* = (-u, w)$ 이다. 

**Inverse** 
$$ qq^* = \|q\|^2 = \|q^*\|^2 $$
$$ q^{-1} = {q^*\over\|q\|^2} $$

만약 $\|q\| = 1$ 인 단위 사원수라면, $q^{-1} = q^*$ 이다. 

##### 회전 
$$ qpq^{-1} = qpq^* $$
$$ = (u, w)(v, 0)(-u, w) $$
$$ = (u, w)(wv - v \times u, v\cdot u) $$
$$ = ((w^2 - u \cdot v)v + 2(u \cdot v)u + 2w(u \times v), 0) $$

$q = (n\sin({\theta\over2}), \cos({\theta\over2})), \|n\| = 1$ 인 경우에 대해 정리하면, 
$$ qpq^* = \cos(\theta)v + (1 - \cos(\theta))(n\cdot v)n + \sin(\theta)(n \times v) $$
위와 같이 나오며, 이 식은 회전된 벡터를 구하는 식과 같다. 