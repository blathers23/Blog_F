# 浅入浅出 Hugo 教程


<!--more-->

> 轻松拿下 Hugo，因为我站在大佬的肩膀上。&emsp;

{{< admonition tip "提示">}}

如有问题欢迎[与我联系](/about/##与我联系)😀。

{{< /admonition >}}

## 0. 安装 Hugo

---

**Hugo** 可以选择二进制安装或者自行下载源码编译安装。此处选择轻松简单快捷的二进制安装。进入 [Hugo Releases](https://github.com/gohugoio/hugo/releases)，根据你的操作系统选择相应的版本。在此处我推荐安装 **Hugo_extended** 版本。该版本可以允许你更加轻松的修改主题。

## 1. 生成站点

---

以 Windows 系统为例，下载二进制程序后，在程序所在文件夹打开 `PowerShell`，运行：

```shell
.\hugo new site .\your_stie_name
```

即可在 `.\your_site_name` 路径下生成你的站点文件夹。现在：

```shell
cd .\your_site_name
..\hugo new about.md
```

就可以看到 `about.md` 自动生成到了 `\your_stie_name\content` 文件夹下。请注意，当前你的 `PowerShell` 的路径应当在 `\your_stie_name`，这意味着 Hugo 将为该站点生成 `about.md`。事实上，你可以通过 Hugo 生成并管理多个站点。

{{<admonition tip "提示">}}

你也可以不通过 `hugo new` 而是手动新建 `.md` 文件在 `\content` 文件夹下。你所有的页面都将由该文件夹下的 `.md` 文件生成。

{{</admonition>}}

现在打开 `about.md`，你将看到：

```yaml
---
title: "About"
date: 2022-04-19T21:43:04+08:00
---

正文内容
```

夹在两排 `---` 之间的内容是对将要生成的页面的描述，包含了标题，日期等内容。现在创建第一篇文章，放到 `posts` 目录，方便之后生成聚合页面：

```shell
..\hugo new posts\first.md
```

打开并编辑 `first.md`：

```yaml
---
date: "2022-04-19T23:33:33+08:00"
title: "first"
---
# Hello Hugo

 1. aaa
 2. bbb
 3. ccc
```

就可以对你将要发布的页面进行编辑。此时你可能正在寻找一款好用的 Markdown 编辑器， [Typora](https://typoraio.cn/) 是很棒的选择。

## 2. 挑选主题

---

在正式发布你的页面之前，你还需要为页面挑选一款耐看的主题。进入 [Hugo themes](https://themes.gohugo.io/) 网页挑选主题并下载后，将其解压到 `\your_stie_name\themes` 文件夹下，例如我下载了 Diary 主题，那么我的文件夹布局为：`\your_stie_name\themes\diary`。请注意，你可以将主题的文件夹修改成一个好记的名字，以后将多次键入这个文件夹的名字。

{{< admonition info "注意"  >}}

不同的主题在一些细节上可能有所不同，当然这是日后的课题。对于不同的主题，你可以查询该主题的文档来获得更多的支持。

{{< /admonition >}}

## 3. 测试你的页面

---

在挑选并下载主题之后，你可以在本地运行你的博客并查看效果。在 `\your_site_name` 打开 PowerShell，运行：

``` shell
..\hugo --theme=your_theme_name server
```

你就可以在 `http://localhost:1313/` 预览网页的效果。值得一提的是，如果你此时对 `about.md` 或者 `config.toml` （稍后会讲到）等进行修改并保存，这些改动将会实时的反映在你的网页上。

## 4. 发布你的页面

---

当你对页面改动满意后，你可以将你的页面发布到 GitHub 上以便使用 GitHub Pages 来托管你的网页。首先在 GitHub 上为你的 Blog 新建一个仓库，然后在 `your_site_name` 文件夹下运行：

```shell
..\hugo --theme=your_theme_name
```

运行结束后，你的页面将会被保存到 `\your_site_name\public` 文件下，接下来你可以将该文件夹上传到仓库，运行：

```shell
cd .\public\
git init
git remote add origin https://github.com/your_user_name/your_repo_name.git
git add .
git commit -m 'first commit'
git push -u origin master
```

## 5. GitHub Pages or Netlify

---

进入你的仓库，选择 `settings - pages`，`Source` 选择 `master`，`folder` 选择 `\(root)`，然后点击 `save`。一段时间后，你的 GitHub Pages 就可以成功访问了。这里建议将仓库命名为 `your_user_name.github.io`，这样的话，你的主页网址将为：`https:\\your_user_name.github.io` 。对于一般仓库名，你的主页网址将为：`https:\\your_user_name.github.io\your_repo_name`。

除了 GitHub Pages 外，你也可以选择其他的静态页面托管服务。这里简短介绍 [Netlify](https://www.netlify.com/)。只需要通过 GitHub 登录 Netlify，你就可以直接将仓库中的网页部署在 Netlify 中，并且支持自定义域名和自动部署等便利的功能。

访问你的页面，你可能会发现一些问题，例如点击页面链接后跳转失败等。这些问题需要通过一些设置来解决。👇

## 6. config.toml

---

该文件位于 `\your_site_name` 文件夹下，页面的大多数设置都可以在此处调整。首先应当调整 `baseURL` 项为你的主页网址。这样就可以使链接跳转正常。

除此之外，页面的其他设置也可以在此处调整。例如页面的边栏 `sidebar`，你可以在 `config.toml` 文件中添加:

```toml
[[menu.main]]
 url = "/posts"
 name = "Archive"
 weight = 1

[[menu.main]]
 url = "/tags"
 name = "Tags"
 weight = 2

[[menu.main]]
 url = "/categories"
 name = "Categories"
 weight = 3

[[menu.main]]
 url = "/about"
 name = "About"
 weight = 4
```

就可以将页面调整的边栏调整为4个选项，分别是 `Archive`、`Tags`、`Categories` 和 `About`。对于 `\about` ，在 `\content` 文件夹内，该地址为一个 `.md` 文件，因此该边栏选项点击后将呈现 `About` 页面，也就是根据你在 `about.md` 中所键入内容生成的网页。对于 `\posts` ，在 `\content` 文件夹下，该地址为一个文件夹，因此该边栏选项点击后将呈现一个页面聚合。`\tags` 和 `\categories` 则是自动对标签和分类进行聚集生成的页面，有关标签和分类的内容将会在稍后讨论。

{{<admonition tip "提示">}}

有关 `\` 和 `/` 的差别，这是 Windows 和 Linux 的一个稍稍的不同。

{{< /admonition >}}

另外一个例子是网页图标，你也可以在 `config.toml` 中进行控制，你可以输入：

```toml
[params]
 favicon = "your_image_path"
```

来控制网页的图标位置。你需要将你的图片放入到 `\your_site_name\static\images` 文件夹下。例如我将图片 `QUIN.png` 放置到该文件夹中，对于 GitHub Pages 的一般仓库名，我的 `"your_image_path"` 应当为 `"\your_repo_name\images\QUIN.png"`，而对于 Netlify 和 GitHub Pages 的独有仓库名，我的`"your_image_path"` 应当为 `"\images\QUIN.png"`。

{{<admonition note "注意">}}

尽管这种方式可以正确的加载网页图标和页面头图（稍后会讲到），但是这种方式并**不能很好的支持页面内图片**的内容，我不清楚这是什么原因。但是这种方式来放置页面内图片，不仅会导致本地编辑 `.md` 文件时不能正常预览图片，有限的网速也会导致网页浏览体验下降，因此可以使用网络图床来解决这个问题。

{{<admonition tip "提示">}}
当然网页图标和页面头图也可以使用图床。
{{</admonition>}}

{{</admonition>}}

当然，网页当中还有很多元素可以自定义，根据你使用的主题不同，又有一些额外的内容可以控制。对于额外的内容，你需要查询你选择的主题的文档，对于通用内容，你可以阅读 [Hugo 文档](https://gohugo.io/variables/)来学习如何调整。

## 7. 写作

---

完成上述设置后，推送你所完成的更改，可以看到网页已经可以正常使用了。接下来我们将要讨论的是写作中一些剩余的问题。在 `.md` 文件的开头，夹在两行 `---` 之间的内容称为元数据（meta data）。这些元数据根据你所选主题的不同，可能会有些出入，请参考你所选择的主题其中的项目。这里以 Diary 主题为例，对一些元数据进行介绍：

```yaml
title: "浅入浅出 Hugo 教程"
date: 2022-04-24T16:33:00+08:00
lastmod: "2022-04-24"
description: "轻松拿下 Hugo，因为我站在大佬的肩膀上。"
featured_image: ""
tags: [Hugo, Blog]
categories: 教程
comment: True
enableLaTeX: False
```

- `title`：文章的标题
-  `data`：文章创建的时间
- `lastmod`：文章最后修改的时间
- `featured_image`：文章头图
- `tags`：标签
- `categories`：分类
- `comment`：时候启用评论
- `enableLaTex`：是否启用 LATEX

不同的主题**可能**有所出入，这里需要以你所用主题的文档为准。以主题 Diary 为例，当你为一篇文章添加 `tags` 和 `categories` 后，将会自动聚合内容生成 `\Tags` 和 `\Categories` 页面，尽管这两个文件夹并不在你 `\content` 文件夹下。

## 8. 评论区

---

评论区很多不同的选择，在这里介绍 **Waline** 评论系统。对于主题 Diary 来说，已经内置了 Waline 评论系统，只需要在 `config.toml` 中添加：

```toml
[params]
 walineServer = "your_waline_server"
```

即可添加评论区。对于其它主题，你可能需要查询其文档来确定是否原生支持 Waline，若不支持且你被允许修改主题的内容，你可以参考 [Waline 官方教程](https://waline.js.org/guide/client/import.html#leancloud-%E8%AE%BE%E7%BD%AE-%E6%95%B0%E6%8D%AE%E5%BA%93)来引入 Waline 评论系统。

一旦你可以正确的引入客户端，你就可以设置 WalineServer。设置 WalineServer 的方式也在 Waline 官网提供了详细的[教程](https://waline.js.org/guide/get-started.html)。这里提醒一点，当完成 WalineServer 的设置后，一定不要忘记注册评论管理页面，首个在评论管理页面注册的用户将成为评论区管理员。

对于 Waline 的其它功能，例如自定义表情，自定义样式等，你可能需要在主题的 HTML 文件中进行修改。以主题 Diary 为例，你需要打开位于 `your_site_name\themes\diary\layouts\partials` 文件夹下的 `comment.html` 文件，对其中的内容进行修改。

以添加自定义表情为例，你需要对 `comment.html` 下 `new Waline` 中的内容进行修改。如要添加哔哩哔哩的表情，你需要将 `new Waline` 中的内容修改为：

```html
<script>
    new Waline({
        el: '#waline',
        path: location.pathname,
        serverURL: {{.Site.Params.walineServer}},
        emoji: [
            'https://cdn.jsdelivr.net/gh/walinejs/emojis@1.0.0/bilibili',
        ],
    });
</script>
```

而以添加自定义样式为例，你则需要在 `comment.html` 中添加：

```html
<style>
	:root {
      /* 主题色 */
      --waline-theme-color: #27ae60;
      --waline-active-color: #2ecc71;
    }
</style>
```

即可对主题色进行修改。更多内容，请参考 [Waline 官方文档](https://waline.js.org/)。

## ⑨. 图床

---

优秀的图床可以大大提升网站浏览的体验，在这里推荐 **PicGo** 图片上传及管理工具。你可以在 [PicGo releases](https://github.com/Molunerfinn/PicGo/releases) 页面下载 PicGo。这里以 SM.MS 图床加 PicGo 简单介绍以下使用 PicGo 进行图片上传及管理的流程。

首先打开 [SM.MS](https://sm.ms/) 官网，进行注册并登录后，点击 `User - Dashboard`，跳转页面后选择 `API token`，然后点击 `Generate Secret Token` 就可生成你的 token。

接下来打开 PicGo，选择 `图床设置 - SM.MS图床`，将上一步生成的 token 填入并点击确定，就完成了全部的设置。PicGo 同样支持很多其它图床，也包括自定义图床。其它内容请参考 [PicGo 文档](https://picgo.github.io/PicGo-Doc/)。

🙌 至此，本教程的全部内容已经施工完成。


