# 优雅地使用Linux

<!--more-->

{{<admonition tip "简介">}}

最近觉得Linux中的好多小点值得一记，但是为此单开一帖体量不足又不值得。因此打算将简单零碎的点记载到这篇文章中。操作系统为Ubuntu22.04，不知道对于其它版本的Linux是否通用。

- Jul 11 更新：历史性和Windows达成和解，日常使用Linux还是不方便，如有需求可以使用WSL。

- Sep 26 更新：学不进去折腾了Fedora36，使用体验和Ubuntu差距还是蛮大的，更新一些在Fedora36上使用的小技巧，同时会在每个技巧前标注系统。

{{</admonition>}}

## 软件包相关

### APT代理

> *Ubuntu22.04*

Linux的代理十分简单，对于Ubuntu系统来说，可以在网络设置里打开手动代理并填写代理地址，这实际上就是设置了环境变量：

```bash
export http_proxy=192.168.0.0:8080
export https_proxy=192.168.0.0:8080
```

相信这一点对于很多其他的Linux版本都是相通的。但是这种代理模式并不能改变APT的网络链接，APT依旧会采取直连模式。为此，需要使用命令：

```bash
sudo apt command -o Acquire::http::proxy="http://192.168.0.0:8080/"
```

来设置相应的代理。

### DNF代理

> *Fedora36*

对于DNF的软件包，很多源在国内都有镜像，可以达到不错的下载速度。最近（2022.9.26）在下载安装wine时，使用官方源下载经常失败，而TUNA也没有提供镜像，只好使用代理的方式解决。修改位于`/etc/yum.repos.d/`下的winehq.repo：

```bash
sudo vim /etc/yum.repos.d/winehq.repo 
```

在最后一行添加代理信息，例如：

```yaml
[WineHQ]
name=WineHQ packages
type=rpm-md
baseurl=https://dl.winehq.org/wine-builds/fedora/36
gpgcheck=1
gpgkey=https://dl.winehq.org/wine-builds/winehq.key
enabled=1
proxy=http://192.168.0.0:8080
```

在更新及下载软件包时即可使用代理加速下载，对于其它软件包也是同理。

## 系统相关

### windows系统模拟：Wine

参考：[Wine的食用方式](../优雅地使用linux之wine的食用方式/)。

### GNOME桌面Main Manu编辑：Alacarte

> *Fedora36*

最近在Fedora系统上配置Aria2和AriaNG时发现使用非常不方便，一方面我需要在terminal中启动Aria2，另一方面又需要使用Chrome启动Aria2NG，步骤繁杂不能做到一键启动。由此我想到了创建一个主菜单启动图标的方法。

首先编写一个启动Aria2和AriaNG的脚本：

```sh
gnome-terminal -- aria2c --enable-rpc --rpc-allow-origin-all
google-chrome /Your_AriaNg_Path/index.html
```

然后安装主菜单编辑器Alacarte：

```bash
sudo dnf install alacarte
```

最后在编辑器中添加启动脚本，并设置图标及名称，就可以方便的在主菜单启动Aira2。

### GNOME Shell Extensions

> *Fedora37*

GNOME拓展是第三方开发者编写的小段代码，可以类比为Chrome的扩展，为系统增加一些便捷的功能。只需要在系统中安装`gnome-extensions`软件包，就可以访问[拓展应用市场](https://extensions.gnome.org/)，选择或搜索自己喜欢的拓展并安装到系统上。

这里推荐**Net Speed**和**Bluetooth Quick Connect**两个拓展，一个可以在panel中显示实时网速，一个可以在control panel中选择已经配对过的蓝牙设备快速连接。

---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/%E4%BC%98%E9%9B%85%E5%9C%B0%E4%BD%BF%E7%94%A8linux/  

