# VSCode 搭配 g++ 配置 Cpp 编译环境

 <!--more-->

**这篇文章来自以往的 Blog，此处仅作搬运收藏，可能不再对内容进行订正**

*😧博主理解粗浅，如有错误还请包涵，或联系博主订正*


## Ⅰ. 安装  MSYS2

博主选择使用 MSYS2 管理 g++ 的版本，有关 MSYS2 的介绍请参照[此处](https://www.zhihu.com/question/22137175/answer/90908473)。

首先安装 MSYS2。这里使用清华大学的[镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/msys2/)

![](https://s2.loli.net/2022/04/26/byNEdGAmxL81Cfc.jpg)

在安装过程中，可能会碰到安装进度卡在 66% 的情况，这时候我们取消安装再重新打开安装程序安装到原位置即可。

安装完成后我们为 MSYS2 配置清华镜像，提高下载的速度和稳定性，同样按照[镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/msys2/)中的教程操作。

![](https://s2.loli.net/2022/04/26/WyqprOANLYoS3gF.jpg)

## Ⅱ.  下载 g++  和  gdb 

首先更新核心软件包，运行指令：
`pacman -Syu`

最后安装 g++，运行指令：
`pacman -S gcc`

然后下载 gdb，运行指令：
`pacman -S mingw-w64-x86_64-gdb`

## Ⅲ.  配置环境变量

将 MSYS2 安装地址下的 `\usr\bin` 加入到Path变量。然后启动 powershell，输入指令 `g++ -v`检查安装是否成功。

*如果使用 everything 搜索 g++.exe 是搜索不到 \usr\bin 文件夹中的，就算它就在文件夹里面。*

## Ⅳ.  配置 VSCode

在 VSCode 扩展商店中搜索 `C++`，然后安装此扩展：
![](https://s2.loli.net/2022/04/26/sGVLxpDm6CQJOiu.jpg)

打开一个 C++ 语言程序，然后在 VSCode 中运行，此时会弹出环境选择框，这里我们选择 `GDB`，即第一项：
![](https://s2.loli.net/2022/04/26/M2fVuIPLcTgBlUa.jpg)

然后选择 g++ 进行编译，此时会报错，显示 miDebuggerPath 不存在：
![](https://s2.loli.net/2022/04/26/aHNYK5ATmq3cgPB.jpg)

这里我们编辑这一项，刚刚在第二步，我们下载了 gdb，其安装位置在 `\mingw64\bin` 下，于是我们将这一行改为：
![](https://s2.loli.net/2022/04/26/H1TsiDFEBUSf6aJ.jpg)

保存，就可以愉快的使用快捷键调试辣👏！

