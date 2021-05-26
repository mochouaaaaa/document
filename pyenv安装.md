



### Pyenv wiki


- `https://github.com/pyenv/pyenv/wiki`



### zshrc下找不到pip、python的问题,这个异常没有更新到Github文档上面

#### Pyenv path manipulations need to be placed in ~/.zprofile for zsh user

- `https://github.com/pyenv/pyenv/issues/1913`



#### Python依赖安装

- `https://github.com/pyenv/pyenv/wiki#suggested-build-environment`

**archlinux 用户**

```
sudo pacman -S base-devel openssl zlib
```

**mac 用户**

```
brew install openssl readline sqlite3 xz zlib
```

**Ubuntu/Debian/Mint 用户**

```
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev
```

**CentOS/Fedora <= 21 用户，请你保证已经安装了 xz 工具**

```
sudo yum install gcc zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel tk-devel libffi-devel
```

**Fedora >= 22 用户，请你保证已经安装了 xz 工具**

```
sudo dnf install -y gcc zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel tk-devel
```

**openSUSE 用户**

```
zypper install gcc automake openssl-devel ncurses-devel readline-devel zlib-devel tk-devel
```

**Solus 用户**

```
sudo eopkg it -c system.devel
sudo eopkg install git gcc make zlib-devel bzip2-devel readline-devel sqlite3-devel openssl-devel tk-devel
```

**Linuxbrew 用户**

```
brew install bzip2 openssl readline sqlite xz
```

**lpine Linux:**

  ```
  apk add --no-cache git bash build-base libffi-dev openssl-dev bzip2-dev zlib-dev readline-dev sqlite-dev 
  ```

Installation of Python 3.7 may fail due to Python 3.7.0 issue [#34555](https://bugs.python.org/issue34555). A workaround is to install the [linux system headers package](https://pkgs.alpinelinux.org/packages?name=linux-headers&branch=edge):

```
apk add linux-headers 
```

**Void Linux:**

  ```
  xbps-install base-devel bzip2-devel openssl openssl-devel readline readline-devel sqlite-devel xz zlib zlib-devel
  ```

See also [Common build problems](https://github.com/pyenv/pyenv/wiki/Common-build-problems) for further information.





#### Pyenv 安装

```shell
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n eval "$(pyenv init -)"\nfi' >> ~/.zshrc
source $HOME/.zprofile
cat ~/.zprofile <<EOF
# added content to ~/.zprofile
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"
EOF
exec $SHELL

git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.zshrc
```





#### Pyenv使用

- 安装 [先看wiki参数及错误出现的问题]

  ```shel
  env PYTHON_CONFIGURE_OPTS="--enable-shared" pyenv install 3.8.3
  ```

  
