---
title: 黑科技：强大的you-get
date: 2016-04-04 19:24:40
tags: 黑科技
---
![](http://7xsgf8.com1.z0.glb.clouddn.com/image/bg%E8%A7%86%E9%A2%91%E7%BD%91%E7%AB%99.jpg)


        you-get，强大的下载视频、图片、文件的工具。

<!--more-->
# [you-get](https://github.com/soimort/you-get)

    $ you-get http://www.fsf.org/blogs/rms/20140407-geneva-tedx-talk-free-software-free-society
    Site:       fsf.org
    Title:      TEDxGE2014_Stallman05_LQ
    Type:       WebM video (video/webm)
    Size:       27.12 MiB (28435804 Bytes)

    Downloading TEDxGE2014_Stallman05_LQ.webm ...
    100.0% ( 27.1/27.1 MB) ├████████████████████████████████████████┤[1/1]   12 MB/s

## you-get之功用:

下载流行网站之音视频，例如YouTube, Youku, Niconico,以及更多. (查看完整支持列表)
于您心仪的媒体播放器中观看在线视频，脱离浏览器与广告
下载您喜欢网页上的图片
下载任何非HTML内容，例如二进制文件

## 安装
以下乃必要依赖，需要单独安装，除非于Windows下使用预包装包:

Python 3(必须安装)
FFmpeg (强烈推荐) or Libav
(可选) RTMPDump（至少安装一个）

** 注意：这个地方一定要√ ![图片](http://7xsgf8.com1.z0.glb.clouddn.com/image/bgyou-get%20pathon.png)




# 有四种安装方式，有Git工具的建议clone
选项 1: 通过pip安装

you-get之官方版本通过PyPI分发, 可从PyPI镜像中通过pip 包管理器安装. 须知您务必使用版本3的 pip:

    $ pip3 install you-get
选项 2: 使用预装包(仅供Windows)

exe (单独文件) 或 7z (包括所有依赖) 可 从https://github.com/soimort/you-get/releases/latest 下载.

选项 3: 于GitHub下载

您可选择稳定版 (与PyPI最新版等同) 或 开发版 (更多的热补丁与不稳定功能)的you-get. 解压并将含有you-get的目录加入PATH.

或者, 运行

    $ make install
以安装you-get 于永久路径.

选项 4: Git clone

即使您不常使用Python，作为开发者，也请使用此方法。

    $ git clone git://github.com/soimort/you-get.git
将目录加入 PATH, 或运行 make install 以安装you-get 于永久路径.

选项 5：用我下载好的安装包
[python-3.5.1-64](http://pan.baidu.com/s/1nv1V9dJ)
[python-3.5.1-32](http://pan.baidu.com/s/1cJSShS)
[ffmpeg-3.0.1.tar](http://pan.baidu.com/s/1hs2fKHY)
[libav-0.8.17.tar(前一个安装了这个不用再安装了)](http://pan.baidu.com/s/1eR296AA)
[Git](https://git-scm.com/download/)


## 升级

考虑到 you-get 安装方法之差异, 请使用:

    $ pip3 install --upgrade you-get
或下载最新更新:

    $ you-get https://github.com/soimort/you-get/archive/master.zip

# 开始

## 下载视频

当观赏感兴趣之视频，您可以使用 --info/-i 以查看所有可用画质与格式、s:

    $ you-get -i 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
    site:                YouTube
    title:               Me at the zoo
    streams:             # Available quality and codecs
        [ DEFAULT ] _________________________________
        - itag:          43
          container:     webm
          quality:       medium
          size:          0.5 MiB (564215 bytes)
        # download-with: you-get --itag=43 [URL]

        - itag:          18
          container:     mp4
          quality:       medium
        # download-with: you-get --itag=18 [URL]

        - itag:          5
          container:     flv
          quality:       small
        # download-with: you-get --itag=5 [URL]

        - itag:          36
          container:     3gp
          quality:       small
        # download-with: you-get --itag=36 [URL]

        - itag:          17
          container:     3gp
          quality:       small
        # download-with: you-get --itag=17 [URL]
标有DEFAULT 为默认画质。如认同，可下载:

    $ you-get 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
    site:                YouTube
    title:               Me at the zoo
    stream:
        - itag:          43
          container:     webm
          quality:       medium
          size:          0.5 MiB (564215 bytes)
        # download-with: you-get --itag=43 [URL]
    Downloading zoo.webm ...
    100.0% (  0.5/0.5  MB) ├████████████████████████████████████████┤[1/1]    7 MB/s

    Saving Me at the zoo.en.srt ...Done.
    (如YouTube视频带有字幕，将被一同下载，以SubRip格式保存.)

    或，如您希望其他格式(mp4)，请使用其他提示选项:

    $ you-get --itag=18 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
## 注意:

目前，格式选择没有大规模铺开；默认选项为最高画质.
ffmpeg为必要依赖，以下载流式视频以及合并分块视频(例如，类似Youku), 以及YouTube的1080p或更高分辨率.
如不希望you-get合并视频，使用--no-merge/-n.
下载其他内容

如你有URL，可以直接使用:

    $ you-get https://stallman.org/rms.jpg
    Site:       stallman.org
    Title:      rms
    Type:       JPEG Image (image/jpeg)
    Size:       0.06 MiB (66482 Bytes)

    Downloading rms.jpg ...
    100.0% (  0.1/0.1  MB) ├████████████████████████████████████████┤[1/1]  127 kB/s
或者, you-get将自动检查网页，下载一切有可能感兴趣的内容:

    $ you-get http://kopasas.tumblr.com/post/69361932517
    Site:       Tumblr.com
    Title:      kopasas
    Type:       Unknown type (None)
    Size:       0.51 MiB (536583 Bytes)

    Site:       Tumblr.com
    Title:      tumblr_mxhg13jx4n1sftq6do1_1280
    Type:       Portable Network Graphics (image/png)
    Size:       0.51 MiB (536583 Bytes)

    Downloading tumblr_mxhg13jx4n1sftq6do1_1280.png ...
    100.0% (  0.5/0.5  MB) ├████████████████████████████████████████┤[1/1]   22 MB/s
## 注意:

此功能为测试性，远未完成。对于类似Tumblr和Blogger的大图有效，但是没有办法为所有网站建立通用格式.
在Google Videos搜索并下载

you-get可以吃任何东西. 如果不是合法的URL, you-get将在Google查找并下载最相关视频. (可能不是最心仪的，但是很有可能)

    $ you-get "Richard Stallman eats"
## 暂停与恢复下载

可以使用Ctrl+C 暂停下载.

临时的.download文件将保存于输出目录。下次使用you-get传入相同参数时，下载将从上次继续开始. 如果下载已经完成 (临时的.download 扩展名消失), you-get将忽略下载.

用--force/-f强行冲下载. (注意: 将覆盖同名文件或临时文件!)

设置输出文件名或路径

使用--output-dir/-o 设定路径, --output-filename/-O 设定输出文件名:

    $ you-get -o ~/Videos -O zoo.webm 'https://www.youtube.com/watch?v=jNQXAC9IVRw'
## 提示:

如果原视频标题含有与系统不兼容字符，十分有效.
也可以帮助使用脚本批量下载于指定目录和文件名.