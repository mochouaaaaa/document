

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
```

