#### 创建本地分之并切换

```bash
git checkout -b dev
```



#### 添加远程分支

```bash
git remote add origin http://github.com/xxx/document.git
```



#### 推送本地分支提交到远程分支

```bash
# 先拉取最新内容
git pull origin master
# 如果出现 fatal: 拒绝合并无关的历史
git pull origin master --allow-unrelated-histories  # 强制拉取然后解决冲突
git push --set-upstream origin master  # 后面就可以直接使用git pull或者git push

git branch -m main master
git fetch origin
git branch -u origin/master master
git remote set-head origin -a
```



- 删除远程分支

  ```shell
  git push origin --delete [branch]
  ```

  

- 新代码指向远程分支

  ```shell
  git remote add origin [URL]
  git push --set-upstream origin [远程分支名称]
  ```

  

- 删除远程分支文件，本地保留

  ```shell
  git rm -r --cached [文件名或者文件夹名]
  ```

  