---
title: "Hexo Icarus -- 搭建赛博朋克风格个人博客"
date: 2022-03-12 12:22:54
categories:
- Front-End
- Hexo
tags:
- Hexo
- GitHub
toc: true # 是否显示目录
---
> 基于 [Hexo](https://hexo.io/zh-cn/) ，使用酷炫的 [Icarus 主题](https://ppoffice.github.io/hexo-theme-icarus/) 且加入众多个性化配置，打造出的个人博客，效果惊艳，让我们从零开始，搭建一个专属自己的 [赛博朋克](https://ppoffice.github.io/hexo-theme-icarus/) 风格博客吧。
<!-- more -->
 * 安装Node.js
 * 安装Hexo
 * 引入Icarus主题
 * 个性化配置
 * 创建GitHub个人仓库
 * 绑定仓库
 * 发布文章
 * Hexo及MarkDown语法
 * 安装插件（评论，统计，分享等）
 * 绑定个人域名
 * 使用图床工具

## 安装Node.js

Hexo基于Node.js，[Node.js下载地址：Download | Node.js 下载安装包](https://nodejs.org/zh-cn/)(或者使用 [nvm](https://www.cnblogs.com/gaozejie/p/10689742.html) 管理node版本)，注意安装Node.js会包含环境变量及npm的安装，安装后，检测Node.js是否安装成功，在命令行中输入 node -v :
``` bash
$ npm -v
```

## 安装Hexo

初始化项目非常简单，按照 [Hexo官网文档](https://hexo.io/zh-cn/docs/setup) 操作即可:
使用npm安装Hexo
```bash
$ npm install -g hexo-cli
# 更新版本
#  npm update hexo
```
``` bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```
标准的目录结构如下：
``` bash
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

使用 generate 命令生成静态文件，可简写如下：
``` bash
$ hexo g
```
使用 server 命令启动本地服务器查看页面效果，可简写如下：
``` bash
$ hexo s
```
此时，打开 [localhost:4000](http://localhost:4000), 一个默认的 landscape 主题的博客页面就出现了，如下：

图片

## 引入Icarus主题

引入主题也非常简单，我们查看 [Icarus 主题](https://ppoffice.github.io/hexo-theme-icarus/) 。更换主题主要有两种方式，一种是使用 npm 安装主题的方式，另一种是下载源码放到 theme 文件夹的方式。简单起见，我们先采用 npm 的方式：
``` bash
$ npm add hexo-theme-icarus
```
使用 hexo 命令修改主题为 Icarus:
``` bash
$ npm hexo config theme icarus
```
启动服务器，很不幸，报错了：
图片

根据提示，添加依赖(提示缺少哪些就安装哪些)：
``` bash
$ npm add bulma-stylus@0.8.0 hexo-renderer-inferno@^0.1.3
```
再次尝试构建并启动，成功出现 Icarus 主题了：

图片

在 <span style="color: #ff502c">_config.icarus.yml</span> 文件中修改：

``` bash
# Icarus theme variant, can be "default" or "cyberpunk"
$ variant: cyberpunk
```
再次构建启动，成功出现 赛博朋克 风格主题了：

图片

>  刚才说到使用 Icarus 主题有两种方式， npm 包的方式虽然简便，但是如果想对 Icarus 主题有更深的配置就不太好弄了，尤其是过去 Icarus 一直都采用的是源码模式，很多 Issue 的解决方案都是修改源码的，而对应的 npm 包的方式则很少提及，所以我们也改为使用源码方式，方便后续配置。

首先删掉 <span style="color: #ff502c">hexo-theme-icarus</span> 依赖，在 [Icarus 仓库](https://github.com/ppoffice/hexo-theme-icarus) 下载代码，解压后拷贝到 theme 文件夹中。

## 个性化配置
修改配置文件 <span style="color: #ff502c">_config.yml</span> 和 <span style="color: #ff502c">_config.icarus.yml</span> ，配置网站相关信息。

主要包括 logo、favicon、navbar 的 menu 和 links、footer、donates（赞助信息，注释掉没用的支付渠道）、widgets。

- 首页如果文章过长，用户下滑就只会看到同一篇文章，如果只想让用户看一部分内容怎么办呢？非常简单，在 md 文件中添加 &lt;!-- more --> 即可，添加完之后，就会出现“阅读更多”的按钮，首页就能看到多篇文章了。

- 目前文章页仍然和首页一样，是三栏布局，为了有效利用空间，希望文章页能够两栏布局。此时我们需要在 Icarus 源码文件夹添加 _config.post.yml 文件，并配置成两栏布局：
``` bash
widgets:
    # Profile widget configurations
    -
        # Where should the widget be placed, left sidebar or right sidebar
        position: right
        type: toc
        # Whether to show the index of each heading
        index: true
        # Whether to collapse sub-headings when they are out-of-view
        collapsed: false
        # Maximum level of headings to show (1-6)
        depth: 3
``` 

看看效果：
图片

## 创建GitHub个人仓库
登录到 [全球最大同性恋交友网站✨(GitHub)](https://github.com/),如果没有GitHub帐号，使用你的邮箱注册GitHub帐号(此处不赘述可参考: [廖雪峰老师Git教程](https://www.liaoxuefeng.com/wiki/896043488029600))。点击GitHub中的New repository创建新仓库，仓库名应该为：用户名.github.io 这个用户名使用你的GitHub帐号名称代替，这是固定写法，如图：
图片
安装成功后，将你的Git与GitHub帐号绑定，同参考 [廖雪峰老师Git教程](https://www.liaoxuefeng.com/wiki/896043488029600) 。

## 绑定仓库
在 <span style="color: #ff502c">_config.yml</span> 中配置你的 GitHub Pages 对应的仓库地址:
``` bash
deploy:
  type: git
  repo: https://github.com/xx/xx.github.io
  branch: master
```
其中： 
  - <span style="color: #ff502c">type</span> 对应部署的服务器类型，我们这里填写git就可以
  - <span style="color: #ff502c">repo</span> 对应仓库地址，也就是仓库克隆的地址
  - <span style="color: #ff502c">branch</span> 不写默认是master，通常我们写成master就可以

  以上配置完成后保存 然后回到终端执行npm install hexo-deployer-git --save安装插件，将写好的文章部署到github服务器上并让别人浏览到。安装完成后在终端中依次执行如下代码(为了简单后续统称为三步)：
   - <span style="color: #ff502c">hexo clean</span> 清理缓存，整个public文件删除，简写为 <span style="color: #ff502c">hexo c</span>
  - <span style="color: #ff502c">hexo generate</span> 进行渲染，将source中的文件按照某种规则方式渲染成静态的页面文件放到public中，简写为 <span style="color: #ff502c">hexo g</span>
  - <span style="color: #ff502c">hexo server</span>  部署到本地，简写为 <span style="color: #ff502c">hexo s</span>
  - <span style="color: #ff502c">hexo deploy</span> 将public中文件部署到git服务器，简写为 <span style="color: #ff502c">hexo d</span>

## 发布文章
创建一个自己的文章，建立好的文章在 <span style="color: #ff502c">source/_posts</span> 中：
```bash
hexo new post “文章名字”
```
注意头部配置文件相关信息，在新版 Icarus 中头图需要额外配置 cover 选项，如下：
``` bash
---
title: "hello" # 标题
date: 2021/03/08  # 日期
categories: # 分类
- Front-End
tags: # 标签
- GitHub
toc: true # 是否显示目录
thumbnail: ''  # 缩略图
cover: '/img/a.png' # 头图
---
```
编辑完成后执行上面三步操作，刷新下浏览器即可看到新文章啦.

## Hexo及MarkDown语法
大家可前往对应官网学习 [MarkDown基本语法](http://markdown.p2hp.com/basic-syntax/) 及 [Hexo文档](https://hexo.io/zh-cn/docs/writing)

> ⚠️注意：可以将HTML标签添加到任何Markdown文件中使用。如果您更喜欢某些HTML标记而不是Markdown语法，这将很有帮助。例如，有些人发现将HTML标签用于图像更容易。
### 添加网易云音乐
打开网页版的 [网易云音乐](https://music.163.com/#)，选择喜欢的音乐，点击生成外链播放器
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=1363948882&auto=1&height=66"></iframe>

## 安装插件（评论，统计，分享等）
### 评论系统
Hexo的评论插件官方推荐了disqus，但国内无法访问，不能保证所有人都有克服的方法。所有我们打算采用其他的第三方来实现，市面上产品很多，我们做如下对比：
- Github大礼包：[gitment](https://github.com/imsun/gitment)， [gitalk](https://github.com/gitalk/gitalk)，[gitter](https://gitter.im/)，风格很像github，评论需要github账号；
- 基于 [leancloud](https://leancloud.app/) 的无后端评论系统：[Valine](https://valine.js.org/)，需要实名注册leancloud帐号；
- [livere](https://livere.com/) 中文名字叫来必力，是一款韩国的评论系统；
- [畅言云评](http://changyan.kuaizhan.com/)，安装需要备案号；
- [disqus](https://github.com/norman/disqus) 比较大牌的评论系统，服务稳定，唯一的缺点是国内无法使用。

最终，推荐使用 valine 评论系统，操作步骤如下：
 1. 注册LeanCloud (https://leancloud.app/)，并实名认证
 2. 创建应用，应用名称随便取
 图片
 3. 点击 设置 > 安全中心 把自己博客网址添加到安全中心，保证数据的调用安全。
 图片
 4. 点击设置 > 应用Key 复制App ID 和 App Key
 图片
 5. 修改配置
 ```shell
 # Valine.
# You can get your appid and appkey from https://leancloud.cn
# more info please open https://valine.js.org
valine:
  enable: true # 启用
  appid:  xxx # leancloud应用中的appId 必填
  appkey:  xxx # leancloud应用中的appKey必填
  notify: false # mail notifier , https://github.com/xCss/Valine/wiki
  verify: false # Verification code
  placeholder: ""                 # 可选填
  avatar: robohash                # 默认头像
  avatar_force: false             # 可选填
  meta: ["nick", "mail", "link"]  # 可选填
  page_size: 10                   # 可选填
  lang: zh-CN                     # 可选填
  visitor: false                  # 可选填
  highlight: true                 # 可选填
  record_ip: false                # 可选填
  server_urls:                    # 可选填
  emoji_cdn:                      # 可选填
  emoji_maps:                     # 可选填
  enable_qq: false                # 可选填
  required_fields: []             
```
6. 测试valine评论
执行 hexo c 三步，重启博客，查看效果，评论的数据存在leancloud应用中，如图：
图片 评论boke 图片 评论lean
> ⚠️注意：在 Front-matter 中通过comments属性设置true或false控制该页面或者是文章的评论功能是否打开，如下：
```bash
---
title: Tags
date: 2019-12-19 16:10:19
type: "tags"
comments: false
---
```
其他个性化设置（统计，分享等），大家可以评论分享，共同探讨。
## 绑定个人域名
### 域名购买
购买渠道有很多，这里以 [阿里云的万网域名](https://wanwang.aliyun.com/)购买为例，找到合适自己的域名：
图片
选择域名，根据提示绑定邮箱上传实名认证信息，审核通过后即可付款
图片
### 域名解析
域名解析到购买厂商处进行解析，此处在阿里云的 控制台 > 域名 > 域名列表 找到域名右侧对应的解析按钮。点击然后添加解析
图片
按照如下填写添加解析，记得把记录值替换成你自己的博客地址
图片
### 仓库配置
回到github仓库，进入你的仓库设置页面，在添加域名，然后保存即可，github默认在仓库里面创建一个CNAME文件，内容为你的域名，之后就可以通过你的域名访问博客啦。
图片
> ⚠️注意：以上步骤完成后可以通过域名访问，但是域名前是http协议，虽说不影响使用和阅读，但如何将自己的博客协议改为Https，可以通过使用一个国外的CDN服务提供商 [Cloudflare](https://www.cloudflare.com/zh-cn/)，详细操作后续补全。
## 使用图床工具
博客文章中有时需要大量的需要图片，图片要么上传到github，但是怕图片过多超出使用容量，要么就是上传到图床，如果你图片不太多的话，可以直接手动上传，推荐几个图床：土豆图床、tinypic、SM.MS、catbox。这几个有的支持多张图一起上传，有的是无损的，有的可以上传敏感资源，有的上传大小有限制，具体的可以自行体会。图片太多的话，使用图床工具比较方便点。刚开始使用ipic，需要付费，而且体验不太好，就换了PicGo。
博文中有图片时，若是少量图片，可以直接把存放在source文件夹中，但会占据大量的存储的空间，加载时缓慢 ，所以考虑把博文里的图片上传到某一网站，然后获得外部链接，使用Markdown语法，![图片信息](外部链接) 完成图片的插入，这种网站就被成为图床。常见的简易的图床网站很多，大家有兴趣可以自行搜索，比如 [SM.MS](https://sm.ms/login) ，[postimg](https://postimages.org/) ，[七牛云](https://marketing.qiniu.com/activity/act-free?entry=index-floatwin) 等，github也是可以用来当做图床。我们这边以 <strong>七牛云</strong> 为例。
> ⚠️注意：七牛云注册帐号后免费提供一个测试域名，每个域名每日限总流量 10GB，每个测试域名自创建起 30 个自然日后系统会自动回收。所以有长久需求的用户需要自己的个人备案域名。具体步骤如下：
 * 注册七牛云
 * 创建七牛云存储空间
 * 个人域名备案
 * 绑定域名
### 注册帐号
根据提示完成注册绑定邮箱等操作。
### 创建七牛云存储空间
在 控制台>对象存储>空间管理 中新建存储空间，名称自定义，区域选离自己近的。
图片
创建完之后，在 控制台>CDN>域名管理 中可看到免费的测试域名：
图片
测试域名有使用限制：
图片
所以需要绑定一个已备案的个人域名：
图片
### 个人域名备案
a. 进入阿里云 控制台>ICP备案 点击我要备案
图片
b. 根据提示，填写信息
图片
点击 信息校验 ，若报错，则根据提示修改，可以参考[ICP备案文档](https://help.aliyun.com/document_detail/61819.html) 或右侧智能在线询问。因为域名实名认证通过后还需同步到工信局，需要1-2天时间，所以报如下错：
图片
### 绑定域名
域名备案通过之后，即可在七牛云中点击 控制台>CDN>域名管理>添加域名：
图片
加速域名可以填一个二级域名。即在你的域名（假定为xxxx.com）面前增加一个比如cdn.xxxx.com或者qn.xxxx.com都是可以的，这个随你的喜欢。创建完成后，就会有一个CNAME的域名。复制之后。到阿里云域名控制台解析DNS增加新的记录（同上面域名解析操作）。
图片（域名解析列表）
### 仓库配置
根据新的二级域名，重新配置，生成新的CNAME文件
图片
在七牛云 控制台>对象存储>空间管理 中上传一张图片，测试是否成功
图片
### 使用图床工具PicGo
图传工具就是说可以方便我们上传图片到图床的客户端工具，Mpic 或 PicGo等，我们使用PicGo：

## 其他
写到此处，差不到也告一段落，总结一些经验，分享一些有趣的东西，开源精神不灭。希望能给正在阅读文章的你带来一些灵感与帮助。如果有任何疑问或者更好的想法欢迎在评论区交流， 大家一起学习。

## ---- End ----