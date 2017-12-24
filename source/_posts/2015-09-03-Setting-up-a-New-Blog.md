title: Setting up a New Blog
date: 2015-09-03 11:13:32
categories: Techniques
---

## 安装 Hexo

请参照 [Hexo 官方文档](https://hexo.io/docs/)

## 安装 git 支持

不安装的话，在部署的时候，会提示找不到 git。

``` bash
$ npm install hexo-deployer-git --save
```

## 创建并初始化 Hexo

``` bash
$ mkdir /path/to/your/blogDir
$ cd /path/to/your/blogDir
$ hexo init
$ npm install
```

## 启动本地服务并查看

``` bash
$ hexo generate
$ hexo server
```
接下来，打开浏览器，输入 localhost:4000，即可查看本地博客。


## 将本地博客部署到 Github 上

1. 在 [Github](https://github.com) 上新建一个 repository，如果你的账号名称为 xyz，那么你的 repository 名称应该为 xyz.github.io。
2. 编辑 /path/to/your/blogDir 目录下的 _config.yml 文件：

    ``` bash
    deploy:
      type: git
      repository: git@github.com:xyz/xyz.github.io.git
      branch: master
    ```
3. 执行部署命令

    ``` bash
    $ hexo generate
    $ hexo deploy # 相当于 git 提交
    # 接下来需要输入你在 github 上的账号密码，以便进行提交，这里可以设置 SSH 免密码提交
    ```


## Hexo 的一些常用快捷键

``` bash
hexo g == hexo generate
hexo d == hexo deploy
hexo n == hexo new
```

## 目录结构

1. /path/to/your/blogDir/source/ 目录用于存放博客相关的文件，如 _posts 目录存放所有文章，我们可以自己新建 assets 目录，用于存放一些图片之类的文件。
2. /path/to/your/blogDir/themes/ 目录用于存放博客主题。

## 配置

编辑 /path/to/your/blogDir/_config.yml 文件：

1. 根据自己的情况，填写 title, subtitle, description, author, language 几项。
2. 自定义文章名称：

    ``` bash
    new_post_name: :year-:month-:day-:title.md
    ```

    假定今天是 2015 年 10 月 1 号，我们新建一篇文章，

    ``` bash
    $ hexo new post "my first post"
    ```

    该命令会在执行后，根据配置文件，自动在 /path/to/your/blogDir/source/_posts 目录下新建名称为 2015-10-01-my-first-post.md 的文件。


## 自定义 404 页面

github 无法对二级域名（xxx.github.io）提供 404 页面自定义功能。目前的做法是，使用其他站点提供的公共 404 页面做跳转，我使用的是腾讯公益页面，具体做法如下：

在 source 目录下新建 404.html 页面，然后把从腾讯公益页面获得的代码加入其中，保存后重新部署即可，试试刷新页面看看，很方便。

## 更换主题

你可以到 [https://github.com/hexojs/hexo/wiki/Themes](https://github.com/hexojs/hexo/wiki/Themes) 挑选你自己喜欢的主题。

我选择的是 [yilia](https://github.com/litten/hexo-theme-yilia)，下面介绍如何安装和配置。

``` bash
$ cd /path/to/your/blogDir
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
$ cd themes/yilia
```

下面编辑 **yilia 目录下**的 _config.yml：

1. subnav 下面的每个链接都应该写上 http 或 https 前缀。
2. mail 的前缀是 mailto。
3. 假定我把头像图片放在了 /path/to/your/blogDir/source/assets 目录下，名称为 pic.jpg，那么 avatar 头像的路径为 "/assets/pic.jpg"。
4. duoshuo 评论需要先在 [多说](http://duoshuo.com) 注册自己的账号，注册之后会有自己的域名，假设域名为 xyz.duoshuo.com，那么配置文件中，将 duoshuo: true 改为 duoshuo: "xyz" 即可。

### 通过 Instagram 获取图片

首先，请参考 [这篇文章](https://github.com/litten/hexo-theme-yilia/wiki/%E5%90%8C%E6%AD%A5%E4%BD%A0%E7%9A%84instagram%E5%9B%BE%E7%89%87)。

然后在文章中给出的 index.md 文件末尾添加如下的代码，否则，图片拉取将会失败：

``` javascript
<script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.8.0.js"></script>
```

## 常见问题

### 找不到 hexo 命令

hexo 的安装默认不会将环境变量添加到 .bashrc 中，我们需要手动更新，否则，在重新打开 terminal 执行 hexo 时，会提示找不到命令。具体命令的位置为：

``` bash
    ~/.nvm/versions/node/v4.2.4/bin
```

### 执行 hexo deploy 后，出现 error deployer not found:git 的错误

``` bash
npm install hexo-deployer-git --save
```
