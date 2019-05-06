### linux 安装vsftpd
1. 运行命令`yum install -y vsftpd`安装 vsftpd。

2. 运行以下命令设置FTP服务开机自启动。
`systemctl enable vsftpd.service`

3. 运行以下命令启动 FTP 服务。
`systemctl start vsftpd.service`

4. 运行以下命令查看 FTP 服务监听的端口。
```netstat -antup | grep ftp```

5. 修改/etc/vsftpd/vsftpd.conf。
运行vim /etc/vsftpd/vsftpd.conf。
按i 键进入编辑模式。
将写权限修改为write_enable=YES。
将匿名上传权限修改为anon_upload_enable=YES。
按Esc键退出编辑模式，然后输入:wq保存并退出文件。

6. 运行以下命令更改/var/ftp/pub目录的权限，为 FTP 用户添加写权限，并重新加载配置文件。
```
chmod o+w /var/ftp/pub/
systemctl restart vsftpd.service
```

### 配置本地用户登录

运行以下命令为FTP服务创建一个Linux用户。本示例中，该用户名为ftptest。
`useradd ftptest`
运行以下命令修改ftptest 用户的密码。
`passwd ftptest`
创建一个供FTP服务使用的文件目录。
`mkdir /var/ftp/test`
更改/var/ftp/test目录的拥有者为ftptest。
`chown -R ftptest:ftptest /var/ftp/test`
输入命令vim /etc/vsftpd/vsftpd.conf打开vsftpd.conf配置文件并按键i进入编辑模式。
修改vsftpd.conf配置文件。
FTP服务器可以配置为主动模式或被动模式。

主动模式下，客户端向服务端发送数据端口的信息，由服务端主动连接客户端发送的数据端口。配置FTP为主动模式的参数如下：
```
#禁止匿名登录FTP服务器
anonymous_enable=NO
#允许本地用户登录FTP服务器
local_enable=YES
#设置本地用户登录后所在的目录
local_root=/var/ftp/test
#全部用户被限制在主目录
chroot_local_user=YES
#启用例外用户名单
chroot_list_enable=YES
#指定例外用户列表，这些用户不被锁定在主目录
chroot_list_file=/etc/vsftpd/chroot_list

#配置其他参数
allow_writeable_chroot=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
listen=YES
pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES
```


被动模式下，服务端开启并发送数据端口的信息给客户端，由客户端连接服务端开启的数据端口，服务端被动接受连接。在被动模式下，您需要配置服务端可以开启的数据端口范围。配置FTP为被动模式的参数如下：
```
#禁止匿名登录FTP服务器
anonymous_enable=NO
#允许本地用户登录FTP服务器
local_enable=YES
#设置本地用户登录后所在目录
local_root=/var/ftp/test
#全部用户被限制在主目录
chroot_local_user=YES
#启用例外用户名单
chroot_list_enable=YES
#指定例外用户列表，这些用户不被锁定在主目录
chroot_list_file=/etc/vsftpd/chroot_list
#开启被动模式
pasv_enable=YES
#FTP服务器公网IP
pasv_address=<FTP服务器公网IP>
#设置被动模式下，建立数据传输可使用port范围的最小值
pasv_min_port=50000
#设置被动模式下，建立数据传输可使用port范围的最大值
pasv_max_port=50010

#配置其他参数
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
xferlog_std_format=YES
tcp_wrappers=YES
allow_writeable_chroot=YES
listen=YES
listen_ipv6=NO
pam_service_name=vsftpd
userlist_enable=YES
说明 建议您把端口范围设在比较高的一段范围内，比如50000-50010，有助于提高访问FTP服务器的安全性。
按Esc键退出编辑模式，然后按键:wq保存并退出文件。
运行vim /etc/vsftpd/chroot_list命令创建chroot_list文件，并写入不受只可以访问其主目录限制的例外用户名单。
说明 如果没有例外用户也必须要有 chroot_list文件，内容可为空。
按键Esc退出编辑模式，然后按键:wq保存并退出文件。
运行以下命令重启vsftpd服务。
systemctl restart vsftpd.service
vsftpd.conf 的配置文件参数说明
运行命令cat /etc/vsftpd/vsftpd.conf查看配置文件内容。
```

### 设置安全组
...

### 连接出现的问题
1. 提示vsftpd 读取目录列表失败
解决：端口没放开，设置安全组

2. 500 Illegal PORT command的问题（主被动模式）
解决：客户端默认是被动模式，改成主动去连接

3. ftp服务器发回了不可路由的地址
解决： 配置文件vsftpd.conf里ipv6没有设置成NO

4. 上传失败553
解决： 可能是权限问题，`chown -R zl123:zl123 /www` 比如把www目录设置成用户zl123权限



