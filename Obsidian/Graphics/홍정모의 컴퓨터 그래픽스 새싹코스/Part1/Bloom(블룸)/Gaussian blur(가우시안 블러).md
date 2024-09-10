### Blur(블러)
이미지를 부드럽게 해주는 이미지 필터의 일종

### Convolution
Kernel(커널)을 이미지의 모든 픽셀에 적용시키는 과정

- 이미지에서 Kernel과 대응되는 부분만 가져와서 각각의 위치에 대응되는 값들끼리 곱하고, 곱한 결과들을 모두 더해준다. 
  ```
	kernel
	 0 -1  0
	-1  5 -1
	 0 -1  0

	image
	 2  5  3
	 3  7  4
	 7  4  3

	pixel_value = 
		(0 * 2) + (-1 * 5) + (0 * 3)
		+ (-1 * 3) + (5 * 7) + (-1 * 4)
		+ (0 * 7) + (-1 * 4) + (0 * 3)
	```
- Kernel이 이미지의 영역을 벗어날 때는 가장 가까운 값을 가져온 뒤 계산한다.

### Separable Convolution
원래는 2D Kernel을 사용해서 주변의 값들을 평균을 내는 방식을 사용하지만, 이렇게 하면 연산량이 많아진다.
```
// 일반적인 Convolution에서 확인하는 픽셀 영역
o o o o o 
o o o o o 
o o o o o 
o o o o o 
o o o o o 
```

Separable Convolution은 1D Kernel을 가로와 세로로 나누어서 두 번 적용함으로써 연산량을 줄인 형태이다.
```
// Separable Convolution에서 확인하는 픽셀 영역
x x o x x
x x o x x
o o o o o
x x o x x
x x o x x
```

### Box Blur(박스 블러)
박스 블러는 픽셀의 가중치를 모두 똑같게 해서 계산하는 방식이다.
예를 들어 5 칸짜리 Kernel이라고 하면, Kernel의 값은 {0.2, 0.2, 0.2, 0.2, 0.2}가 된다.

### Gaussian Blur(가우시안 블러)
가우시안 블러는 박스 블러와는 다르게 가운데의 가중치를 높게 둔다.
많이 사용하는 값은 {0.0545, 0.2442, 0.4026, 0.2442, 0.0545}이다. 

