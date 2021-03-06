# 编译升级OpenSSH
```
CentOS 7
OpenSSH 7.4
下载：
https://www.openssh.com/portable.html#http
https://www.openssh.com/portable.html#ftp
https://www.openssh.com/portable.html#rsync
安装参考：
https://ftp.openbsd.org/pub/OpenBSD/OpenSSH/portable/INSTALL
```
## 安装dropbear并启动
`
Dropbear is a relatively small SSH server and client. It runs on a variety of POSIX-based platforms. Dropbear is open source software, distributed under a MIT-style license. Dropbear is particularly useful for "embedded"-type Linux (or other Unix) systems, such as wireless routers.
https://matt.ucc.asn.au/dropbear/dropbear.html
`
```
yum install dropbear
firewalld add dropbear port(default 22)
systemctl enable dropbear
systemctl start dropbear
# 安装各种依赖
yum install glibc-devel glibc-headers gcc gcc-c++ zlib zlib-devel pam-devel pam bzip2 kernel-headers openssl-devel
```
## 编译安装dropbear
```
https://matt.ucc.asn.au/dropbear/dropbear.html

./configure --prefix=/usr/local/dropbear/ && make -j $(nproc) && sudo make install

sudo mkdir /etc/dropbear/ && \
sudo /usr/local/dropbear/bin/dropbearkey -t dss -f /etc/dropbear/dropbear_dss_host_key && \
sudo /usr/local/dropbear/bin/dropbearkey -t rsa -f /etc/dropbear/dropbear_rsa_host_key && \
sudo /usr/local/dropbear/bin/dropbearkey -t ecdsa -f /etc/dropbear/dropbear_ecdsa_host_key && \
sudo /usr/local/dropbear/bin/dropbearkey -t ed25519 -f /etc/dropbear/dropbear_ed25519_host_key && \
sudo /usr/local/dropbear/sbin/dropbear -p :12321
```
## 编译安装OpenSSH
```
./configure --prefix=/usr  --sysconfdir=/etc/ssh  --with-md5-passwords  --with-pam  --with-zlib --with-tcp-wrappers --without-hardening --with-ssl-dir=/usr/local/openssl

make -j $(nproc)
#备份之前的配置文件
mv /etc/ssh /etc/ssh-bak
#移除之前yum安装的OpenSSH,（如果有），主要是解决systemd和service会冲突
yum remove openssh
sudo make install
#不移除，可以mask之前的sshd服务
systemctl stop sshd && systemctl disable sshd && systemctl mask sshd

cp openssh-src-path/contrib/redhat/sshd.init /etc/init.d/sshd
chkconfig --add sshd
```

## 修改配置文件 启动sshd 测试链接

## 选择卸载dropbear或者保留
