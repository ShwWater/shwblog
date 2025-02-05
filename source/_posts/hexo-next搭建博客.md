---
title: hexo+next搭建博客
date: 2022-07-26 08:49:54
updateDate: 2022-10-10 08:49:54
tags: 博客
categories:
  - hexo
comments: true
top: 3
---

前情提要：不花一分钱搭建一个**带有后台**的个人 Blog 。

分为两个部分：1. 用 github 和 hexo 搭建不带后台的博客。 2. 迁移到 netlify 上，加入后台。

本站的搭建经历了很多的步骤，现记录如下，写一个备忘，也是帮助其他人。

<!--more-->

## hexo+github

首先是 hexo 的使用。先前我了解过 github pages 的使用，原理很简单： github 对每一个项目都可以建立一个 page ，前提是代码仓库的根目录上至少要有一个 `index.html` 。生成的 page 放在域名 `username.github.io/repositoryname` 上。当 `repositoryname` 和 `username` 相同的时候，域名简化成 `username.github.io` ，很适合建立个人博客。这样的博客都是静态的，也就是说，网站的所有文件都是生成好的，直接上传到 github 即可。

那么怎么生成网站的静态文件呢？使用 hexo 、 hugo 等静态网站生成器就可以完成。生成完的网站文件在 `public` 目录下，将 `public` 目录上传到 github 即可。

### hexo

