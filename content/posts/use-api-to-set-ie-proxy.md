+++
date = "2023-01-29T09:10:00+08:00"
draft = false
title = "use Windows API to set IE proxy"
tags = ["internet","soft"]

+++

使用 Windows API 设置 IE 代理服务器


### 缘
最近折腾各种代理框架，需要设置 IE 代理服务器。了解有两种方式，注册表和API方式。

注册表方式使用时，偶尔会出现设置异常，无法成功的情况，所以想实验一下API的方式。

通过Google，查到了在 [SysTutorials](www.systutorials.com) 上，ERIC MA 的一篇[文章](https://www.systutorials.com/setproxy-%E4%B8%80%E4%B8%AA%E8%AE%BE%E7%BD%AEie%E4%BB%A3%E7%90%86%E7%9A%84%E5%B0%8F%E5%B7%A5%E5%85%B7/)有相关实现。

因为原先的源代码是C++写的，编译后程序有点大，在原作者基础上用C语言稍微修改了下，控制下二进制大小。代码如下：


### 源代码
SetProxy.c

```
#include <stdio.h>
#include <windows.h>
#include <wininet.h>

BOOL SetConnectionOptions(LPCTSTR conn_name,LPCTSTR proxy_full_addr);
BOOL DisableConnectionProxy(LPCTSTR conn_name);

int main(int argc, char* argv[])
{
	int nRetCode = 0;
	
	//set proxy
	if (argc<2) { 
		printf("SetProxy Usage:\n\n");
		printf("Examples:\n");
		printf("SetProxy.exe 10.1.1.1:8080       -- set proxy to 10.1.1.1:8080.\n" );
		printf("SetProxy.exe \"\"                  -- disable proxy, set proxy to null.\n" );
		printf("\n");
		printf("Version 1.0.0\n");
		printf("Author: Eric Ma <mazq.eric@gmail.com>.\n\n");
		printf("Version 1.0.1\n");
		printf("Editor: Nivrrex <nivrrex@gmail.com>.");
		return 1;
	}
	
	const char *connection_name="";
	char* proxy_addr=argv[1];
	
	if (SetConnectionOptions(connection_name,proxy_addr)) {
		char msg[100]="Proxy ";
		if (strlen(proxy_addr)!=0) {
			strcat(msg,"set to ");
			strcat(msg,proxy_addr);
		}
		else {
			DisableConnectionProxy(connection_name);
			strcat(msg,"disabled");
		}
		printf("%s\n",msg);
	}
	return nRetCode;
}

BOOL SetConnectionOptions(LPCTSTR conn_name,LPCTSTR proxy_full_addr)
{
    //conn_name: active connection name. 
    //proxy_full_addr : eg "210.78.22.87:8000"
    INTERNET_PER_CONN_OPTION_LIST list;
    BOOL    bReturn;
    DWORD   dwBufSize = sizeof(list);
    // Fill out list struct.
    list.dwSize = sizeof(list);
    // NULL == LAN, otherwise connectoid name.
    list.pszConnection = conn_name;
    // Set three options.
    list.dwOptionCount = 2;//3;
    list.pOptions = (INTERNET_PER_CONN_OPTION*) malloc (sizeof(INTERNET_PER_CONN_OPTION) * 2/*3*/);
    // Make sure the memory was allocated.
    if(NULL == list.pOptions)
    {
        // Return FALSE if the memory wasn't allocated.
        OutputDebugString("failed to allocat memory in SetConnectionOptions()");
        return FALSE;
    }
    // Set flags.
    list.pOptions[0].dwOption = INTERNET_PER_CONN_FLAGS;
    list.pOptions[0].Value.dwValue = PROXY_TYPE_DIRECT |
        PROXY_TYPE_PROXY;
	
    // Set proxy name.
    list.pOptions[1].dwOption = INTERNET_PER_CONN_PROXY_SERVER;
    list.pOptions[1].Value.pszValue = proxy_full_addr;//"http://proxy:80";
	
	/*
    // Set proxy override.
    list.pOptions[2].dwOption = INTERNET_PER_CONN_PROXY_BYPASS;
    list.pOptions[2].Value.pszValue = "local";
	*/
	
    // Set the options on the connection.
    bReturn = InternetSetOption(NULL,
        INTERNET_OPTION_PER_CONNECTION_OPTION, &list, dwBufSize);
	
    // Free the allocated memory.
    free(list.pOptions);
	
    InternetSetOption(NULL, INTERNET_OPTION_SETTINGS_CHANGED, NULL, 0);
    InternetSetOption(NULL, INTERNET_OPTION_REFRESH , NULL, 0);
    return bReturn;
}


BOOL DisableConnectionProxy(LPCTSTR conn_name)
{
    //conn_name: active connection name. 
    INTERNET_PER_CONN_OPTION_LIST list;
    BOOL    bReturn;
    DWORD   dwBufSize = sizeof(list);
    // Fill out list struct.
    list.dwSize = sizeof(list);
    // NULL == LAN, otherwise connectoid name.
    list.pszConnection = conn_name;
    // Set three options.
    list.dwOptionCount = 1;
    list.pOptions = (INTERNET_PER_CONN_OPTION*) malloc (sizeof(INTERNET_PER_CONN_OPTION) * list.dwOptionCount);
    // Make sure the memory was allocated.
    if(NULL == list.pOptions)
    {
        // Return FALSE if the memory wasn't allocated.
        OutputDebugString("failed to allocat memory in DisableConnectionProxy()");
        return FALSE;
    }
    // Set flags.
    list.pOptions[0].dwOption = INTERNET_PER_CONN_FLAGS;
    list.pOptions[0].Value.dwValue = PROXY_TYPE_DIRECT  ;
    // Set the options on the connection.
    bReturn = InternetSetOption(NULL,
        INTERNET_OPTION_PER_CONNECTION_OPTION, &list, dwBufSize);
    // Free the allocated memory.
    free(list.pOptions);
    InternetSetOption(NULL, INTERNET_OPTION_SETTINGS_CHANGED, NULL, 0);
    InternetSetOption(NULL, INTERNET_OPTION_REFRESH , NULL, 0);
    return bReturn;
}

```

### 编译步骤
原始程序是 32 位的，原作者用 VC 编译的，我是在 Debian11 下，用 mingw64 编译的，具体编译步骤如下：

```
i686-w64-mingw32-gcc SetProxy.c -lwininet -O2 -o setproxy.exe
i686-w64-mingw32-strip --strip-unneeded setproxy.exe
```

### 使用方式

```
setproxy.exe 127.0.0.1:1080    #设置代理服务器
setproxy.exe ""                #取消代理服务器
```

### 结
比较注册表方式，用起来要稳定的多，就这样开心的用起来了~