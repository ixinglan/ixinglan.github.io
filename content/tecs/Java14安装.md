---
title: "Java14安装"
date: 2020-06-19T09:02:17+08:00
draft: false
---
# jdk最新版本
截止目前,jdk版本已经更新到14

# jdk14新特性
`https://openjdk.java.net/projects/jdk/14/` --openjdk网站我们可以看到有以下16大特性
```http request
305:	Pattern Matching for instanceof (Preview)
343:	Packaging Tool (Incubator)
345:	NUMA-Aware Memory Allocation for G1
349:	JFR Event Streaming
352:	Non-Volatile Mapped Byte Buffers
358:	Helpful NullPointerExceptions
359:	Records (Preview)
361:	Switch Expressions (Standard)
362:	Deprecate the Solaris and SPARC Ports
363:	Remove the Concurrent Mark Sweep (CMS) Garbage Collector
364:	ZGC on macOS
365:	ZGC on Windows
366:	Deprecate the ParallelScavenge + SerialOld GC Combination
367:	Remove the Pack200 Tools and API
368:	Text Blocks (Second Preview)
370:	Foreign-Memory Access API (Incubator)
```
下面我们就常用到的特性改变举例看一下
1. 305:	Pattern Matching for instanceof (Preview) instanceof新特性
```java
//以前我们是这么使用的
if (obj instanceof String) {
    String s = (String) obj;
    // use s
}

//新特性我们可以这么使用,即将强制转换类型绑定给变量,但变量只能在true块中使用
if (obj instanceof String s) {
    // can use s here
} else {
    // can't use s here
}
if (!(obj instanceof String s)) {
    // can't use s here
} else {
    // can use s here
}

//可以在&&后使用, 但不能在||后使用
if (obj instanceof String s && s.length > 10) {
    // can use s here
}
```
2. 359:	Records (Preview) 记录类型
```java
import static java.util.Calendar.*;

//有望替代lombok
public class Test {
    public record User(int x, int y) {
    }

    public static void main(String[] args) {
        User user = new User(1, 2);
        System.out.println(user.x);
        System.out.println(user.y);

    }
}

```
3. 361:	Switch Expressions (Standard) switch语法形式改变
```java
import static java.util.Calendar.*;

public class Test {
    public static void main(String[] args) {
        formatWeek(0);
        formatWeek(1);
        formatWeek(2);
        formatWeek(3);
        formatWeek(4);
        formatWeek(5);
        formatWeek(6);

        System.out.println(formatWeek2(5));
        System.out.println(formatWeek2(6));
    }

    public static void formatWeek(int day) {
        switch (day) {
            case MONDAY -> System.out.println("MONDAY");
            case TUESDAY -> System.out.println("TUESDAY");
            case WEDNESDAY -> System.out.println("WEDNESDAY");
            case THURSDAY -> System.out.println("THURSDAY");
            case FRIDAY -> System.out.println("FRIDAY");
            case SATURDAY -> System.out.println("SATURDAY");
            case SUNDAY -> System.out.println("SUNDAY");
        }
    }

    public static String formatWeek2(int day) {
        String str = switch (day) {
            case MONDAY -> "MONDAY";
            case TUESDAY -> "TUESDAY";
            case WEDNESDAY -> "WEDNESDAY";
            case THURSDAY -> "THURSDAY";
            case FRIDAY -> "FRIDAY";
            case SATURDAY -> "SATURDAY";
            case SUNDAY -> "SUNDAY";
            default -> "NONE";
        };
        return str;
    }
}

```
4. 368:	Text Blocks (Second Preview) 文本块
```java
//old
String html = "<html>\n" +
              "    <body>\n" +
              "        <p>Hello, world</p>\n" +
              "    </body>\n" +
              "</html>\n";
//new 
String html = """
              <html>
                  <body>
                      <p>Hello, world</p>
                  </body>
              </html>
              """;
```
# jdk14的安装
## macOS安装
下载最新的jdk-14.0.1_osx-x64_bin.dmg包, `https://www.oracle.com/java/technologies/javase-jdk14-downloads.html`  
按步骤安装即可,自动配置环境变量,安装完`java -version` 查看显示版本号即安装成功
## linux安装
下载最新的jdk-14.0.1_linux-x64_bin.tar.gz包
### 解压包
```shell script
cd /usr
mkdir java
cd java
#将.tar.gz包拷贝到java目录
tar zxvf jdk-14.0.1_linux-x64_bin.tar.gz 
```
### 配置环境变量
在/etc/profile配置文件里追加以下配置即可
```shell script
export JAVA_HOME=/usr/java/jdk-14.0.1
export PATH=$JAVA_HOME/bin:$PATH
```
`java -version` 查看显示版本号即安装成功

