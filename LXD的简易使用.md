

### 注：LXD命令操作是没有"-" [lxc ls而不是lxc-ls]



#### 直接使用镜像源拉取镜像

```bash
lxc image copy tuna-images:centos/8 local: --alias centos8 指定别名
```



#### 删除镜像

```bash
lxc launch delete local:centos8<alias_name>
```



#### 镜像alias别名操作

```bash
# 添加别名
sudo lxc image alias create centos8<alias_name> 1e66a562229e<指纹ID>

#如果我们在创建image的时候就添加了alias，这个时候想要修改，我们可以通过先增加alias，再删掉旧的进行
lxc image alias delete <old_alias>
```



#### 使用镜像创建容器

```bash
sudo lxc launch centos8<alias_name> c-centos8<容器名称>
```



#### 登录容器

```bash
sudo lxc exec c-centos8 -- /bin/bash
```

