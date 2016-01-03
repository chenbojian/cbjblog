title: 个人windows工具总结
date: 2015-07-03 21:17:02
tags: windows
---

## ConEmu

终于不用忍受原始cmd或者powershell难看的界面和难看的字体了。

目前存在一个小问题，我之前一般会把常用的环境变量配置在一个bat文件中，比如cbjps.bat，每次要启动命令行只需要WIN+R，然后输入cbjps即可解决。之前的bat代码如下：

```bat
@echo off
set path=%path%;C:\python27
set path=%path%;C:\python27\scripts
call "C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\vcvarsall.bat" x64
powershell
```

用了ConEmu之后将powershell改成start PATH\TO\ConEmu\ConEmu.exe后可以使用，但是会一闪而过一个cmd窗口，并且ConEmu的窗口不会自动前置，强迫症表示无法忍受，于是想到了用没有窗口的pythonw来启动，解决了窗口不会自动前置的问题。

```bat
@echo off
set path=%path%;C:\python27
set path=%path%;C:\python27\scripts
call "C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\vcvarsall.bat" x64
pythonw D:\chenbojian\cbjtool\ConEmu\conemu.pyw
```

python代码就非常简单了

```python
import subprocess as sp
sp.Popen(r"D:\chenbojian\cbjtool\ConEmu\ConEmu64.exe")
```

不过有个一闪而过的窗口还没能解决，困难点主要在于cmd可以通过call***.bat来设置环境变量，而powershell简单的这么做，不然就可以把所有的环境配置到powershell的$PROFELE的文件中了，列一下我的$PROFILE内容

```powershell
$env:PATH=$env:PATH+";C:\Program Files\iojs\"+";"+$env:APPDATA+"\npm"
$env:JAVA_HOME='C:\Program Files\Java\jdk1.8.0_20'
set-alias 'vi' 'gvim'
set-alias 'gradle' 'D:\chenbojian\cbjtool\gradle-2.4\bin\gradle.bat'
set-alias 'mvn' 'D:\chenbojian\cbjtool\apache-maven-3.3.3\bin\mvn.cmd'
import-module PsGet

# Load posh-git example profile
. 'C:\Users\johr\Documents\WindowsPowerShell\Modules\posh-git\profile.example.ps1'
```

据说Powershell Community Extension可以解决问题，有个Invoke-batch命令可以从bat文件读取环境变量，有空研究下。

### update

安装了Powershell Community Extension后可以Invoke-batch，也可以直接Import-visualstudiovars来加载vs编译器的环境变量，这样就不需要先从一个bat文件启动conEmu了。windows的运行（WIN+R）可以直接启动快捷方式文件，比如环境变量目录中有cbjps.lnk，只需要WIN+R，然后cbjps即可启动快捷方式对应的程序。不过注册表右键菜单对应项需要直接指向原始exe文件才行。

```powershell
$env:PATH += ";C:\Program Files\iojs\" 
$env:PATH += ";" + $env:APPDATA + "\npm"
$env:PATH += ";C:\Python27\" 
$env:PATH += ";C:\Python27\scripts\"
# Invoke-BatchFile "C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\vcvarsall.bat" x64
function vs {
    Import-VisualStudioVars 2013 x64
}
$env:JAVA_HOME='C:\Program Files\Java\jdk1.8.0_20'
set-alias 'vi' 'gvim'
set-alias 'gradle' 'D:\chenbojian\cbjtool\gradle-2.4\bin\gradle.bat'
set-alias 'mvn' 'D:\chenbojian\cbjtool\apache-maven-3.3.3\bin\mvn.cmd'
import-module PsGet

# Load posh-git example profile
. 'C:\Users\johr\Documents\WindowsPowerShell\Modules\posh-git\profile.example.ps1'
```


未完待续。。。

