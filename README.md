申请/连接/配置aws linux(redhat)服务器的总结
=
1.申请aws服务器（注册账号-绑定信用卡-选择ec2-配置安全组）
-
2.puttv连接aws
-
###### 1)使用puttygen生成ppk文件
###### 2)使用ppk文件和服务器ipv4来连接服务器
###### 3)使用默认的ec2-user来登录服务器
###### 4)设置root用户密码，切换到root用户
    sudo passwd root
    su root
    
3.搭建nginx
-
###### 1)使用curl下载nginx(.tar.gz)
    curl -L "http://nginx.org/download/nginx-1.13.7.tar.gz" -H "Cookie: oraclelicense=accept-securebackup-cookie"  -H "Connection: keep-alive" -O 
###### 2)解压
    tar -zxvf 文件名
###### 3)编译安装nginx
    make
    make install
###### 4)搭建静态资源访问服务
修改nginx的conf文件夹下的nginx.conf,配置服务<br>
>server {<br>
>>    listen     80;<br>
>>    server_name    res.17xs.org;<br>
>>    charset utf-8;<br>
>>    access_log off;<br>
>>    location / {<br>
>>>  	root  /usr/local/tomcat-7.0.55/webapps/upload;
>>    }<br>
> }<br>
###### 5)启动服务 
    /opt/nginx/sbin/nginx  -c /opt/nginx/conf/nginx.conf 
###### 6)停止服务 
    /opt/nginx/sbin/nginx -s stop 
###### 7)查看端口占用情况 
    netstat -tunlp 
###### 8)如果其它机器无法访问，解决方法如下： 
    /sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT
    然后保存
    /etc/rc.d/init.d/iptables save
    重启防火墙
    /etc/init.d/iptables restart
    
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
    ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password_复杂点的密码';
`注意`：若修改失败，ERROR 1820 (HY000)，修改validate_password_policy参数的值<br>
    set global validate_password_policy=0;<br>
    set global validate_password_length=1;<br>
    在执行修改密码：ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password_复杂点的密码'
###### 9)授权其他机器访问
    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'mypassword' WITH GRANT OPTION;
    刷新权限:FLUSH  PRIVILEGES;

5.卸载mysql
-
###### 1)卸载前关闭MySQL服务，方法1
    check mysql运行状态：service mysql status
    stop mysql：service mysql stop
    check mysql运行状态：service mysql status<
###### 2)卸载前关闭MySQL服务，方法2
    check mysql运行状态：./mysql status
    stop mysql：./mysql stop
    check mysql运行状态：./mysql status
###### 3)检查是否安装了MySQL组件
    rpm -qa | grep -i mysql
    MySQL-devel-5.6.23-1.linux_glibc2.5
    MySQL-client-5.6.23-1.linux_glibc2.5
    MySQL-server-5.6.23-1.linux_glibc2.5
###### 4)卸载删除mysql各类组件
    rpm -ev 组件名
###### 5)收集MySQL对应的文件夹信息
    find / -name mysql
###### 6)删除对应文件夹
    rm -rf mysql路径
###### 7)删除mysql用户及用户组
    more /etc/passwd | grep mysql
    more /etc/shadow | grep mysql
    more /etc/group | grep mysql
    userdel mysql
    groupdel mysql

6.搭建tomcat
-
###### 1)下载tomcat(.tar.gz)
    curl -L "http://mirrors.hust.edu.cn/apache/tomcat/tomcat-8/v8.5.23/bin/apache-tomcat-8.5.23.tar.gz" -H "Cookie: oraclelicense=accept-securebackup-cookie"  -H "Connection: keep-alive" -O
###### 2)解压tomcat,编译安装（make;make install）
###### 3)启动tomcat
    ./startup.sh
###### 4)停止tomcat
    ./shutdown.sh

7.windows下忘记mysql密码，跳过登录进入mysql解决方案
-
###### 1)stop mysql service
    net stop mysql
###### 2)运行cmd,切换到mysql的bin目录，运行命令
    mysqld --defaults-file="C:\Program Files\MySQL\MySQL Server 5.1\my.ini" --console --skip-grant-tables
###### 3)打开第二个cmd2窗口，连接mysql,提示输入密码时，直接回车
    mysql -u root -p
###### 4)查看数据库，切换数据库
    show databases;
    use mysql;
###### 5)更改root密码
    UPDATE user SET Password=PASSWORD('newpassword') where USER='root';
###### 6)刷新权限,退出重新登录
    FLUSH PRIVILEGES;

8.linux里java环境配置
-

