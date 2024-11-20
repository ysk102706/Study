Fresnel 효과는 물체를 바라볼 때, 바라보는 각도가 수평에 가까울수록 반사가 강해지고, 수직에 가까울수록 투과가 강해지는 효과이다. 

Specualr가 반사광이기 때문에 Specular를 통해서 Fresnel 효과를 나타낸다. 

Fresnel 효과는 Cook-Torrance, Schlick 두 가지 정도로 구현할 수 있으며, CookTorrance 보다 Schlick의 방식이 더 가볍다. 

##### Cook-Torrance 
$$ F = 0.5*{(g - c)^2\over(g + c)^2}*(1 + {{(c(g + c) - 1)^2}\over{(c(g - c) + 1)}^2}) $$
$$ c = V\cdot H $$
$$ g = \sqrt{n^2 + c^2 - 1} $$
$$ n = {1 + \sqrt{F_0}\over1 - \sqrt{F_0}} $$

##### Schlick 
$$ F = F_0 + (1 - F_0)(1 - V\cdot H)^5 $$

