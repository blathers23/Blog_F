# Linux 下利用 Jallyfin 搭建家庭媒体服务器


<!--more-->

{{<music auto="https://music.163.com/#/song?id=28785355">}} 

## 前言

---

人年龄越来越大，总是会将过去的一些记忆美化，正如大仲马在《三个火枪手》中借阿多斯之口所说的那样：

> 无论多么酸楚的回忆，时间总会将其变成甜美的回眸。

数天前，突然想重新看一些过去非常喜欢的作品（当然并不是说现在就不喜欢了），就在我折腾着将这些影视作品从互联网的犄角旮旯下载到电脑上的时候，我突然意识到非常关键的问题：这些视频太大了，已经大到不能放在手机里躺在床上看的程度。

诚然，这个流媒体服务越来越发达的时代，观看影视作品已经简单到只需要动动手指打开需要的 APP。但这表示你将不得不面对：不合理的会员收费（手机端观看和电视端观看需要两种不同的会员）、铺天盖地的广告（听说一些平台就算开会员也不能完全屏蔽这些广告）、恼人的热门推送（点名会员购魔力赏）以及视频资源可能会失效的各种问题。但是，离开了影视平台，选择下载到本地播放，自然也就不能继续享受平台提供的诸多便利，例如多端播放，播放进度记录，资源整理等等服务。

**一言以蔽之，说人话就是：我想躺在床上用手机看电脑里下载的视频**。在这样简单的想法下，我开始了本次的折腾之旅。

经过一番了解，为解决此类问题，一般需要搭建 **Media System**。现在比较流行的 **Media Solution** 有 Jellyfin（免费）、Plex（免费）、Emby（收费）等，考虑到钱包，自然而然的选择是 Jellyfin。接下来，我会从服务端和客户端两个方面，详细的介绍如何搭建基于 Jellyfin 的家庭媒体系统。

## Server

---

### 服务器

首先介绍一下服务器。被我用作服务器的机器是一台安装了 Fedora37 的型号为三星 350XAA 的笔记本，具体的配置如下：

```sh
➜  ~ neofetch
             .',;::::;,'.                ******************
         .';:cccccccccccc:;,.            ------------------ 
      .;cccccccccccccccccccccc;.         OS: Fedora Linux 37 (Workstation Edition) x86_64 
    .:cccccccccccccccccccccccccc:.       Host: 340XAA/350XAA/550XAA P02REP 
  .;ccccccccccccc;.:dddl:.;ccccccc;.     Kernel: 6.0.17-300.fc37.x86_64 
 .:ccccccccccccc;OWMKOOXMWd;ccccccc:.    Uptime: 1 day, 2 hours, 14 mins 
.:ccccccccccccc;KMMc;cc;xMMc:ccccccc:.   Packages: 2393 (rpm) 
,cccccccccccccc;MMM.;cc;;WW::cccccccc,   Shell: zsh 5.9 
:cccccccccccccc;MMM.;cccccccccccccccc:   Resolution: 1920x1080 
:ccccccc;oxOOOo;MMM0OOk.;cccccccccccc:   DE: GNOME 43.2 
cccccc:0MMKxdd:;MMMkddc.;cccccccccccc;   WM: Mutter 
ccccc:XM0';cccc;MMM.;cccccccccccccccc'   WM Theme: Adwaita 
ccccc;MMo;ccccc;MMW.;ccccccccccccccc;    Theme: Adwaita [GTK2/3] 
ccccc;0MNc.ccc.xMMd:ccccccccccccccc;     Icons: Adwaita [GTK2/3] 
cccccc;dNMWXXXWM0::cccccccccccccc:,      Terminal: gnome-terminal 
cccccccc;.:odl:.;cccccccccccccc:,.       CPU: Intel i5-8250U (8) @ 3.400GHz 
:cccccccccccccccccccccccccccc:'.         GPU: NVIDIA GeForce MX110 
.:cccccccccccccccccccccc:;,..            GPU: Intel UHD Graphics 620 
  '::cccccccccccccc::;,.                 Memory: 3986MiB / 7853MiB 
```

得益于低压 U，这台笔记本有着不错的性能表现的同时还能保持较低的功耗。

### 安装 Jellyfin Server

