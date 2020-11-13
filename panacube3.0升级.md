### 升级文件压缩包
-  升级压缩包可以是任意`tar`压缩格式
-  压缩包下必须包含以下2个文件一个文件夹
-  压缩包必须是单层，解开就能直接得到2个文件一个文件夹



#### upgrade.yaml  升级描述文件必须以下格式

- ```yaml
  version:  # 版本
    3.2.1
  
  details: # 升级内容
    新增:  # 具体升级操作
      - 升级测试
      - 升级数据库
      - 升级docker
      - xxxxx
  
    删除:
      - eeeeeeeeeeeeeeee
      - rrrrrrrrrrrrrrrrrrr
  
    更新:
      - 更新test文件
      - 更新数据库表
  ```



#### sqls 数据库升级文件

- ```shell
  sqls
  ├── 000001_3.1.06.down.sql
  ├── 000001_3.1.06.up.sql
  ├── 000002_3.1.0.13.down.sql
  ├── 000002_3.1.0.13.up.sql
  ├── 000003_3.1.0.16.down.sql
  ├── 000003_3.1.0.16.up.sql
  ├── 000004_3.1.0.20.down.sql
  └── 000004_3.1.0.20.up.sql
  
  // 1、先从前缀000XX开始排序，再根据版本号进行排序
  // 2、先进行up升级文件执行再执行down文件
  ```



#### panacube-3.XX-.tar

- docker升级文件

