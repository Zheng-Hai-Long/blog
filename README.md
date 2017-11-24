申请及连接aws服务器的总结
=
1.申请aws服务器（注册账号-绑定信用卡-选择ec2-配置安全组）
-
2.puttv连接aws
-
###### 1)使用puttygen生成ppk文件
###### 2)使用ppk文件和服务器ipv4来连接服务器
###### 3)使用默认的ec2-user来登录服务器
###### 4)创建root用户

3.搭建nginx
-
###### 1)使用curl下载nginx(.tar.gz)
                curl -L "http://nginx.org/download/nginx-1.13.7.tar.gz" -H "Cookie: oraclelicense=accept-securebackup-cookie"  -H "Connection: keep-alive" -O 
###### 2)解压 tar -zxvf 文件名
###### 3)编译安装nginx <1:make  <2:make install
###### 4)搭建静态资源访问服务
修改nginx的conf文件夹下的nginx.conf,配置服务<br>
server {\<br>
        listen     80;<br>
        server_name    res.17xs.org;<br>
        charset utf-8;<br>
        access_log off;<br>
        location / {<br>
        	root  /usr/local/tomcat-7.0.55/webapps/upload;<br>
        }<br>
    }\<br>
###### 5 )启动服务 
/opt/nginx/sbin/nginx  -c /opt/nginx/conf/nginx.conf 
###### 6)停止服务 
/opt/nginx/sbin/nginx -s stop 
###### 7)查看端口占用情况 
netstat -tunlp 
###### 8)如果其它机器无法访问，解决方法如下： 
/sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT <br>
然后保存： \<br>
/etc/rc.d/init.d/iptables save <br>
重启防火墙 \<br>
/etc/init.d/iptables restart<br>
    
4.搭建mysql
-
###### 1)下载mysql(.rpm)
curl -L "https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm" -H "Cookie: oraclelicense=accept-securebackup-cookie"  -H "Connection: keep-alive" -O
###### 2)下载 mysql57-community-release-el7-10.noarch.rpm
###### 3)添加Mysql Yum Repository
sudo rpm -Uvh mysql57-community-release-el7-10.noarch.rpm
###### 4)rpm 安装mysql
sudo yum install mysql-community-server-5.7.18-1.el7.x86_64.rpm
###### 5)启动mysql
sudo service mysqld start
###### 6)登录mysql
mysql -uroot -p
###### 7)5.7版本是随机生成root密码，使用查看日志找到密码
sudo grep 'temporary password' /var/log/mysqld.log 
###### 8)修改root密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password_复杂点的密码';<br>
注意：若修改失败，ERROR 1820 (HY000)，修改validate_password_policy参数的值<br>
set global validate_password_policy=0;\<br>
set global validate_password_length=1;\<br>
在执行修改密码：ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password_复杂点的密码'<br>
###### 9)授权其他机器访问
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'mypassword' WITH GRANT OPTION;<br>
刷新权限<br>
FLUSH  PRIVILEGES;<br>

5.搭建tomcat
-
###### 1)下载tomcat(.tar.gz)
curl -L "http://mirrors.hust.edu.cn/apache/tomcat/tomcat-8/v8.5.23/bin/apache-tomcat-8.5.23.tar.gz" -H "Cookie: oraclelicense=accept-securebackup-cookie"  -H "Connection: keep-alive" -O
###### 2)解压tomcat,编译安装（make;make install）
###### 3)启动tomcat
tomcat bin目录下 ./startup.sh
###### 4)停止tomcat
./shutdown.sh
