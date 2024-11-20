IBL은 큐브맵 이미지를 활용한 라이팅 기술이며, Diffuse와 Specular을 통해서 라이팅을 적용한다. 

Diffuse와 Specular의 큐브맵은 한번의 샘플링만으로도 어떤 위치에서 받을 빛의 양을 얻을 수 있도록 미리 처리해 놓은 큐브맵이다. 

Diffuse를 샘플링할 때는 해당 위치의 Normal 방향으로 샘플링을 하며, Specular를 샘플링할 때는 시야 벡터를 Normal에 대해서 반사시킨 벡터로 샘플링한다. 

IBL은 Diffuse와 Specular를 샘플링한 것의 합으로 마무리한다. 