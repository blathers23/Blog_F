# 将 Jellyfin 开放至公网并设立域名

<!--more-->

## 前言

---

~~最近突然感觉我写的前言全是没啥用处的废话啊~~

前书说到，利用一台低功耗的笔记本搭建了家庭流媒体服务器，只需要使用浏览器或者专用软件访问服务器的地址，就可以在内网的各个设备上享受流媒体服务。但是，人不可能是每天都蹲在家里的，偶尔也会有在公网中访问家庭内网服务的需求。

我们知道，由于 IPv4 地址数量有限，很多人是不能独占公网 IP 的，只能共享一个大内网 IP。具体表现为：路由器的公网 IP 地址和 [ifconfig.me](https://ifconfig.me/) 等 IP 查询网站得到的 IP 地址不相同，以本人的为例：

{{<image src = "https://s2.loli.net/2023/02/08/uRyW5KvIUg1JdCs.png" caption = "TP-Link 路由器设置界面 IP 地址" title = "TP-Link 路由器设置界面 IP 地址" height = "70%" width = "70%">}}

{{<image src = "https://s2.loli.net/2023/02/08/c1UxoY6azfCOFwV.png" caption = "ifconfig.me 查询得到的地址" title = "ifconfig.me 查询得到的地址" height = "70%" width = "70%">}}

这意味着你将不能通过端口映射（TP-Link 称之为虚拟服务器）的方式在公网访问你内网的服务，据说可以和运营商要求分配公网 IP，但成功者不多见。其他的解决方案，例如内网穿透，或者异地组网等方案，则不得不在使用体验和价格之间做权衡，实在是捉襟见肘。

{{<admonition tip>}}

事实上，如果你的两个设备都被分入了同一个大内网中，那么端口映射的方式是可以使用的。以 TP-Link 路由器为例，在虚拟服务器中，将 Jellyfin 服务器的地址和端口映射到一个公网端口上后，就可以使用 TP-Link 路由器显示的公网 IP + 映射的公网端口来访问内网的 Jellyfin 服务器。

然而两个设备分入同一个大内网是比较苛刻的条件，以目前的经验仅有本地电信宽带 + 本地电信手机成功过。

{{</admonition>}}

IPv6 的出现，就是为了应对逐渐枯竭的 IPv4 地址资源。随着 IPv6 的逐渐普及，三大运营商的宽带及蜂窝网络基本上都已经支持 IPv6。与 IPv4 不同，对于支持 IPv6 的网络环境及设备，每一个设备都拥有全球唯一的 IPv6 地址，因此，只要服务器和客户端都获取了 IPv6 地址的话，就可以实现访问，并且不需要设置端口映射等步骤。

本篇文章以 Jellyfin 为例，介绍配置 IPv6 并实现公网访问家庭内网服务器的步骤，为解决 IP 地址变化的问题，同时设置一个本地 DDNS 服务的方案。

## IPv6 访问

---

为实现 IPv6 访问内网应用，则需要同时满足 3 个条件：1. 应用支持 IPv6；2. 客户端支持 IPv6；3. 服务器支持 IPv6。这三个条件缺一不可，这里就这三个条件展开阐述。

### 应用

对于不同的应用此处可能出入较大。对于 Jellyfin 来说，只需要在控制台 -> 联网 ->  IP 协议中勾选 IPv6 协议并重启应用即可。

### 客户端

首先提出一个 IPv6 可用性的测试基准：访问 [test-ipv6.com](http://test-ipv6.com/index.html.zh_CN) 网站，就可以查看当前设备对 IPv6 的支持性。如果得分为 10，那么恭喜你，你可以查看**下一小节**的内容了。不同的客户端可以通过不同的方式查询当前 IP 地址，如果存在公网 IPv6 地址（国内三大运营商的公网 IPv6 地址前三位均为 240，如果你不符合这个条件，那么可能是分配到了内网 IPv6），同样表明当前设备网络环境支持 IPv6。Linux 客户端可以运行 `ifconfig` 命令查看本机的 IP 地址；Windows 客户端则可以运行 `ipconfig /all` 命令查看；对于手机来说，以 MIUI 系统为例，进入设置 -> 我的设备 -> 全部参数 -> 状态信息 -> IP 地址查看。

客户端 IPv6 的支持往往与所连接的网络环境有关，如果是蜂窝移动网络，那么一般来说是支持 IPv6 的。如果是公共场所的 Wi-Fi，那么直接选择放弃挣扎比较实在。如果是家庭网络的 Wi-Fi，那么可以直接查看下一小节的内容。

### 服务器

在设置之前，同样在服务器上访问 [test-ipv6.com](http://test-ipv6.com/index.html.zh_CN) 网站，如果得分为 10，那么恭喜你，你可以查看**下下下一段以后**的内容了。如果不是，则你可能需要一些配置才可以使用。

首先，检查一下家庭的网络架构，如果你在阅读这段部分，那我想你的家庭网络架构不外乎为：*光猫连接路由器再通过路由器连接*和*直接通过光猫连接*两种。对于第一种，光猫连接路由器再通过路由器连接的方案，这里非常建议将光猫改为**桥接模式**并通过路由器拨号上网，以减少光猫对网络的损耗，然后在路由器中打开 IPv6 的支持。以 TP-Link 路由器为例，在路由设置 -> IPv6 设置 -> 基本设置中勾选开启 IPv6 功能、宽带拨号上网和复用 IPv4 拨号链路即可。如果你不知道如何给自己的光猫设置为**桥接模式**，请拨打三大运营商的客服电话询问。

对于第二种，直接连接光猫的 LAN 口或者 Wi-Fi 的方案，则需要登陆光猫的后台，设置启用 IPv6。如果你不知道怎样设置，那么同样的建议你拨打三大运营商的电话询问。

经过一番设置后，现在你访问 [test-ipv6.com](http://test-ipv6.com/index.html.zh_CN) 网站应该就会显示对 IPv6 的支持了。接下来，尝试在服务器中以 IPv6 地址访问服务，以 Jellyfin 默认设置为例，浏览器访问 `http://[::1]:8096`，如果成功访问，则说明 Jellyfin 已经启用 IPv6 服务。接下来获取本机的公网 IPv6 地址，以 Linux 为例，执行 `ifconfig | grep "inet6"` 即可获取本机全部的 IPv6 地址，公网 IPv6 往往是最长的那个。接下来在服务器浏览器访问 `http://[(YOUR_IPV6_ADDRESS)]:8096`，如果成功访问，那么说明你已经正确的获得了公网的 IPv6 地址。

然而，当你使用手机或者其他客户端登陆对应的地址时，很可能会出现**拒绝访问**的情况。这是因为市面上大多数路由器都默认打开了 IPv6 防火墙的缘故，你可能需要在路由器中进行设置或者直接安装关闭了 IPv6 防火墙的固件。以 TP-Link 路由器为例，可以直接向客服索要关闭了 IPv6 防火墙的固件。如果一切顺利，更新好固件后，就可以使用任何拥有 IPv6 地址的设备访问内网的 Jellyfin 服务器了！🪅

## DDNS

---

由于 IP 地址可能会发生变化，与之相比，一个固定的域名显着更加美观且使用。为了使用域名访问内网服务，我们需要解决域名购买及 DNS 解析和本地 DDNS 服务两个问题。

### 域名购买及 DNS 解析

粗糙的域名往往是便宜货，阿里云或腾讯云上 10 元以下包年的域名一抓一大把。以腾讯云为例，登陆 [DNSPod](https://www.dnspod.cn/) 网站，搜索想注册的域名，根据提示创建实名认证模板并购买后，设置 DNS 解析。

进入控制台 -> DNS 解析 -> [我的域名选项页](https://console.dnspod.cn/dns/list)，点击**添加域名**，输入刚刚购买的域名(YOUR_DOMAIN)，完成域名添加的操作。点击购买的域名，点击添加记录，这里有一些选项需要填写：

| 选项                     | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 主机记录(YOUR_SUBDOMAIN) | 按照个人喜好填写，以我为例，我填入的内容为 "jellyfin"，这样的话，当一切设置完成后，要访问这个 IP 地址，只需要在浏览器中输入 "jellyfin.(YOUR_DOMAIN)"。 |
| 记录类型                 | 由于我们需要访问 IPv6 地址，因此这里填入的内容应该为 "AAAA"。 |
| 线路类型                 | 选择“默认”即可。                                             |
| 记录值                   | 填入你需要解析的 IPv6 地址，此处就填入你服务器的 IPv6 地址。 |
| 权重                     | 留空即可                                                     |
| TTL                      | 默认 "600" 即可                                              |

填写完毕后，点击添加，等待 DNS 服务器刷新缓存后，就可以使用 `http://(YOUR_SUBDOMAIN).(YOUR_DOMAIN):8096` 来访问 Jellyfin 服务了，根据应用的不同，端口号可能会有相应的改变。

接下来是额外教学，简单讲一下如何给 Jellyfin 配置 SSL 证书。首先进入[我的域名选项页](https://console.dnspod.cn/dns/list)，点击刚购买的域名，然后在想要配置 SSL 证书的记录的右侧，将鼠标放置在 SSL 选项上，点击**免费申请**，即可快速申请免费的 SSL 证书。按照提示申请证书后，等待证书签发，即可在[证书管理页面](https://console.cloud.tencent.com/ssl)下载证书。

这里选择 Tomcat(pfx 格式) 下载，压缩文件中有 `.pfx` 格式和 `.txt` 格式的两个文件。接下来打开 Jellyfin 控制台 -> 联网页面，在服务器地址设置里勾选**启用 HTTPS**，在 HTTPS 设置里，自定义 SSL 证书路径选择刚才下载的 `.pfx` 格式文件的路径，证书密码则填写 `.txt` 格式文件中的内容。保存完成后，重启 Jellyfin 服务，即可使用默认的 `8920` 端口采用 HTTPS 访问 Jellyfin 服务。

### 本地 DDNS 服务

进入腾讯云 -> 云 API 控制台 -> API Explorer -> DNSPod -> 记录相关接口 -> [修改记录页面](https://console.cloud.tencent.com/api/explorer?Product=dnspod&Version=2021-03-23&Action=ModifyRecord)，按照以下说明填写（未提及则留空）：

| 选项       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| Domain     | 填写你购买的域名                                             |
| SubDomain  | 填写需要修改的主机记录                                       |
| RecordType | AAAA                                                         |
| Value      | 这一栏将填入修改的 IP 地址，因此可以先随便填点啥占个坑       |
| RecordId   | 进入腾讯云 -> 云 API 控制台 -> API Explorer -> DNSPod -> 记录相关接口 -> [获取域名的解析记录列表页面](https://console.cloud.tencent.com/api/explorer?Product=dnspod&Version=2021-03-23&Action=DescribeRecordList)，输入 `Domain` 和 `SubDomain` ，点击最下方的发起调用，即可在返回结果中查询到。 |

全部填写完成后，就可以自动生成修改记录的代码。这里以 Python 代码为例继续下面的讲解。

我们需要对代码进行一些修改，来获取本机 IP 地址并修改记录，首先设置 API 访问密钥。下载保存 Python 代码后，根据 Python 代码中的提示，访问腾讯云控制台[访问密钥界面](https://console.cloud.tencent.com/cam/capi)，点击新建密钥，获得一对 SecretId 和 SecretKey，并修改代码中对应的位置。

接下来设置 Python 环境，使用 `pip install tencentcloud-sdk-python tencentcloud-sdk-python-dnspod` 安装依赖 SDK，如果下载速度过慢，可以调整 `pip` 镜像的设置。

接下来添加获取本机 IP 地址的代码：

```python
import os
import re

def get_global_ipv6_address():
    getIPV6_process = os.popen("/usr/sbin/ifconfig")
    output = str(getIPV6_process.read())
    result = re.findall(r"(([a-f0-9]{1,4}:){7}[a-f0-9]{1,4})", output, re.I)
    # print("本机当前公网IPv6地址为: ", result[0][0])
    return result[0][0]
```

这样返回的即为当前本机的 IPv6 地址。

{{<admonition tip>}}

在第 7 行的正则表达式中，采取了一个取巧的写法：

```python 
re.findall(r"(([a-f0-9]{1,4}:){7}[a-f0-9]{1,4})", output, re.I)[0][0]
```

等同于

```python
re.findall(r"[a-f0-9]{1,4}:[a-f0-9]{1,4}:[a-f0-9]{1,4}:[a-f0-9]{1,4}:[a-f0-9]{1,4}:[a-f0-9]{1,4}:[a-f0-9]{1,4}:[a-f0-9]{1,4}", output, re.I)[0]
```

第一种写法中，通过加入小括号，得以将 `[a-f0-9]{1,4}:` 的模式通过 `{7}` 来重复 7 次。然而，`re` 库有一个特点是，当正则表达式中存在小括号时，只输出小括号中的内容，以 `"1:2:3:4:5:6:7:8"` 为输入为例，当正则表达式为 `([a-f0-9]{1,4}:){7}[a-f0-9]{1,4}` 时，输出的内容为 `7:`，即小括号中模式的第 7 次重复。为了输出全部的内容，则需要在正则表达式最外层再添加一个小括号。在拥有两个小括号的情况下，返回值为一个元组，第一个位置为 `1:2:3:4:5:6:7:8`，第二个位置为 `7:`。

{{</admonition>}}

{{<admonition bug>}}

在第 5 行的调用 `ifconfig` 命令时，采用了绝对路径的写法。这是因为这个脚本将交给 Linux 下的自动化任务 `crontab` 程序处理，该程序在运行脚本时是没有环境变量的（除非使用命令指定）。因此如果使用 `ifconfig` 而不是绝对路径 `/usr/sbin/ifconfig`，将会导致自动化任务的异常。

{{</admonition>}}

最后将两份代码组合，效果如下：

```python
import os
import re
def get_global_ipv6_address():
    getIPV6_process = os.popen("/usr/sbin/ifconfig")
    output = str(getIPV6_process.read())
    result = re.findall(r"(([a-f0-9]{1,4}:){7}[a-f0-9]{1,4})", output, re.I)
    return result[0][0]
    
import json
from tencentcloud.common import credential
from tencentcloud.common.profile.client_profile import ClientProfile
from tencentcloud.common.profile.http_profile import HttpProfile
from tencentcloud.common.exception.tencent_cloud_sdk_exception import TencentCloudSDKException
from tencentcloud.dnspod.v20210323 import dnspod_client, models
try:
    cred = credential.Credential("YOUR_SECRETID", "YOUR_SECRETKEY")
    httpProfile = HttpProfile()
    httpProfile.endpoint = "dnspod.tencentcloudapi.com"
    clientProfile = ClientProfile()
    clientProfile.httpProfile = httpProfile
    client = dnspod_client.DnspodClient(cred, "", clientProfile)
    req = models.ModifyRecordRequest()
    params = {
        "Domain": "YOUR_DOMAIN",
        "SubDomain": "YOUR_SUBDOMAIN",
        "RecordType": "AAAA",
        "RecordLine": "默认",
        "Value": get_global_ipv6_address(),
        "RecordId": YOUR_RECORDID
    }
    req.from_json_string(json.dumps(params))
    resp = client.ModifyRecord(req)

except TencentCloudSDKException as err:
    print(err)
```

在设置自动化任务之前，运行一下脚本确定无误。

最后设置自动化脚本，这里使用 `crontab` 工具进行设置，Fedora 系统可以运行 `dnf install cronie` 来安装。安装完成后，使用命令 `crontab -e` 编辑自动化命令，输入 `0 * * * * YOUR_PYTHON_PATH YOUR_SCRIPT_PATH` ，这里的 `0 * * * *` 表示每小时的 0 分运行后面的命令，`YOUR_PYTHON_PATH` 和 `YOUR_SCRIPT_PATH` 都应该填写绝对地址。

编辑自动命令后，我们需要使用命令 `service crond start` 来启动自动化命令的服务。至此，全部的设置完成。


---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/jellyfineverywhere/  