在各种平台上安装 Jellyfin Server 的方式，在 Jellyfin 官网的[下载页面](https://jellyfin.org/downloads/server/)有详尽的教程。这里只演示在 Fedora 系统上安装的流程。

首先确保你开启了 RPM Fusion 仓库。如果没有开启，这里推荐使用[清华大学的镜像](https://mirrors.tuna.tsinghua.edu.cn/help/rpmfusion/)，可以避免网络连接不通畅的问题，开启 RPM Fusion 的方式如下。

1. 安装基础包

   在终端中运行如下命令：

   ```sh
   sudo dnf install --nogpgcheck https://mirrors.tuna.tsinghua.edu.cn/rpmfusion/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.tuna.tsinghua.edu.cn/rpmfusion/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
   ```

2. 修改链接指向 TUNA 镜像

   安装成功后，修改 `/etc/yum.repos.d/` 目录下以 `rpmfusion` 开头，以 `.repo` 结尾的文件。具体而言，需要将文件中的 `baseurl=` 开头的行等号后面链接中的 `http://download1.rpmfusion.org/` 替换为 `https://mirrors.tuna.tsinghua.edu.cn/rpmfusion/`，替换后的文件类似如下：

   ```
   [rpmfusion-free]
   name=RPM Fusion for Fedora $releasever - Free
   baseurl=https://mirrors.tuna.tsinghua.edu.cn/rpmfusion/free/fedora/releases/$releasever/Everything/$basearch/os/
   mirrorlist=http://mirrors.rpmfusion.org/mirrorlist?repo=free-fedora-$releasever&arch=$basearch
   enabled=1
   metadata_expire=7d
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmfusion-free-fedora-$releasever-$basearch
   
   [rpmfusion-free-debuginfo]
   name=RPM Fusion for Fedora $releasever - Free - Debug
   mirrorlist=http://mirrors.rpmfusion.org/mirrorlist?repo=free-fedora-debug-$releasever&arch=$basearch
   enabled=0
   metadata_expire=7d
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmfusion-free-fedora-$releasever-$basearch
   
   [rpmfusion-free-source]
   name=RPM Fusion for Fedora $releasever - Free - Source
   baseurl=https://mirrors.tuna.tsinghua.edu.cn/rpmfusion/free/fedora/releases/$releasever/Everything/source/SRPMS/
   mirrorlist=http://mirrors.rpmfusion.org/mirrorlist?repo=free-fedora-source-$releasever&arch=$basearch
   enabled=0
   metadata_expire=7d
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmfusion-free-fedora-$releasever-$basearch
   ```

确保在你的系统上，RPM Fusion 仓库是开启状态后，只需要运行：

```sh
sudo dnf install jellyfin
```

即可将 Jellyfin Server 安装到你的服务器上。

### 资源整理

安装完成 Jellyfin Server 后，先不必着急运行服务，首先应当整理资源以便 Jellyfin 读取。Jellyfin 通过媒体在不同的文件夹来确定媒体的类型（电影、电视剧、音乐等），并通过文件夹的组织来识别不同媒体的之间的关系。以电影文件夹举例来说：

```sh
.
├── 东邪西毒 
│   └── 东邪西毒.mkv
└── 星际牛仔：天国之门
    └── 星际牛仔：天国之门.mp4

```

在这样存放电影的文件夹中有两个子文件夹，分别命名为《东邪西毒》和《星际牛仔：天国之门》，表示 Jellyfin 的电影目录中有着这样两个电影。在各自的文件夹中，又存放了各自的视频文件。

采用这样每个电影分别占一个文件夹的结构，其优势在于可以更好的管理同一个电影相关的文件，例如某个电影的外挂字幕，就可以放到对应电影的文件夹中，防止文件结构混乱。

再举一个电视剧文件夹的例子：

```sh
.
└── 摇曳露营
    ├── Season 1
    │   ├── S01E01.mp4
    │   └── S01E02.mp4
    └── Season 2
        ├── S02E01.mp4
        └── S02E02.mp4
```

与电影不同的是，一部电视剧往往由不同的季（Season）组成，因此在一部电视剧下又出现了不同的 Season 文件夹。除此之外，视频文件的命名也变成了“第几季第几集.mp4”的形式，这种命名方式方便后期刮削电视剧的信息。

有关媒体文件的整理和命名的详细信息，可以查看 Jellyfin 官网的[文档](https://jellyfin.org/docs/general/server/media/movies)来了解。

### Jellyfin Server 设置 - 媒体库

整理好所有的媒体文件之后，接下来就可以启动 Jellyfin Server 了。在终端中键入：

```sh
jellyfin
```

启动 Jellyfin Server。服务启动完成后，只需要在浏览器中输入 `localhost:8096` 就可以登陆到网页的 Jellyfin 客户端。

{{<admonition bug>}}

本文介绍的启动方式是以你当前登陆的用户启动 Jellyfin，而不是以系统服务（`service jellyfin start`）的方式启动。这两种启动方式**似乎是不兼容**。以系统服务的方式启动更适合于一台固定的服务器，要以这种方式使用 Jellyfin，请参考官方的[教程](https://jellyfin.org/docs/general/installation/)来启动 Jellyfin 服务。

{{</admonition>}}

首次进入 Jellyfin，需要按照提示注册用户，设置偏好语言以及媒体库。设置媒体库只需要在选择内容类型后，将上一步整理好的文件夹添加到媒体库的文件夹中即可。需要**注意**的是，为了便于接下来利用其他软件整理电影的资料，需要勾选媒体资料存储方式中的 *Nfo* 选项以及图片获取程序下的*将媒体图像保存到媒体所在文件夹*选项。

进入 Jellyfin，等待媒体库扫描完成后，就可以看到媒体已经分门别类的存放好了。

### 媒体元数据刮削

尽管已经手动对视频文件做了一定程度的整理，但是可以看到我们的媒体库依旧缺少海报，描述等等信息。这些信息可以通过 Jellyfin 内置的插件来自动的从网络上下载，但是这样做不仅会受到网络的影响，而且也不能方便的手动调整错误的数据。这里介绍另一款软件来整理电视剧的媒体内容：[tinyMediaManagement](https://www.tinymediamanager.org/)，以下简称 tmm。

tmm v3 是免费软件，但是从 v4 开始，一些高级功能需要付费。接下来我分别介绍一下这两个版本各自的特点和使用体验。

#### tmm v3

tmm v3 所有的功能都是免费的，包括不同的数据源，字幕刮削等高级功能。但是 v3 版本并不提供对 Jellyfin 的原生支持，并且有时会有刮削不到一些数据的情况，尽管你可以在数据源的网站搜索到这些数据。

另外，要使用 tmm v3，还需要 Java 环境的支持。Fedora 自带的 Java 环境很可能是 headless 的，这会导致启动 tmm 失败。为解决这个问题，需要使用如下命令来安装 openjdk，以安装 17 版本为例：

```sh
sudo dnf install java-17-openjdk
```

#### tmm v4

tmm v4 的基本功能免费，但是一些高级功能则需要每年数百元人民币的激活。这些高级功能有：多种数据源，字幕刮削功能，预告片下载等。如果不购买许可证，那么你只能使用免费的 TMDB 数据源。

与 tmm v3 不同，tmm v4 原生支持 Jellyfin，同时，虽然不能使用其他数据源，但对于中文语境的用户来说，只有 TMDB 支持中文，并且 TMDB 的数据源也足够准确好用。因此我推荐使用最新的 v4 版本作为元数据刮削器。

接下来简单介绍一下 tmm 的配置和使用方式，这里以 tmm v4.3.7 版本为例。初次进入软件，需要设置媒体库文件夹位置和语言偏好等设置，设置完成进入界面后，关闭软件重新进入即可刷新语言设置为中文。

打开设置，由于网络原因，访问 TMDB 网站会比较卡顿， 在通用-系统-代理设置处可以设置软件使用的网络代理。然后在电影 - 自动任务 - 自动重命名处勾选，这个选项在刮削电影数据后可以自动整理文件名称。接下来是电影 - NFO 设置 - NFO 格式处选择 Jellyfin，如果使用错误的格式，在刮削数据后，可能会出现 Jellyfin 无法读取刮削得到的数据的情况。同样的，在电视剧 - NFO 设置 - NFO 格式处同样选择 Jellyfin。最后，进入电视剧 - 图片 - 图片文件名选项，将季海报、季节同人画、季略缩图、季横幅四个选项从 `<season_folder>/*` 调整为 `*` ，确保 Jellyfin 可以正确的读取这些文件。

设置完毕后，回到主界面，选择搜索&抓取 - 搜索并抓取所选电影/电视剧 - 自动匹配即可自动的刮削电影和电视剧的元数据。对于未匹配到或者匹配错误的电影或电视剧，也可以右键编辑来自己搜索并调整。

### 服务器配置 - 解码器

对于推流的视频，如果采用了客户端不支持的编码方式或者复杂的字幕，则可能会在服务器上启动对视频的转码再推流。转码播放方式虽然可以保证播放的稳定性，但是会大量占用服务器资源，或者导致播放卡顿的问题。

为了减少转码对服务器资源的占用，提升转码的速度，我们可以采用硬件加速转码。在 Jellyfin 主页 - 管理 - 控制台 - 播放 - 转码设置选项可以查看 Jellyfin 支持的硬件加速方式。但是这些硬件加速方式均须要服务端的一些配置才可以正常使用，这里介绍一下在使用Intel CPU 的 Linux 平台上常用的硬件加速方式：VAAPI 和 QSV 的配置方式。

#### VAAPI

要启用 VAAPI 的支持，在 Fedora 系统上只需要下载安装几个所需要的库：

```sh 
sudo dnf install libva libva-utils libva-intel-driver
```

如果安装成功，在终端输入 `vainfo` 则会有类似如下的输出：

```sh
➜  ~ vainfo
Trying display: wayland
libva info: VA-API version 1.16.0
libva info: Trying to open /usr/lib64/dri/iHD_drv_video.so
libva info: va_openDriver() returns -1
libva info: Trying to open /usr/lib64/dri/i965_drv_video.so
libva info: Found init function __vaDriverInit_1_15
libva info: va_openDriver() returns 0
vainfo: VA-API version: 1.16 (libva 2.16.0)
vainfo: Driver version: Intel i965 driver for Intel(R) Kaby Lake - 2.4.1
vainfo: Supported profile and entrypoints
......
```

其中第6&7行 `libva info: Found init function __vaDriverInit_1_15` 的存在意味着 VAAPI 安装成功，则可以在 Jellyfin 中启用 VAAPI 加速转码。

#### QSV

要启用 QSV 的支持，同样是只需要安装几个所需要的库：

```sh
sudo dnf install intel-media-driver intel-mediasdk
```

如果安装成功，在终端输入 `vainfo` 则会有类似如下的输出：

```sh
➜  ~ vainfo
Trying display: wayland
libva info: VA-API version 1.16.0
libva info: Trying to open /usr/lib64/dri/iHD_drv_video.so
libva info: Found init function __vaDriverInit_1_16
libva info: va_openDriver() returns 0
vainfo: VA-API version: 1.16 (libva 2.16.0)
vainfo: Driver version: Intel iHD driver for Intel(R) Gen Graphics - 22.5.4 ()
vainfo: Supported profile and entrypoints
......
```

其中第4&5行 `libva info: Found init function __vaDriverInit_1_16` 的存在意味着 QSV 安装成功，则可以在 Jellyfin 中启用 QSV 加速转码。

## Client

---

尽管在任何设备上都可以使用浏览器登陆 Jellyfin 浏览本地的媒体，但是浏览器对视频格式和字幕格式的支持是非常有限的。对于不支持的格式，将会触发服务端对视频的转码，大大降低播放的流畅度，败坏使用体验。因此配置并使用一个客户端是非常必要的。这里介绍一下 Android 和 Android TV 客户端的选择和配置。

### Android

参照[官方的客户端列表](https://jellyfin.org/downloads/clients/)，安卓客户端可以选择官方的 Jellyfin for Android 和非官方的 Findroid，这里分别介绍一下两个客户端与配置方式，并比较一下优劣。

#### Jellyfin for Android

Jellyfin for Android 客户端是官方支持的客户端，保持大约每三个月更新一次的频率，除了可以浏览媒体之外，官方客户端还支持在对服务端的一些设置进行配置（与浏览器可以配置的范围一致）。视频播放方式支持浏览器播放器、整合播放器（ExoPlayer）和外部播放器（MX Player | VLC for Android）。

整合播放器对视频格式和字幕支持的效果最差，大多数视频格式都需要在服务端转码再推流到客户端播放，因此十分不推荐使用。

整合播放器对大多数视频格式支持良好，但是对于字幕的支持效果差，需要将字幕烧录到视频中才可以正常播放。当然，你也可以在设置中配置由安卓系统自带的字幕来播放视频的字幕，虽然这种方式对于复杂字幕的支持效果较差，但足以应对多数情况。

外部播放器对视频格式和字幕格式的支持效果最好，往往不需要转码就可以直接播放。唯一的槽点是外部播放器播放完一集之后不能自动播放第二集，非常膈应。

#### Findroid

Findroid 是第三方开发者开发的客户端，保持大约每一个月一次的更新频率。与官方的 Jellyfin for Android 客户端不同，Findroid 不支持在客户端中修改服务端设置。但是 Findroid 整合了 mpv 播放器作为内置播放器，mpv 播放器对各种视频格式和字幕格式都有很好的支持，并且由于是内置播放器，避免了外部播放器不能自动继续播放的问题。

### Android TV

Jellyfin for Android TV 客户端是官方支持的客户端，总体来说体验良好。为了获得最佳的视频和字幕格式支持体验，需要将播放器切换为内置的 libVLC 播放器。


---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/jallyfin/  

