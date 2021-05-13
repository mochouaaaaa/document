

### 更新Docker自启

```bash
docker container update --restart=always {容器ID}
```



### Docker参数

```bash
-v 宿主机目录:docker目录
--name  容器名称
-itd  容器后台运行
-p		宿主机端口:docker端口
-e设置环境变量
--restart=always  自启动
```



### Docker启动redis

```bash
mkdir /home/nan/device/db_source_data/redis/data
touch /home/nan/device/db_source_data/redis/redis.conf
docker run -itd -p 6379:6379 -v /home/nan/device/db_source_data/redis/data:/data -v /home/nan/device/db_source_data/redis/redis.conf:/etc/redis/redis.conf --name redis --restart=always redis redis-server /etc/redis/redis.conf --appendonly yes
```

- redis-server /etc/redis/redis.conf 让redis使用配置文件启动



### Docker启动Mysql

```bash
mkdir -p /home/nan/device/db_source_data/mariadb/data
docker run -itd --name mariadb -p 3306:3306 -e MYSQL_ROOT_PASSWORD=P@ssw0rd -v /home/nan/device/db_source_data/mariadb/data/:/var/lib/mysql -v /home/nan/device/db_source_data/mariadb/config/my.cnf:/etc/mysql/my.cnf --restart=always mariadb
```





### Docker启动Myadmin

```bash
docker run --name myadmin -d --link mysql_db_server:db -p 8080:80 -e PMA_ARBITRARY=1 phpmyadmin/phpmyadmin
```


### Dokcer搭建ftp服务器
```bash
docker run -itd --name ftp -v /home/nan/code:/home/vsftpd  -p 20:20 -p 21:21 -p 47400-47470:47400-47470 -e FTP_USER=nan -e FTP_PASS=P@ssw0rd -e PASV_ADDRESS=0.0.0.0 --restart=always bogem/ftp
```

### Docker搭建Smb
```bash
docker run -itd --name samba -p 139:139 -p 445:445 -v /home/nan/device/share:/mount  --restart=always dperson/samba

### 进入docker bash 添加用户
groupadd xxx(用户名) -g  6000
useradd  xxx(用户名） -u 6000 -g 6000 -s /sbin/nologin -d /dev/null
### 添加smb用户
smbpasswd -a admin
### 修改配置文件，在/etc/samba/smb.conf的配置文件最下面添加
[root@localhost ~]# cat /etc/samba/smb.conf
[samba-share]
   comment = this is share
   path = /mount
   browseable = yes
   public = no
   writable = yes
   valid users = admin
   write list = admin

    
[global]
    workgroup = SAMBA
    security = user
    map to guest = Bad User
    username map = /etc/samba/smbusers   \\增加项
    passdb backend = tdbsam
```

