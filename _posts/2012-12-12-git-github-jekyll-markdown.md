---
layout: post
title: Git+Github+Jekyll+Markdown=搭建个人博客
---
{{ page.title }}
================
<p class="meta"> 12 Dec 2012 </p>

很早之前就想搭建一个自己的博客了，只是比较懒惰，总是不肯抽时间去弄，不过，好在github流行起来了，给我提供了更多的选择，搭建过程也非常简单，废话少说，先介绍下用到的工具和我搭建的过程

Git和Github
-------------------

首先，需要一个github的账号，因为我们的内容是使用git来托管到github上的，你可以到github\.com上去申请一个，以后会经常用到。

git是李牛斯·脱袜子（Linus·Torvalds）大神花了一个星期搞出的神一般版本控制管理器，非常科学+非常好用+非常酷，如果你还用svn，只能说你太out了，赶紧追随大神的脚步吧。github\.com是另外3个很牛叉的大神基于git搭建起来的托管服务器，任何人都可以把自己的项目免费托管到这里（前提是开源），如果不想开源，只要交点钱就可以了，当前最流行的开源项目（如Ruby）都托管在这里，类似于bootstrap等许多非常好的开源项目，往往也选择在github上首发。

Jekyll
-------------------

Jekyll是github的三个创始人之一Tom的作品，简单来说就是一个博客生成器，我们按照Y-M-D-Title\.md的格式写好自己的博客就好了，它会帮你转换成静态的html页面，更科学的是，它可以在本地开一个http的server，然后你就可以在自己的浏览器预览刚才自己写的博客啦

Markdown
-------------------

Markdown是一种文本格式，只提供比较简单的几个标记，用它来写博客，我们不用考虑那么多的格式的问题，非常轻量级的文本格式。当然，jekyll还支持其他的格式，我目前用的是markdown

开始搭建博客之旅（傻瓜教程，有点长）
-------------------

1. 注册github账号，比如叫yourname，然后创建一个叫yourname.github.com的Repo，一般创建好之后，github会自动发邮件提示你是否开启GithubPages功能（它识别了刚才创建的Repo是用来创建静态网页的），在Setting中可以开启Pages功能
2. 在本地新建一个文件夹（比如myblog），然后终端输入git init(我使用的是mac ox，自带git，如果是linux，可以自己安装git，如果是windows，那就不要玩这个了，哈哈)，可以初始化git目录，然后git clone https://github.com/yourname/yourname.github.com.git把刚才创建的库pull到本地来
3. 在yourname.github.com创建一个index.html，随便输点什么东西，git commit index.html -m "add index.html"和git push origin master输入github的账号和密码，就可以把你刚才创建的index.html提交到github上了，so easy，蓝后，你就可以访问yourname.github.com看到你刚才输入的东西啦
4. 说好的jkeyll呢？jkeyll是ruby写的，我们先要检查下自己电脑上ruby的版本，我电脑自带的是1.8我们需要去ruby的官网上下载1.9的版本（因为1.8的版本在mac上安装jekyll始终失败，被坑惨了），安装好后，运行gem install jekyll就可以自动帮你把jekyll安装好
5. 调整下我们代码库下的文件，创建_config.yml文件，这是配置文件；_layouts目录，这里面可以放模板文件；_posts目录，这里面放你写好的博客；另外就是css目录，img目录之类的
6. 运行jekyll --pygments --safe可以自动生成一个_sites目录，然后你会发现，凡是不以下划线开头的文件，都被原封不动的拷到该目录下了，然后_post目录下的md文件都被以某种规则传换成了html文件，当然index.html也被转换了，我们需要在git的.gitignore把_sites目录屏蔽掉,只要把其他文件传到github上就可以了，因为github会自动在它那边用jekyll帮我们生成需要的文档
7. 添加模板，在_layouts目录下新建一个default.html文件，然后用html语言设计你的页面风格吧
8. 个人域名，在你的代码根目录下创建CNAME文件，然后写上你的域名比如yourname.com，然后在域名服务器，指向github的IP 207.97.227.245就可以啦

最后
-------------------

先简单写这些，markdown语法我还不熟，弄熟之后，好好排版一下
