[实用教程](http://edu.cnzz.cn/s/201510/231594.shtml)


# linux下apache的安装配置

- 下载安装
从http://httpd.apache.org/上下载httpd-2.2.6.tar.gz，上传到linux主机，然后开始安装。
解压tar -zxvf httpd-2.2.6.tar.gz,完成之后，会在当前目录出现一个httpd-2.2.6目录，然后顺序执行如下命令
mv httpd-2.2.6 apache
cd apache
设置安装参数,命令如下:
./configure --prefix=/usr/local/apache2 --enable-module=so
其中prefix参数指明将apache安装到/usr/local/apache2目录，如果登录用户不是root,可能没有权限在/usr/local下创建目录的权限，这样在make install时会报错误。解决的办法有两个，1）修改prefix参数，使之指向一个登录用户有创建目录权限的路径；2）用root用户在/usr/local目录下创建apache2目录，然后将apache2的wrx权限授权给登录用户。
最后编译，安装;顺序执行如下命令:
make
make install
总之，安装顺序执行的命令序列如下，如果都没有出错，表示安装成功。
tar -zxvf httpd-2.2.6.tar.gz
mv httpd-2.2.6 apache
cd apache
./configure --prefix=/usr/local/apache2 --enable-module=so
make
make install

- apache主要目录和文件说明
主配置文件位置/etc/httpd/conf/*或者/usr/local/apache2/conf/httpd.conf
启动脚本/etc/rc.d/init.d/httpd 
格式的帮助文档/usr/local/apache2/manual/*html
用在html网页中的图标文件/usr/local/apache2/icons/*
建立和更新apache用户的程序/usr/local/apache2/htpasswd
http服务器程序/usr/sbin/httpd
日志文件/usr/local/apache2/logs

- 启动配置
修改PATH环境变量，在登录用户的home目录打开.bash_profile，在文件中添加一行
PATH=$PATH:/usr/local/apache2/bin
然后退出重新登录，执行apachectl -l，如果有返回信息，说明路径设置成功。
apache的配置文件是/usr/local/apache2/conf/httpd.conf

由于apache的默认监听端口是80，linux/unix上，小于等于1024范围内的端口只有root用户才有权限打开，所以为了用非root用户启动apache,必须修改默认端口,打开httpd.conf,修改Listen属性(或者Port)为8000。
运行命令apachectl start，如果没有出现错误信息（如果有错误信息，根据错误信息纠正错误），打开ie,在地址栏输入http://ip:8000（这里的ip是apache的安装主机的ip地址），如果有能显示页面，表明apache启动成功.

- 参数说明
ServerType standlone/inetd 服务器的运行方式
Port 80 或者Listen 80 在standlone方式下监听的端口号
User nobody 进程运行的属主
Group nobody 进程运行的属组
ServerAdmin admin@localhost 管理员信箱
ServerRoot "/home/httpd/" 服务器文件位置
ServerName httpserver服务器名称
Timeout 300设置服务器和客户端的连接超时时间
MaxClient 300允许同时连接的客户数
ProxyRequests on允许充当proxy
DocumentRoot "/home/httpd/html"提供服务器文档服务的根目录
UserDir public_html服务器上用户自己主页所在目录
DirectoryIndex index.html目录索引文件
AccessFileName .htaccess在每个目录中包含访问控制信息的文件名
Alias /icons/ "/home/user/icons/"为不在DocumentRoot之下的文档建立别名
<Directory></Directory>目录设置,在中间可以加入一些目录参数,示例如下：
<Directory "/">
Options Indexes
AllowOverride None
order allow,deny
allow from all
</Directory>

在上面的目录设置中,Options选项配置指令,后面的参数用于控制目录的访问特性
ExecCGI:可以执行cgi脚本
Indexes:访问一个无索引文件的目录时，返回一个文件 列表
AllowOverride指令 ".htaccess"文件中哪些设置允许覆盖先前的设置
None:不读取.htaccess
Limit:覆盖控制主机访问的指令(allow,deny)
AuthConfig:允许覆盖跟认证有关指令 (AuthType,AuthName)
这儿是None

order指令
控制处理allow和deny指令的顺序
如上例:
order allow,deny
deny from all
allow from host11

allow指令
对于一个目录来说，allow指令设置允许哪些主机访问
all:所有主机
或 域名:eloo.com
或 一个完整IP:192.168.0.1
网络号码/子网掩码:192.168.0.0/255.255.255.0
网络号码/nnn:192.168.0.0/24 表示一段范围的ip地址

deny指令
对于一个目录来说，allow指令设置禁止哪些主机访问
-all:所有主机
或 域名:eloo.com
或 一个完整IP:192.168.0.1
网络号码/子网掩码:192.168.0.0/255.255.255.0
网络号码/nnn:192.168.0.0/24 表示一段范围的ip地址

- 结束 
不同版本的apache，在目录和参数名字上有微小的差异，根据名字一般都能猜出来。
