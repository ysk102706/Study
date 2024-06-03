렌더타겟의 렌더링 영역에 관한 설정이며, 렌더타겟에 넓이와 높이, 깊이 값으로 렌더링 영역을 설정할 수 있다. 렌더타겟에 대한 설정이기 때문에 뷰포트는 렌더타겟 별로 설정해야한다. 

``` cpp
// 뷰포트의 구조체
typedef struct D3D11_VIEWPORT
{
    FLOAT TopLeftX; // 좌측 상단의 x좌표 
    FLOAT TopLeftY; // 좌측 상단의 y좌표
    FLOAT Width; // 영역의 너비
    FLOAT Height; // 영역의 높이
    FLOAT MinDepth; // 최소 깊이
    FLOAT MaxDepth; // 최대 깊이
} D3D11_VIEWPORT;
```

``` cpp
// 뷰포트 설정
viewport.TopLeftX = 0;
viewport.TopLeftY = 0;
viewport.Width = width;
viewport.Height = width;
viewport.MinDepth = 0.0f;
viewport.MaxDepth = 1.0f;

// 뷰포트를 바인딩한다.
deviceContext.Get()->RSSetViewports(1, &viewport);
```