```yaml
bash-5.0# cat smb.conf 
# This is the main Samba configuration file. You should read the
# smb.conf(5) manual page in order to understand the options listed
# here. Samba has a huge number of configurable options (perhaps too
# many!) most of which are not shown in this example
#
# For a step to step guide on installing, configuring and using samba, 
# read the Samba-HOWTO-Collection. This may be obtained from:
#  http://www.samba.org/samba/docs/Samba-HOWTO-Collection.pdf
#
# Many working examples of smb.conf files can be found in the 
# Samba-Guide which is generated daily and can be downloaded from: 
#  http://www.samba.org/samba/docs/Samba-Guide.pdf
#
# Any line which starts with a ; (semi-colon) or a # (hash) 
# is a comment and is ignored. In this example we will use a #
# for commentry and a ; for parts of the config file that you
# may wish to enable
#
# NOTE: Whenever you modify this file you should run the command "testparm"
# to check that you have not made any basic syntactic errors. 
#
#======================= Global Settings =====================================
[global]

# workgroup = NT-Domain-Name or Workgroup-Name, eg: MIDEARTH
   workgroup = MYGROUP

# server string is the equivalent of the NT Description field
   server string = Samba Server

# Server role. Defines in which mode Samba will operate. Possible
# values are "standalone server", "member server", "classic primary
# domain controller", "classic backup domain controller", "active
# directory domain controller".
#
# Most people will want "standalone server" or "member server".
# Running as "active directory domain controller" will require first
# running "samba-tool domain provision" to wipe databases and create a
# new domain.
   server role = standalone server

# This option is important for security. It allows you to restrict
# connections to machines which are on your local network. The
# following example restricts access to two C class networks and
# the "loopback" interface. For more examples of the syntax see
# the smb.conf man page
;   hosts allow = 192.168.1. 192.168.2. 127.

# Uncomment this if you want a guest account, you must add this to /etc/passwd
# otherwise the user "nobody" is used
;  guest account = pcguest

# this tells Samba to use a separate log file for each machine
# that connects
   log file = /dev/stdout

# Put a capping on the size of the log files (in Kb).
   max log size = 50

# Specifies the Kerberos or Active Directory realm the host is part of
;   realm = MY_REALM

# Backend to store user information in. New installations should 
# use either tdbsam or ldapsam. smbpasswd is available for backwards 
# compatibility. tdbsam requires no further configuration.
   username map = /etc/samba/smbusers
   passdb backend = tdbsam

# Using the following line enables you to customise your configuration
# on a per machine basis. The %m gets replaced with the netbios name
# of the machine that is connecting.
# Note: Consider carefully the location in the configuration file of
#       this line.  The included file is read at that point.
;   include = /usr/local/samba/lib/smb.conf.%m

# Configure Samba to use multiple interfaces
# If you have multiple network interfaces then you must list them
# here. See the man page for details.
;   interfaces = 192.168.12.2/24 192.168.13.2/24 

# Where to store roving profiles (only for Win95 and WinNT)
#        %L substitutes for this servers netbios name, %U is username
#        You must uncomment the [Profiles] share below
;   logon path = \\%L\Profiles\%U

# Windows Internet Name Serving Support Section:
# WINS Support - Tells the NMBD component of Samba to enable it's WINS Server
;   wins support = yes

# WINS Server - Tells the NMBD components of Samba to be a WINS Client
#	Note: Samba can be either a WINS Server, or a WINS Client, but NOT both
;   wins server = w.x.y.z

# WINS Proxy - Tells Samba to answer name resolution queries on
# behalf of a non WINS capable client, for this to work there must be
# at least one	WINS Server on the network. The default is NO.
;   wins proxy = yes

# DNS Proxy - tells Samba whether or not to try to resolve NetBIOS names
# via DNS nslookups. The default is NO.
   dns proxy = no 

# These scripts are used on a domain controller or stand-alone 
# machine to add or delete corresponding unix accounts
;  add user script = /usr/sbin/useradd %u
;  add group script = /usr/sbin/groupadd %g
;  add machine script = /usr/sbin/adduser -n -g machines -c Machine -d /dev/null -s /bin/false %u
;  delete user script = /usr/sbin/userdel %u
;  delete user from group script = /usr/sbin/deluser %u %g
;  delete group script = /usr/sbin/groupdel %g


   pam password change = yes
   map to guest = bad user
   usershare allow guests = yes
   create mask = 0664
   force create mode = 0664
   directory mask = 0775
   force directory mode = 0775
   force user = smbuser
   force group = smb
   follow symlinks = yes
   load printers = no
   printing = bsd
   printcap name = /dev/null
   disable spoolss = yes
   strict locking = no
   aio read size = 0
   aio write size = 0
   vfs objects = catia fruit recycle streams_xattr
   recycle:keeptree = yes
   recycle:maxsize = 0
   recycle:repository = .deleted
   recycle:versions = yes

   # Security
   client ipc max protocol = SMB3
   client ipc min protocol = SMB2_10
   client max protocol = SMB3
   client min protocol = SMB2_10
   server max protocol = SMB3
   server min protocol = SMB2_10

   # Time Machine
   fruit:delete_empty_adfiles = yes
   fruit:time machine = yes
   fruit:veto_appledouble = no
   fruit:wipe_intentionally_left_blank_rfork = yes

[samba-share]
   comment = this is share
   path = /mount
   browseable = yes
   public = no
   writable = yes
   valid users = admin
   write list = admin

```





### Docker搭建AdminMongo

```bash
docker run -itd --name mongoadmin --network host -e HOST=0.0.0.0 -e PORT=1234 mrvautin/adminmongo
```



### Docker 搭建MongoDB

```bash
docker run --name mongodb -v ~/docker_data/mongodb:/data/db -p 27017:27017 -d 995ccc33e58f --auth
docker exec -it mongodb mongo admin
db.createUser({ user: 'admin', pwd: 'P@ssw0rd', roles: [ { role: "userAdminAnyDatabase", db: "admin" } ] });
mongodb://admin:P%40ssw0rd@127.0.0.1:27017  # 需要16进制转义
db.createUser(
     {
       user:"blogs",
       pwd:"P@ssw0rd",
       roles:[{role:"dbAdmin",db:"blogs"}]
     }
  )
  
read:允许用户读取指定数据库
readWrite:允许用户读写指定数据库
dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户
clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。
readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限
readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限
userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。
root：只在admin数据库中可用。超级账号，超级权限
```



### Docker搭建AdguardHome

```bash
docker pull adguard/adguardhome
mkdir workdir confdir
docker run -itd --name adguardhome -v /home/nan/device/db_source_data/adguardhome/workdir:/opt/adguardhome/work -v /home/nan/device/db_source_data/adguardhome/confdir:/opt/adguardhome/conf -p 54:53/tcp -p 54:53/udp -p 3000:3000/tcp -p 8088:80/tcp --restart=always adguard/adguardhome
```

