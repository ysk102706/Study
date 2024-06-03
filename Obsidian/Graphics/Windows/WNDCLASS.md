윈도우 클래스의 속성을 정의하는 구조체로 윈도우를 생성할 때 필요한 다양한 정보를 포함하고 있다.

##### 구조
```cpp
typedef struct tagWNDCLASSA {
	// 클래스 스타일의 조합
	UINT style,

	// 윈도우 프로시저에 대한 포인터
	// CS_HREDRAW - 수평 폭에 대해 윈도우를 다시 그림
	// CS_VREDRAW - 수직 폭에 대해 윈도우를 다시 그림 
	// CS_DBLCLKS - 마우스가 더블클릭되는 메시지를 받는다. (미지정시 마우스더블클릭 메시지 무시됨)
	// CS_NOCLOSE - 시스템 메뉴중 닫기를 비활성화 시킴 (ALT + F4, 닫기버튼 모두 해당)
	// CS_CLASSDC - 클래스별로 만들어진 로컬 인스턴스의 모든 윈도우가 DC를 공유
	// CS_OWNDC - 로컬 인스턴스 내의 윈도우들이 개별적인 DC를 사용
	// CS_PARENTDC - 자식윈도우가 부모윈도우 DC를 사용
	// CS_GLOBALCLASS - 응용프로그램 전역 클래스로 등록
	// CS_SAVEBITS - 윈도우의 배경화면 영역을 비트맵으로 저장
	//등
	WNDPROC lpfnWndProc,
	
	// 윈도우 클래스에서 사용할 여분 메모리 바이트 수
	int cbClsExtra,
	
	// 윈도우 instance에서 사용할 여분 메모리 바이트 수
	int cbWndExtra,
	
	// 클래스의 윈도우 프로시저가 포함된 instance에 대한 핸들
	HINSTANCE hInstance,
	
	// 클래스 아이콘에 대한 핸들
	// IDI_ASTERISK - 정보 모양
	// IDI_ERROR - 에러 모양
	// IDI_EXCLAMATION - 경고 모양
	// IDI_QUESTION - 질문 모양
	HICON hIcon,
	
	// 클래스 커서에 대한 핸들
	// IDC_ARROW - 화살표 모양
	// IDC_CROSS - 크로스 모양
	// IDC_IBEAM - I 모양
	// IDC_NO - X 모양
	// IDC_WAIT - 모래시계
	HCURSOR hCursor,
	
	// 클래스 배경 브러시에 대한 핸들
	HBRUSH hbrBackground,
	
	// 클래스 메뉴의 리소스 이름
	LPCSTR lpszMenuName,
	
	// 등록하고자하는 윈도우 클래스의 이름
	LPCSTR lpszClassName
}
```

##### 등록, 생성 및 화면에 출력
``` cpp
// 등록
RegisterClass(&wc);

// 생성
hwnd = CreateWindow(
	// 윈도우 클래스 이름
	wc.lpszClassName,

	// 창 제목
	title.c_str(), 

	// 창 스타일
	// WS_OVErLAPPEDWINDOW - 메뉴, 최대화, 최소화, 제목, 표시줄, 프레임을 포함한 윈도우
	// WS_CAPTION - 제목 표시줄 표시
	// WS_HSCROLL - 수평 스크롤바 표시
	// WS_VSCROLL - 수직 스크롤바 표시
	// WS_MAXIMIZEBOX - 최대화 버튼 표시
	// WS_MINIMIZEBOX - 최소화 버튼 표시
	// WS_SYSMENU - 시스템 메뉴 표시
	// WS_THICKFRAME - 크기조걸 프레임 표시
	WS_OVERLAPPEDWINDOW,

	// 창의 초기 가로, 세로 위치
	0, 0, 

	// 창의 너비, 높이
	width, height, 

	NULL, NULL, 

	// 창과 연결된 인스턴스에 대한 핸들
	hInstance, 

	NULL
);

// 화면에 출력
// SW_HIDE - 원도우 숨김
// SW_MINIMIZE - 윈도우 최소화
// SW_RESTORE - 윈도우 활성화
// SW_SHOW - 윈도우 표시
// SW_SHOWNORMAL - 윈도우 퐐성화 후 표시
ShowWindow(hwnd, SW_SHOW);
UpdateWindow(hwnd);
```
