---
layout: post
title: "坎坷之路，终抵群星——博客上的第一篇文章"
date: 2015-05-23 19:10:17 +0800
comments: true
categories: [Blog, Octopress, Github Pages]
---

很久之前便有了搭建一个自己的博客的打算，不过之前由于长期存在的拖延症的问题，一直没有行动。后面终于下定决心要开始做这个事，VPS也买了，域名也买了，不过终究还是没有落实到行动上。直到最近进入到新的项目组，碰到了刚刚出差回来的小明同学，在看过了小明同学的Blog过后，才终于决定要付诸行动。

之前考虑的使用WordPress把博客搭建在自己的服务器上，但是考虑到VPS的稳定性和后期的维护（其实就是比较懒），最终还是选择了和小明同学一样的方案：使用Octopress，使用GitHub Pages来部署（主要还是因为足够简单）。下面还是简单介绍一下：

### Octopress
Octopress自己在官网上的的介绍就是`A blogging framework for hackers`，它是一个静态博客引擎。“为黑客设计的博客框架”，听起来还很高端的感觉，其实并没有。只是说Octopress可以纯命令行编辑和部署，很受黑客喜欢而已。关于Octopress的搭建和部署网上各种文章已经很多了，我在这里就不再详细介绍了。只谈两点我在搭建时遇到的小问题：

1. Octopress提交到GitHub的repository有两个branch，一个master，一个source。master就是GitHub Pages呈现的页面内容，而source就是用于生成页面的源代码了。在source这个branch根目录运行`rake generate`过后会在`_deploy`目录下生成master branch的文件，这个时候再运行`rake deploy`就会自动进入`_deploy`目录，将master的内容commit并push到你的repo，再回到根目录中。`_deploy`目录默认是加入了source branch的`.gitignore`文件的。你如果想在不同的电脑进行blog的管理，那么也记得将source push到repo上。换了电脑过后将这个branch clone下来就可以继续管理blog了。
2. 在国内使用Octopress会存在一个隐藏的坑。因为Octopress是基于ruby的，而由于某种莫名其妙的原因，连接**rubygems.org**会出现链接被重置的情况，这直接导致了无法在国内使用gem来进行ruby的包管理。还好淘宝的代码库有一个**rubygems.org**的镜像**ruby.taobao.org**，只要将gem的source地址指向淘宝的镜像站便可搞定这个问题。使用方法如下：

	```
	$ gem sources --remove https://rubygems.org/
	$ gem sources -a https://ruby.taobao.org/
	$ gem sources -l
	*** CURRENT SOURCES ***

	https://ruby.taobao.org
	# 请确保只有 ruby.taobao.org
	```
另外，由于Octopress还用到了bundle，所以还需要将根目录下`Gemfile`文件中的第一行改为`source 'https://ruby.taobao.org/'`，这样所有的包都将会从淘宝镜像下载，绕过了国内网络的问题。PS:使用https有可能会遇到`ERROR:  While executing gem ... (OpenSSL::SSL::SSLError)`这个问题，这时把淘宝镜像改成使用http协议访问就可以了。

关于怎么搭建和部署，Octopress的官网文档已经介绍得很清楚了，只需要按照步骤做下去就好了。详见[Octopress Documentation](http://octopress.org/docs/).

### GitHub Pages
GitHub大家应该都不陌生了，GitHub Pages的初衷是为了让用户建立一个托管在GitHub的静态页面用于介绍自己的项目或者一个简单的主页。不过从一开始GitHub就鼓励大家使用Pages来`Create a blog and spread your ideas. Whatever you want!`，所以，使用GitHub Pages来搭建博客也算是充分利用了GitHub提供的资源，省去了自己架设的麻烦。

详情可以访问[GitHub Pages](https://pages.github.com/)来查看相关的介绍和教程。

### Markdown
Octopress中上传的文章都是使用Markdown书写的，本文也不例外。Markdown是一种轻量级的标记语言，由John Gruber创造，大名鼎鼎的Aaron Swartz也参与了Markdown的开发。它的目标就是为了实现`易写易读`。markdown的语法全是由一些符号组成的，而这些符号代表的意思几乎是一目了然的。以Markdown撰写的文件是能够直接阅读的，而不会让人觉得看起来是由一些标签或者格式指令构成的.Markdown 的理念是，能让文档更容易读、写和随意改。关于Markdown的语法，可以从[Markdown 语法说明 (简体中文版)](http://wowubuntu.com/markdown/)查阅。

---

关于Blog的搭建就先讲这么多了。开设博客的原因还是在用于记录自己在遇到和解决了的问题，以及写下自己的一些看法和思考。到现在差不多毕业已经三年了，在ThoughWorks这样的环境中总觉得自己还有很大差距，看到的东西越多结果越发觉自己懂得越少，可能经历了这样的过程才能更快的成长吧。之前走了不少的弯路，掉了不少的坑，现在是时候爬出来，不断地去强化自己，脚踏实地的继续前行了。多读书，多写代码，同时还需要多思考和注意积累，才能让接下来的每一步走得更稳，更快。

标题为什么叫`坎坷之路，终抵群星`呢？这是NASA在阿波罗1号发生事故后为纪念三名牺牲的航天员的两块纪念铭牌其中一块上面的一句话:

> IN MEMORY
>
> OF
> THOSE WHO MADE THE ULTIMATE SACRIFICE
> SO OTHERS COULD REACH FOR THE STARS
>
> AD ASTRA PER ASPERA
> (A ROUGH ROAD LEADS TO THE STARS)
>
> GOD SPEED TO THE CREW
> OF
> APOLLO 1

这句话其实是一句拉丁语`AD ASTRA PER ASPERA`，英文翻译为`A ROUGH ROAD LEADS TO THE STARS`。通往璀璨星空的路总是会遇到坡坎挫折，但是走过崎岖坎坷的路终将抵达灿烂的星空。人类在太空探索上是这样，在其他任何领域也都是这样。