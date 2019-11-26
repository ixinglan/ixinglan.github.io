---
title: "个性化个人博客网站"
date: 2019-11-07T16:07:53+08:00
draft: false
---

## GitHub Pages绑定到自己的域名

1. 申请个人域名,完成备案,比如,我的*zhaojq.top*
2. 域名管理后台设置域名解析,比如,我将我的二级域名 blog.zhaojq.top 添加一个CNAME解析到https://{username}.github.io,比如,我将我的二级域名 *blog.zhaojq.top* 添加一个CNAME解析到*https://{username}.github.io*
3. 在GitHub账户工程  Settings/GitHub Pages /Custom domain处填写自己的域名信息
4. 即可通过 *https://blog.zhaojq.top* 访问自己的个人博客

## 如何修改主题模板

常用的主题形式一般是固定死的,我们想添加一些模块等的时候,并不能达到我们想要的效果,那么如何来修改主题模板呢?

以我个人使用的主题举例说明

```http
https://github.com/vaga/hugo-theme-m10c
```

#### 配置文件配置

我们发现,基本每个主题下都有目录**exampleSite**或者其他处可以找到*.toml配置文件示例,我们只要参照此配置文件配置我们主目录下的config.toml文件即可

####  增加模块

我发现这个主题只支持一个模块,即只有首页,这时我们就得查找Hugo官方文档,是英文的,凑合理解吧

```toml
[menu]
  [[menu.main]]
    name                   = "首页"
    weight                 = 1
    identifier             = "首页"
    url                    = "/posts/"
    title                  = "首页"
  [[menu.main]]
    name                   = "技术"
    weight                 = 2
    identifier             = "技术"
    url                    = "/tecs/"
    title                  = "技术分享"
  [[menu.main]]
    name                   = "技术系列"
    weight                 = 3
    identifier             = "系列"
    url                    = "/books/"
    title                  = "技术系列"
  [[menu.main]]
    name                   = "随笔"
    weight                 = 4
    identifier             = "随笔"
    url                    = "/free/"
    title                  = "生活随笔"
  [[menu.main]]
    name                   = "关于"
    weight                 = 5
    identifier             = "关于"
    url                    = "/about/"
    title                  = "关于"
```

效果如图

<img src="/hugo/4.png" width="400" height="300" alt="" align=center>

#### 配置其他参数

```toml
[params]
  author = "zhaoxr"
  description = "The road through the summit is always difficult and lonely"
  avatar = "avatar.jpeg"
  
  [[params.social]]
    name = "github"
    url = "https://github.com/zhao-xiaoer"
  [[params.social]]
    name = "csdn"
    url = "https://me.csdn.net/qq_34581279"
```

效果如图

<img src="/hugo/5.png" width="300" height="700" alt="" align=center>

#### 遇到的问题

* 此主题没有跳转csdn的图标?在/assets/css下.scss文件,可通过F12调试确定github图标,参照配置即可

* 此主题不支持上述配置的多个模块,如何添加?

  通过官方文档发现,hugo在主目录下layouts没有模板文件时,会找到themes下的layouts文件夹内的模板文件进行渲染

  ```
  baseof.html #渲染模块
  list.html   #渲染模块下文章列表
  single.html #渲染单个详情页面
  terms.html  #渲染文章浏览总数
  ```

  增加如下main参数遍历,即可展示

  ```html
  <main class="app-container">
        {{ block "main" . }}
          {{ .Content }}
        {{ end }}
  </main>
  ```

  

具体效果查看本博客: *https://blog/zhaojq.top* 具体代码查看本人gitHub: *https://github.com/zhao-xiaoer*

##### 如果想配置其他特性,可参数官方文档.