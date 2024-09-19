여기서는 투명한 물체와의 빛의 굴절에 대해서 설명한다.

먼저 공기에서 유리로 들어가면서 굴절되는 경우랑 유리 안에서 공기로 나갈 때의 굴절은 다음과 같다.
- 공기->유리
	- -RayDir과 충돌 지점의 Normal 벡터 사이의 각도를 Theta1이라 하고, 충돌 지점에서 유리 안으로 들어가는 벡터와 Normal 벡터의 반대 벡터 사이의 각도를 Theta2라고 할 때, 다음 식을 만족한다. $$ {\sin\theta_1\over\sin\theta_2} = 1.5 $$
	  여기서 cos(Theta1)을 구할 수 있기 때문에 sin(Theta1) 또한 다음 식을 통해서 구할 수 있다. $$ \sin^2\theta_1 + \cos^2\theta_1 = 1 $$$$ \sin^2\theta_1 = 1 - \cos^2\theta_1 $$$$ \sin\theta_1 = \sqrt{1 - \cos^2\theta_1} $$
	  이렇게 sin(Theta1)을 구했고, 그러면 아래와 같이 식을 변경해서 sin(Theta2)를 구할 수 있다. $$ \sin\theta_2 = {\sin\theta_1\over1.5} $$
	  이렇게 sin(Theta2)를 구했으므로 cos(Theta2) 또한 구할 수 있다. 
	  
	  이제 필요한 값을 다 구했으니 유리 안으로 들어가는 벡터를 t라고하고 이것을 구하려면, 먼저 Ray의 시작 점과  Normal 벡터를 이으면서, Normal 벡터에 수직인 벡터 m을 구해야 한다. 
	  이건 아래 식으로 간단하게 구할 수 있다.$$ m - d = ((-d)\cdot n)n $$ $$ m = ((-d)\cdot n)n + d $$
	  이렇게 해서 m을 구할 수 있고, 이것을 통해서 t를 구하는 식을 세우면 다음과 같다. $$ t = normalize(-normal*\cos\theta_2 + m*\sin\theta_2) $$
- 유리->공기
	- 이것은 두각 Theta1과 Theta2의 관계가 다음과 같다. $$ {\sin\theta_1\over\sin\theta_2} = {1\over1.5} $$
	  또한 충돌 지점의 Normal 벡터를 양수가 아닌 음수의 것을 사용해야 한다.
	  나머지는 공기->유리와 같다.
 

[참고] https://samdriver.xyz/article/refraction-sphere