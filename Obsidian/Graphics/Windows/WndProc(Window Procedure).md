wWinMain 함수와는 별도로 메시지가 발생할 때 프로그램의 반응을 처리하는 일을 담당한다. 메시지 루프는 메시지 처리 함수로 메시지를 보내기만 한다.

``` cpp
LRESULT CALLBACK WndProc(
	HWND hwnd, 
	UINT msg, 
	WPARAM wParam, 
	LPARAM lParam) 
{
	switch (msg) {
	// 창이 제거될 때
	case WM_DESTROY:
		// WM_QUIT 메시지를 보내며, WM_QUIT은 메지시 루프의 GetMessage()의 반환을 0으로 만들어 프로그램을 종료시킨다.
		PostQuitMessage(0);
		return 0;
	...
	}

	// 위의 switch문에서 처리되지 않은 메시지들을 처리한다.
	return DefWindowProc(hwnd, msg, wParam, lParam);
}
```