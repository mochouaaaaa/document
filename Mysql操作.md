​	

### mysql创建用户、授权

- 创建用户

  ```mariadb
  create user username identified by 'password';
  ```

- 授权

  ```mariadb
  grant all privileges on *.* to 'username'@'%' identified by 'password' with grant option;
  ```

- 查看 mysql 初始的密码策略

  ```mariadb
  SHOW VARIABLES LIKE 'validate_password%';
  ```

- 创建数据库并设置编码

  ```mariadb
  CREATE DATABASE `mydb` CHARACTER SET utf8 COLLATE utf8_general_ci;
  ```

- 查询当前的用户，用户可登录的IP，还有用户的密码

  ```mariadb
  select host,user,authentication_string from mysql.user;
  ```



mariadb安全配置向导mysql_secure_installation

```bash
MariaDB的相关简单配置
mysql_secure_installation
首先是设置密码，会提示先输入密码
Enter current password for root (enter for none):<–初次运行直接回车
设置密码
Set root password? [Y/n] <– 是否设置root用户密码，输入y并回车或直接回车
New password: <– 设置root用户的密码
Re-enter new password: <– 再输入一次你设置的密码
其他配置
Remove anonymous users? [Y/n] <– 是否删除匿名用户，回车
Disallow root login remotely? [Y/n] <–是否禁止root远程登录,回车,
Remove test database and access to it? [Y/n] <– 是否删除test数据库，回车
Reload privilege tables now? [Y/n] <– 是否重新加载权限表，回车
初始化MariaDB完成，接下来测试登录
mysql -uroot -p
完成。
```



#### docker启动mariadb

```bash
docker run -itd --name mariadb -p 3306:3306 -e MYSQL_ROOT_PASSWORD=P@ssw0rd -v /home/nan/docker_data/mariadb/data/:/var/lib/mysql -v /home/nan/docker_data/mariadb/config/my.cnf:/etc/mysql/my.cnf ---restart=always mariadb
```



#### docker启动adminer

```bash
docker run -itd --link mariadb:mariadb --name adminer -p 8999:8080 --restart=always adminer
```


### mysql异常

- 启动MySQL数据库是却是这样的提示Failed to start mysqld.service: Unit not found

  ```shell
  yum install -y mariadb-server
  ```

  

