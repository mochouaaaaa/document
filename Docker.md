

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
docker run -itd -p 6379:6379 -v /home/nan/docker_data/redis/data:/data -v /home/nan/docker_data/redis/redis.conf:/etc/redis/redis.conf --name redis --restart=always redis redis-server /etc/redis/redis.conf --appendonly yes
```

- redis-server /etc/redis/redis.conf 让redis使用配置文件启动



### Docker启动Mysql

```bash
docker run -itd --name mariadb -p 3306:3306 -e MYSQL_ROOT_PASSWORD=P@ssw0rd -v /home/nan/docker_data/mariadb/data/:/var/lib/mysql -v /home/nan/docker_data/mariadb/config/my.cnf:/etc/mysql/my.cnf --restart=always mariadb
```





### Docker启动Myadmin

```bash
docker run --name myadmin -d --link mysql_db_server:db -p 8080:80 -e PMA_ARBITRARY=1 phpmyadmin/phpmyadmin
```


### Dokcer搭建ftp服务器
```bash
docker run -itd --name ftp -v /home/nan/code:/home/vsftpd  -p 20:20 -p 21:21 -p 47400-47470:47400-47470 -e FTP_USER=nan -e FTP_PASS=P@ssw0rd -e PASV_ADDRESS=0.0.0.0 --restart=always bogem/ftp
```