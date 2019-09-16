## CompareStringOrdinal
* 今天工作时，需要用到字符串的比较，因为是在Windows下编程，所以WindowsAPI是我的首选。
  就选择了CompareStringOrdinal,但是在使用过程中，一直遇到明明两个相同的字符串，就是执行结果不相等。
  通过调试发现，原来是参数的原因，对于字符串的长度，在Unicode定义了的情况下，需要使用wcslen来获取，
  而不是sizeof或者__countof等计算方法。代码放送如下：
  
```
	TCHAR wnd_title[256];
	HWND hwnd = GetForegroundWindow();
	GetWindowText(hwnd, wnd_title, 256);
	DWORD dwPID;
	GetWindowThreadProcessId(hwnd, &dwPID);
	HANDLE handle = OpenProcess(PROCESS_QUERY_INFORMATION | PROCESS_VM_READ, FALSE, dwPID);
	if (handle){
		TCHAR Buffer[MAX_PATH];
		if (GetModuleFileNameEx(handle, 0, Buffer, MAX_PATH)){
			_tprintf(_T("Path: %s\n"), Buffer);
		}

		LPWSTR filename = PathFindFileName(Buffer);
		_tprintf(_T("filename = %s\n"), filename);

		LCID local = GetThreadLocale();
		
		if (CompareStringOrdinal(filename, wcslen(filename), _T("ApplicationFrameHost.exe"), wcslen(_T("ApplicationFrameHost.exe")), TRUE) == CSTR_EQUAL){
			_tprintf(_T("Edge Found"));
		}

		CloseHandle(handle);
```