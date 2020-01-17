---
title: "mac常用配置"
date: 2019-11-06T00:08:17+08:00
draft: false
---

### 个人笔电的选择

如果你是一个java程序员,那么你肯定用过Eclipse 和 Intellij IDEA, Mac 之于Windows,就像idea之于eclipse, mac设计的美观性,系统的流畅度,环境的整洁,应用的管理,友好的操作.....绝对会让你迷上它.

### 如何配置一台适合开发使用的mac

这里我只介绍一些关键性配置

#### 触发角

在系统偏好设置/桌面与屏幕保护程序/屏幕保护程序 菜单下,会有触发角的设置,即鼠标快速移动到屏幕四个角,可以触发不同的操作,比如,关闭屏幕,调度中心,启动台,程序窗口等,可以快速的帮助你进行一系统方便的操作.

#### zsh 

mac的终端是默认是bash,但zsh才是一个更加强大的命令行工具

```bash
切换到bash:
chsh -s /bin/bash
```

```oz
切换到zsh
chsh -s /bin/zsh
```

#### Oh my zsh

zsh具有强大的可定制的特点，支持许多插件,补全功能也强大很多.但是却配置起来十分的麻烦，但有了oh-my-zsh之后，一切变得简单起来了

1. 安装 oh my zsh

   首先切换到zsh,以下命令由于是国外链接,有时会显示失败,多尝试或者切换网络再尝试

   ```oz
   $ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
   ```

2. 配置主题

   ```oz
   vim ~/.zshrc
   ```

   <img src="/mac/zshrc.png" width="460" height="250" alt="" align=center>

   主题选择可以从这里选择,直接覆盖配置文件名字即可

   ```http
   https://github.com/ohmyzsh/ohmyzsh/wiki/Themes
   ```

#### Homebrew

Homebrew 可以理解为命令行应用管理软件,就像你能看得见的appStore一样,只不过它是通过命令行管理的

1. 安装homebrew,同oh my zsh一样,如果失败,切换网络或者多次尝试即可

   ```oz
   /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   ```

2. 查找软件

   ```oz
   brew search ***
   ```

   这时会有**Formulae**和**Casks**两种类型,Formulae是一些常用包,比如java,python,go等安装包, cask是软件

3. 安装软件

   ```oz
   brew install ***
   brew cask install ***
   ```

4. 查看安装的软件

   ```oz
   brew list
   brew cask list
   ```

5. 卸载包

   ```oz
   brew uninstall ***
   ```
   
6. 启动服务

   ```oz
   brew services run formula|--all  # 启动服务（仅启动不注册）
   brew services start formula|--all  # 启动服务，并注册
   brew services restart formula|--all  # 重启服务，并注册
   ```

7. 查看启动的服务

   ```oz
   brew services list  # 查看使用brew安装的服务列表
   brew services cleanup  # 清除已卸载应用的无用的配置
   ```

8. 停止服务

   ```oz
   brew services stop formula|--all   # 停止服务，并取消注册
   ```

#### 环境变量的配置

```bash
a. /etc/profile 
b. /etc/paths 
c. ~/.bash_profile 
d. ~/.bash_login 
e. ~/.profile 
f. ~/.bashrc 
```

其中a和b是`系统级别`的，系统启动就会加载，其余是用户接别的。c,d,e按照从前往后的`顺序读取`，如果c文件存在，则后面的几个文件就会被忽略`不读了`，以此类推。~/.bashrc没有上述规则，它是bash shell打开的时候载入的。这里建议在.bash_profile中添加环境变量
以maven举例:

```visual basic
export M2_HOME=/Users/zhaojianqiang/tools/apache-maven-3.6.2
export PATH=$PATH:$M2_HOME/bin
```

