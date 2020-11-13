#### sys_flavor	规格

```mariadb
create table sys_flavor
(
    id          char(32)     not null primary key,
    name        varchar(100) not null comment '规格名称',
    cpus         int          not null comment 'cpu限制',
    memory_mb   int          not null comment '内存限制',
    is_public   tinyint(1)   not null comment '是否公用',
    user_id     int          not null comment '创建者id',
    create_time datetime(6)  null,
    update_time datetime(6)  null,
    constraint name unique (name),
    constraint flavor_user_id foreign key (user_id) references profile_user (id)
);
```



#### cloud_pool	业务池

```mariadb
create table cloud_pool
(
    id                 char(32)                    not null comment '业务池id，主键，UUID'
        primary key,
    name               varchar(32)                 not null comment '业务池名称，对应lxd的project名称',
    description        varchar(100)     default '' null comment '描述',
    status             tinyint unsigned default 0  null comment '业务池状态',
    cpu_limit          int unsigned     default 0  not null comment 'cpu限制',
    memory_limit       int unsigned     default 0  not null comment '内存限制，MB',
    storage_limit      int unsigned     default 0  not null comment '存储限制，GB',
    gpu_limit          int unsigned     default 0  null comment 'gpu限制',
    file_store_id      varchar(32)                 null comment '文件存储id',
    file_store_alloc   int unsigned                null comment '文件存储配额',
    file_store_actual  int unsigned                null comment '文件存储实际可用空间',
    block_store_id     varchar(64)                 null comment '块存储id',
    block_store_alloc  int unsigned                null comment '块存储配额',
    block_store_actual int unsigned                null comment '块存储实际可用空间',
   user_id     int          not null comment '创建者id',
    create_time        datetime                    null comment '创建时间',
    update_time        datetime                    null comment '更新时间',
    constraint name unique (name),
    constraint cloud_pool_user_id foreign key (user_id) references profile_user (id)
);
```



#### storage_snapshot	快照

```mariadb
create table storage_snapshot
(
    id  char(32)    not null primary key,
    snapshot_id   varchar(64) not null comment '镜像ID',
    snapshot_type varchar(32) not null comment '镜像类型',
    name          varchar(32) not null comment '镜像名称',
    create_time   datetime(6) not null comment '创建时间',
    disk_id       char(32)    null  comment '磁盘ID',
    pool_id       varchar(32) not null  comment '池ID',
    constraint name unique (name),
    constraint storage_snapshot_disk_id foreign key (disk_id) references storage_disk (id),
    constraint storage_snapshot_pool_id foreign key (pool_id) references cloud_pool (id)
);
```



#### disk	云硬盘

```mariadb
create table disk
(
    id          char(32)     not null primary key,
    name        varchar(32)  not null comment '云硬盘名称',
    size        int          null     comment '空间大小',
    disk_type   int          not null comment '磁盘类型',
    store_type  int          not null comment '是否为数据盘',
    status      int          not null comment '磁盘状态',
    is_public   int          not null comment '是否为共享卷',
    description varchar(100) null     comment '描述',
    create_time datetime(6)  null,
    update_time datetime(6)  null,
    pool_id     varchar(32)  null     comment '所属业务池',
    constraint storage_disk_pool_id foreign key (pool_id) references cloud_pool (id)
);
```



#### AccessStrategy	安全访问策略

```mariadb
create table access_strategy
(
    id  int auto_increment primary key,
    name          varchar(32)       not null comment '名称',
    strategy_type smallint unsigned not null comment '名单类型 1: 白名单， 0：黑名单',
    begin_ip      longtext          not null comment '其实IP',
    end_ip        char(39)          null     comment '结束IP',
    status        smallint unsigned not null comment '状态；1激活,2不激活',
    description   varchar(300)      null     comment '描述信息',
    create_time   datetime(6)       not null,
    update_time   datetime(6)       not null
);
```



#### ParamSetting	配置表

```mariadb
create table param_setting
(
    id          int auto_increment primary key,
    param_name  varchar(100) not null comment '配置名称',
    param_value longtext     not null comment '配置值',
    description varchar(100) null comment '配置描述',
    level       int          not null comment '配置层级',
    update_time datetime(6)  not null comment '更新时间',
    parent_id   int          null comment '上级ID，和level配合使用',
    constraint param_name unique (param_name),
    constraint param_setting_parent_id foreign key (parent_id) references param_setting (id)
);

```



#### Department	部门

```mariadb
create table profile_department
(
    id              int auto_increment primary key,
    department_name varchar(32)  not null comment '部门名称',
    description     varchar(100) null comment '部门描述',
    parent_name     varchar(32)  not null comment '上级部门名称',
    dep_path        varchar(99)  not null comment '部门层级路径',
    create_time     datetime(6)  not null comment '创建时间',
    update_time     datetime(6)  not null comment '更新时间',
    parent_id       int          null comment '上级ID',
    constraint profile_department_parent_id foreign key (parent_id) references profile_department (id)
);
```