首先来看[hexo](https://hexo.io/zh-cn/)。

参考官方教程就可以完成基础配置。这里简单归纳一下：

#### 安装

hexo 要由 node.js 的软件包管理器 npm 安装。此外 hexo 依赖于 git ,所以要现安装 node.js 和 git 。

**（不是非要 Linux。关于 npm、hexo 的脚本在 powershell 就可以运行。）**

```sh
yay -S nodejs git
# sudo pacman -S nodejs git
# sudo apt install nodejs git-core 
# sudo yum install nodejs git-core
```

对于 Windows，Mac 用户：

- [git](https://git-scm.com/downloads)
- [nodejs](https://nodejs.org/zh-cn/)

然后使用 npm 安装 hexo ：

```sh
sudo npm install -g hexo-cli
```

{% note danger %}
##### 关于 windows 系统上禁止运行脚本

在设置里查找 powershell，找到并打开“允许本地powershell脚本在未签名的情况下运行”。

{% endnote %}

#### 建站

选择你想要建立blog的路径。假设想要建立在 `~/Blog` 文件夹：

```sh
cd ~; hexo init Blog; cd Blog; npm install
```

（`~` 是 Linux 的主目录，类似 Windows 的 `C:/Users/Admin/`）

ok。现在 hexo 已经可以使用。默认有一篇文章 `helloworld` 。注意：如果 hexo 一篇文章都没有，则无法生成网站。

此时网站的目录如下：

```txt
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

`_config.yml` 是配置网站的地方， `source` 里面存储的都是网站的文章，其中 `_posts` 的内容会被编译成 html , `_drafts` 目录会被忽略，而其他文件会原样复制到网站的根目录。 `themes` 文件夹里存储了主题文件。下文说的 next 主题就要放在这里。

在开始配置之前，先来试一试吧！你将看到默认主题 landscape 下的 helloworld 文章。

```sh
hexo clean && hexo s
```

这将会在你的计算机上运行一个网站实例，一般来说会在 `localhost:4000` 。这意味着跟你在同一局域网的人就可以访问这个网站。如果 4000 端口被占用，你也可以选择其他端口：

```sh
hexo clean && hexo s -p 1234
```

这将会在 `localhost:1234` 上运行一个网站实例。

无论在哪个端口，它看上去应该像这样：

![](/images/helloworld.png)

#### 配置

`_config.yml` 文件包括了站点的基础属性。

其中的大部分内容我们不用改。我们只要改基本信息：

```yaml
# Site
title: Hexo
subtitle: ''
description: ''
keywords:
author: John Doe
language: en
timezone: ''

# URL
## Set your site url here. For example, if you use GitHub Page, set url as 'https://username.github.io/project'
url: http://example.com
permalink: :year/:month/:day/:title/
permalink_defaults:
pretty_urls:
  trailing_index: true # Set to false to remove trailing 'index.html' from permalinks
  trailing_html: true # Set to false to remove trailing '.html' from permalinks
```

这里是网站的基本信息。 `title` 是主标题， `subtitle` 是副标题。 `description` 可以是你的一句座右铭， `keywords` 越多越好，用逗号隔开。 `author` 写你的名字， `language` 选 `zh-CN` ， `timezone` 选 `Asia/Shanghai` 。 `url` 就填写你的网址（ `username.github.io` ）。

再试一下 `hexo s` ，就可以发现网站标题等改变了。

### github

好。现在我们考虑怎么把网站放到 github 上：

首先我们要知道怎么生成 `public` 文件夹：

```sh
hexo clean && hexo g
```

现在 `Blog` 目录中就有了 `public` 目录。我们可以手动把它 push 到 git 仓库上。

```sh
git push git@github.com:username/username.github.io --set-upstream main -f
```

这里前提是要配置好 ssh-key 。

{% note info no-icon 如果没有配置 ssh-key，看这里： %}

操作也很简单：

```sh
ssh-keygen -t rsa -b 4096
# 一路回车，直到出现如下字样：
# Your identification has been saved in ~/.ssh/id_rsa
# Your public key has been saved in ~/.ssh/id_rsa.pub
# The key fingerprint is:
# SHA256: ###############################
# The key's randomart image is:
# +---[RSA ####]----+
# |##########       |
# |##########       |
# |##########       |
# |##########       |
# |##########       |
# |##########       |
# |##########       |
# |##########       |
# |##########       |
# +----[SHA256]-----+
# #号部分是你的SHA256信息。
```

然后将公钥上传github：

```sh
cat ~/.ssh/id_rsa.pub
```

将文件内容复制后，打开 github “个人settings”-“ssh” ，添加公钥，将复制内容粘贴进去。（Windows 直接复制就好啦）

{% endnote %}

那么打开 username.github.io 的仓库配置，settings-pages ,选择打开 pages （默认应该是打开的），再打开网站 `username.github.io` 就可以看到刚刚生成的网址了。

#### 自动化

但是这么做还是太 low 了。 hexo 有一个功能 deploy ，可以将已经生成好的 `public` 文件夹自动上传，不过需要我们配置一下。

安装插件 `hexo-deployer-git`

```sh
npm install hexo-deployer-git --save
```

然后打开之前提到的 `_config.yml` ，找到deploy字段，更改如下：

```yaml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repo: git@github.com:username/userame.github.io
  branch: main
```

现在只要一行命令，就可以直接编译并部署了。

```sh
hexo clean && hexo d -g # or hexo g -d
```

### 新建文章

hexo 新建文章最好使用命令：

```sh
hexo new #postname
```

文章无需后缀名，自动使用 `.md` 后缀。如果题目中有空格或特殊字符，加上双引号：

```sh
hexo new "postname"
```

hexo 的文章开头存储了一些 yaml 信息，用 `---` 表示开头结尾。这些信息被称为 `front-matter` ，记录了文章的标题、日期等，文章的属性都在 `front-matter` 上。

### 新建页面

hexo 支持建立一些页面，同时也支持对文章进行分类和标签，分类和标签信息都存储在 `front-matter` 中。

#### 分类和标签

在 hexo 中，分类和标签不是一个东西。他们的形式很像：

```yaml
categories: #分类
-category1
-category2
#...
tags: #标签
-tag1
-tag2
#...
```

但是他们的含义不一样。 categories 表示文章属于 category1 类别中的 category2 类别，而 tags 表示文章既是 tag1 标签，又是 tag2 标签。可以看到 categories 是有严格父子关系的，而 tags 是并列的。

如果希望读者能够通过分类和标签索引文章，需要新建 categories 页面和tags页面：

```sh
hexo new page categories && hexo new page tags
```

此时在 `source` 文件夹下就会多出 categories 文件夹和 tags 文件夹。文件夹中有一个 `index.md` 。打开 markdown ，在 `front-matter` 中分别加入 `type: "categories"` 和 `type: "tags"` 。正文留空。就完成了构建。

#### 关于

同理，新建 about 页面。

```sh
hexo new page about
```

编辑 `source/about/index.md` ，加入 `type: "about"` ，然后在正文部分编辑自我介绍内容。

#### 404

腾讯公益404接入方法如下：

```html
<script type="text/javascript" src="//qzonestyle.gtimg.cn/qzone/hybrid/app/404/search_children.js" charset="utf-8" homePageUrl="http://yoursite.com/yourPage.html" homePageName="回到我的主页"></script>
```

直接在 `source` 目录下新建一个 `404.md` （手动新建），加入上面一段就好。

### 其他插件

#### next 内置插件

next 在蛰伏了许久之后，终于迎来了 next8.0。

现在已经有超多的内置功能和社区维护插件。内置功能在这：[Link](https://theme-next.js.org/docs/)

#### 文章排序

hexo 默认的排序是根据发布日期。有时我们会希望将文章置顶或按照 update 日期排序，可以利用一个插件实现：

```sh
npm uninstall hexo-generator-index --save
npm install hexo-generator-index-plus --save
```

然后将 `_config.yml` 中的 `index-generator` 字段删除，添加如下配置：

```yaml
index_generator_plus:
  path: ''
  per_page: 10
```

无需其他改动，只要在 `front-matter` 中加入 `top: #你的值` 就可以排序。 top 越大越靠前。

#### spoiler

编程向的 Blog 难免会有大量代码，这时如果直接放着就很丑。可以利用 `hexo-sliding-spoiler` 实现折叠代码，而且 js 效果还算不错。

用法如下：

```txt
{% spoiler /*想折叠内容的概述，如 Code*/ %}
//...折叠内容
//...内部仍然支持markdown语法
{% endspoiler %}
```

示例：

{% spoiler code %}
```cpp
#include <iostream>

int main() {
    std::cout << "Hello world!" << std::endl;
    return 0;
}
```
{% endspoiler %}

安装：

```sh
npm install hexo-sliding-spoiler --save
```

bug 修复：
spoiler 内容默认超过一定长度就会隐藏。打开 `node_modules/hexo-sliding-spoiler/assets/spoiler.css` 编辑 `.spoiler.expanded .spoiler-content` 中的 `overflow` 选项，改为 `auto` ，就会出现滚轮滑动来查看。

#### note

感谢 @[Eray](https://www.luogu.com.cn/user/192806) 提醒，next 内置了可以折叠的 note 功能。并且还有比较完整的高亮设计，跟 OI-wiki 的用法差不多。

可以代替 spoiler 使用。详见官方 [Wiki](https://theme-next.js.org/docs/tag-plugins/note.html)

示例：

{% note info %}
这是一段话。
{% endnote %}

#### mathjax

这与主题 next 相关，因此放到 next 中去讲。

#### sitemap

sitemap 可以用来向百度和谷歌等搜索引擎推荐自己的站点。使用方法也简单：

```sh
npm install hexo-generator-sitemap hexo-generator-baidusitemap --save
```

这样网站的根目录下就有 `sitemap.txt` 、 `sitemap.xml` 和 `baidusitemap.xml` 可供提交。

更多插件在 next 中讲解。

## next

next 是一个~~我觉得~~挺好看并且插件多，社区活跃的主题。

### 安装

{% note danger %}
现在 next 不再推荐使用 git 的安装方法， 但是如果想要更高的定制化，推荐使用 git。
{% endnote %}

```sh
npm install hexo-theme-next
```

### 配置

在站点目录下新建配置文件：`_config.next.yml` 。

建议复制一份官方示例：[Link](https://github.com/next-theme/hexo-theme-next/blob/master/_config.yml)

接下来我们看一看 next 的配置：

PS：以下配置内容如没有就新建。

{% note info #### 主题 %}

next 默认有4中主题可选。找到 `Schemes` ：

```yaml
# Schemes
scheme: Muse
#scheme: Mist
#scheme: Pisces
#scheme: Gemini
```

默认主题是 Muse ，可以逐个试一试。

{% endnote %}

{% note info #### 页面 %}

在 `menu` 选项中取消你想要的页面的注释：

```yaml
  menu:
    home: / || fa fa-home
    about: /about/ || fa fa-user
    tags: /tags/ || fa fa-tags
    categories: /categories/ || fa fa-th
    archives: /archives/ || fa fa-archive
    #schedule: /schedule/ || fa fa-calendar
    # sitemap: /sitemap.xml || fa fa-sitemap
    #commonweal: /404.html || fa fa-heartbeat
```

{% endnote %}

{% note info #### 头像 %}

找到 `avatar` 字段：

```yaml
  avatar:
    # Replace the default image and set the url here.
    url: /images/head.jpg
    # If true, the avatar will be dispalyed in circle.
    rounded: true
    # If true, the avatar will be rotated with the cursor.
    rotated: true
```

在 `source` 中新建 `images` 文件夹，里面放入你的图片。 `rounded` 设置圆形， `rotated` 设置鼠标放上去就旋转。

{% endnote %}

{% note info #### 社交账号和友链 %}

找到 `social` 字段：

```yaml
  social:
    #GitHub: https://github.com/yourname || fab fa-github
    #E-Mail: youremail || fa fa-envelope
    #Weibo: https://weibo.com/yourname || fab fa-weibo
    #Google: https://plus.google.com/yourname || fab fa-google
    #Twitter: https://twitter.com/yourname || fab fa-twitter
    #FB Page: https://www.facebook.com/yourname || fab fa-facebook
    #StackOverflow: https://stackoverflow.com/yourname || fab fa-stack-overflow
    #YouTube: https://youtube.com/yourname || fab fa-youtube
    #Instagram: https://instagram.com/yourname || fab fa-instagram
    #Skype: skype:yourname?call|chat || fab fa-skype

  social_icons:
    enable: true
    icons_only: false
    transition: false

  # Blog rolls
  links_settings:
    icon: fa fa-link
    title: Links
    # Available values: block | inline
    layout: block

  links:
```

`social` 中添加你的社交账号，不局限于给出选项，也可以自己添加。

`social_icons` 控制显示时是否有图标。

`links` 设置友链。

{% endnote %}

{% note info #### 打赏 %}

~~说不定有好心人呢？~~找到 `reward` 字段：

```yaml
  reward_settings:
    # If true, reward will be displayed in every article by default.
    enable: true
    animation: false
    comment: "如果阅读本篇文章需要付费，你愿意为此支付1块钱吗？"

  reward:
    wechatpay: /images/weixin.jpg
    #alipay: /images/alipay.png
    #paypal: /images/paypal.png
    #bitcoin: /images/bitcoin.png
```

在 `reward` 中设置你的收款码。方法同头像。

{% endnote %}

{% note info #### 代码块设置 %}

找到字段 `codeblock` ：

```yaml
  codeblock:
    # Code Highlight theme
    # Available values: normal | night | night eighties | night blue | night bright | solarized | solarized dark | galactic
    # See: https://github.com/chriskempson/tomorrow-theme
    highlight_theme: normal
    # Add copy button on codeblock
    copy_button:
      enable: true
      # Show text copy result.
      show_result: true
      # Available values: default | flat | mac
      style:
```

`highlight_theme` 配置风格，可以自行尝试。

`copy_button` 配置复制按钮

{% endnote %}

{% note info #### 一键回到顶部 %}

找到字段 `back2top` ：

```yaml
  back2top:
    enable: true
    # Back to top in sidebar.
    sidebar: true
    # Scroll percent label in b2t button.
    scrollpercent: true
```

{% endnote %}

{% note info #### 阅读进度 %}

找到字段 `reading_progres` ：

```yaml
  reading_progress:
    enable: true
    # Available values: top | bottom
    position: top
    color: "#37c6c0"
    height: 3px
```

`position` 中 `top` 在顶部， `bottom` 浮动在按钮上。

{% endnote %}

{% note info #### 你的Github %}

找到字段 `github_banner`：

```yaml
  github_banner:
    enable: true
    permalink: https://github.com/yourname
    title: Fork me on GitHub
```

`title` 可修改

{% endnote %}

{% note info #### mathjax %}

找到字段 `math` ：

next 支持 mathjax 和 katex 。一般来说 katex 渲染快，而且 next 也支持了复制 katex 源码，但是在 mathjax3 之后， katex 和 mathjax 速度差不多，而 mathjax 支持更多样的复制和多种渲染选项，而且支持更多语法，再加之化学方程式的支持，我最终选择了 mathjax 。

```yaml
  math:
  # Default (false) will load mathjax / katex script on demand.
  # That is it only render those page which has `mathjax: true` in front-matter.
  # If you set it to true, it will load mathjax / katex script EVERY PAGE.
  every_page: true

  mathjax:
    enable: true
    # Available values: none | ams | all
    tags: none

  katex:
    enable: false
    # See: https://github.com/KaTeX/KaTeX/tree/master/contrib/copy-tex
    copy_tex: false
```

`every_page` 配置是否对每篇文章开启渲染。建议开启，不然每篇文章开头都要加上 `front-matter` ： `mathjax: true` 。

想要启用 mathjax ,还需：

```sh
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-pandoc hexo-math --save
```

同时安装[pandoc](https://pandoc.org/installing.html)（[非 Linux 用户点这个链接](https://pandoc.org/installing.html)）：

```sh
yay -S pandoc
# sudo pacman -S pandoc
# sudo apt install pandoc
# sudo rpm install pandoc
```

{% endnote %}

{% note info #### 评论 %}

评论的插件其实有很多，在多方考虑之后，现在使用的是 OI-wiki 同款的 giscus，最大的好处就是在评论区支持 Mathjax。（曾经用的是utteranc）

giscus 官方文档很详细，这里直接放链接：[giscus](https://github.com/giscus/giscus)

{% endnote %}

{% note info #### 访问量统计 %}

找到字段 `busuanzi_count` ：

```yaml
  # Show Views / Visitors of the website / page with busuanzi.
  # Get more information on http://ibruce.info/2015/04/04/busuanzi
  busuanzi_count:
    enable: true
    total_visitors: true
    total_visitors_icon: fa fa-user
    total_views: true
    total_views_icon: fa fa-eye
    post_views: true
    post_views_icon: fa fa-eye
```

全部为 `true` 即可。

{% endnote %}

{% note info #### 站内搜索 %}

找到字段 `localsearch` ：
  
```yaml
  # Local Search
  # Dependencies: https://github.com/theme-next/hexo-generator-searchdb
  local_search:
    enable: true
    # If auto, trigger search by changing input.
    # If manual, trigger search by pressing enter key or search button.
    trigger: auto
    # Show top n results per article, show all results by setting to -1
    top_n_per_article: 1
    # Unescape html strings to the readable one.
    unescape: false
    # Preload the search data when the page loads.
    preload: false
```

同时安装 `hexo-generator-searchdb` ：

```sh
npm install hexo-generator-searchdb --save
```

{% endnote %}

{% note info #### 分享文章 %}

找到字段 `needmoreshare` ：

```yaml
  # NeedMoreShare2
  # Dependencies: https://github.com/theme-next/theme-next-needmoreshare2
  # For more information: https://github.com/revir/need-more-share2
  # iconStyle: default | box
  # boxForm: horizontal | vertical
  # position: top / middle / bottom + Left / Center / Right
  # networks:
  # Weibo | Wechat | Douban | QQZone | Twitter | Facebook | Linkedin | Mailto | Reddit | Delicious | StumbleUpon | Pinterest
  # GooglePlus | Tumblr | GoogleBookmarks | Newsvine | Evernote | Friendfeed | Vkontakte | Odnoklassniki | Mailru
  needmoreshare:
    enable: true
    cdn:
      js: //cdn.jsdelivr.net/gh/theme-next/theme-next-needmoreshare2@1/needsharebutton.min.js
      css: //cdn.jsdelivr.net/gh/theme-next/theme-next-needmoreshare2@1/needsharebutton.min.css
    postbottom:
      enable: true
      options:
        iconStyle: box
        boxForm: horizontal
        position: bottomCenter
        networks: Weibo,Wechat,Douban,QQZone,Twitter,Facebook，
    float:
      enable: false
      options:
        iconStyle: box
        boxForm: horizontal
        position: middleRight
        networks: Weibo,Wechat,Douban,QQZone,Twitter,Facebook
```

同时安装 `hexo-next-share` ：

```sh
npm install hexo-next-share
```

{% endnote %}

### 效果

就像你现在看到的这样。

## netlify

github 站虽好，但是一来访问慢，二来不能被百度爬到。所以我决定迁移到 [netlify](https://netlify.app/) 。

这是一个提供免费的（基础服务免费）网页托管，持续部署的网站， CDN 也很快，非常适合我们使用。而且 netlify 还有 CMS （后台）系统，可以在线修改你的文章，原来必须要服务器+域名才能完成的事，被我们免费做到，岂不是爽歪歪？

### 建站

netlify 提供自动部署服务，如果想要用他的 CMS ,就不能只把 `public` 文件夹传上去了，我们要把整个 Blog 文件夹都传上去。

在 github 上新建一个代码仓库 Blog ，然后把本地文件夹传上去：

```sh
git init && git add -A && git commit -a -m "init" && git push git@github.com:username/Blog.git
```

随后在[netlify](https://netlify.app/)中选择用 github 登陆。新建一个站点，选择绑定到 github 仓库，选择刚刚新建的Blog仓库。在站点的构建命令中填写 `npm install && npm build` ，发布目录填 `public` 。

然后 netlify 会自动帮你运行部署，相当与我们在本地运行 `hexo g` ，然后通过 `public` 文件夹访问站点。不过这一切都是由 netlify 的机器来做的。在站点配置中你可以更改自己网站的名字，任意选取吧！

这个东西他有个好处，就是对于小型博客部署时间短。因为 netlify 会将你部署所需的环境、插件作为缓存存起来，不需要每次都安装，而不像 github 的 CI ，每次都要装依赖，甚至 pandoc 都要重装一遍。所以虽然 netlify 只有 300 分钟一个月的构建时间，但是绝对够用。

如果不够用了可以考虑关闭 netlify 的自动部署，然后在 Blog 仓库中启用 Action ，配置构建指令并部署到 netlify 。这样子就会有2000分钟的构建时间，但每次部署都要多花1分多钟。不过这样还是可以使用CMS的。

既然已经迁移到了 netlify ，我们可以让原来的 github page重定向到 netlify ，避免流量流失。将 github page 的仓库的内容全部删除，添加 `index.html` ：

```html
<html>
<head>
    <title>ShwBlog</title>
    <meta http-equiv="refresh" content="1;url= https://shwstone.netlify.app/ ">
</head>
<body>
    <script> alert("本站迁移至https://yoursitename.netlify.app/，请您记住新网址，将为您自动跳转。"); </script>
</body>
</html>
```

### debug

如果你按照上文配置了 `hexo-sliding-spoiler` ，你就会发现长的内容又被隐藏了，这是因为 `node_modules` 作为插件的安装位置，并不会被push到github上，netlify重新安装了插件，采用了默认配置。

解决办法如下：

新建一个文件夹 `prebuild` ，将更改后的文件复制到其中。 `prebuild` 里面添加一个文件 `file` ， `file` 中每一行存储需要更改的文件路径和更改后的文件路径，如下：

```txt
prebuild/spoiler.css node_modules/hexo-sliding-spoiler/assets/spoiler.css
```

然后在网站根目录下新建 `prebuild.sh` 并 `chmod +x prebuild.sh` ，编辑如下：

```sh
#!/bin/sh
while read line
do
    cp $line -f
done < prebuild/file
```

（Windows 用户照着抄就好了。 Netlify 自动构建的环境是 Linux，所以要这么写）

push 到 github 上。

最后在 netlify 的构建指令中加上一条，改为 `npm install && ./prebuild.sh && npm run build` 。

PS：如果 windows 无法执行 chmod 的话，把构建指令改为 `npm install && /bin/sh ./prebuild.sh && npm run build`

如果还有别的文件要更改，复制到 `prebuild` 文件夹并且在 `file` 中添加就行了。

### 百度验证

百度站长平台要求你验证自己的网站，可以通过往 `</head>` 标签之前插入一个 `<meta name="baidu-site-verification" content="***" />` 来完成。 netlify 支持在网站 html 中注入代码：进入 netlify.app ，选择 site-settings ，找到 build & deploy 中的 Snippet injection ，选择在 `</head>` 或 `</body>` 标签前加入代码。

### CMS

接下来我们来看后台的使用。

upd：netlify-cms 已经被弃用，现在使用 decap-cms。

#### 配置

进入 netlify.app ，选择 site-settings ，找到 build & deploy 中的 Snippet injection ，在 `</body>` 标签前加入：

```html
<script src="https://identity.netlify.com/v1/netlify-identity-widget.js"></script>
<script>
  if (window.netlifyIdentity) {
    window.netlifyIdentity.on("init", user => {
      if (!user) {
        window.netlifyIdentity.on("login", () => {
          document.location.href = "/admin/";
        });
      }
    });
  }
</script>
<!--netlify cms 有一个小 bug ，登录时需要手动刷新。这是自动刷新的代码-->
```

然后在 `source` 文件夹下新建一个文件夹：`source/admin`，再建两个文件：`source/admin/index.html`，`source/admin/config.yml`

分别编辑如下：

{% note info **config.yml** %}

```yaml
backend:
  name: github
  repo: # repo name
  branch: main # Branch to update (optional; defaults to master)

site_url: https://shwst.one
display_url: https://shwst.one
# logo_url: https://shwst.one/images/
locale: 'cn'

# This line should *not* be indented
publish_mode: editorial_workflow

# This line should *not* be indented
media_folder: "source/images" # Media files will be stored in the repo under images/uploads
public_folder: "source"

# A list of collections the CMS should be able to edit
collections:
  # Used in routes, ie. : /admin/collections/:slug/edit
  - name: "posts"
    # Used in the UI, ie.: "New Post"
    label: "Post"
    folder: "source/_posts" # The path to the folder where the documents are stored
    sort: "date:desc"
    create: true # Allow users to create new documents in this collection
    editor:
      preview: true
    fields: # The fields each document in this collection have
      - {label: "Title", name: "title", widget: "string"}
      - {label: "Publish Date", name: "date", widget: "datetime", format: "YYYY-MM-DD HH:mm:ss", date_format: "YYYY-MM-DD", time_format: "HH:mm:ss", required: false}
      - {label: "Update Date", name: "updateDate", widget: "datetime", format: "YYYY-MM-DD HH:mm:ss", date_format: "YYYY-MM-DD", time_format: "HH:mm:ss", required: false}
      - {label: "Tags", name: "tags", widget: "list", required: false}
      - {label: "Categories", name: "categories", widget: "list", required: false}
      - {label: "Body", name: "body", widget: "markdown", required: false}
      - {label: "Comments", name: "comments", widget: "boolean", default: true, required: false}
      - {label: "Top", name: "top", widget: "number", value_type: "int", default: 2, required: false}
```
{% endnote %}

{% note info **index.html** %}

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="robots" content="noindex" />
  <title>Content Manager</title>
</head>
<body>
  <!-- Include the script that builds the page and powers Decap CMS -->
  <script src="https://unpkg.com/decap-cms@^3.0.0/dist/decap-cms.js"></script>
</body>
</html>
```
{% endnote %}

注意 `backend` 中的 `repo & branch` 要与你的实际分支相匹配。

然后在 netlify 中找到 site-settings-identity ，找到 git-gateway ，点击启用，选择 Blog 仓库。

这样你就可以在 `你的网址/admin` 中看到登陆界面了。

另外，如果出现权限问题，请按照 [Netlify-docs](https://docs.netlify.com/visitor-access/oauth-provider-tokens/) 配置 auth。

自己注册一个 admin 账户，然后在 identity 中关闭注册，只允许邀请，保证安全。

ok 。尽情享受 CMS 吧！

### To do

decap CMS 不支持 latex ，并且 bug 较多，我以后有时间可能会把 Luogu 的 Markdown*Palettes 嵌入到 netlify 的 markdown 编辑器里，自己做一个小部件，或者哪位大佬帮我写一下吧 qwq 。
