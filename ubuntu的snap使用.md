## snap简介  

什么是snap，snap是一种全新的软件包管理方式，它类似一个容器拥有一个应用程序所有的文件和库，各个应用程序之间完全独立。所以使用snap包的好处就是它解决了应用程序之间的依赖问题，使应用程序之间更容易管理。但是由此带来的问题就是它占用更多的磁盘空间。

Snap的安装包扩展名是.snap，类似于一个容器，它包含一个应用程序需要用到的所有文件和库（snap包包含一个私有的root文件系统，里面包含了依赖的软件包）。它们会被安装到单独的目录；各个应用程序之间相互隔离。使用snap有很多好处，首先它解决了软件包的依赖问题；其次，也使应用程序更容易管理。



## snap安装

```shell
sudo apt-get install snapd

sudo apt-get install snapcraft 
```



## 一些常用的命令 

```shell
列出已经安装的snap包
snap list

搜索要安装的snap包
snap search <app_name>
 snap find <text to search>

安装一个snap包
snap install <snap name>

更新一个snap包，如果你后面不加包的名字的话那就是更新所有的snap包
sudo snap refresh <snap name>

把一个包还原到以前安装的版本
sudo snap revert <snap name>

删除一个snap包
sudo snap remove <snap name>

启动一个snap应用
snap satrt/run <snap_name>

停止一个snap应用
snap stop <snap_name>

查询一个应用详细信息/具体版本
snap info <snap_name>
```



