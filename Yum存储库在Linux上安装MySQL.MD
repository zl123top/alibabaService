# 2.5.1使用MySQL Yum存储库在Linux上安装MySQL
适用于Oracle Linux，Red Hat Enterprise Linux，CentOS和Fedora 的MySQL Yum存储库提供用于安装MySQL服务器，客户端，MySQL Workbench，MySQL Utilities，MySQL路由器，MySQL Shell，Connector / ODBC，Connector / Python等的RPM软件包。 （并非所有软件包都可用于所有发行版; 有关详细信息，请参阅 使用Yum安装其他MySQL产品和组件）。

在你开始之前
作为一种流行的开源软件，MySQL以其原始或重新打包的形式广泛安装在许多系统上，来自各种来源，包括不同的软件下载站点，软件存储库等。以下说明假定您的系统上尚未使用第三方分发的RPM软件包安装MySQL; 如果不是这种情况，请按照第2.11.1.6节“使用MySQL Yum存储库升级MySQL”或 使用MySQL Yum存储库替换MySQL 的第三方分发中给出的说明进行操作 。

# 全新安装MySQL的步骤
按照以下步骤使用MySQL Yum存储库安装最新的GA版本的MySQL：

# 添加MySQL Yum存储库
首先，将MySQL Yum存储库添加到系统的存储库列表中。这是一次性操作，可以通过安装MySQL提供的RPM来执行。跟着这些步骤：

转到MySQL Developer Zone中的下载MySQL Yum存储库页面（https://dev.mysql.com/downloads/repo/yum/）。

选择并下载适用于您的平台的发行包。

使用以下命令安装下载的发行包，替换 platform-and-version-specific-package-name 为下载的RPM包的名称：
```
shell> sudo yum localinstall platform-and-version-specific-package-name.rpm
对于基于EL6的系统，该命令的形式为：

shell> sudo yum localinstall mysql80-community-release-el6-{version-number}.noarch.rpm
对于基于EL7的系统：

shell> sudo yum localinstall mysql80-community-release-el7-{version-number}.noarch.rpm
对于Fedora 29：

shell> sudo dnf localinstall mysql80-community-release-fc29-{version-number}.noarch.rpm
对于Fedora 28：

shell> sudo dnf localinstall mysql80-community-release-fc28-{version-number}.noarch.rpm
对于Fedora 27：

shell> sudo dnf localinstall mysql80-community-release-fc27-{version-number}.noarch.rpm
```
安装命令将MySQL Yum存储库添加到系统的存储库列表中，并下载GnuPG密钥以检查软件包的完整性。有关GnuPG密钥检查的详细信息，请参见 第2.1.3.2节“使用GnuPG进行签名检查”。

您可以通过以下命令检查MySQL Yum存储库是否已成功添加（对于启用dnf的系统，使用dnf替换命令中的yum）：

```shell> yum repolist enabled | grep "mysql.*-community.*"```
注意
一旦在您的系统上启用了MySQL Yum存储库，yum update 命令的任何系统范围更新（或启用dnf的系统的dnf升级）都将升级系统上的MySQL软件包，并替换任何本机第三方软件包，如果Yum在MySQL Yum存储库中找到它们的替换; 请参见 第2.11.1.6节“使用MySQL Yum存储库升级MySQL”，有关对系统可能产生的影响的讨论，请参阅 升级共享客户端库。

# 选择发布系列
使用MySQL Yum存储库时，默认情况下会选择安装最新的GA系列（目前是MySQL 8.0）。如果这是您想要的，您可以跳到下一步， 安装MySQL。

在MySQL Yum存储库中，MySQL社区服务器的不同发行版系列托管在不同的子存储库中。默认情况下启用最新GA系列（当前为MySQL 8.0）的子存储库，默认情况下禁用所有其他系列（例如，MySQL 8.0系列）的子存储库。使用此命令查看MySQL Yum存储库中的所有子存储库，并查看哪些子存储库已启用或禁用（对于支持dnf的系统，请使用dnf替换命令中的 yum）：

```shell> yum repolist all | grep mysql```
要从最新的GA系列安装最新版本，无需进行任何配置。要从最新GA系列以外的特定系列安装最新版本，请在运行安装命令之前禁用最新GA系列的子存储库并启用特定系列的子存储库。如果您的平台支持 yum-config-manager，您可以通过发出这些命令来执行此操作，这些命令禁用5.7系列的子存储库并启用8.0系列的子存储库：
```
shell> sudo yum-config-manager --disable mysql57-community
shell> sudo yum-config-manager --enable mysql80-community
```
对于支持dnf的平台：
```
shell> sudo dnf config-manager --disable mysql57-community
shell> sudo dnf config-manager --enable mysql80-community
```
除了使用yum-config-manager或 dnf config-manager命令外，您还可以通过手动编辑/etc/yum.repos.d/mysql-community.repo 文件来选择版本系列 。这是文件中发布系列的子存储库的典型条目：

[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/6/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
找到要配置的子存储库的条目，然后编辑该enabled选项。指定 enabled=0禁用子存储库，或 enabled=1启用子存储库。例如，要安装MySQL 8.0，请确保您具有 enabled=0MySQL 5.7的上述子存储库条目，并且具有 enabled=18.0系列的条目：

#Enable to use MySQL 8.0
[mysql80-community]
name=MySQL 8.0 Community Server
baseurl=http://repo.mysql.com/yum/mysql-8.0-community/el/6/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
您应该只在任何时候为一个发布系列启用子存储库。当启用多个版本系列的子存储库时，Yum将使用最新的系列。

通过运行以下命令并检查其输出来验证是否已启用和禁用了正确的子存储库（对于启用dnf的系统，请使用dnf替换 命令中的 yum）：
```
shell> yum repolist enabled | grep mysql
```
# 安装MySQL
通过以下命令安装MySQL（对于支持dnf的系统，使用dnf替换命令中的 yum）：
```
shell> sudo yum install mysql-community-server
```
这将安装MySQL server（mysql-community-server）的包以及运行服务器所需组件的包，包括client（mysql-community-client）的包，客户端和服务器的常见错误消息和字符集（mysql-community-common）以及共享客户端库（mysql-community-libs） 。

# 启动MySQL服务器
使用以下命令启动MySQL服务器：
```
shell> sudo service mysqld start
Starting mysqld:[ OK ]
```
您可以使用以下命令检查MySQL服务器的状态：
```
shell> sudo service mysqld status
mysqld (pid 3066) is running.
```
在服务器初始启动时，如果服务器的数据目录为空，则会发生以下情况：

服务器已初始化。

SSL证书和密钥文件在数据目录中生成。

validate_password 已安装并已启用。

将'root'@'localhost创建一个超级用户帐户。设置超级用户的密码并将其存储在错误日志文件中。要显示它，请使用以下命令：

```shell> sudo grep 'temporary password' /var/log/mysqld.log```
通过使用生成的临时密码登录并为超级用户帐户设置自定义密码，尽快更改root密码：
```
shell> mysql -uroot -p
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```
注意
validate_password 默认安装。实现的默认密码策略validate_password要求密码包含至少一个大写字母，一个小写字母，一个数字和一个特殊字符，并且密码总长度至少为8个字符。

有关安装后过程的更多信息，请参见 第2.10节“安装后设置和测试”。

注意
基于EL7的平台的兼容性信息：来自平台的本机软件存储库的以下RPM软件包与安装MySQL服务器的MySQL Yum存储库中的软件包不兼容。使用MySQL Yum存储库安装MySQL后，您将无法安装这些软件包（反之亦然）。

Akonadi的MySQL的
