---
title: "如何用hugo搭建一个个人博客网站"
date: 2019-11-07T15:43:59+08:00
draft: false
---
## 准备工作

- 安装homebrew(可参考mac常用配置文章)

* 安装go环境(可通过homebrew安装)

* Github账号(用来托管静态页面)

## 环境的安装

### 安装Hugo

安装hugo有两种方式,二进制安装和源代码模式,这里推荐使用二进制模式,直接通过homebrew即可安装

```bash
brew install hugo
```

安装完成后通过以下命令查看

```shell
brew list 
```

如果有Hugo,即代表安装成功

### 生成一个站点

```bash
cd ~/  # 进入到个人目录下
hugo new site test-blog  # 生成一个名为test-blog的站点
```

<img src="/hugo/1.png" width="70%" height="10%" alt="" align=center>

如图进入test-blog目录,我们看到站点目录结构如下,我们只关注有注释的目录即可

```go
>archetypes
>content    #静态页面目录
>data       
>layouts
>static     #静态文件目录
>thems      #主题目录
-config.toml#配置文件
```

### 创建页面

页面都必须生成在content目录下,如果有多个模块,可创建多个文件夹

这里举例生成两个模块

```bash
hugo new a/a.md
hugo new b/b.md
```

如下:

```markdown
 我设置a/a.md title为test-A,内容随便写
---
title: "test-A"
date: 2019-11-26T17:48:47+08:00
draft: true
---
* a
* b
* c
................................................................
我设置b/b.md title为test-B,内容随便写
---
title: "test-B"
date: 2019-11-26T17:48:47+08:00
draft: true
---
1. a
2. b
3. c
```

### 安装主题

```http
https://themes.gohugo.io/ 选择一款自己喜欢的主题
```

```bash
$ cd themes
$ git clone https://github.com/spf13/hyde.git
```

### 修改配置文件config.toml

```toml
baseURL = "http://example.org/"
languageCode = "zh-cn"
title = "My New Hugo Site"
theme = "hyde"              #theme即为刚下载的主题名称
```

### 测试运行

```bash
hugo server -D  
# 启动本地测试,
```

如图所示,即代表成功

<img src="/hugo/2.png" width="60%" height="25%" alt="" align=center>

**我们可通过标示地址: *localhost:1313*进行本地预览**

如下图即为运行结果

<img src="/hugo/3.png" width="60%" height="40%" alt="" align=center>

### 编译

hugo的编译相当快

```bash
hugo
#执行hugo命令,会看到目录下多了 public 目录,此即为生成的静态页面
```

### 发布

我们用Github Pages 来托管我们的静态页面,在github账号创建一个{username}.github.io,括号内为你的github用户名称,修改配置文件config.toml的baseURL为如下:

```toml
baseURL = "http://{username}.github.io"
```

将public内容推送到该目录即可

```bash
git init
git remote add origin https://github.com/{username}/{username}.github.io.git
git add -A
git commit -m "my blog"
git push -u origin master
```

接下来,即可使用 http://{username}.github.io 地址来访问你的个人博客了~

### 强制使用https

在github工程   Settings/GitHub Pages,勾选Enforce HTTPS 选项,即可启用https访问

> ps:github的访问速度有些慢,国内的gitee pages也支持Hugo的托管,感兴趣的可以查看文档配置即可