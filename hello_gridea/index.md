# Hello Gridea! - 如何使用Gridea和Coding搭建自己的Blog


 <!--more-->

**这篇文章来自以往的 Blog，此处仅作搬运收藏，可能不再对内容进行订正**

由于介绍的非常详细，本文不再重复啰嗦，[有关 Gridea 的简介请见此](https://gitee.com/fehey/gridea)。

## Ⅰ.  Gridea 的安装

[Gridea 的下载地址](https://gridea.dev/#started)
以 Windows 系统位例，安装完成后，我们注意软件左边栏，选择*远程*项，并将其中的*平台*项改为 *Coding Pages*，继续以下的工作。

## Ⅱ. 部署到 Coding Pages

Gridea 提供了将博客部署到 GitHub Pages、Coding Pages 和通过 SFTP 的方式上传到自己的服务器的功能。有关使用 GitHub 的 Gridea 部署方式，官方给出了一个十分详细的[教程](https://gridea.dev/gridea-start/)。但是由于网络的原因，GitHub 不能成为博主的优先选择，因此本教程采用腾讯系的 Coding Pages 进行部署。

### ① .创建一个 Coding 账号，并创建一个 Token。
点击 [Coding](https://e.coding.net/register) 进行注册。登录至工作台主页后（\*\*\*\*.coding.net/user），注意页面右上角自己的头像，在悬停时弹出的菜单中选择*个人账户设置*；注意左侧边栏，选择*访问令牌*，进入访问令牌页面（\*\*\*\*.coding.net/user/account/setting/tokens）。将页面中的令牌用户名填入到 Gridea 软件**令牌用户名**一栏中。点击*新建令牌*，令牌描述以便于区分为佳，例如“Gridea Site”。选择权限勾选 **project:depot**，即完全的仓库控制权限。点击确定，将会生成一个新令牌，请务必将令牌复制到本地保存，因为页面关闭后令牌将无法在页面上查看。将生成的**令牌**填入到 Gridea 软件**令牌**一栏中。
![](https://s2.loli.net/2022/04/26/voHCpxsr5kM1dRV.png)

### ②.创建一个公开仓库，以存放构建后的网站。
回到工作台主页，注意左侧边栏，选择*项目*，单击*新建项目*，选择 *DevOps 项目*。这时项目名称可能将无法更改，这是 Coding 网站的引导示例项目，只需随着教程操作后重新回到工作台并新建项目即可。选择 *DevOps 项目*，并为自己的项目起一个好听的名字~~虽然并没有什么用~~，点击完成创建，进入项目主页（\*\*\*\*.coding.net/p/\*\*/）。注意左侧边栏，选择*代码仓库*项，并点击*新建代码仓库*。为自己的仓库起一个好听的名字~~同样也没什么用~~，勾选*生成 README 文件*，勾选*公开仓库*，完成创建。注意项目名所在的横栏，点击*设置*，注意到代码仓库名称中给出的“⚠️修改仓库名称将导致仓库的访问 URL 改变，在此之前的 URL 地址将失效。Git 仓库地址修改方法”附带了代码，其格式为：
```git
git remote set-url origin https://e.coding.net/“字段 A”/“字段 B”/“字段 C”.git
```
打开Gridea软件，在**仓库名称**中填入`“字段 C”`，在**仓库用户名**中填入`“字段 A”/“字段 B”`，在**分支**中填入 `master`。
![](https://s2.loli.net/2022/04/26/QBPvWtnrXoFfGRJ.png)

### ③.部署静态网站。
点击左侧边栏*持续部署*，选择*静态网站*，按照提示配置腾讯云权限，完成实名认证并新建静态网站。随便给网站起一个名字，点击确定，网站将进行部署。稍等至网站部署成功，点击*访问*，访问的网址即为 Blog 的网址。打开 Gridea 软件，将网址复制到**域名**中，注意 “https://” 不要重复。此时点击软件左下角的*检测远程连接*，如果一切配置正确的话，会出现“远程连接成功”字样。点击保存，然后点击软件右下角的同步，就可以将网页部署到 Coding Pages 上。稍等约5分钟左右再次访问 Blog 网站即可访问成功。

以上，就完成了使用 Gridea 将博客部署到 Coding Pages 上的全部步骤。

---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/hello_gridea/  

