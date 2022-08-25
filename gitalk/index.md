# 记录Gitalk的一个Bug

 <!--more-->

**这篇文章来自以往的 Blog，此处仅作搬运收藏，可能不再对内容进行订正**

**✍️ Gitalk版本 1.7.0；Gridea版本 0.9.2。**

使用 Gridea 配置 Gitalk 功能，进入网页，登录 GitHub 提示 403 错误。这是由于 Gitalk 使用的反向代理网站在 2021 年 1 月 31 日起开始限制使用。参照[这篇](https://mp.weixin.qq.com/s/Lwl9rf95EqlTYLfconjflQ)文章，总结 Gridea 的解决方式如下（博主使用了 Windows 系统）：

使用 everything 搜索 `gitalk.ejs` 可以找到如下文件：
![](https://s2.loli.net/2022/04/26/Q2ACBcFn57vw3mG.png)
博主的 Gridea 安装地址为： `D:/Blog_Gridea`，站点源文件的保存位置为 `D:/Blog_Gridea_Documents`，~~博主并不确定应该修改哪一项，于是采用了这两者都修改的方式。这里以修改 `D:/Blog_Gridea_Documents` 中的文件为例~~，这里我们只需要修改站点源文件保存位置内的文件。博主目前使用的主题是 notes，因此打开 `D~~:/Blog_Gridea_Documents/themes/notes/templates/includes/gitalk.ejs`，将下述代码添加到第16行（即 `new Gitalk` 中）（这里要注意格式）

```json
proxy: '<%= commentSetting.gitalkSetting.proxy %>'
```
![](https://s2.loli.net/2022/04/26/3diToUMhzEbrq7J.png)
然后打开 `D:/Blog_Gridea_Documents/config/setting.json`，将下述代码添加到第26行（即 `gitalkSetting` 中）（同样注意格式）

```json
"proxy": "https://netnr-proxy.cloudno.de/https://github.com/login/oauth/access_token"
```
![](https://s2.loli.net/2022/04/26/74dyQRothBHJwWG.png)

~~⚠️ 博主将 `D:/Blog_Gridea` 下的文件做了上述同样的处理，这里要注意 `D:/Blog_Gridea` 中的 theme 和 config 文件夹在 default_files 下。博主并不清楚是否只做一处修改就可以解决Bug。~~

最后，只需要在 Gridea 中点击同步，等待网站部署完成即可。以上，就是解决该 Bug 的全部操作。

*2021/2/14 订正* 
*（小问号：`,`接  '~~' 会导致删除线不能正确出现）*

*2022/4/26 补充上条：*
*这是因为你多打了个 `~~`。*

---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/gitalk/  