9.linux里mysql数据的备份
-


10.java io File的使用
-
###### 1)各种编码占用的字节数
    1.gbk编码中文占用2个字节，英文占用1个字节
    2.utf-8编码中文占用3个字节，英文占用一个字节
    3.utf-16be 编码中文占用2个字节，英文占用2个字节
###### 2)Java文件模型（在硬盘的文件是 byte byte byte存储的，是数据的集合)
###### 3)File API
    1.构造方法：new File("相对路径/绝对路径")，new File("文件目录","文件名")
    2.File.separator(路径分隔符，适用windows/linux)，也可以用"/"或"\\"
    3.判断是否是目录：file.isDirectory()，目录不存在或不是目录，返回false
    4.判断是否是文件：file.isFile()
    5.判断文件是否存在：file.exists()
    6.创建目录或多级目录：file.mkdir()或者file.mkdirs()
    7.删除文件或目录：file.delete()
    8.创建新文件：file.createNewFile()
    9.获取文件名称：file.getName()
    10.获取绝对路径：file.getAbsolutePath()
    11.获取父级绝对路径：file.getParent()
    12.获取文件大小：file.getSize()
    13.获取文件格式名：file.getFormat()
    14.父目录路径：file.getParent()
    15.父目录对象File：file.getParentFile()，file.getParentFile().isDirectory/delete/mkdir/mkdirs/exists...
    16.获取文件长度：file.length()
    17.根据目录获取多文件File[]：file.listFiles()
###### 4)遍历文件示例
> public static void listDirectory(File dir) throws IOException{<br>
>> if(!dir.exists()){<br>
>>> throw new IllegalArgumentException("文件不存在");<br>
>> }<br>
>> if(!dir.isDirectory()){<br>
>>> throw new IllegalArgumentException("目录不存在");<br>
>> }<br>
>> File[] files = dir.listFiles();<br>
>> if(files != null && files.length > 0){<br>
>>> for (File file : files){<br>
>>>> if(file.isDirectory()){<br>
>>>>> //递归<br>
>>>>> listDirectory(file);<br>
>>>> }else{<br>
>>>>> System.out.println(file);<br>
>>>> }<br>
>>> }<br>
>> }<br>
> }<br>
###### 5)RandomAccessFile(提供的对文件内容的访问，既可以读文件，也可以写文件;支持随机访问文件，可以访问文件的任何位置)
    1.打开文件有两种模式 “rw”(读写) 和“r”(只读),RandomAccessFile raf = new RandomAccessFile(file, "rw");
    2.raf.seek(0);//定位指针位置，读取文件初始值是0，从开头读取
    3.写方法raf.write(char)  -->只写一个字节（后8位），同时指针指向下一个位置，准备再次写入
    4.读方法int b = raf.read()  --> 只读一个字节
    5.文件读写完成之后一定要关闭，避免出现一些异常情况
###### 6)RandomAccessFile示例
>>File file = new File(path, fileName);
>>RandomAccessFile randomAccessFile = new RandomAccessFile(file, "rw");
>>//打印指针
>>System.out.println("文件指针初始：" + randomAccessFile.getFilePointer());
>>//写入
>>randomAccessFile.write('b');//每次写入一个字节
>>//randomAccessFile.writeBytes("郑海龙");//用writeBytes写入中文，默认用ansi编码
>>//randomAccessFile.write("萨达".getBytes());默认用项目的编码
    
>>randomAccessFile.seek(0);
>>byte[] buff = new byte[1];
>>randomAccessFile.read(buff);
>>String s1 = new String(buff);
>>System.out.print(s1);

>>randomAccessFile.seek(1);
>>byte[] buff2 = new byte[9];
>>randomAccessFile.read(buff2);
>>String s2 = new String(buff2);
>>System.out.println(s2);
###### 7)InputSteam(抽象了应用程序读取数据的方式)
    EOF = End 读到-1就读到结尾
    int b = in.read();读取一个字节无符号填充到int低八位.-1是EOF
    in.read(byte[] buf) 读取数据填充到字节数组buf
    in.read(byte[] buf,int start, int size)读取数据到字节数组buf从buf的start位置开始存放size长度分数据
