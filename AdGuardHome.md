



### Ubuntu安装

```bash
snap install adguard-home
```



```config
所有接口端口 8088
所有监听端口 553
```





### 配置

- https://adguardteam.github.io/AdGuardSDNSFilter/Filters/filter.txt
- https://cdn.jsdelivr.net/gh/privacy-protection-tools/anti-AD/anti-ad-easylist.txt
- https://gitee.com/xinggsf/Adblock-Rule/raw/master/rule.txt
- https://gitee.com/halflife/list/raw/master/ad-edentw.txt
- https://gitee.com/halflife/list/raw/master/ad.txt
- https://gitee.com/halflife/list/raw/master/ad2.txt



NEOHosts

链接：https://cdn.jsdelivr.net/gh/neoFelhz/neohosts@gh-pages/full/hosts

AdBlock 自定义拦截规则

链接：https://cdn.adblockcdn.com/filters/adblock_custom.txt

反规避拦截规则

链接：https://easylist-downloads.adblockplus.org/abp-filters-anti-cv.txt

允许非侵入式广告

链接：https://easylist-downloads.adblockplus.org/exceptionrules.txt

Adblock 警告移除列表

链接：https://easylist-downloads.adblockplus.org/antiadblockfilters.txt

anti-AD v4

链接：https://gitee.com/privacy-protection-tools/anti-ad/raw/master/easylist.txt

neohosts

链接：https://cdn.jsdelivr.net/gh/neoFelhz/neohosts@gh-pages/basic/hosts.txt

EasyList China : 国内网站广告过滤的主规则。

链接：https://easylist-downloads.adblockplus.org/easylistchina.txt

EasyList Lite

链接: https://raw.githubusercontent.com/cjx82630/cjxlist/master/cjxlist.txt

EasyPrivacy : EasyPrivacy 是隐私保护，不被跟踪。

链接：https://easylist-downloads.adblockplus.org/easyprivacy.txt

CJX’s Annoyance List : 过滤烦人的自我推广，并补充 EasyPrivacy 隐私规则。

链接：https://raw.githubusercontent.com/cjx82630/cjxlist/master/cjx-annoyance.txt

I don’t care about cookies : 我不关心 Cookie 的问题，屏蔽网站的 cookies 相关的警告。

链接：https://www.i-dont-care-about-cookies.eu/abp/





## **常见问题**

### **端口冲突**

在 Linux 设备上运行 AdGuard Home，通常会出现 53（本地 DNS 服务器）、68（DHCP 客户端）、80（Http）、443（Https） 端口冲突的问题，可以通过 netstat -tunlp | grep 端口号 查询占用进程。有两种解决方案：使用不同端口、停用冲突进程。

如果是通过 Docker 方式运行 AdGuard Home，出现 `listen udp 0.0.0.0:53: bind: address already in use` 的提示，需要手动处理，方法如下：

```shell
#停止 DNSStubListener
systemctl stop systemd-resolved

#创建文件夹（如果不存在）
mkdir /etc/systemd/resolved.conf.d/

#使用 Nano 创建配置文件
nano /etc/systemd/resolved.conf.d/adguardhome.conf
```

在编辑器中粘贴以下内容：

```null
[Resolve]
DNS=127.0.0.1
DNSStubListener=no
```

保存后执行以下命令。

```shell
#创建备份
sudo mv /etc/resolv.conf /etc/resolv.conf.backup

#将 /etc/resolv.conf 链接至 /run/systemd/resolve/resolv.conf
ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf

#重启 DNSStubListener
systemctl restart systemd-resolved
```

完成后使用 `netstat -tunlp | grep 53` 命令检查是否依旧有进程占用 53 端口，如无冲突，重启 AdGuard Home 容器即可。

### 平均处理时间过长？

以下几个因素会使 AdGuard Home 的处理时间过长：

- 本地到上游DNS的速度：如果本地运营商的 DNS 没有 DNS 劫持、投毒的问题，建议使用运营商 DNS + 公共 DNS 的方案，DNS 速度可以通过 Ping 值比较。并在 AdGuard Home 中选择以「并行请求」的方式处理 DNS 请求
- 浏览安全、家庭控制与强制安全搜索服务：以上三个功能，在 DNS 请求时不会经过 DNS 缓存，直接向上游 DNS 服务器请求，从而减慢 AdGuard Home 的处理速度
- IPv6：如果使用的宽带、校园网不支持 IPv6，可以禁用解析 IPv6，提高响应速度
- 过滤规则：过多的过滤规则会影响响应速度，宁缺毋滥，选择最合适自己的规则，一般保留 3 - 4 个广告过滤规则即可。
- 统计周期： 在完成以上优化后，发现平均处理时间并没有太大改变，使用体验上也并不慢，有可能是因为统计周期过长，将优化前的结果计入，导致优化前后的结果无太大差异。不妨将仪表盘的统计周期缩短为 24 小时再看看。

完成以上步骤后使用体验比没有使用 AdGuard Home 还要糟糕，问题有亿点严重了。这个时候需要查找 AdGuard Home 的日志，寻找问题的原因。

### 部分网页被 AdGuard Home 误杀

如果一些网页被 AdGuard Home 误杀，可以在 AdGuard Home 的日志寻找是否被拦截。如果与规则发生冲突，需要将误杀网址通过自定义过滤规则添加至白名单中，或选择其它的过滤规则。常见的冲突有网站统计服务（Google Analytics）、广告联盟等。

### 自定义过滤规则

AdGuard Home 的过滤规则兼容 Adblock 语法、Hosts 语法及 Domain-only 语法。

| **语法**                | **作用**                                  |
| ----------------------- | ----------------------------------------- |
| `||example.org^`        | 拦截 example.org 域名及其所有子域名       |
| `@@||example.org^`      | 放行 example.org 及其所有子域名           |
| `127.0.0.1 example.org` | 将 example.org 解析到 127.0.0.1           |
| `/REGEX/`               | 阻止访问与 example_regex_meaning 匹配的域 |
| `! 这是一行注释`        | 只是一条注释                              |
| `# 这是一行注释`        | 只是一条注释                              |

### 能否将 AdGuard Home DNS 与 Surge / Clash 网关结合使用？

可以。Surge 与 Clash 分别提供了 `dns-server` 与 `dns-nameserver` 字段以供用户修改 DNS 解析服务器，在配置文件中填入 AdGuard Home 的 DNS 服务器地址即可。