#### ProfileUser	用户

````mariadb
create table profile_user
(
    id            int auto_increment primary key,
    is_delete     tinyint(1)   not null comment '是否软删除',
    create_time   datetime(6)  not null comment '创建时间',
    update_time   datetime(6)  not null comment '更新时间',
    realname      varchar(50)  not null comment '用户姓名',
    username      varchar(32)  not null comment '用户登录名称',
    password      varchar(128) not null comment '用户密码',
    email         varchar(50)  null comment '用户邮箱',
    phone         varchar(11)  null comment '用户手机号',
    state         tinyint(1)   not null comment '用户状态(0, 锁定), (1, 正常)',
    last_login    datetime(6)  null comment '最后登录时间',
    is_active     tinyint(1)   not null comment 'django所用的用户标示',
    is_admin      tinyint(1)   not null comment '是否为超级管理员',
    department_id int          null comment '部门ID',
    constraint profile_user_department_id foreign key (department_id) references profile_department (id)
);
````



### Log 操作日志

```mariadb
create table log
(
    id  int auto_increment primary key,
    create_time    datetime(6)  null comment '创建时间',
    update_time    datetime(6)  null comment '更新时间',
    operation_type varchar(20)  not null comment '操作类型',
    ip             char(39)     not null comment '操作者Ip地址',
    description    varchar(999) not null comment '描述信息',
    state          tinyint(1)   not null comment '0失败；1成功',
    error_code     varchar(10)  not null comment '错误码',
    operator_id    int          not null comment '操作人员ID',
    constraint log_operator_id foreign key (operator_id) references profile_user (id)
);
```







#### 工作流

```mariadb
create table approve_order_type
(
    id   int auto_increment primary key,
    name varchar(64)  not null,
    type int          not null,
    icon varchar(225) null
);

create table workflow_info
(
    id                    int auto_increment primary key,
    name                  varchar(50) not null,
    description           varchar(50) null,
    notice_type           varchar(50) null,
    flowchart             varchar(50) null,
    is_deleted            tinyint(1)  not null,
    create_time           datetime(6) not null,
    update_time           datetime(6) not null,
    approve_order_type_id int         not null,
    constraint workflow_info_approve_order_type foreign key (approve_order_type_id) references approve_order_type (id)
);

create table workflow_transition_rule
(
    id              int auto_increment primary key,
    name            varchar(50)  not null,
    alert_content   varchar(100) not null,
    create_time     datetime(6)  not null,
    update_time     datetime(6)  not null,
    source_state_id int          not null,
    target_state_id int          not null,
    workflow_id     int          not null,
    constraint workflow_transition__source_state_id foreign key (source_state_id) references workflow_state (id),
    constraint workflow_transition__target_state_id foreign key (target_state_id) references workflow_state (id),
    constraint workflow_transition__workflow_id foreign key (workflow_id) references workflow_info (id)
);


create table workflow_state
(
    id             int auto_increment
        primary key,
    name           varchar(100) not null,
    `order`        int          null,
    approve_domain int          null,
    approver       varchar(100) null,
    doc_state      int          null,
    workflow_id    int          not null,
    constraint workflow_state_workflow_id foreign key (workflow_id) references workflow_info (id)
);

create table approve_order_log
(
    id               int auto_increment primary key,
    comment          varchar(200) null,
    approve_domain   int          null,
    approver         varchar(100) null,
    create_time      datetime(6)  not null,
    is_deleted       tinyint(1)   not null,
    approve_order_id int          not null,
    state_id         int          not null,
    transition_id    int          null,
    constraint approve_order_log_approve_order_id foreign key (approve_order_id) references approve_order (id),
    constraint approve_order_log_state_id foreign key (state_id) references workflow_state (id),
    constraint approve_order_log_transition_id foreign key (transition_id) references workflow_transition_rule (id)
);


create table approve_order
(
    id                    int auto_increment
        primary key,
    sn                    varchar(20)   not null,
    name                  varchar(50)   not null,
    approve_domain        int           null,
    approver              varchar(100)  null,
    relation              varchar(1000) not null,
    doc_state             int           null,
    is_deleted            tinyint(1)    not null,
    form_data             longtext      null,
    create_time           datetime(6)   not null,
    update_time           datetime(6)   not null,
    approve_order_type_id int           not null,
    creator_id            int           not null,
    workflow_id           int           not null,
    workflow_state_id     int           not null,
    constraint sn unique (sn),
    constraint approve_order_approve_order_type foreign key (approve_order_type_id) references approve_order_type (id),
    constraint approve_order_creator_id foreign key (creator_id) references profile_user (id),
    constraint approve_order_workflow_id foreign key (workflow_id) references workflow_info (id),
    constraint approve_order_workflow_state_id foreign key (workflow_state_id) references workflow_state (id)
);
```

