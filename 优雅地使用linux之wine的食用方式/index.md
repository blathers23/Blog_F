# 优雅地使用Linux之Wine的食用方式

<!--more-->

{{<admonition tip "补记">}}

最近（2022.9.26）安装了Feodra系统，发现这篇文章实在是没什么用处；对于不同的操作系统，wine的安装方式直接参考[winehq的wiki](https://wiki.winehq.org/Download)即可。对于Ubuntu、Debian系统来说，还可以配置[TUNA的镜像](https://mirrors-i.tuna.tsinghua.edu.cn/help/wine-builds/)，加速安装。对于Fedora用户，很遗憾只能自行配置代理；具体操作为修改`/etc/yum.repos.d/winehq.repo`，在最后一行添加`proxy="XXXXXX"`之后再运行`dnf makecache`等命令。

wine在首次运行程序之前，需要先运行`winecfg`命令来生成配置文件。

解决安装问题之后，如果使用中文环境，那么还可能会遇到中文全部方框的问题。这是因为没有安装中文字体导致的。比较方便的解决方式有两种，一是使用winetricks安装字体，受制于网络的限制，这种方式常常面临着失败；另一种较为快捷的方式就是打开安装了中文的Windows系统，并将`C:\windows\Fonts`目录下的字体复制到`~/.wine/drive_c/windows/Fonts`文件夹下，通常可以解决文字方框的问题。

对于wine的中文乱码问题，如正文所说是字符集的问题。如果我们的操作系统设置的语言为英文，那么在运行`wine cmd`之后使用`chcp`命令检查会得到437，即英文环境；而当我们使用`env LC_ALL=zh_CN.UTF-8 wine cmd`，即设置环境为中文环境并启动cmd，此时检查`chcp`得到的代码为65001，也就是使用了UTF-8编码，在这种情况下，基本可以解决所有的乱码问题。

还有一类导致无法运行程序的错误是系统缺少某些驱动，然而缺少驱动的报错信息在双击运行exe的报错中是没有的，这时最好的方式应该是使用`wine xxx.exe`来启动程序，在命令行中检查即可发现缺少的驱动报报错等信息。 

{{</admonition>}}

很多时候，你不得不运行一些Windows可执行程序，但是苦于手头仅有一台Linux设备，你或许想要使用虚拟机，不巧的是你又讨厌虚拟机那种令人厌烦的卡顿、不跟手的感觉，那么Wine，就是为你量身定制的！Wine全称Wine Is Not an Emulator（Wine不是一个模拟器），是一个能在Linux系统上运行Windows应用的兼容层。简单来说，只要安装了Wine，就可以简单的在Linux系统上通过双击exe文件来运行这些可执行程序！但是呢，Wine的安装与使用还有一些小坑，在这里简单记录一下这些坑以及Wine的最佳食用方式。

最简单的方法莫过于apt安装，即简单的输入：

```bash
sudo apt-get install wine
```

但是这种最简单的安装方式有着一个致命的缺陷，那就是版本太老了。以本文写成的时间为例，最新版本的Wine已经更新到了7.10，然而apt还只能安装6.X版本的Wine。并且6.X版本的Wine对Direct的支持还有待提高，很多基于Direct游戏不能正常的在Linux系统上运行。那么似乎下一步就是自行编译最新版本的Wine了。在编译之前首先应当卸载当前的Wine。

```bash
sudo apt-get remove wine
```

卸载Wine之后，摸到Wine的官网，在这里提供两个下载选项，分别是稳定版的7.0版本和开发版的7.10。下载自己所需要的版本，解压后即可运行：

```bash
./configure
make
```

来对Wine进行一个简单的安装。当然，在这个过程中会被提示缺少各种各样的依赖，但总之是可以凭借着毅力解决的！这里不再记录解决过程，因为这是一个非常麻烦并且不推荐的安装方式。事实上，当我费尽九牛二虎之力，我的垃圾笔电也编译了一个多小时之后，终于安装好Wine。自以为可以愉快体验一个Windows游戏而洋洋得意时，很讨厌的是，游戏进去是进去了，但是完完全全就是一个黑屏，经排查除了游戏之外的一些小程序可以正常运行。事实上问题的原因我现在还没有搞清楚，当时使用的版本是稳定版7.0版本，各种无头苍蝇一样的debug方式尝试过之后，只好将最后的希望放在Wine开发版上。说来也是巧合，当我在Wine官网苦苦寻找解决办法无果，却偶然发现，就在官网上就清清楚楚的写着，Wine安装的最佳方式！以Ubuntu22.04系统举例，安装方式如下：

```bash
# 开启32bit架构支持
sudo dpkg --add-arrchitecture i386
# 下载添加仓库密钥
wget -nc https://dl.winehq.org/wine-builds/winehq.key
sudo mv winehq.key /usr/share/keyrings/winehq-archive.key
# 添加仓库
wget -nc https://dl.winehq.org/wine-builds/ubuntu/dists/jammy/winehq-jammy.sources
sudo mv winehq-jammy.sources /etc/apt/sources.list.d/
# 更新安装包
sudo  apt-get update
# 安装开发分支的Wine
sudo apt install --install-recommends winehq-devel
```

就这样，轻而易举的解决了安装过程中的依赖问题和编译耗时，并且在安装了开发分支的Wine7.10之后，游戏终于运行了起来！可谓：

> 踏破铁鞋无觅处，得来全不费功夫！

但是呢，人生总是这么跌宕起伏，游戏进去了，但是却又碰到了闪退的问题。经排查，发现游戏存档文件夹出现了乱码情况，初步怀疑是游戏存档文件夹为中文导致的编码问题。使用中文环境启动游戏：

```bash
env LANG=zh_CN.UTF-8 wine game.exe
```

发现成功读取了游戏文件夹，确定为编码问题。修改编码问题最简单的方式为在`~/.bashrc`中添加`export LANG=zh_CN.UTF_8`，这样，当以当前用户使用Terminal启动游戏时，游戏可以正常显示中文。但是当双击游戏文件启动时，依旧不能正常显示中文。为了解决这个问题，经搜索，需要修改默认的环境配置，配置文件夹位于`/etc/profile`，需要在最后一样添加`LANG=zh_CN.UTF_8`即可修改默认编码为中文的UTF-8编码，十分的方便。

---

后记：啰啰嗦唆没用的话

毕业终于完事了，没少费功夫，最后终于是拿到了毕业证，和大家伙也天各一方。毕业离开学校的那天，头一次喝醉吐的自己浑身都是，可能我自己心里是非常喜欢大家伙的吧，我也不知道。离别总是酸楚，但是回到家乡，离开校园，还是得直面困难的。现在隔离在宾馆，天天闲的难受，这样的日子，今天也是最后一晚了。和我一样二战的家伙已经开始规律作息好好复习了，我也不能拉了胯，考前最后一次熬夜！！！再也不熬夜了！最后就是，果然还是好好的把自己心中所想表达出来能让人心情舒畅。


---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/%E4%BC%98%E9%9B%85%E5%9C%B0%E4%BD%BF%E7%94%A8linux%E4%B9%8Bwine%E7%9A%84%E9%A3%9F%E7%94%A8%E6%96%B9%E5%BC%8F/  

