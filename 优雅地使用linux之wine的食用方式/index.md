# 优雅地使用Linux之Wine的食用方式

<!--more-->

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

来对Wine进行一个简单的安装。当然，在这个过程中会被提示缺少各种各样的依赖，但总之是可以凭借着毅力解决的！这里不再记录解决过程，因为这是一个非常麻烦并且不推荐的安装方式。事实上，当我费尽九牛二虎之力，我的垃圾笔电也编译了一个多小时之后，终于安装好Wine。自以为可以愉快体验一个游戏而洋洋得意时，大无语事件发生，游戏进去是进去了，但是完完全全就是一个黑屏，经排查除了游戏之外的一些小程序可以正常运行。事实上问题的原因我现在还没有搞清楚，当时使用的版本是稳定版7.0版本，各种无头苍蝇一样的debug方式尝试过之后，只好将最后的希望放在Wine开发版上。说来也是巧合，当我在Wine官网苦苦寻找解决办法无果，却偶然发现，就在官网上就清清楚楚的写着，Wine安装的最佳方式！以Ubuntu22.04系统举例，安装方式如下：

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

后记啰啰嗦唆没用的话:

毕业终于完事了，没少费功夫，最后终于是拿到了毕业证，和大家伙也天各一方。毕业离开学校的那天，头一次喝醉吐的自己浑身都是，可能我自己心里是非常喜欢大家伙的吧，我也不知道。离别总是酸楚，但是回到家乡，离开校园，还是得直面困难的。现在隔离在宾馆，天天闲的难受，这样的日子，今天也是最后一晚了。和我一样二战的家伙已经开始规律作息好好复习了，我也不能拉了胯，考前最后一次熬夜！！！再也不熬夜了！最后就是，果然还是好好的把自己心中所想表达出来能让人心情舒畅。


---

> 作者: Blathers  
> https://blathers23.netlify.app/%E4%BC%98%E9%9B%85%E5%9C%B0%E4%BD%BF%E7%94%A8linux%E4%B9%8Bwine%E7%9A%84%E9%A3%9F%E7%94%A8%E6%96%B9%E5%BC%8F/
