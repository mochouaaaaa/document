## 一、前提条件

你可以从 Ubuntu 18.04 或者 Ubuntu 19.10 直接升级到 版本 20.04。 如果你运行任何之前的发行版本，你必须先[升级到 18.04](https://link.zhihu.com/?target=https%3A//linuxize.com/post/how-to-upgrade-to-ubuntu-18-04-bionic-beaver/) 或者 19.10。



## 二、备份你的数据

首先并且最重要的事情，请确保在你的操作系统进行重大升级之前，备份好你的数据。



## 三、升级当前已经安装的软件包

在开始版本升级之前，我们推荐你将所有当前安装的软件包升级到它们的最新版。

所有被标记为锁定版本的软件包都不能被自动安装，升级或者移除。这将可能会在升级过程中造成问题。想要检查你的系统上是否有被锁住版本的软件包：

```text
sudo apt-mark showhold
```

如果有`on hold`，软件包，你应该考虑使用下面的命令，解锁这些软件包:

```text
sudo apt-mark unhold package_name
```

刷新APT 列表，并且升级所有的已安装软件包：

```text
sudo apt update
sudo apt upgrade
```

如果内核被升级，重启机器，并且一旦启动完成，重新登录进去：

```text
sudo systemctl reboot
```

对于所有已经安装的软件包执行一个主要版本升级：

```text
sudo apt full-upgrade
```

`apt full-upgrade`可能会移除一些不必要的软件包。

移除任何被自动安装的，但是不再被任何包所依赖的软件包：

```text
sudo apt --purge autoremove
```



## 四、升级到 Ubuntu 20.04 LTS (Focal Fossa)

升级到最新的 Ubuntu 版本是一个很简单直接的过程。你可以通过图形升级工具，或者在命令行中执行`do-release-upgrade`来升级。

我们将会从命令行进行升级，这适合于桌面版和服务器版本系统。

`do-release-upgrade`是 “update-manager-core” 软件包的一部分，而该软件包在大多数的 Ubuntu 系统上都已经默认安装了。如果因为一些原因，在你的系统上，该软件包没有被安装，通过下面的命令安装它：

```text
sudo apt install update-manager-core
```

开始升级，输入：

```text
sudo do-release-upgrade
```

在终端命令远程升级过程中会提示1022端口作为额外的临时ssh端口防止22端口断开的备用端口。



## 五、确认

等待几分钟，直到你的系统启动，并且登录进去。

如果你是一个桌面版本用户，你将会看到一个新的图形启动界面和登录屏幕。

你可以输入下面的命令，检查 Ubuntu 版本：

```text
lsb_release -a
```

输出：

```text
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04 LTS
Release:	20.04
Codename:	focal
```

就这些。你的新 Ubuntu 20.04 安装完成。



## ****、鼠标键盘无法使用
```bash
sudo apt install xserver-xorg-input-all 
```