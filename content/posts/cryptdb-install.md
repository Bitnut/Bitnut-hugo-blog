---
title: "cryptdb 安装及使用说明"
date: 2017-08-07T20:44:51+08:00
categories:
  - 专题
tags:
  - "cryptdb"
  - "database"
---

>版权声明：本文为博主原创文章，欢迎转载；转载请注明来自 *瓜哥*：D

## 博主写的 CryptDB 另外几篇相关文章：

[CryptDB 简单原理论述](https://bitnut.github.io/posts/cryptdb1/)

[详细论述 CryptDB 的原理](https://bitnut.github.io/posts/cryptdb2/)

*这个暑假的数据库实训内容，研究的项目背景下面会介绍～*

**背景资料**

Popa, R.A 在 MIT 攻读博士时发明了 CryptDB;
后来这个数据库方案迅速被微软和谷歌等知名企业采用和学习;
作者毕业后自己创立了公司PreVeil;
同时也是Assistantprofessor (讲师), UC Berkeley;

###### *想要理解CryptDB, 你可能需要阅读如下资料*：

CryptDB.	 Popa, R. A., et al. (2011). CryptDB: protecting confidentiality	with encrypted query processing. [文章链接](http://web.cs.ucdavis.edu/~franklin/ecs228/2013/popa_etal_sosp_2011.pdf)
Guidelines	for Using the CryptDB System Securely [链接](https://eprint.iacr.org/2015/979)

###### 其他可能有用的资源:

CryptDB
* [项目主页:](http://css.csail.mit.edu/cryptdb/) 有软件的下载和使用介绍。
* [github主页:](https://github.com/CryptDB/cryptdb)这个比较方便，建议看这个！
* [发明人主页:](https://people.eecs.berkeley.edu/~raluca/)建议～

## 本文会详细介绍CryptDB的安装过程，以及它的使用方法
-----------------------------------------
#### 准备阶段
#### GitHub 上官方 readme 截取的提示文字
Convention : When this document uses syntax like single-quote text single-quote + 'some-text-here' + 'a second example' it indicates that the reader is to use the value without the single-quotes.

意思是 readme 中用到【文字+文字+文字】的段落只要理解单引号中的文字就好。


######  Getting started

* Requirements:
    > Ubuntu 12.04, 13.04; Not tested on a different OS.
    >
    > ruby
* Build CryptDB using the installation script.
    > note, this script will stop and start your mysql instance.
    >
    > scripts/install.rb + should just be '.' if you are in the cryptdb directory + the script will likely require root privileges in order to install dependencies and UDFs.
* Username/Password By default cryptdb uses 'root' for the username and 'letmein' for the password. You can change this by modifying the source. + Tests: test/test_utils.hh + Shell: main/cdb_test.cc + Proxy: mysqlproxy/wrapper.lua
* Rebuildling CryptDB If you modify the source and want to rebuild, issue 'make' in the cryptdb directory. If you change the UDFs you will also need to do 'make install' (which will likely require root privilege).


#### 开始安装

 1. 请安装 Ubuntu 12.04, 13.04。本文只在 *12.04* 上运行所有的指令和测试！(下面给出镜像地址)    http://releases.ubuntu.com/12.04/

		####*磁盘空间不要分配的太少！默认的 8G 肯定是不够的！不然 Linux 会炸.我分配了 20G*

 2. 鉴于文中提到的 root 权限会被使用频繁。进入操作界面之后先设置好设置 root 权限：

     命令：
               1. 设置 root：`sudo passwd root`
	       2. 切换至 root：`su`
 3. 下载 git
 		命令：`apt-get install git`
__注__： *apt-get 下载失败的问题几乎都可以通过 update 和 upgrade 解决。所以如果 apt-get 出错，请不要烦恼~ 小问题而已！*
 4. 安装 ruby
		命令：`apt-get install ruby-full`

 5. clone 源码
	 	命令：git clone <https://github.com/CryptDB/cryptdb>

 6. 运行脚本
 		1. 切换至 cryptdb 文件夹
 		2. ./scripts/install.rb ~/cryptdb  （后面的参数是cryptdb的安装路径，注意看清楚，这里是默认的home目录下）

至此安装结束

* 成功的样子：
![InstallGood.jpg](http://upload-images.jianshu.io/upload_images/7277397-45fccac3c1b02317.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 修改一些配置

 1. 安装 vim 编辑器
 		命令：`apt-get install vim`
 2. 打开 wrapper.lua 进行修改
 		命令：`vim /你的路径/cryptdb/mysqlproxy/wrapper.lua`
 3. 修改第一个函数 read_auth() 下的*os.getenv("CRYPTDB_PASS") or "letmein"*为*os.getenv("CRYPTDB_PASS") or "root"*
 4. 回到 cryptdb 根目录执行 make 语句(就是直接在命令行输入 `make`，回车)

## 接下来瓜哥决定用半小时带你走进cryptdb的世界！！

#### 一些前期准备
#### 首先在 *MySQL* 上面建立自己的数据库 *test*
MySQL 的语法和我们学习的（在大二学的 Oracle）略有出入，我这里把我们数据库实验的脚本编写了一下，重新导入到 MySQL 中。

步骤：
###### 一. 共享文件夹（如果你习惯使用 win）
 1. 安装VBox的增强功能，点击一下上方的按键，找到：设备==>安装增强功能

![applySharing.jpg](http://upload-images.jianshu.io/upload_images/7277397-3825f3bc3ee3b068.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


 2. 打开终端，进入到 /media 再进入到里面的那个文件夹，跑一下里面的 VBoxLinuxAdditions.run 的那个文件，完成后`reboot`。
 3. （前面的为了全屏的话你肯定做过）在 VBox 软件中选择共享文件夹，选择你在 win 下想要共享的文件夹，自动挂载，和固定分配。
 4. 把我写的脚本放进那个文件夹里。
 5. 在 Linux 的终端中输入命令： `sudo mount -t vboxsf` 你的文件夹名字 /mnt/随便建一个文件夹的名字。这样把你的共享文件夹手动挂载到Linux中（想要自动挂载的话，请把这个命令完完整整得写到 /etc/rc.local 的末行）


![autoMount.png](http://upload-images.jianshu.io/upload_images/7277397-c10469401156dcb7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



###### 二.搭建数据库

 6. 终端中打开 MySQL 命令:`mysql -u root -p`
 7. 输入你的密码:`blablabla`
 8. 先创建数据库 test 命令：`create database test;`
 9. 运行脚本 命令：`source 你的路径/first.sql`
 10. 其实改了脚本之后还是有蜜汁bug，不过吧第一张表格classroom的代码粘贴运行即可。
 11. 再次运行该脚本
 12. 搞定，可以用命令：`show tables;`查看一下已经导入的表格。
这里给出脚本：

``` sql
use test;
    drop table prereq;
    drop table time_slot;
    drop table advisor;
    drop table takes;
    drop table student;
    drop table teaches;
    drop table section;
    drop table instructor;
    drop table course;
    drop table department;
    drop table classroom

    create table classroom
	(building		varchar(15),
	 room_number		varchar(7),
	 capacity		numeric(4,0),
	 primary key (building, room_number)
	);

    create table department
	(dept_name		varchar(20),
	 building		varchar(15),
	 budget		        numeric(12,2) check (budget > 0),
	 primary key (dept_name)
	);

    create table course
	(course_id		varchar(8),
	 title			varchar(50),
	 dept_name		varchar(20),
	 credits		numeric(2,0) check (credits > 0),
	 primary key (course_id),
	 foreign key (dept_name) references department(dept_name)
	);


    create table instructor
	(ID			varchar(5),
	 name			varchar(20) not null,
	 dept_name		varchar(20),
	 salary			numeric(8,2) check (salary > 29000),
	 primary key (ID),
	 foreign key (dept_name) references department(dept_name)
	);

    create table section
	(course_id		varchar(8),
         sec_id			varchar(8),
	 semester		varchar(6)
		check (semester in ('Fall', 'Winter', 'Spring', 'Summer')),
	 year			numeric(4,0) check (year > 1701 and year < 2100),
	 building		varchar(15),
	 room_number		varchar(7),
	 time_slot_id		varchar(4),
	 primary key (course_id, sec_id, semester, year),
	 foreign key (course_id) references course(course_id),
	 foreign key (building, room_number) references         classroom(building,room_number)
	);

    create table teaches
	(ID			varchar(5),
	 course_id		varchar(8),
	 sec_id			varchar(8),
	 semester		varchar(6),
	 year			numeric(4,0),
	 primary key (ID, course_id, sec_id, semester, year),
	 foreign key (course_id,sec_id, semester, year) references     section(course_id,sec_id,semester,year),
	 foreign key (ID) references instructor(ID)
	);

    create table student
	(ID			varchar(5),
	 name			varchar(20) not null,
	 dept_name		varchar(20),
	 tot_cred		numeric(3,0) check (tot_cred >= 0),
	 primary key (ID),
	 foreign key (dept_name) references department(dept_name)
	);

    create table takes
	(ID			varchar(5),
	 course_id		varchar(8),
	 sec_id			varchar(8),
	 semester		varchar(6),
	 year			numeric(4,0),
	 grade		        varchar(2),
	 primary key (ID, course_id, sec_id, semester, year),
	 foreign key (course_id,sec_id, semester, year) references section(course_id,sec_id, semester, year),
	 foreign key (ID) references student(ID)
	);

    create table advisor
	(s_ID			varchar(5),
	 i_ID			varchar(5),
	 primary key (s_ID),
	 foreign key (i_ID) references instructor (ID)
		on delete set null,
	 foreign key (s_ID) references student (ID)
	);

    create table time_slot
	(time_slot_id		varchar(4),
	 day			varchar(1),
	 start_hr		numeric(2) check (start_hr >= 0 and start_hr < 24),
	 start_min		numeric(2) check (start_min >= 0 and start_min < 60),
	 end_hr			numeric(2) check (end_hr >= 0 and end_hr < 24),
	 end_min		numeric(2) check (end_min >= 0 and end_min < 60),
	 primary key (time_slot_id, day, start_hr, start_min)
	);

    create table prereq
	(course_id		varchar(8),
	 prereq_id		varchar(8),
	 primary key (course_id, prereq_id),
	 foreign key (course_id) references course(course_id)
	);



    delete from prereq;
    delete from time_slot;
    delete from advisor;
    delete from takes;
    delete from student;
    delete from teaches;
    delete from section;
    delete from instructor;
    delete from course;
    delete from department;
    delete from classroom;
    insert into classroom values ('Packard', '101', '500');
    insert into classroom values ('Painter', '514', '10');
    insert into classroom values ('Taylor', '3128', '70');
    insert into classroom values ('Watson', '100', '30');
    insert into classroom values ('Watson', '120', '50');
    insert into department values ('Biology', 'Watson', '90000');
    insert into department values ('Comp. Sci.', 'Taylor', '100000');
    insert into department values ('Elec. Eng.', 'Taylor', '85000');
    insert into department values ('Finance', 'Painter', '120000');
    insert into department values ('History', 'Painter', '50000');
    insert into department values ('Music', 'Packard', '80000');
    insert into department values ('Physics', 'Watson', '70000');
    insert into course values ('BIO-101', 'Intro. to Biology', 'Biology', '4');
    insert into course values ('BIO-301', 'Genetics', 'Biology', '4');
    insert into course values ('BIO-399', 'Computational Biology', 'Biology', '3');
    insert into course values ('CS-101', 'Intro. to Computer Science', 'Comp. Sci.', '4');
    insert into course values ('CS-190', 'Game Design', 'Comp. Sci.', '4');
    insert into course values ('CS-315', 'Robotics', 'Comp. Sci.', '3');
    insert into course values ('CS-319', 'Image Processing', 'Comp. Sci.', '3');
    insert into course values ('CS-347', 'Database System Concepts', 'Comp. Sci.', '3');
    insert into course values ('EE-181', 'Intro. to Digital Systems', 'Elec. Eng.', '3');
    insert into course values ('FIN-201', 'Investment Banking', 'Finance', '3');
    insert into course values ('HIS-351', 'World History', 'History', '3');
    insert into course values ('MU-199', 'Music Video Production', 'Music', '3');
    insert into course values ('PHY-101', 'Physical Principles', 'Physics', '4');
    insert into instructor values ('10101', 'Srinivasan', 'Comp. Sci.', '65000');
    insert into instructor values ('12121', 'Wu', 'Finance', '90000');
    insert into instructor values ('15151', 'Mozart', 'Music', '40000');
    insert into instructor values ('22222', 'Einstein', 'Physics', '95000');
    insert into instructor values ('32343', 'El Said', 'History', '60000');
    insert into instructor values ('33456', 'Gold', 'Physics', '87000');
    insert into instructor values ('45565', 'Katz', 'Comp. Sci.', '75000');
    insert into instructor values ('58583', 'Califieri', 'History', '62000');
    insert into instructor values ('76543', 'Singh', 'Finance', '80000');
    insert into instructor values ('76766', 'Crick', 'Biology', '72000');
    insert into instructor values ('83821', 'Brandt', 'Comp. Sci.', '92000');
    insert into instructor values ('98345', 'Kim', 'Elec. Eng.', '80000');
    insert into section values ('BIO-101', '1', 'Summer', '2009', 'Painter', '514', 'B');
    insert into section values ('BIO-301', '1', 'Summer', '2010', 'Painter', '514', 'A');
    insert into section values ('CS-101', '1', 'Fall', '2009', 'Packard', '101', 'H');
    insert into section values ('CS-101', '1', 'Spring', '2010', 'Packard', '101', 'F');
    insert into section values ('CS-190', '1', 'Spring', '2009', 'Taylor', '3128', 'E');
    insert into section values ('CS-190', '2', 'Spring', '2009', 'Taylor', '3128', 'A');
    insert into section values ('CS-315', '1', 'Spring', '2010', 'Watson', '120', 'D');
    insert into section values ('CS-319', '1', 'Spring', '2010', 'Watson', '100', 'B');
    insert into section values ('CS-319', '2', 'Spring', '2010', 'Taylor', '3128', 'C');
    insert into section values ('CS-347', '1', 'Fall', '2009', 'Taylor', '3128', 'A');
    insert into section values ('EE-181', '1', 'Spring', '2009', 'Taylor', '3128', 'C');
    insert into section values ('FIN-201', '1', 'Spring', '2010', 'Packard', '101', 'B');
    insert into section values ('HIS-351', '1', 'Spring', '2010', 'Painter', '514', 'C');
    insert into section values ('MU-199', '1', 'Spring', '2010', 'Packard', '101', 'D');
    insert into section values ('PHY-101', '1', 'Fall', '2009', 'Watson', '100', 'A');
    insert into teaches values ('10101', 'CS-101', '1', 'Fall', '2009');
    insert into teaches values ('10101', 'CS-315', '1', 'Spring', '2010');
    insert into teaches values ('10101', 'CS-347', '1', 'Fall', '2009');
    insert into teaches values ('12121', 'FIN-201', '1', 'Spring', '2010');
    insert into teaches values ('15151', 'MU-199', '1', 'Spring', '2010');
    insert into teaches values ('22222', 'PHY-101', '1', 'Fall', '2009');
    insert into teaches values ('32343', 'HIS-351', '1', 'Spring', '2010');
    insert into teaches values ('45565', 'CS-101', '1', 'Spring', '2010');
    insert into teaches values ('45565', 'CS-319', '1', 'Spring', '2010');
    insert into teaches values ('76766', 'BIO-101', '1', 'Summer', '2009');
    insert into teaches values ('76766', 'BIO-301', '1', 'Summer', '2010');
    insert into teaches values ('83821', 'CS-190', '1', 'Spring', '2009');
    insert into teaches values ('83821', 'CS-190', '2', 'Spring', '2009');
    insert into teaches values ('83821', 'CS-319', '2', 'Spring', '2010');
    insert into teaches values ('98345', 'EE-181', '1', 'Spring', '2009');
     insert into student values ('00128', 'Zhang', 'Comp. Sci.', '102');
    insert into student values ('12345', 'Shankar', 'Comp. Sci.', '32');
    insert into student values ('19991', 'Brandt', 'History', '80');
    insert into student values ('23121', 'Chavez', 'Finance', '110');
    insert into student values ('44553', 'Peltier', 'Physics', '56');
    insert into student values ('45678', 'Levy', 'Physics', '46');
    insert into student values ('54321', 'Williams', 'Comp. Sci.', '54');
    insert into student values ('55739', 'Sanchez', 'Music', '38');
    insert into student values ('70557', 'Snow', 'Physics', '0');
    insert into student values ('76543', 'Brown', 'Comp. Sci.', '58');
    insert into student values ('76653', 'Aoi', 'Elec. Eng.', '60');
    insert into student values ('98765', 'Bourikas', 'Elec. Eng.', '98');
    insert into student values ('98988', 'Tanaka', 'Biology', '120');
    insert into takes values ('00128', 'CS-101', '1', 'Fall', '2009', 'A');
    insert into takes values ('00128', 'CS-347', '1', 'Fall', '2009', 'A-');
    insert into takes values ('12345', 'CS-101', '1', 'Fall', '2009', 'C');
    insert into takes values ('12345', 'CS-190', '2', 'Spring', '2009', 'A');
    insert into takes values ('12345', 'CS-315', '1', 'Spring', '2010', 'A');
    insert into takes values ('12345', 'CS-347', '1', 'Fall', '2009', 'A');
    insert into takes values ('19991', 'HIS-351', '1', 'Spring', '2010', 'B');
    insert into takes values ('23121', 'FIN-201', '1', 'Spring', '2010', 'C+');
    insert into takes values ('44553', 'PHY-101', '1', 'Fall', '2009', 'B-');
    insert into takes values ('45678', 'CS-101', '1', 'Fall', '2009', 'F');
    insert into takes values ('45678', 'CS-101', '1', 'Spring', '2010', 'B+');
    insert into takes values ('45678', 'CS-319', '1', 'Spring', '2010', 'B');
    insert into takes values ('54321', 'CS-101', '1', 'Fall', '2009', 'A-');
    insert into takes values ('54321', 'CS-190', '2', 'Spring', '2009', 'B+');
    insert into takes values ('55739', 'MU-199', '1', 'Spring', '2010', 'A-');
    insert into takes values ('76543', 'CS-101', '1', 'Fall', '2009', 'A');
    insert into takes values ('76543', 'CS-319', '2', 'Spring', '2010', 'A');
    insert into takes values ('76653', 'EE-181', '1', 'Spring', '2009', 'C');
    insert into takes values ('98765', 'CS-101', '1', 'Fall', '2009', 'C-');
    insert into takes values ('98765', 'CS-315', '1', 'Spring', '2010', 'B');
    insert into takes values ('98988', 'BIO-101', '1', 'Summer', '2009', 'A');
    insert into takes values ('98988', 'BIO-301', '1', 'Summer', '2010', null);
    insert into advisor values ('00128', '45565');
    insert into advisor values ('12345', '10101');
    insert into advisor values ('23121', '76543');
    insert into advisor values ('44553', '22222');
    insert into advisor values ('45678', '22222');
    insert into advisor values ('76543', '45565');
    insert into advisor values ('76653', '98345');
    insert into advisor values ('98765', '98345');
    insert into advisor values ('98988', '76766');
    insert into time_slot values ('A', 'M', '8', '0', '8', '50');
    insert into time_slot values ('A', 'W', '8', '0', '8', '50');
    insert into time_slot values ('A', 'F', '8', '0', '8', '50');
    insert into time_slot values ('B', 'M', '9', '0', '9', '50');
    insert into time_slot values ('B', 'W', '9', '0', '9', '50');
    insert into time_slot values ('B', 'F', '9', '0', '9', '50');
    insert into time_slot values ('C', 'M', '11', '0', '11', '50');
    insert into time_slot values ('C', 'W', '11', '0', '11', '50');
    insert into time_slot values ('C', 'F', '11', '0', '11', '50');
    insert into time_slot values ('D', 'M', '13', '0', '13', '50');
    insert into time_slot values ('D', 'W', '13', '0', '13', '50');
    insert into time_slot values ('D', 'F', '13', '0', '13', '50');
    insert into time_slot values ('E', 'T', '10', '30', '11', '45 ');
    insert into time_slot values ('E', 'R', '10', '30', '11', '45 ');
    insert into time_slot values ('F', 'T', '14', '30', '15', '45 ');
    insert into time_slot values ('F', 'R', '14', '30', '15', '45 ');
    insert into time_slot values ('G', 'M', '16', '0', '16', '50');
    insert into time_slot values ('G', 'W', '16', '0', '16', '50');
    insert into time_slot values ('G', 'F', '16', '0', '16', '50');
    insert into time_slot values ('H', 'W', '10', '0', '12', '30');
    insert into prereq values ('BIO-301', 'BIO-101');
    insert into prereq values ('BIO-399', 'BIO-101');
    insert into prereq values ('CS-190', 'CS-101');
    insert into prereq values ('CS-315', 'CS-101');
    insert into prereq values ('CS-319', 'CS-101');
    insert into prereq values ('CS-347', 'CS-101');
    insert into prereq values ('EE-181', 'PHY-101');
```


#### 使用cryptdb的开始

步骤：

###### 一. 登陆，以及登陆会遇到的问题
1. 首先把另外一个文件 Mysql Proxy 放到共享目录下并且打开，方便复制。
Mysql Proxy( 来自github ):

        How to Run Mysql Proxy
            ----------------------

        to start proxy:

        % export EDBDIR=<...>/cryptdb
        % mysql-proxy --plugins=proxy \
                --event-threads=4 \
		--max-open-files=1024 \
		--proxy-lua-script=$EDBDIR/mysqlproxy/wrapper.lua \
		--proxy-address=localhost:3307 \
		--proxy-backend-addresses=localhost:3306

        to specify username / password / shadow dir for proxy, run before starting proxy:

        % export CRYPTDB_USER=...
        % export CRYPTDB_PASS=...
        % export CRYPTDB_SHADOW=...

        to send a single command to mysql:

        % mysql -u root -p -h 127.0.0.1 -P 3307 -e 'command'

2. 把里面的环境变量先设置一下，把第二个命令路径改成你自己的，主要是cryptdb的安装路径（其实你要是用脚本安装的话基本不会变了）。
3. 运行第二条命令，进入到cryptdb的代理服务器中。
4. 可以看到我们利用命令设置了两个端口,其中：

    >  --proxy-address=:3307 指定mysql proxy的监听端口，也可以用 127.0.0.1:3307 表示
    >
    > --proxy-backend-addresses=:3306 指定mysql主机的端口
    >
    > --proxy-lua-script=￥EDBDIR/mysqlproxy/mysql-proxy/wrapper.lua 指定lua脚本（具体它的作用是什么，要打开细看才行）

5. 这时候代理服务器已经启动打开另外两个窗口分别查看数据库
6. 复制最后一条命令打开数据库。注意这里有可能会出错，主要有两个原因：
	> * 有可能显示说脚本不能运行，那么请检查之前你有没有把到脚本的路径设好，也就是文件中的第一条命令
	> * 第二个原因是你没有root，这时候登录的终端窗口显示Lost connection to MySQL server at ‘reading authorization packet’
	> * 第三个原因可能是你密码输错了，这样 proxy 会运行一段时间然后跳出，返回错误信息，登录的终端会显示 Access denied for user....YES),请注意这里的密码是之前我们在wrapper.lua中修改的CRYPTDB_PASS的变量，应该是‘root’，你可以自己试着改一改来玩一玩。

### 使用cryptdb

这时候就已经可以通信了
登录的终端root不root无所谓（按道理也应该是这样的嘛~哪有说是个登陆者都要root的权限才能访问数据库的）

###### 先是在3307端口的操作
1. 查看一下数据库
	命令：`show databases;`
2. 看到了我们之前建立的 test
3. 使用一下
	命令：`use test`
4. 查看一下表格
	命令：`show tables;`
		   `select * from advisor;`
5. 哈哈，被骗了吧。没看论文的你就会跟着我在前面不断犯错在 MySQL 上面直接导入脚本中的数据库。
6. 实际上你必须在连接proxy的条件下创建表格或者创建数据库，否则你创建的表格在你使用 proxy 访问数据库的时候肯定会出错。

###### 重新在3307端口操作（本操作主要是为了建立数据库，在 3306 或者 3307 端口都是可行的）
7. 打开已经准备好的 demo 文档（来自 Github），重新创建数据库，复制里面的命令试一下吧~~~
8. 当你使用 show tables; 的时候你会发现表格名称都是不能识别的加密之后的表格名字。然而如果你试图通过查询这些表格来获取里面的信息，那将会遭受很大的挫折（never success），你必须知道原来的名字，并且用他们来查询信息。
9. 这个是正常的访问 proxy 代理的操作，可见必须是 application 才会知道这些表格名称，要不然怎么访问都是徒劳的。

这里给出demo：

    Single-principal demo:

    * in  proxy shell:
    mysql-proxy --plugins=proxy --event-threads=4 --max-open-files=1024 --proxy-lua-script=$EDBDIR/../mysqlproxy/wrapper.lua --proxy-address=127.0.0.1:3307 --
    proxy-backend-addresses=localhost:3306
    * in plain shell: drop database cryptdbtest; create database cryptdbtest; use cryptdbtest;
    * in demo shell: mysql -u root -pletmein -h 127.0.0.1 -P 3307 cryptdbtest

    CREATE TABLE t1 (id integer, name text, salary integer);
    (plain: show tables; describe table0;)
    INSERT INTO t1 VALUES (1, 'alice', 100);
    INSERT INTO t1 VALUES (2, 'bob', 200);
    INSERT INTO t1 VALUES (0, 'chris', 0);
    INSERT INTO t1 VALUES (4, 'dan dennis', 0);
    SELECT * FROM t1;
    (plain: select * from table0;)
    SELECT * FROM t1 WHERE id = 1;
    SELECT name, salary FROM t1 WHERE salary > 0;
    SELECT sum(salary) FROM t1 WHERE salary > 0;
    (plain: SELECT field1SWP from table0;)
    SELECT name FROM t1 WHERE name ILIKE 'dennis';

    others:
    SELECT * FROM t1 ORDER BY name;
    SELECT count(*) FROM t1 GROUP BY salary;
    DELETE FROM t1 WHERE id = 1;
    (plain: select * from table0;)
    DROP TABLE t1;

    CREATE TABLE t2 (id integer);
    INSERT INTO t2 VALUES (2);

    SELECT * FROM t1, t2 WHERE t1.id = t2.id;


    Multi-principal demo:

    export CRYPTDB_MODE=multi -->sets either to single or multi principle

    -- make sure allDefaultEncrypted in EDBProxy.hh is set to false and then make

    CREATE TABLE msgs (msgid equals privmsg.msgid integer, msgtext encfor msgid text);
    CREATE TABLE privmsg (msgid integer, recid equals u_mess.userid speaksfor msgid integer, senderid speaksfor msgid integer);
    CREATE TABLE u_mess (userid equals privmsg.senderid integer, username givespsswd userid text);
    (log in Alice:)
    INSERT INTO pwdcryptdb__u_mess (username, psswd) VALUES ('alice', 'secretalice');
    (log in Bob:)
    INSERT INTO pwdcryptdb__u_mess (username, psswd) VALUES ('bob', 'secretbob');
    INSERT INTO u_mess VALUES (1, 'alice');
    INSERT INTO u_mess VALUES (2, 'bob');
    INSERT INTO privmsg (5, 1, 2);

    INSERT INTO msgs VALUES (5, 'secret message');
    (log off Bob:)
    DELETE FROM pwdcryptdb__u_mess WHERE username = 'bob';
    SELECT * FROM msgs; --> should be able to see secret message
    (log off Alice similarly to above)
    SELECT * FROM msgs; --> should not be able to see secret message any more
    because none of the users with access to it are logged in
    (log in Alice as above)
    SELECT * FROM msgs; --> should be able to see the secret message

###### 重新在3307端口操作
1. 查看一下数据库
	命令：`show databases;`
2. 看到了我们之前建立的 test
3. 使用一下
	命令：`use test`
4. 查看一下表格
	命令：`show tables;`
		   `select * from advisor;`

###### 再是3306端口的操作
1. 在使用3306端口的时候，查询到的的将会全都是密文（鬼画符一样）
2. 而且在输入原来的名字的时候没有自动补全，很烦，只能输入错误的表格名最后输出密文。
3. 目前只知道这个是针对内鬼的（curious DMA）真实可怕，诸位大佬玩玩就好，不需要耗费太多时间在这上面。

###### 看截图对比一下两个端口

1. `show tables;`两个端口显示的 tables 数据一致 （右上是3306，右下是3307）
![compareTables.png](http://upload-images.jianshu.io/upload_images/7277397-f14b56f25af1d471.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. `SELECT * FROM t1;` 3306 显示没有这个表格， 3307 则完整显示了整个 t1。
![selectT1.png](http://upload-images.jianshu.io/upload_images/7277397-f7ede4d62889a8d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. `SELECT * FROM table_QZKOAZMXZW;`3306 显示出大段密文，而 3307 则显示没有这样的表格。（右是3306，中是3307）
![selectQZKOAZMXZW.png](http://upload-images.jianshu.io/upload_images/7277397-f097726a392c5832.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后的操作请看官自己完成。。。已经没有任何难度了。
