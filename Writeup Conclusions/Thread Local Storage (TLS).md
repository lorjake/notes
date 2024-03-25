TLS : Every thread of a process shares its virtual address space. The local variable of a function is unique to each thread. But what about global and static variables. They are shared by all the threads in a process. So, it can happen that in a multithreaded program, these global variables are being accessed and changed by one thread.

We have TLS callbacks. (functions that passed to another function are callbacks)
TLS callbacks execute before the entry point of executable.
```
#include "stdafx.h"
#include <Windows.h>
#include <cstdio>
__declspec(thread) int a;
void NTAPI on_tls_callback(void* dll, DWORD reason, void* reserved)
{
    a = 543;
    MessageBox(nullptr, L"In TLS", L"In TLS", MB_OK);
}

#pragma comment (linker, "/INCLUDE:__tls_used")
#pragma comment (linker, "/INCLUDE:__xl_b")
#pragma data_seg(".CRT$XLB")
EXTERN_C

PIMAGE_TLS_CALLBACK _xl_b = on_tls_callback;
#pragma data_seg()

int main()
{
    printf("%d\n", a);
    MessageBox(nullptr, L"In Main", L"In Main", MB_OK);
    return 0;
}
```

Address : PE header -> optional headers -> TLS Directory
In TLS Directory we have a structure named `IMAGE_TLS_DIRECTORY`.
This structure contains address of a pointer to a null terminated array that store callback addresses.
