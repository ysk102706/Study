Bloom 효과를 만드는 방법은 다음과 같다.
1. 밝기가 낮은 픽셀은 완전히 검은색으로 바꾼다.
	- 밝기는 Relative Luminance를 이용해서 계산한다.
	- Relative Luminance Y = 0.2126 * R + 0.70152 * G + 0.0722 * B
2. 1에서 처리된 이미지를 Gaussian Blur를 통해서 부드럽게 만들어준다.
3. 원본 이미지와 2에서 만들어진 이미지를 더한다.