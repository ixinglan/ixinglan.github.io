---
title: "如何使用Gitbook搭建个人文档"
date: 2019-11-27T09:41:50+08:00
draft: false
---

GitBook的使用相较之下还是比较的简单,插件也相对丰富

## 安装node.js(使用homebrew安装即可)

```bash
node -v #v12.12.0
```

## 安装gitbook

```bash
$ npm install gitbook-cli -g
```

## 检验安装

```bash
gitbook -V
#  CLI version: 2.3.2
#  GitBook version: 3.2.3
```

## 创建目录 test-gitbook

```bash
cd test-gitbook
gitbook init
# 可以看到多了README.md  SUMMARY.md 两个文件
```

## 编辑summary.md

```markdown
# Summary

* [前言](README.md)
* [JAVA技术](Chapter1/README.md)
* [GO技术](Chapter2/README.md)
* [深入理解JVM](Chapter3/README.md)
* [算法知识](Chapter4/README.md)
* [SPRING系列](Chapter5/README.md)
    * [Spring](Chapter5/Spring.md)
    * [SpringBoot](Chapter5/SpringBoot.md)
    * [SpringCloud](Chapter5/SpringCloud.md)
* [DOCKER](Chapter6/README.md)
* [NETTY](Chapter7/README.md)
```

## 生成网页并启动gitbook

```bash
gitbook serve

# 通过http://localhost:4000即可访问
```

如图所示,就是我们刚创建的目录

<img src="/gitbook/1.png" width="70%" height="40%" alt="" align=center>

## 生成网页不启动gitbook

```bash
gitbook build 
#默认将静态页面生成在 _book目录下,如果想指定目录,比如托管在github上,则需要生成在docs目录,可以使用以下命令
gitbook build ./ ./docs
```

## 目录结构

再次执行gitbook init,即可看到目录下初始化了在SUMMARY.md里配置的目录结构

```bash
>Chapter1   
>Chapter2   
>Chapter3   
>Chapter4   
>Chapter5   
>Chapter6   
>Chapter7   
README.md  
SUMMARY.md 
>_book       #此目录为gitbook build后生成的静态页面目录
```

我们只要在相应目录下添加我们的内容即可

## 插件的使用

```markdown
Gitbook默认自带有5个插件：
如果不用可以前面加-去掉
highlight： 代码高亮
search： 导航栏查询功能（不支持中文）
sharing：右上角分享功能
fontsettings：字体设置（最上方的"A"符号）
livereload：为GitBook实时重新加载
```

如果想使用插件等,我们可以在目录下创建book.json配置文件,附上我的配置文件

```json
{
    "title": "古道长亭",  //标题
    "author": "古道长亭", //作者
    "description": "welcome to my docs", //书描述
    "language": "zh-hans", //语言 en, zh-hans, zh-tw
    "gitbook": "3.2.3",  //指定gitbook版本
    "styles": {       //样式文件
        "website": "./style/website.css"
    },
    "structure": {   //指定 Readme、Summary、Glossary 和 Languages 对应的文件名
        "readme": "README.md"
    },
    "links": {      //左侧导航栏链接信息
        "sidebar": {
            "回到博客": "https://blog.zhaojq.top"
        }
    },
    "plugins": [    //插件列表
        "anchors",
        "anchor-navigation-ex",  //给页面H1-H6标题增加锚点效果,浮动导航模式,页面内顶部导航模式等
        "auto-scroll-table", //表格滚动条
        "advanced-emoji",   //支持emoji表情    
        "code", //代码添加行号&复制按钮
        "chapter-fold", //导航目录折叠
        "donate", //打赏插件
        "expandable-chapters-small",//可扩展导航章节,比toggle-chapters好用
        "favicon", //标题栏图标配置
        "fontsettings",   //字体设置（最上方的"A"符号）                     
        "github",  //在右上角添加github图标
        "github-buttons",//github按钮显示,包括可配置stars数量等
        "klipse",  //嵌入类似IDE的功能
        "-livereload", //为GitBook实时重新加载
        "-lunr", //自带搜索
        "pageview-count", //阅读量计数
        "page-toc-button", //page-toc-button
        "popup", //弹出大图
        "sharing-plus", //分享插件
        "-sharing", //自带分享
        "splitter", //侧边栏宽度可调节
        "-search",  //自带搜索,前面加-去掉
        "search-pro", //高级搜索插件 需要将默认的search和lunr 插件去掉
        "toggle-chapters", //扩展导航章节,由expandable-chapters-small代替
        "tbfed-pagefooter", //页面添加页脚（简单的）
        "todo", //待做项
        "hide-element" //隐藏元素,比如导航栏中Published by GitBook,"elements": [".gitbook-link"]
    ],
    "pluginsConfig": {   //配置插件的属性
        "github": {
            "url": "https://github.com/zhao-xiaoer"
        },
        "hide-element": {
            "elements": [".gitbook-link"]
        },
        "theme-default": {
            "showLevel": true
        },
        "code": {
            "copyButtons": true
        },
        "tbfed-pagefooter": {
            "copyright": "Copyright © zhaojq 2019",
            "modify_label": "本书发布时间：",
            "modify_format": "YYYY-MM-DD HH:mm:ss"
        },
        "donate": {
            "wechat": "/gitbook/source/images/wechat.jpeg",
            "alipay": "/gitbook/source/images/alipay.jpeg",
            "title": "感谢支持",
            "button": "\uD83D\uDE00",
            "alipayText": "支付宝打赏",
            "wechatText": "微信打赏"
        },
        "github-buttons": {
            "buttons": [{
                "user": "zhao-xiaoer",
                "repo": "gitbook",
                "type": "star",
                "size": "small",
                "count": true
                }
            ]
        },
        "page-toc-button": {
            "maxTocDepth": 2,
            "minTocSize": 2
        },
        "sharing": {
            "douban": false,
            "facebook": false,
            "google": false,
            "hatenaBookmark": false,
            "instapaper": false,
            "line": false,
            "linkedin": false,
            "messenger": false,
            "pocket": false,
            "qq": true,
            "qzone": true,
            "stumbleupon": false,
            "twitter": false,
            "viber": false,
            "vk": false,
            "weibo": true,
            "whatsapp": false,
            "all": [
                "weibo","qq", "qzone", "douban", "facebook","google",
							  "linkedin","twitter","whatsapp"
            ]
        },
        "anchor-navigation-ex": {
            "showLevel": true
        },
        "favicon":{
            "shortcut": "",
            "bookmark": ""
        }
    }
}
```

### 使用以下命令安装插件

```bash
gitbook install
```

## 发布

首先build到docs目录,在github新建一个工程,例如:gitbook,将gitbook整个目录push到github/gitbook,配置个人域名的方法和hugo的配置一样,github会自动从docs目录加载静态页面.

接下来你可以通过个人域名访问gitbook

我的gitbook地址: *https://blog.zhaojq.top/gitbook*