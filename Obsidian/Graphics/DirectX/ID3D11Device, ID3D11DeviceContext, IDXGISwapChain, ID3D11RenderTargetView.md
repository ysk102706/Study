ID3D11Device와 ID3D11DeviceContext는 DirectX에서 Graphics Pipeline을 정의하는 인터페이스이다.
물리적인 그래픽 장치 하드웨어에 대한 소프트웨어 제어

#### ID3D11Device
Graphics Pipeline에서 사용될 리소스를 생성하는데 사용된다.

#### ID3D11DeviceContext
생성된 리소스를 바인딩하고, GPU가 수행할 렌더링 명령들을 지시하는데 사용된다.

#### IDXGISwapChain
DirectX에서 [[SwapChain]]을 사용하기 위해 사용한다.

##### SwapChain 설정
``` cpp
// SwapChain 설정
DXGI_SWAP_CHAIN_DESC sc_desc;
ZeroMemory(&sc_desc, sizeof(sc_desc));
// 백버퍼 개수
sc_desc.BufferCount = 1; 
// 사용목적
sc_desc.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;
// 출력할 윈도우 핸들
sc_desc.OutputWindow = Window::GetHWND(); 
// 창 모드 여부
sc_desc.Windowed = true; 
// Buffer Swap 효과
sc_descSwapEffect = DXGI_SWAP_EFFECT_DISCARD;
// 버퍼 디스플레이 형식, RGBA 8비트, UNORM(0~1)
sc_desc.BufferDesc.Format = DXGI_FORMAT_R8G8B8A8_UNORM; 
// 버퍼 해상도 너비
sc_desc.BufferDesc.Width = Window::Width();
// 버퍼 해상도 높이
sc_desc.BufferDesc.Height = Window::Height(); 
// 멀티 샘플링 개수
sc_desc.SampleDesc.Count = 1;
// 이미지 품질 수준
sc_desc.SampleDesc.Quality = 0;
```
##### 백버퍼 설정
``` cpp
// 백버퍼 설정
// SwapChain을 생성한 뒤에 backbufferTexture를 통해서 백버퍼에 접근할 수 있도록 설정
ID3D11Texture2D* backbufferTexture;
ret = swapChain.Get()->GetBuffer(
	// 접근할 버퍼의 번호
	NULL,
	// 백버퍼를 받을 인터페이스의 타입
	__uuidof(ID3D11Texture2D), 
	// 반환된 백버퍼 인터페이스에 대한 포인터
	(void**)(&backbufferTexture)
);
```

#### ID3D11RenderTargetView
렌더타겟에 접근할 때 사용된다.
``` cpp
// 렌더타겟 뷰 생성
HRESULT ret = device.Get()->CreateRenderTargetView(
	// 렌더타겟 뷰가 엑세스하는 리소스
	backbufferTexture,
	// D3D11_RENDER_TARGET_VIEW_DESC의 포인터
	nullptr
	// ID3D11RenderTargetView의 포인터
	renderTargetView.GetAddressOf()
);

// 렌더타겟 뷰를 렌더타겟으로 설정
deviceContext.Get()->OMSetRenderTargets(
	// 렌더타겟 개수
	1,
	// 렌더타겟 뷰의 배열
	renderTargetView.GetAddressOf(),
	// 렌더링 파이프라인에 넘겨주는 깊이/스텐실 뷰의 포인터
	nullptr
);
```

#### Device, DeviceContext, SwapChain 생성
``` cpp
// Device, DeviceContext, SwapChain 생성
HRESULT ret = D3D11CreateDeviceAndSwapChain(
	nullptr,
	D3D_DRIVER_TYPE_HARDWARE,
	nullptr,
	0,
	nullptr,
	0,
	D3D11_SDK_VERSION,
	// DXGI_SWAP_CHAIN_DESC
	&sc_desc, 
	// IDXGISwapChain
	swapChain.GetAddressOf(), 
	// ID3D11Device
	device.GetAddressOf(),
	NULL,
	// ID3D11DeviceContext
	deviceContext.GetAddressOf()
);
```
