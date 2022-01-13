

[TOC]

# mysql数据库实验——建表准备

==**tips**==:*在SQL中，所有的命令和关键字以及自定义名称都**不区分大小写**，但是强烈推荐：关键字部分使用全部大写，自定义名称（数据库名、数据库表名、字段名）用小写字母开头*

！！！很牛很🐂的数据库大佬总结：[mysql-朱双印博客-第4页 (zsythink.net)](https://www.zsythink.net/archives/tag/mysql/page/4)

## 1.dos界面中数据库的登录操作

​	1.win+R（cmd）进入dos界面

![image-20211015150310813.png](https://github.com/Yolo-hwt/csdnPicAtGithub/blob/main/image-20211015150310813.png)

​	2.进入mysql安装目录中

tips:进入安装目录下首先启动数据库mysql服务：`net start` 服务名称

服务名称可在本机系统服务中查看(此电脑->管理->服务和应用程序->服务)，这里我的是mysql80

### #这里解决一个bug

发生意料之外的错误：

![image-20211016111740578](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016111740578.png)

baidu+搜索：权限不够，以管理员模式启动cmd

![image-20211016113317222](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016113317222.png)



- 运行mysql -u -root -p命令

- 输入root用户密码进入数据库服务

  ![image-20211015150717660](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015150717660.png)

## 2.数据库及表格创建

### (1)利用sql语句创建数据库DBtest

- CREATE DATABASE DBtest

@**`更加确切的创建方式`**（判断是否重名创建，指定字符集，指定校对规则）

```
CREATE DATABASE [IF NOT EXISTS] <数据库名>
[[DEFAULT] CHARACTER SET <字符集名>] 
[[DEFAULT] COLLATE <校对规则名>];

CREATE DATABASE IF NOT EXISTS DBtest
DEFAULT CHARACTER SET utf8mb4
DEFAULT COLLATE utf8mb4_0900_ai_ci;
```

创建之后利用**`show databases`**命令查看当前所有数据库

![image-20211015155646791](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015155646791.png)

**`SHOW CREATE DATABASE`**查看数据库的定义声明

![image-20211015160055884](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015160055884.png)

### (2)表格创建

1.依据本地表格文件DBtest.xls

![image-20211015153914867](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015153914867.png)

创建数据库表格

利用sql语句结合表格所考虑到的完整性约束

```
CREATE TABLE stu_scores(
studentID varchar(10)PRIMARY KEY,/*学号为主码*//*此处引发下面的bug*/
schoolyear varchar(20),
id_course varchar(20)UNIQUE,/*课程号取唯一值*//*后续更改后为主码*/
name_course varchar(30),
nature_course varchar(20),
value_course double,
score_course double,
grade_point double
);
```

使用`show columns from stu_scores`语句查看已创建的表格列属性

![image-20211015163621554](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015163621554.png)

也可在mysql-front中创建关联数据库dbtest的账号查看表格属性

![image-20211015163909701](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015163909701.png)



## 3.导入数据至数据据库中

- 要点：

  1.需要在数据库中事先创建好表和表结构，应该和excel的结构一样

  2.先把本地DBtest.xls另存为.csv文件，再将此文文件在记事本中打开，另存为.txt文件

  [CSV（逗号分隔值文件格式）_百度百科 (baidu.com)](https://baike.baidu.com/item/CSV/10739?fr=aladdin)

  3.执行该语句

  ```
  LOAD DATA LOCAL INFILE'E:\DBtest.txt' INTO TABLE stu_scores 
  FIELDS TERMINATED BY ','  
  LINES TERMINATED BY '\r\n'
  IGNORE 1 LINES;
  
  ```

  `INFILE`后面跟文件路径，

  `TERMINATED BY`指数据分隔方式，

  `LINES TERMINATED BY`指行的分隔方式，

  ![image-20211015213504943](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015213504943.png)

  - *windows换行是\r\n，十六进制数值是：0D0A。*
    *LINUX换行是\n，十六进制数值是：0A*

  `GNORE 1 LINES`忽略第一行（由于txt文件头是对应的excel的表头将其忽略）

  

==**tips:**ctrl+[ 格式化上面段落的格式继承==

### #这里解决一个bug

在使用这段命令时候报错:==**ERROR 1148 (42000): The used command is not allowed with this MySQL version**==

![image-20211015193301465](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015193301465.png)

一番搜索：

[相关解决方案1](https://blog.csdn.net/sunflower_cao/article/details/40347461)

[相关解决方案2](https://www.imooc.com/wenda/detail/573872)

tips:disappointed_relieved:==点击链接跳转要按住ctrl==

**原因分析**：

根据官方的解释是mysql在编译的时候默认把local-infile的参数设为0，就是关闭了从本地load的功能，所以如果需要使用只能自己打开

- 很多类似的都是方案一的解决手法，在连接自己的mysql服务时候用此命令启动：**mysql -u root --local-infile=1 -p**（本机测试无用）

- 方案二中提供如下方式
- ![image-20211015214851041](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015214851041.png)

亲测有用

先使用命令**`SHOW GLOBAL VARIABLES LIKE 'local_infile'`**查看一下当前==**local_infile**==状态

![image-20211015215307468](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015215307468.png)

不出所料，==**off**==

然后使用命令**`SET GLOBAL local_infile=true`**更改标识

![image-20211015215534085](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015215534085.png)

然后再次尝试加载本地DBtest.txt（感觉上应该没问题，但是！）

### #这里解决一个bug	

![image-20211015215957690](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015215957690.png)

跳过了47条记录

使用**`SELECT* FROM stu_scores`**查询结果如下：

![image-20211015220157810](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015220157810.png)

同样学号数据仅仅出现一次

于是联想到前面我们将`studentID`设置为了主码

 PRIMARY KEY：指示某一列为表的主码，是==**非空且唯一**==的

那么也就可以猜想，加载数据时候读取到一个学号之后，那么后续此学号开头的元组也就全部被过滤掉了

使用命令

```
ALTER TABLE stu_scores
DROP PRIMARY KEY;
```

取消主码约束

然后使用**`show columns from stu_scores;`**

![image-20211015222456966](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015222456966.png)

此时`studentID`已取消主码约束，Key部分未显示任何值

#### 然后清空刚才录入错误的表格

```
1.truncate table table_name;
2.delete from table_name;
//两者均可但稍有不同
```

详见：[mysql -- 清空表中数据 - ma_fighting - 博客园 (cnblogs.com)](https://www.cnblogs.com/mafeng/p/10833267.html)

***这里尤其注意不能用drop语句，drop直接将表删除而不是清空***

若使用drop后果如下

![image-20211015224112732](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015224112732.png)

再次录入本地DBtest.txt（呜呜~finally）

![image-20211015224355782](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015224355782.png)

***aaaaaaaaa***

***get it!!!!!!!!!!!***

通过mysql-front细看如下

![image-20211015224531763](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211015224531763.png)



成功导入！

#### #一点小瑕疵更改

- 第二个学生学号为2，试着将它改为1，看着齐整一点

这里就涉及到数据更新

```
UPDATE stu_scores
SET studentID='1'
WHERE id_course="4120348171";
```

![image-20211016114246452](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016114246452.png)

修改成功！

---

# 基于dbtest.stu_scores的数据库实验

## 实验一 数据库管理系统(DBMS)实验

### 1）熟悉DBMS的界面和操作

本次实验采用mysql数据库，结合mysql-front实现，界面及使用已悉知

### 2）创建数据库和查看数据库属性

上述已创建数据库`dbtest`，

这里扩展一下创建数据库命令

```
CREATE {DATABASE|SCHEMA}[IF NOT EXISTS] dbname [create_specification]
//对于create_specification的解释
create_specification:
[DEFAULT] CHARACTER SET [=] charset_name //字符集
  OR
[DEFAULT] COLLATE [=] collation_name//校对规则

    /*
    使用character set对应字符集名称即可指定使用什么字符集，如果使用了default关键字，那么这个数据库中创建的所有表默认都会继承这个数据库的字符集，default为可选选项，如果你不知道存在哪些字符集，可以使用”show character set;”命令查看所有可用字符集
    */

show databases;//查看所有数据库
show create databases db_name;//列出创建对应数据库的sql语句
show collation;//查看排序方式
status//查看当前数据库概要信息
show variables like 'character%';//查看字符集
```



另：mysql-front中右键数据库点击属性查看即可

### 3）创建表、确定表的主码和约束条件

表`stu_scores`已创建并从本地导入数据

- 设置此表中主码为`id_course`

`alter table 表名 add primary key(字段1,字段2);`

```
alter table stu_scores add primary key (id_course);
```

- 设置课程名，课程性质为非空

`alter table 表名 modify 列名[属性] not null;`

```
alter table stu_scores modify name_course varchar(30) not null;

alter table stu_scores modify nature_course varchar(20) not null;
```

![image-20211016155242975](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016155242975.png)

### 4）查看和修改表的结构

#### 查看表结构四种方式

```
1.desc table_name

2.show columns from table_name

3.show create table table_name

4.

use information_schema;

select * from columns where table_name='table_name';
```

你肯定想知道information_schema是啥

[MySQL中information_schema是什么 - Wopus教程站](http://help.wopus.org/mysql-manage/607.html)

- 1和2方式均得到如👆上图所示

- 3.

  ```
  show create table stu_scores;
  ```

  ![image-20211016160415825](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016160415825.png)

  个人理解：相当于把你创建这张表时的create语句以sql方式给具体展示出来

- 4.

- 包含有两条命令

  ```
  use information_schema;
  
  select * from columns where table_name='stu_scores';
  ```

  结果非常强悍

  ![image-20211016161331985](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016161331985.png)

#### 修改表结构

仅测试部分功能

相关：[mysql唯一索引和普通索引的区别](https://blog.csdn.net/zou_albert/article/details/107364227)

额外链接：[MySQL之修改数据库 - 菜菜成长记 - 博客园 (cnblogs.com)](https://www.cnblogs.com/ccstu/p/12156098.html)

```
1.ALTER TABLE table_name DROP column_name;//删除列
2.ALTER TABLE table_name ADD column_name column_type[列级完整性约束];//添加列
3.ALTER TABLE table_name MODIFY column_name column_type[列级完整性约束]NOT NULL[first|after col_name];//更改列类型，完整性约束或添加not null属性
//需注意not null 要在位置之前使用
//modify不能修改字段(列)名称

4.ALTER TABLE table_name ADD PRIMARY KEY (columon_name,...);//添加主键
5.ALTER TABLE table_name DROP PRIMARY KEY (columon_name,...);//删除主键

6.ALTER TABLE table_name ADD UNIQUE index_name(`column_name`);//为column_name这一列添加名为index_name的唯一索引
7.ALTER TABLE table_name ADD INDEX index_name(`column_name`);//添加普通索引


8.ALTER TABLE table_name CHANGE old_name new_name column_type[完整性约束];//更改列名
*ALTER TABLE table_name CHANGE column_name column_name new_column_type[完整性约束];//可以使得新旧名称相同转而改变列类型

9.
    修改表名称：
语法一： ALTER TABLE table_name RENAME[to|as] new_table_name
讲解：可以更改一张数据表名称
语法二：RENAME TABLE table_name to new_table_name [,new_table_name2 TO new_new_table_name2...]
讲解：可以多表更改名称

```



### 5）向数据库输入数据，观察违反列级约束时出现的情况

往`stu_scores`中插入一条记录，其中`id_course`为空

```
INSERT INTO stu_scores(
studentID,
id_course,
schoolyear,
name_course,
nature_course,
value_course,
score_course,
grade_point
)values(
'2',
null,
"2021-2022-1",
'12345',
"test",
1,
1,
0
);
```

报错：

😫![image-20211016174825608](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016174825608.png)



### 6）修改数据

将上述例子更改`id_course`为8，插入到表中，利用`update`命令修改其值

![image-20211016175258165](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016175258165.png)

update修改对应学号为8，课程名称为test，课程性质为mytest

```
UPDATE stu_scores SET studentID='8',name_course='test',nature_course='mytest'
WHERE id_course='8';
```

![image-20211016175848324](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016175848324.png)

### 7）删除数据，观察违反表级约束时出现的情况

利用delete语句删除上述添加的test

```
DELETE FROM stu_scores
WHERE id_course='8';
```

成功删除

但是还注意到题目要求的是 ***违反表级约束*** 所以说一张表无法满足这样的测试条件，于是建立表`stu_failed`

并确立`id_failed`为外码，链接`stu_scores`中的`id_course`

```
CREATE TABLE stu_failed(
id_failed varchar(20)PRIMARY KEY,
name_failed varchar(30)NOT NULL,
score_failed double,
FOREIGN KEY(id_failed)REFERENCES stu_scores(id_course)
);
```

######这里遇到一个错误

一开始准备以`studentID`为主表中链接主键，得到错误反馈

上网查询后：[(12条消息) mysql添加外键报错：ERROR 1215 (HY000): Cannot add foreign key constrain_weixin_30919919的博客-CSDN博客](https://blog.csdn.net/weixin_30919919/article/details/97039860)

注意到作为外键链接的主表主键必须为**UNIQUE**也就是拥有唯一值，显然`stu_scores`中`studentID`多次相同值重复出现，不符合规则

所以不妨换个想法，反正就是建造一张便于理解的表而已，将其作为一张不及格成绩的表单，将`stu_scores`中的`id_course`作为连接主键，外键为`id_failed`

创建成功！

![image-20211016221312574](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016221312574.png)

---

继续刚才。。。

#### 主表导入数据测试

利用子查询语句结合insert将课程成绩低于60分的对应数据添加到`stu_failed`中

```
INSERT INTO stu_failed(id_failed, name_failed, score_failed)
 (SELECT id_course, name_course, score_course 
FROM stu_scores
WHERE score_course<60);
```

![image-20211016222430374](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016222430374-16347296655751.png)

![image-20211016222450252](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016222450252.png)

然后就是准备删除了，验证表级约束

直接用`drop`删除表`stu_scores`

`DROP TABLE stu_scores RESTRICT`（虽说默认情况下是`RESTRICT`保险起见，还是注明一下，要是万一表毁人亡，毕竟是个测试！）

![image-20211016223008398](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016223008398.png)

hhh

mysql诚不欺我，存在表级约束，you can not DROP！！！

但是反观没有对外约束的stu_failed

![image-20211016223600304](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211016223600304.png)

就没有这么幸运了



### 8）备份数据库到其它磁盘（如U盘），从其它磁盘恢复数据库

参考链接：

[mysql备份工具之mysqldump-朱双印博客 (zsythink.net)](https://www.zsythink.net/archives/1450)

[MySQL数据备份之mysqldump使用 - 星朝 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jpfss/p/7867668.html)

[(12条消息) mysql 备份 还原指定路径_MySQL 数据备份与还原_刘宏甲的博客-CSDN博客](https://blog.csdn.net/weixin_42495556/article/details/113438741)



- **mysqldump**是mysql自带的客户端工具，所以当**mysqldump**连接到数据库时，也会读取mysql数据库的配置文件，加载跟客户端相关的配置。

- 它的备份原理是，通过协议连接到mysql数据库，将需要备份的数据查询出来，**将查询出的数据转换成对应的insert语句，当我们需要还原这些数据时，只要执行这些insert语句，即可将对应的数据还原**

一番阅读之后......

开干！

简要思路就是，再次创建我们上面用到的`stu_failed`表但是这次我们并不是放在`dbtest`数据库中，

额外创建一个数据库`testmouse`（小白鼠数据库）

流程：

1.创建-->2.填充-->3.备份-->4.销毁-->5.恢复

#### 1.创建`testmouse`和`stu_failed`

```
CREATE DATABASE IF NOT EXISTS testmouse
DEFAULT CHARACTER SET utf8mb4
DEFAULT COLLATE utf8mb4_0900_ai_ci;

CREATE TABLE stu_failed(
id_failed varchar(20)PRIMARY KEY,
name_failed varchar(30)NOT NULL,
score_failed double,
FOREIGN KEY(id_failed)REFERENCES dbtest.stu_scores(id_course)//由于不在一个数据库中采用dbtest.stu_scores约束
);
```

#### 2.填充`stu_failed`数据

利用insert子查询

```
INSERT INTO stu_failed(id_failed, name_failed, score_failed)
(SELECT id_course, name_course, score_course 
FROM dbtest.stu_scores//同上
WHERE score_course<60);
```

#### 3.备份数据库

如果未开启二进制日志，备份指定的数据库，可以使用如下语句

==tips:==***[InnoDB](https://baike.baidu.com/item/InnoDB) ：5.5版本后Mysql的默认数据库，事务型数据库的首选引擎，支持ACID事务，支持行级锁定***

- 在备份innodb存储引擎的表时，如果想让备份操作基于”独立的事务”进行，则需要使用 –single-transaction选项

- –routines选项：表示备份时，存储过程和存储函数也会被备份。

  –triggers选项：表示备份时，触发器会被备份。

  –events选项：表示备份时，事件表会被备份。

```
mysqldump -uroot 
    --single-transaction 
    --routines --triggers 
    --events 
    --databases database_name -p 
    > [路径约束|默认当前目录]backup_file_name.sql

//这里仅仅测试一个小demo所以不需要用上面所有命令约束
//将testmouse数据库备份到E盘（也就是我的桌面）back_file_name.sql文件
mysqldump -uroot --databases testmouse -p>E:\backup_file_name.sql
```

备份成功，记事本打开如下

![image-20211017221157203](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211017221157203.png)



#### 4.销毁数据库`testmouse`

![image-20211017221554334](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211017221554334.png)

然后我们显示本地所有数据库

![image-20211017221640443](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211017221640443.png)

成功删除，其中并没有`testmouse`数据库

#### 5.恢复数据库

- 创建空数据库`testmouse`
- 然后使用`source`命令导入备份文件

```
CREATE DATABASE testmouse;
use testmouse;
source E:/backup_file_name.sql;//这里记录一下很奇怪使用E:\这种格式会报错，未解决
```

恢复成功！

![image-20211017225829166](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211017225829166.png)



## 实验二数据库的创建与修改

此实验包含对于数据库，数据库基本表的创建和修改操作

### a)数据库的创建与修改

相关参考：[mysql/mariadb知识点总结（8）：库管理语句-朱双印博客 (zsythink.net)](https://www.zsythink.net/archives/806)

上面我们已经将数据库创建完成了，这里我们来对数据库修改做一些工作

```
ALTER {DATABASE | SCHEMA} [db_name] [alter_specification]

//
alter_specification:
//数据库修改工作修改数据库的字符集或排序规则
[DEFAULT] CHARACTER SET [=] charset_name |  [DEFAULT] COLLATE [=] collation_name

alter database db character set utf8;
```

以我目前已有的空数据库`db`为例

更改其字符集为utf8

![image-20211018192936240](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211018192936240.png)



### b)基本表的创建、修改、删除

相关链接：[mysql/mariadb知识点总结（9）：表管理语句-朱双印博客 (zsythink.net)](https://www.zsythink.net/archives/885)

#### ---创建表

```
CREATE TABLE  [IF NOT EXISTS] tbl_name[create_definition]
    
create_definition:
[字段定义|表级约束定义|索引定义]

字段定义 
column_name column_defination
    
column_defination:
[NOT NULL | NULL] [DEFAULT default_value] [AUTO_INCREMENT] [UNIQUE [KEY] | [PRIMARY] KEY] [COMMENT ‘string’]
/*AUTO_INCREMENT 表示对应字段使用自动增长，一个表中只有一个字段能被设置为自动增长，而且这个字段必须被定义为key（或者索引），mysql默认也会认为”自动增长的键字段”为主键字段。所以，结合着一个表中只能有一个主键的定义，auto_increment往往只针对于主键字段进行设置，因为一个表中如果已经存在主键，当我们对非主键的键字段定义auto_increment时会报错*/

表级别约束定义（key定义）
PRIMARY KEY(col1[,col2, ….]) //用于定义主键，一个表中只能有一个主键，一个主键可以包含多个字段。
UNIQUE KEY (col1[,col2, ….]) //用于定义唯一键，一个表中可以有多个唯一键。
FOREIGN KEY  //用于定义外键
CHECK(expr)  //用于定义检查性约束

索引定义（index定义）：
{INDEX|KEY}//如果key写在此位置，与index相同，表示定义索引，而不是定义key。
```

#### ---修改表

```
ALTER TABLE tbl_name [alter_specification [, alter_specification] …]
```

祥见修改表结构部分

#### ---删除表

```
DROP TABLE table_name;
```



## 实验三数据插入，查询，修改，删除

这里具体一点就是对于

[select](#SELECT)

[insert](##INSERT)

[update](##UPDATE)

[delete](##DELETE)

的使用

### 先来补充了解一下视图view

相关链接：[mysql/mariadb知识点总结（11）：视图管理语句-朱双印博客 (zsythink.net)](https://www.zsythink.net/archives/960)

视图是一个”虚表”,用大白话说，就是从已经存在的表的全部字段或数据中，挑选出来一部分字段或数据，组成另一张”并不存在的表”,这张虚表被称之”视图“

#### &创建视图

```
CREATE VIEW view_name(view_col1,...) AS [select]

CREATE OR REPLACE VIEW view_name(view_col1,...) AS [select]
/*create or replace ... as... 表示，如果view_name这个视图如果不存在，那么则按照指定的查询语句创建视图，如果当下已经这个视图，那么则使用当前视图覆盖之前的视图*/
//视图创建后，视图中的字段名与”基表”中的字段名称相同
```

**e.g.**将`stu_scores`表中成绩高于85的课程的`id_course,name_course,score_course`

组成名为`excellent`的视图

其中将上述三列简化为

`id,name,score`

```
//创建
CREATE VIEW excellent(id,name,score) 
AS SELECT id_course,name_course,score_course
FROM score.stu_scores
WHERE score_course>=85;
//查询
SELECT* FROM excellent;
```

![image-20211018204559968](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211018204559968.png)

#### ^删除视图

```
DROP VIEW [IF EXISTS] view_name;
```



### SELECT

```
//简单查询
SELECT * FROM [table_name|view_name|...];
SELECT * FROM [table_name|view_name|...] LIMIT 3;//显示查询出所有数据的前三行

//查找表或视图中的某几列
SELECT col1,col2... FROM [table_name|view_name|...];

//w
SELECT [DISTINCT(去除重复行)]
col1,col2... FROM <table_name|view_name|...> 
[WHERE <条件表达式>]
    [GROUP BY <column_name>[HAVING <条件表达式>]]
    	[ORDER BY <column_name>[ASC(升序)|DESC(降序)]]
//注意！！！
    //where子句中不能用聚集函数作为函数表达式
    
<条件表达式>:
比较：>,<,=,!=或<>(两者都是不等于)...(前面还可+NOT)
范围：BETWEEN AND,NOT BETWEEN AND
集合：IN,NOT IN
匹配：LIKE,NOT LIKE
空值：IS NULL,IS NOT NULL
逻辑：AND,OR,NOT
```

```
//查询的col1,col2...还可取别名 这样查询出来的结果属性栏可显示别名
SELECT col1 other_name,col2 other_name... FROM...
别名的方式
 column_name other_name 
 column_name = other_name
    
鲜活的例子：
   查询stu_scores中2019-2020学年中成绩不足75且学分不低于1.5的课程的名称，课程号，学分，并按成绩降序排列,其中全部取'_'前面部分为别名
    
SELECT 
name_course name,
id_course id,
value_course as value,
score_course as score
FROM stu_scores
WHERE schoolyear in('2019-2020-1','2019-2020-2') AND score_course < 75
AND value_course > 1.5
ORDER BY score_course DESC;
```

查询成功

![image-20211018213438337](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211018213438337.png)

#### 分组查询GROUP BY & HAVING

**e.g.**

按学号分组，查询其中成绩高于90的学生的所有课程信息

```
SELECT * FROM stu_scores
GROUP BY studentID
HAVING score_course>90;    
   //有问题！！！
```

![image-20211019162013046](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211019162013046.png)

仅仅查出一条记录

这里简要说明一下，曲解了GROUP BY 语句的正确用法，分组查询语句是依据我们给出的分组条件将源数据进行具体划分的语句，

条件：按学号分组，那么就是有多少个学号就分作多少组，这里之所以只有一条记录应该是having语句的问题，查到有一个符合要求，那么就停止了继续查找

正确方式应该是使用where

```
SELECT * FROM stu_scores
WHERE score_course>90; 
```

![image-20211019162531892](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211019162531892.png)



#### 聚集函数在select中的使用

先看一个例子：

查询各个学号成绩记录数，按照学号降序排列

```
SELECT studentID,COUNT(name_course)FROM stu_scores
GROUP BY studentID
order by studentID;
```

![image-20211018225857921](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211018225857921.png)

常用聚集函数如下

```
min(col)返回指定列的最小值

max(col)返回指定列的最大值

avg（col）返回指定咧的平均值

cout(*) 返回元组个数

count（col）返回指定列中非null值的个数

sum（col）返回指定列的所有值之和

group_concat(col)返回查询结果中所有指定列的值
```

#### 谓词

另外还可以使用许多谓词来扩充查询功能

```
IN
//子查询结果在某个集合中
//当查询结果唯一时候，可以用‘=’代替‘IN’

ANY,ALL
>ANY //大于子查询结果中的某个值，即大于最小值
<ANY //小于子查询结果中的某个值，即小于最大值
>ALL //大于子查询结果中的所有值，即大于最大值
<ALL //小于子查询结果中的所有值，即小于最小值
=ANY //等于子查询结果中某个值
...

EXISTS/NOT EXISTS
//EXISTS只产生逻辑值，ture or false

```



#### 1.多表查询

为方便起见我们先在`dbtest`数据库中创建另一张表，学生信息表`stu_info`

```
CREATE TABLE stu_info(
stu_ID varchar(10)PRIMARY KEY,/*学号为主码*/
stu_name varchar(20),/*姓名*/
stu_sex varchar(4)/*性别*/
);
```

并导入数据

![image-20211019165059356](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211019165059356.png)

```
LOAD DATA LOCAL INFILE 'E:\stu_name.txt' 
INTO TABLE stu_info
FIELDS TERMINATED BY ','  
LINES TERMINATED BY '\r\n';
```

![image-20211019170604877](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211019170604877.png)

导入成功！

#######这里解决一个bug

虽然在视图中看着没问题导入成功

但是在cmd界面中查看所有数据

![image-20211019171915396](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211019171915396.png)

1的前面出现了一个问好？，这就很郁闷，而且直接导致后面的交叉查询失败，也不是失败就1号学生由于这个问好？没能和stu_scores中的studentID相匹配

莫名想到之前导入时候的文档，是带有第一条表头的，于是随心加了一个试试

![image-20211019172129160](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211019172129160.png)

这时候清空stu_info数据，然后再在原先的导入命令中最后一行加上

```
IGNORE 1 LINES;
```

重新导入后

![image-20211019172308764](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211019172308764.png)

成功！原理尚不清楚

#### 2.连接查询

##### 1)内连接

等值连接（连接运算中运算符为=）

使用其他连接运算符的为非等值连接

两种连接方式组合起来就是‘内连接’

标准语法为

```
SELECT <what you want select> 
FROM <基本表|视图> INNER JOIN <基本表|视图>
ON <连接条件>

其中连接条件的子条件还可以用where
```

***e.g.***

查询学生成绩在90分以上的课程,按照成绩降序排列

这相当于是在两个表中根据学号这个共同点进行筛查，并将两表结果连接起来

```
SELECT stu_info.*,
name_course,score_course
FROM stu_info,stu_scores
WHERE 
stu_info.stu_ID=stu_scores.studentID
AND
score_course>90
ORDER BY score_course DESC;

//标准化：
SELECT stu_info.*,
name_course,score_course
FROM stu_info INNER JOIN stu_scores
ON 
stu_info.stu_ID=stu_scores.studentID
AND
score_course>90
ORDER BY score_course DESC;  

//值得注意的是如果遇到两表中共有的字段，需要加以表名前缀以示区别，这里仅仅只是使用一下，没有其他意思，因为stu_ID和studentID本身就不同，可以区别开
```

![image-20211019172612705](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211019172612705.png)

(唐三同学成绩针不错！)

##### 2)自身连接

在同一张表内的连接查询

以`stu_scores`为例

//只是随便写一个并无实际意义

//注意是在一张表中操作所以为了区别就需要取别名

```
SELECT FIRST.studentID,
SECOND.studentID,
FROM stu_scores FIRST INNER JOIN stu_scores SECOND
ON FIRST.studentID=SECOND.studentID;
```

##### 3)外连接

分为两种：`left join` ， `right join`

- 将`stu_info`和`stu_scores`左外连接

- ```
  //将学号为1的学生课程信息和学号信息左外连接
  SELECT stu_info.*,name_course,nature_course
  FROM stu_info LEFT OUTER JOIN stu_scores
  ON stu_ID=studentID AND stu_ID='1';
  ```

  ![image-20211019221618664](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211019221618664.png)

  这里有个值得注意的点，由于是将`stu_info`去左外连接`stu_scores`所以把其中没有找到匹配项的记录都以NULL填充,可以预见，若左表中所有查找项均得到匹配，就不会有NULL值

  而这里如果换成连接查询就很有意思**（inner）**

  ```
  SELECT stu_info.*,name_course,nature_course
  FROM stu_info INNer JOIN stu_scores
  ON stu_ID=studentID AND stu_ID='1';
  ```

  ![image-20211019221835061](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211019221835061.png)

  可以看见这里就没有NULL值

- 同理右外连接就是将连接词换成`RIGHT OUTER`

#### 3.嵌套查询

一个

```
SELECT-FROM-WHERE
```

语句称为一个查询块

将一个查询块嵌套在另一个查询块的`WHERE`子句或`HAVING`子句中称为嵌套查询

```
//查找平均成绩大于80的学生及其学号信息
SELECT DISTINCT studentID,stu_name,stu_sex
FROM stu_scores,stu_info
WHERE studentID IN
(SELECT studentID 
FROM stu_scores
GROUP BY studentID 
HAVING AVG(score_course)>80
)
AND studentID=stu_ID;

```

![image-20211019225225230](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211019225225230.png)



#### 4.集合查询

`UNION` 并

`INTERSECT` 交

`EXCEPT` 差

旨在将多个`SELECT`语句的结果进行集合操作

***e.g.***

```
查询学分高于2分的专业必修课程信息
SELECT * FROM stu_scores
WHERE nature_course='专业必修'
UNION
SELECT * FROM stu_scores
WHERE value_course>2;

```

![image-20211020151648057](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211020151648057.png)

不仅如此，结合上述的

#### 5.派生表子查询

当子查询出现在FROM子句中，这时子查询生成临时派生表成为著查询的查询对象

***e.g.***[回想上述中嵌套查询部分](####3.嵌套查询)

在查询结果中我们只能看到基本信息，没有成绩，不够直观

下面来一个高级的方法，将成绩也显示出来

```
//查询平均成绩高于80的学生信息
SELECT avg_id,stu_name,stu_sex,avg_grade
FROM stu_info,
//这是一个派生子表查询，就是将查询结果作为一张表，提供给FRO子句，需另起表名
//将数据按照学号分组，求平均成绩，得到子表
(SELECT studentID,AVG(score_course) 
FROM stu_scores 
GROUP BY studentID
HAVING AVG(score_course)>80)
AS avg_stu(avg_id,avg_grade)//子表名（列名，列名...）
//这里avg_stu即为子查询结果表名，
//后面括号内两列属性分别对应select后的studentID,AVG(score_courses)

WHERE avg_id=stu_ID//取别名之后需使用别名，这里不能使用studentID
ORDER BY avg_grade DESC;
```

![image-20211020154426927](https://raw.githubusercontent.com/Yolo-hwt/csdnPicAtGithub/image-20211020154426927.png)



### INSERT

插入数据

1.往table_name表中插入一条记录

```
INSERT INTO <table_naem> [(col1,col2...)]
VALUES(valueOfcol1,value2Ofcol2...);

//也可以不指定字段，表示对每个字段都有值插入
INSERT INTO <table_naem>
VALUES(value_all_columns...);

//还可以使用set方法
INSERT INTO <table_naem>
SET col1=valueOfcol1,...;
```

2.插入子查询结果

实际上是insert和select语句的联用

```
INSERT INTO <table_naem> [(col1,col2...)][子查询];
```

***e.g.***

[戳这里看上面已经用过的实例](#2.填充`stu_failed`数据)

### UPDATE

修改数据操作

```
UPDATE table_name SET col1=value1,col2=value2...
WHERE <条件>;
```

### DELETE

删除数据

```
DELETE FROM table_naem
[WHERE <条件>]

//如若不带条件的使用delete那就直接将表格清空
delete from tab1

//灵活使用条件
//从tb1表中找出age>30的数据行，然后将这些行按照age进行降序排列，排列后删除第一个
delete from tb1 where age > 30 order by age desc limit 1;
```



# 后续补充

-------

- 日常错误

  - 在你逐个字母排查还是不知道sql语句中的语法错误，然而他还是一直报错，在你用到的名称里面用反引号``把他框起来，因为很有可能用到了mysql的保留字
- 待续...

