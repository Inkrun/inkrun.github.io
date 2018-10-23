# 记一次在 CentOS7 安装Python 3.7

## 按官方地址下载源码
下载官方最新 3.7 源码包
```bash
wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tar.xz
```

## 下载后进行解压
两种解压方式：
- 下载后的文件是 `xz` 格式，可以使用 `xz -d Python-3.7.0.tar.xz` 解压成 `Python-3.7.0.tar` 再使用 `tar -xvf Python-3.7.0.tar` 解压出 `Python-3.7` 文件夹
- 直接使用 `tar` 命令 `tar -xvJf Python-3.7.0.tar.xz`  进行操作，解压出 `Python-3.7` 文件夹

## 编译环境准备
先进行开发者工具安装：
```bash
yum -y groupinstall "Development tools"
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel  libffi-devel
```
第一条命令会安装 55 个模块，第二条命令特殊说明一下，单独添加了一个 `libffi-devel` ，3.7版本需要一个新的包`libffi-devel`，如果没有安装此包，最后进行源码安装时会提示 `ModuleNotFoundError: No module named '_ctypes'`
> 若已经出现了错误，并且在安装前移除了/usr/bin下python的文件链接依赖，此时yum无法正常使用，需要自己下载相关软件包安装，为节省读者时间，放上链接：
```bash
wget http://mirror.centos.org/centos/7/os/x86_64/Packages/libffi-devel-3.0.13-18.el7.x86_64.rpm
rpm -ivh libffi-devel-3.0.13-18.el7.x86_64.rpm
```
> 安装完成后重新进行make install，结束后再次配置相关文件的软连接即可。


## 另一个坑 `openssl`
CentOS 自带的 openssl 版本比较低，我在 CentOS7 上查到的版本还是 2013 年的版本，所以需要升级 OpenSSL 版本，不然pip3 安装 module 时会提示 `pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.` 导致无法安装模块，下面进行 openssl 升级.

1. 下载最新 openssl
和上面一样进行
```bash
wget https://www.openssl.org/source/openssl-1.1.1-pre8.tar.gz
```
2. 解压
```bash
tar -zxvf openssl-1.1.1-pre8.tar.gz
```
3. 编译安装
```bash
cd openssl-1.1.1-pre8
./config --prefix=/usr/local/openssl no-zlib #不需要zlib
# make && make install
```
4. 备份原配置
```bash
mv /usr/bin/openssl /usr/bin/openssl.bak
mv /usr/include/openssl/ /usr/include/openssl.bak
```
5. 新版本配置
```bash
#将安装好的openssl 的openssl命令软链到/usr/bin/openssl
ln -s /usr/local/openssl/include/openssl /usr/include/openssl
#软链到升级后的libssl.so
ln -s /usr/local/openssl/lib/libssl.so.1.1 /usr/local/lib64/libssl.so
#将安装好的openssl命令软连到/usr/bin/openssl
ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl
```
6. 修改系统配置
```bash
#写入openssl库文件的搜索路径
echo "/usr/local/openssl/lib" >> /etc/ld.so.conf
#使修改后的/etc/ld.so.conf生效 
ldconfig -v
```

7. 查看 `openssl` 版本
```bash
openssl version
# OpenSSL 1.1.1-pre8 (beta) 20 Jun 2018 -- 即安装成功
```

## 终于要安装 Python37 了
进入刚才解压的源码文件夹：`Python-3.7.0` 执行命令
```bash
./configure --prefix=/usr/local/python3 --with-openssl=/usr/local/openssl
make && make install
```

## 最后一步
配置可访问的 `python3`，因为本人不希望更改 CentOS 原生的 python2 的运行环境以及 `yum` 的配置，所以只使用 python3 来创建虚拟运行环境，因此只执行： `ln -s /usr/local/python/bin/python3 /usr/bin/python3` 即可