###### 8)FileInputSteam(具体实现了在文件上读取数据)
###### 9)FileInputSteam示例
> File file = new File(path, fileName);<br>
> FileInputStream in = new FileInputStream(file);<br>
> byte[] buff = new byte[1024];<br>
> int j = 0;<br>
> int k;<br>
> while ((k = in.read(buff, 0, buff.length)) != -1){<br>
>> for (int i = 0; i < k; i++){<br>
>>> System.out.print(Integer.toHexString(buff[i] & 0xff) + " ");<br>
>> }<br>
>> if(j++ % 10 == 0){<br>
>>> System.out.println();<br>
>> }<br>
> }<br>
###### 10)OutSteam(抽象了应用程序写出数据的方式)
    out.write(int b)写出一个byte到流，b的低8位
    out.write(byte[] buf)将buf字节数组都写到流
    out.write(byte[] buf, int start,int size) 字节数组buf从start位置开始写size长度的字节到流
###### 11)FileOutSteam(实现了在文件上写byte数据的方法)
    FlieOutputStream out = new FileOutputStream("文件名");创建文件
    FlieOutputStream out = new FileOutputStream("文件名",true);文件存在则追加
###### 12)FileOutSteam示例
>public static void d4(String path, String fileName) throws IOException{
>>File file = new File(path, fileName);
>>FileInputStream fileInputStream = new FileInputStream(file);
>>FileOutputStream fileOutputStream = new FileOutputStream(new File(path, "d2.txt"));
>>byte[] buff = new byte[1024];
>>int by;

>>/*while ((by = fileInputStream.read(buff, 0, buff.length)) != -1){//一个字节一个字节的读取System.out.println((char)by);}*/
>>while ((by = fileInputStream.read(buff, 0, buff.length)) != -1){//批量读取
>>>fileOutputStream.write(buff, 0, by);
>>>fileOutputStream.flush();
>>}
>>fileOutputStream.close();
>>fileInputStream.close();
>}
###### 13)DataInputSteam/DataOutputSteam
    DataIntputStream对“流”功能的扩展，可以更加方便的读取 int,long，字符等类型数据，readInt readLong 方法都是对FileInputStream方法的包装
    DataOutputStream：使用FileOutputStream构造出来，通过包装FileOutput，可以调用FileOutput类的write方法来构造新的更方便的写方法：
    new DataOutputStream(new FileOutptStream(file))
    wrieteUTF()采用utf-8编码写出字符串
    用utf-16be写出字符串，或字符串数组
###### 13)DataInputSteam/DataOutputSteam示例
>public static void piliang_data_copy(String path, String fileName) throws IOException{
>>long startTimes = System.currentTimeMillis();
>>File file = new File(path, fileName);
>>DataInputStream dataInputStream = new DataInputStream(new FileInputStream(file));
>>DataOutputStream dataOutputStream = new DataOutputStream(
>>new FileOutputStream(new File(path,"15.rar")));
>>byte[] buff = new byte[1024];
>>int by;
>>while ((by = dataInputStream.read(buff, 0, buff.length)) != -1){
>>>dataOutputStream.write(buff, 0, by);
>>>dataOutputStream.flush();
>>}
>>dataOutputStream.close();
>>dataInputStream.close();
>>long endTimes = System.currentTimeMillis();
>>System.out.println("DataInputStream 批量读取：" + (endTimes - startTimes));
>}
###### 14)BufferedInputSteam/BufferedOutputSteam示例
>public static void piliang_buffered_copy(String path, String fileName) throws IOException{
>>long startTimes = System.currentTimeMillis();
>>File file = new File(path, fileName);
>>BufferedInputStream bufferedInputStream = new BufferedInputStream( new FileInputStream(file));
>>BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(
>>new FileOutputStream(new File(path,"16.rar")));
>>byte[] buff = new byte[1024];
>>int by;
>>while ((by = bufferedInputStream.read(buff, 0, buff.length)) != -1){
>>>bufferedOutputStream.write(buff, 0, by);
>>>bufferedOutputStream.flush();
>>}
>>bufferedOutputStream.close();
>>bufferedInputStream.close();
>>long endTimes = System.currentTimeMillis();
>>System.out.println("BufferedInputStream 批量读取：" + (endTimes - startTimes));
>}
###### 15)字节流的整体效率：FileOut/FileInt < DataOut/DataInt < BufferedOut/BufferedInt，文件执行完毕要关闭流，在finally里面关，不然抛出异常就没法关闭了
###### 16)字符流
    字符的处理，一次处理一个字符
    字符的底层依然是基本的字节序列
    InputStreamReader isr=new InputStreamReader(in，"编码格式");
    InputStreamReader isr=new InputStreamReader(in);//默认编译器编码
    InputStreamReader  完成byte流解析为char流，按照编码解析
    OutputStreamWriter 提供char流到byte流，按照编码处理
###### 17)InputStreamreader/OutputStreamWrite
11.java nio与io
-


