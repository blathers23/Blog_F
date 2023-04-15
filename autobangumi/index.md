# 使用 Python 实现自动追番! 

<!--more-->

> 当我花了 2 天把这个脚本折腾出来之后发现, 这季新番没有想看的, 只剩下深深的空虚 :(

## 序言

考研结束，人也终于闲了下来. 前两天刚到北京和大学同学重聚, 大家都挺好的; 又狠狠打了几天游戏, 想打新卷堡传说 600 从头工打到尾工也是没打上去; 还把吃灰多年的 kindle 捡起来擦了擦, 打算从头看<脑髓地狱>, 翻了几页最后还是看不进去. 终于是躺的开始头疼了, 打算是把一直想做的自动追番脚本写一下. 

之前一直使用这样的流程追番: 首先从设定好的 RSS 订阅里获取动画片的种子, 然后使用 qbittorrent 下载, 接下来使用 tmm 刮削数据, 最后在 Jellyfin 的 web 控制台刷新媒体库. 这样一套流程下来, 只能评价为繁琐. 在 RSS 订阅里找种子需要动手, 等待下载和刮削需要时间, 刷新媒体库还要打开浏览器. 就这样看了一个季度的动画片之后, 萌生了写一个自动追番脚本的想法. 

那么如何实现一个自动追番脚本呢? 首先明确一下脚本的功能: 可以根据给定的信息自动解析 RSS 订阅; 可以根据种子自动下载文件并重命名以及可以自动刷新媒体库并刮削. 最开始的时候, 我曾考虑过在脚本中使用 tmm 刮削视频元数据然后刷新媒体库, 然而事实上，jellyfin 完全可以通过功能强大的插件在刷新媒体库时自动实现刮削的功能, 少走一步弯路. 这里推荐使用 [MetaShark](https://gitee.com/cwhzy/jellyfin-plugin-metashark) 插件, 可以刮削豆瓣和 TMDB 的元数据.

## RSS 解析

Python 使用 feedparser 包实现 RSS 订阅的解析.`feedparser.parse()` 方法以一个 `str` 类型的链接作为输入，解析 RSS 链接并以类似于字典的 `feedparser.FeedParserDict` 类型返回. 得到返回后, 根据字典的内容查询种子的下载链接.

订阅的链接可以人为设置, 也可以由给定的条件自动形成. ,为了最大化考虑兼容性, 我这里采用由给定动画片名称和搜索条件, 由脚本自动生成 RSS 链接的方式. 这种方式的好处在与可以根据不同的 RSS 订阅源采取不同的解析方式. 这里以 [agerip](https://acg.rip/) 的订阅源为例. agerip 的 RSS 订阅遵循以下的形式: `https://acg.rip/.xml?term=[要查询的内容]`, `[要查询的内容]` 块内的内容必须是经 url 编码后的内容, 不能出现中文. 这里调用 `urllib` 库中的 `quote` 方法对块内容进行 url 编码处理.

示例代码如下:

```python
def _parse_rss(self) -> feedparser.FeedParserDict:
    r"""
    根据 Anime 的名称和条件解析 RSS 订阅链接，返回一个字典。
    
    字典中应包含['published_parsed'], ['title'], 及['links'][1]['href']三个键, 
    分别为 time.struct_time 类型的发布时间, str 类型的 Anime 标题, 以及对应 Anime 的种子的下载链接
    """
    if (self.source == "agerip"):
        source_link = "https://acg.rip/.xml?term=" + quote(name) + "%2B" + quote(condition)

        return feedparser.parse(source_link)['entries']
```

## 种子链接下载

种子通过 qbittorrent 软件进行下载, 为了通过 Python 与正在运行的 qbittorrent 程序进行通信, 需要使用 `qbittorrentapi` 库调用所需的 api. 

示例代码如下:

```python
from qbittorrentapi import Client as QbittorrentClient

qb = QbittorrentClient(
    host = BASE_URL,
    port = QB_PORT,
    username = QB_USERNAME,
    password = QB_PASSWORD,
)
```

`qbittorrent` 库提供了两种下载方式, 分别是使用 `str` 类型的磁力链接下载和使用 `str` 类型的种子文件地址下载. 在上一步中, 我们通过某些处理手段获得了种子文件的 http 协议下载链接, 为了能够下载种子中的内容, 首先要将种子文件下载到某个位置. 这里使用 `urllib` 库中的 `urlretrieve` 方法对种子文件进行下载. 然后将种子文件的地址传递给 `qbittorrent.Client` 类型对象的 `torrents.add` 方法对种子文件进行下载.

示例代码如下:

```python
torrent_paths = [urlretrieve(link)[0] for link in torrent_links]
qb.torrents_add(torrent_files = torrent_paths, category = name)
```

`torrent_files` 参数可以以地址的列表为输入. 同时, 这里将下载的文件在 qb 中放入与下载名称相同的分类, 便于后期的管理. 

当我们通过种子地址提交任务之后, 为了能确定种子文件地址与 qb 中种子下载的文件的对应关系, 我们还需要计算种子的哈希值. 这里使用 `torf` 库 `Torrent` 类的 `infohash` 方法. 

计算哈希值和查询对应哈希值种子下载信息的示例代码如下:

```python
torrent_hashes = [Torrent.read(path).infohash for path in torrent_paths]
torrents_info = qb.torrents_info(torrent_hashes = torrent_hashes)
```

**这里有一点需要注意的是, 传入种子哈希值列表的顺序并不对应返回列表种子下载信息的顺序, 这是因为在内部的实现中, 传入的哈希值列表将拼接为一个字符串传递给 qb 的 api, 非常迷惑的设计.**

## 文件重命名

在对文件重命名前, 我们需要的信息包括: 动画片的名称, 季数, 以及集数, 有了这些信息, 才能唯一确定一部动画片. 最稳妥的方法应当是从 RSS 订阅每一条的 `title` 中读取这些信息, 然而, 不同字幕组之间, 信息组织的格式不同, 使用正则语句直接读取这些信息存在一些困难. 

这里我采用的方法是, 给定动画片的名称和季数作为 RSS 订阅的搜索条件, 根据集数和对应集数的时间戳, 自动推导当前的集数. 具体的做法是: 获取 RSS 订阅之后, 从最新一条订阅逐条向后查找, 直到找到比给定的集数和对应的时间戳更旧的条目位置, 按时间顺序得到集数.

这种做法断然会遇到很多限制, 其一, 对 RSS 订阅的质量有较高的要求, 这需要人手动测试订阅查询的条件是否满足这种方法的标准; 其二, 由于 RSS 订阅有深度限制, 这种方式对于较老以及有着较多集数的动画片不能发挥自动下载的功能. 但事实上, 针对第一条, 这点代价对于在某个季度查找新番来说并没有多出太多工作, 针对第二条, 老番直接找全集下载更加方便, 较旧动画片的单集也很难说下载的动.

得到这些信息后, 只需要按照 jellyfin 的要求重命名即可.

## 刮削

将整理好的文件放到对应位置后, 只需要调用 jellyfin 的扫描媒体库功能即可自动进行刮削. 在一些系统上, jellyfin 可能支持检测文件变化并自动刷新媒体库, 然而很可惜的是我的系统不在此范围之内. 这里采用 `jellyfin_apiclient_python` 库调用 jellyfin 的 api.

示例代码如下:

```python
from jellyfin_apiclient_python import JellyfinClient

jf = JellyfinClient()
jf.config.app("app", __version__, "name", JELLYFIN_USERNAME)
jf.config.data["auth.ssl"] = True
jf.auth.connect_to_address(BASE_URL + ":" + JELLYFIN_PORT)
jf.auth.login(BASE_URL + ":" + JELLYFIN_PORT, 
              JELLYFIN_USERNAME, 
              JELLYFIN_PASSWORD)

# 使用内建的 POST 方法调用刷新媒体库的 api
jf.jellyfin._post("Library/Refresh")
```

## 组合

实现主要的模块之后, 只需要将这些模块按需求组织起来即可实现自动追番的功能. 我的脚本如下:

```python
import time
import os
from shutil import copyfile
from reprint import output
from torf import Torrent
from urllib.request import urlretrieve
from urllib.parse import quote
import feedparser
from qbittorrentapi import Client as QbittorrentClient
from jellyfin_apiclient_python import JellyfinClient

NAME = "AutoAnime"
VERSION = "1.4.1"
HOST_NAME = ""
BASE_URL = "" 
BASE_PATH = ""
FEED_PATH = "aniFeed.txt"
CKPT_PATH = "aniCkpt.txt"
JELLYFIN_PORT = ""
JELLYFIN_USERNAME = ""
JELLYFIN_PASSWORD = ""
QB_PORT = ""
QB_USERNAME = ""
QB_PASSWORD = ""
REFRESH = 10
DOWNLOAD = False

jf = JellyfinClient()
jf.config.app(NAME, VERSION, HOST_NAME, JELLYFIN_USERNAME)
jf.config.data["auth.ssl"] = True
jf.auth.connect_to_address(BASE_URL + ":" + JELLYFIN_PORT)
jf.auth.login(BASE_URL + ":" + JELLYFIN_PORT, 
              JELLYFIN_USERNAME, 
              JELLYFIN_PASSWORD)

qb = QbittorrentClient(
    host = BASE_URL,
    port = QB_PORT,
    username = QB_USERNAME,
    password = QB_PASSWORD,
)

class Anime():
    def __init__(self, name: str, condition: str, season = 1, episode = 0, published = 0., source = "agerip") -> None:
        r"""
        初始化 Anime 类
        """
        self.name = name
        self.condition = condition
        self.season = season
        self.episode = episode
        self.published = published
        self.source = source
        self.folder = BASE_PATH + "/" + self.name + "/Season " + str(self.season)
        
        print("导入的追番:", self.name)
        if os.path.exists(self.folder):
            print("文件夹已经存在...")
        else:
            os.makedirs(self.folder)
            print("创建文件夹...")

    def _parse_rss(self) -> feedparser.FeedParserDict:
        r"""
        根据 Anime 的名称和条件解析 RSS 订阅链接，返回一个字典。

        字典中应包含['published_parsed'], ['title'], 及['links'][1]['href']三个键, 
        分别为 time.struct_time 类型的发布时间, str 类型的 Anime 标题, 以及对应 Anime 的种子的下载链接
        """
        if (self.source == "agerip"):
            source_link = "https://acg.rip/.xml?term=" + quote(self.name) + "%2B" + quote(self.condition)

        return feedparser.parse(source_link)['entries']
    
    def _download_anime(self, torrent_links: list) -> list:
        r"""
        以一个种子链接的列表作为输入参数，下载种子中的内容

        以与输入列表同序的下载内容地址的列表为返回
        """
        torrent_paths = [urlretrieve(link)[0] for link in torrent_links]

        if qb.torrents_add(torrent_files = torrent_paths, category = self.name) != "Ok.":
            return []

        print("已提交任务, 等待任务启动...")
        while(len(qb.torrents_info(status_filter = "downloading", category = self.name)) == 0):
            time.sleep(1)
        
        print("传输中的信息: ")
        with output(output_type = "dict") as output_lines:
            while(len(qb.torrents_info(status_filter = "downloading", category = self.name)) != 0):
                for torrent in qb.torrents_info(status_filter = "downloading", category = self.name):
                    output_lines[torrent['name']] = "\t进度:{:.4%}".format(torrent['progress'])

                time.sleep(.5)

        return [qb.torrents_info(
                torrent_hashes = [Torrent.read(path).infohash]
                )[0]['content_path'] 
                for path in torrent_paths]
    
    def _copy(self, index: list, file_paths: list) -> None:
        r"""
        以集数列表及对应的文件地址列表为输入

        将文件复制到 Anime 存放的位置并按集数进行重命名
        """
        for i, file_path in zip(index, file_paths):
            print("正在复制第 {} 集...".format(i))
            if (os.path.isfile(file_path)):
                copyfile(file_path, self.folder + 
                         "/S" + str(self.season) + 
                         "E" + str(i) + 
                         file_path[file_path.rfind('.'):])
            
            else:
                files = [os.path.join(path, name) 
                         for path, _, names in os.walk(file_path) 
                         for name in names]

                for file in files:
                    if (file[-4:] == ".zip"):
                        continue

                    elif (file[-4:] == ".ass"):
                        if (file.rfind('.', 0, len(file) - 4) != -1): 
                            copyfile(file, self.folder + 
                                     "/S" + str(self.season) + 
                                     "E" + str(i) + 
                                     file[file.rfind('.', 0, len(file) - 4):])

                        else:
                            copyfile(file, self.folder + 
                                     "/S" + str(self.season) + 
                                     "E" + str(i) + 
                                     file[file.rfind('.'):])

                    else:
                        copyfile(file, self.folder + 
                                 "/S" + str(self.season) + 
                                 "E" + str(i) + 
                                 file[file.rfind('.'):])
                        
    def _check_updates_online(self, download = False) -> None:
        r"""
        联网检查是否有更新并下载
        """
        print("联网检查更新中...".format(self.name))
        rss_data = self._parse_rss()

        if (rss_data == []):
            print("解析 rss 出现错误，请检查! ")

        torrent_links = []
        i = 0
        while(i < len(rss_data) and (self.published < time.mktime(rss_data[i]['published_parsed']))):
            print(rss_data[i]['title'])
            torrent_links.append(rss_data[i]['links'][1]['href'])
            i += 1

        torrent_links.reverse()

        if len(torrent_links) > 0:
            index = list(range(self.episode + 1, self.episode + len(torrent_links) + 1))
            print("检测到 {} 集更新, 准备下载...".format(index))

            if (download):
                file_paths = self._download_anime(torrent_links)
            else:
                file_paths = []
                self.episode = index[-1]
                self.published = time.mktime(rss_data[0]['published_parsed'])

                return

            if (file_paths == []):
                print("下载失败力...qaq")
            else:
                print("下载成功！")

                self._copy(index, file_paths)
                print("文件拷贝成功！")

                jf.jellyfin._post("Library/Refresh")
                print("媒体库刷新中...")

            self.episode = index[-1]
            self.published = time.mktime(rss_data[0]['published_parsed'])

        else:
            print("未检测到更新 :(")

    def update(self, download: bool) -> None:
        r"""
        检查上次更新和当前时间的差, 若超过 7 天, 则联网检查更新
        """
        if (time.mktime(time.gmtime()) - self.published > 604800):
            self._check_updates_online(download)
        
        else:
            print("还未到更新的时间捏 ;)")

def _check_file(path: str) -> list:
    r"""
    检查输入文件, 返回 Anime 记录列表
    """
    aniRecords = []
    with open(path, "r") as f:
        lines = f.read().splitlines()
        for line in lines:
            record = line.split(',')
            if (len(record) >= 2 and len(record) <= 6):
                aniRecords.append(record + ([''] * (6 - len(record))))
            
    return aniRecords

def _make_ckpt(aniDict: dict) -> None:
    r"""
    创建检查点
    """
    with open(CKPT_PATH, "w") as f:
        for Ani in aniDict:
            f.write(aniDict[Ani].name + ',' + 
                    aniDict[Ani].condition + ',' + 
                    str(aniDict[Ani].season) + ',' + 
                    str(aniDict[Ani].episode) + ',' + 
                    str(aniDict[Ani].published) + ',' + 
                    aniDict[Ani].source + '\n')
            
    print("检查点已经创建")

def _new_anime(aniInfo: list) -> Anime:
    r"""
    根据导入的信息初始化 Anime 对象
    """
    season = 1
    episode = 0
    published =  0.
    source = "agerip"

    if aniInfo[2] != '':
        season = int(aniInfo[2])

    if aniInfo[3] != '':
        episode = int(aniInfo[3])
        published = float(aniInfo[4])

    if aniInfo[5] != '':
        source = aniInfo[5]

    return Anime(aniInfo[0], aniInfo[1], season, episode, published, source)

def main():
    aniDict = dict()

    print("正在启动...", NAME)
    print("版本:", VERSION)

    print("从检查点恢复中...")

    for aniInfo in _check_file(CKPT_PATH):
        aniDict[aniInfo[0]] = _new_anime(aniInfo)
        print()

    while (True):
        print("检查订阅...")
        keepAlive = []
        for aniInfo in _check_file(FEED_PATH):
            keepAlive.append(aniInfo[0])

            if (aniInfo[0] not in aniDict):
                aniDict[aniInfo[0]] = _new_anime(aniInfo)
                print()

        for aniName in list(set(aniDict.keys()) - set(keepAlive)):
            print("删除动漫 {}\n".format(aniName))
            del aniDict[aniName]

        print("检查更新...")
        for Ani in aniDict:
            print("Anime:", aniDict[Ani].name)
            print("当前集数: {}\n上次更新时间: {}".format(aniDict[Ani].episode, time.ctime(aniDict[Ani].published + 28800)))
            aniDict[Ani].update(DOWNLOAD)
            print()

        _make_ckpt(aniDict)
        print()
        
        time.sleep(REFRESH)

if __name__ == "__main__":
    main()
```





---

> 作者: Blathers  
> URL: https://blathers23.netlify.app/autobangumi/  

