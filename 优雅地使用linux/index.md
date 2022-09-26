# 优雅地使用Linux

<!--more-->

{{<admonition tip "简介">}}

最近觉得Linux中的好多小点值得一记，但是为此单开一帖体量不足又不值得。因此打算将简单零碎的点记载到这篇文章中。操作系统为Ubuntu22.04，不知道对于其它版本的Linux是否通用，可能需要测试。

Jul 11 更新：历史性和Windows达成和解，日常使用Linux还是不方便，如有需求可以使用WSL。

{{</admonition>}}

## 软件包安装相关

### APT代理

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

## 系统相关

### Wine

参考[Wine的食用方式](../优雅地使用linux之wine的食用方式/)。

---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/%E4%BC%98%E9%9B%85%E5%9C%B0%E4%BD%BF%E7%94%A8linux/  

