安装Mysql时会自动安装一个名为**mysql的数据库**。这个数据库下面存储的是**权限表**。

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| mysql              |
| test               |
+--------------------+
```

Mysql会根据这些权限表的内容为每个用户赋予相应的权限  
所以我们也是通过这些表来实现管理用户和权限的

```
use mysql;
show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| host                      |
| innodb_index_stats        |
| innodb_table_stats        |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| servers                   |
| slave_master_info         |
| slave_relay_log_info      |
| slave_worker_info         |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
```

## user表

1. user表的作用相当与mysql工作流程中的"用户管理模块"，它决定着我们对一个连接允许或拒绝

2.**user表用于记录允许连接到服务器的用户帐号信息，里面的对于数据库的权限是全局生效的**  


3. 根据user表结构，表的字段可以分为4类  
\(1\) 用户列

> Host          主机名   ——&gt;如果想要从别的机器来登入数据库，Host可以设成 % 
>
> User          用户名
>
> Password  密码
>
> 用户登录时，首先要判断这三个字段是否匹配，匹配则允许登录；
>
> 用户创建时，也是设置这三个字段的值；
>
> 修改用户密码时，实际也是修改了user表的Password字段的值。

\(2\) 权限列\(以priv结尾的\)

> * Grant\_priv 
>          是否有Grant权限
> * Shutdown\_priv
>     是否有停止mysql服务的权限
> * Super\_priv 
>         是否有超级权限
> * Execute\_priv
>       是否有执行存储过程和函数的权限
>
> 包含普通权限:查询权限、修改权限等 操作数据库的动作；
>
> 包含高级管理权限:关闭服务权限、超级权限、加载用户等 管理数据库的动作；
>
> 这些字段只有N和Y两个选项，为安全起见默认值都设为N；
>
> 对这些权限的管理可以使用GRANT语句、也可以通过UPDATE user表的这些列来实现。

\(3\) 安全列

> ssl用于加密，不过一般的发行版本并不支持ssl
>
> ```
> mysql
> >
> show variables LIKE'have_openssl';
>
> +---------------+----------+
> | Variable_name | Value    |
> +---------------+----------+
> | have_openssl  | DISABLED |  ——
> >
> DISABLED表示没有支持ssl加密功能
> +---------------+----------+
> 1 row in set (0.03 sec)
> ```
>
> x509标准可以用来标识用户

\(4\) 资源控制列  

> * max\_questions
>       每小时允许执行多少次查询
> * max\_updates
>          每小时允许执行多少次更新
> * max\_connections
>    每小时允许建立多少连接
> * max\_user\_connections
>    单个用户可以同时具有的连接数
>
> 这些字段默认值为0，表示没有限制

```
desc user;

+------------------------+-----------------------------------+------+-----+----
| Field                  | Type          | Null | Key | Default   | Extra |
+------------------------+-----------------------------------+------+-----+---
| 
Host 
                  | 
char(60)      | NO   | PRI |                       |       |
| 
User
                   | char(16)      | NO   | PRI |                       |       |
| 
Password 
              | char(41) 
     | NO   |     |                       |       |
| 
Select_priv
            | 
enum('N','Y') | NO   |     | N                     |       |
| 
Insert_priv
            | enum('N','Y') | NO   |     | N                     |       |
| 
Update_priv
            | enum('N','Y') | NO   |     | N                     |       |
| 
Delete_priv
            | enum('N','Y') | NO   |     | N                     |       |
| 
Create_priv
            | enum('N','Y') | NO   |     | N                     |       |
| 
Drop_priv
              | enum('N','Y') | NO   |     | N                     |       |
| 
Reload_priv
            | enum('N','Y') | NO   |     | N                     |       |
| 
Shutdown_priv
          | enum('N','Y') | NO   |     | N                     |       |
|
 Process_priv
           | enum('N','Y') | NO   |     | N                     |       |
| 
File_priv
              | enum('N','Y') | NO   |     | N                     |       |
| 
Grant_priv 
            | enum('N','Y') | NO   |     | N                     |       |
| 
References_priv
        | enum('N','Y') | NO   |     | N                     |       |
| 
Index_priv 
            | enum('N','Y') | NO   |     | N                     |       |
| 
Alter_priv
             | enum('N','Y') | NO   |     | N                     |       |
| 
Show_db_priv
           | enum('N','Y') | NO   |     | N                     |       |
|
 Super_priv
             | enum('N','Y') | NO   |     | N                     |       |
| 
Create_tmp_table_priv
  | enum('N','Y') | NO   |     | N                     |       |
| 
Lock_tables_priv
       | enum('N','Y') | NO   |     | N                     |       |
| 
Execute_priv
           | enum('N','Y') | NO   |     | N                     |       |
| 
Repl_slave_priv
        | enum('N','Y') | NO   |     | N                     |       |
| 
Repl_client_priv 
      | enum('N','Y') | NO   |     | N                     |       |
| 
Create_view_priv
       | enum('N','Y') | NO   |     | N                     |       |
| 
Show_view_priv
         | enum('N','Y') | NO   |     | N                     |       |
| 
Create_routine_priv
    | enum('N','Y') | NO   |     | N                     |       |
| 
Alter_routine_priv
     | enum('N','Y') | NO   |     | N                     |       |
|
 Create_user_priv
       | enum('N','Y') | NO   |     | N                     |       |
| 
Event_priv
             | enum('N','Y') | NO   |     | N                     |       |
| 
Trigger_priv  
         | enum('N','Y') | NO   |     | N                     |       |
| 
Create_tablespace_priv
 | enum('N','Y') | NO   |     | N                     |       |
| 
ssl_type
               | enum('','ANY','X509','SPECIFIED') |
 NO             |       |          
| 
ssl_cipher
             | blob          | NO   |     | NULL                  |       |
| 
x509_issuer
            | blob          | NO   |     | NULL                  |       |
| 
x509_subject
           | blob          | NO   |     | NULL                  |       |
|
 max_questions 
         | 
int(11) unsigned | NO   |     | 0                     |       |
| 
max_updates
            | int(11) unsigned | NO   |     | 0                     |       |
| 
max_connections
        | int(11) unsigned | NO   |     | 0                     |       |
| 
max_user_connections
   | int(11) unsigned | NO   |     | 0                     |       |
| 
plugin
                 | char(64)         | YES  |     | mysql_native_password |       |
| 
authentication_string
  | text             | YES  |     | NULL                  |       |
| 
password_expired
       | enum('N','Y')    | NO   |     | N  
                   |       |
+------------------------+-----------------------------------+------+-----+----------------
```







## db表

1.用户被允许连接了，但是是不是可以就能操作所有数据库了呢？  


2. 所以我们需要**db表/host表，用来规定某一个用户对一个数据库的权限**  


3. db表的字段分为两类:  
\(1\)用户列

> * Host
>      主机名
> * Db 
>       数据库名
> * User
>      用户名

\(2\)权限列      

用户是先根据user表的内容获得权限，再根据db表的内容获取权限          

例如，user表中某用户的Select\_priv字段为‘N’,表示所有数据库中的表它都无权查询

但db表中这一用户对student表的Select\_priv字段设为了‘Y’,表示它只有查询student表的权限  


```
desc db;

+-----------------------+---------------+------+-----+---------+-------+
| Field                 | Type          | Null | Key | Default | Extra |
+-----------------------+---------------+------+-----+---------+-------+
| 
Host 
                 | 
char(60)      | NO   | PRI |         |       |
|
 Db
                    | char(64)      | NO   | PRI |         |       |
| 
User
                  | char(16)
      | NO   | PRI |         |       |
| Select_priv           | 
enum('N','Y') | NO   |     | N       |       |
| Insert_priv           | enum('N','Y') | NO   |     | N       |       |
| Update_priv           | enum('N','Y') | NO   |     | N       |       |
| Delete_priv           | enum('N','Y') | NO   |     | N       |       |
| Create_priv           | enum('N','Y') | NO   |     | N       |       |
| Drop_priv             | enum('N','Y') | NO   |     | N       |       |
| Grant_priv            | enum('N','Y') | NO   |     | N       |       |
| References_priv       | enum('N','Y') | NO   |     | N       |       |
| Index_priv            | enum('N','Y') | NO   |     | N       |       |
| Alter_priv            | enum('N','Y') | NO   |     | N       |       |
| Create_tmp_table_priv | enum('N','Y') | NO   |     | N       |       |
| Lock_tables_priv      | enum('N','Y') | NO   |     | N       |       |
| Create_view_priv      | enum('N','Y') | NO   |     | N       |       |
| Show_view_priv        | enum('N','Y') | NO   |     | N       |       |
| Create_routine_priv   | enum('N','Y') | NO   |     | N       |       |
| Alter_routine_priv    | enum('N','Y') | NO   |     | N       |       |
| Execute_priv          | enum('N','Y') | NO   |     | N       |       |
| Event_priv            | enum('N','Y') | NO   |     | N       |       |
| Trigger_priv          | enum('N','Y') | NO 
  |     | N       |       |
+-----------------------+---------------+------+-----+---------+-------+
```







## tables\_priv表和columns\_priv表

1. 同理，即便用户有权对某一数据库操作，那么是不是对所有表都可以操作?对表中的所有记录都可以操作?  
2.**tables\_priv表用来对单个表进行权限设置**  
3.**columns\_priv表用来对单条记录\(列\)进行权限设置**  
4. 其包含的字段主要有：

> * Host
>                    主机名
> * Db
>                       数据库名
> * User
>                    用户名
> * Table\_name     
>   表名
> * column\_name
>     表示可以对哪些数据列进行操作
> * Table\_priv
>           对表进行操作的权限\(select,insert,update,delete,create,drop,grant,references,index,alter\)
> * Column\_priv    
>   对记录进行操作的权限\(select,insert,update,references\)
> * Timestamp
>          修改权限的时间
> * Grantor
>                权限的设置者

```
mysql
>
desc tables_priv;

+-------------+-----------------------------------------------------------------------------------------------------------------------------------+------+-----+-------------------+-----------------------------+
| Field       | Type                                                                                                                              | Null | Key | Default           | Extra                       |
+-------------+-----------------------------------------------------------------------------------------------------------------------------------+------+-----+-------------------+-----------------------------+
| 
Host
        | char(60)                                                                                                                          | NO   | PRI |                   |                             |
| 
Db
          | char(64)                                                                                                                          | NO   | PRI |                   |                             |
| 
User
        | char(16)                                                                                                                          | NO   | PRI |                   |                             |
| 
Table_name
  | char(64)                                                                                                                          | NO   | PRI |                   |                             |
| 
Grantor
     | char(77)                                                                                                                          | NO   | MUL |                   |                             |
| Timestamp   | timestamp                                                                                                                         | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
| 
Table_priv
  | set('Select','Insert','Update','Delete','Create','Drop','Grant','References','Index','Alter','Create View','Show view','Trigger') | NO   |     |                   |                             |
| 
Column_priv
 | set('Select','Insert','Update','References')                                                                                      | NO   |     |                   |                             |
+-------------+-----------------------------------------------------------------------------------------------------------------------------------+------+-----+-------------------+-----------------------------+
8 rows in set (0.01 sec)
```

```
mysql
>
desc columns_priv;

+-------------+----------------------------------------------+------+-----+-------------------+-----------------------------+
| Field       | Type                                         | Null | Key | Default           | Extra                       |
+-------------+----------------------------------------------+------+-----+-------------------+-----------------------------+
| 
Host
        | char(60)                                     | NO   | PRI |                   |                             |
| 
Db
          | char(64)                                     | NO   | PRI |                   |                             |
| 
User
        | char(16)                                     | NO   | PRI |                   |                             |
| 
Table_name
  | char(64)                                     | NO   | PRI |                   |                             |
| 
Column_name
 | char(64)                                     | NO   | PRI |                   |                             |
| Timestamp   | timestamp                                    | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
| 
Column_priv
 | set('Select','Insert','Update','References') | NO   |     |                   |                             |
+-------------+----------------------------------------------+------+-----+-------------------+-----------------------------+
7 rows in set (0.00 sec)
```







## procs\_priv表

1. 对存储过程和存储函数进行权限设置

2. 主要字段：

> * Host
>                    主机名
> * Db
>                       数据库名
> * User
>                    用户名
> * Routine\_name
>   存储过程/存储函数的名字
> * Routine\_type
>      标识它是FUNCTION\(存储函数\)还是PROCEDURE\(存储过程\)
> * Proc\_priv
>            拥有的权限\(Execute、Alter Routine、Grant\)
> * Timestamp
>          更新的时间
> * Grantor
>               权限是谁设置的

```
mysql
>
 desc procs_priv;

+--------------+----------------------------------------+------+-----+-------------------+-----------------------------+
| Field        | Type                                   | Null | Key | Default           | Extra                       |
+--------------+----------------------------------------+------+-----+-------------------+-----------------------------+
| 
Host
         | char(60)                               | NO   | PRI |                   |                             |
| 
Db
           | char(64)                               | NO   | PRI |                   |                             |
| 
User
         | char(16)                               | NO   | PRI |                   |                             |
| 
Routine_name
 | char(64)                               | NO   | PRI |                   |                             |
| 
Routine_type
 | enum('FUNCTION','PROCEDURE')           | NO   | PRI | NULL              |                             |
| 
Grantor
      | char(77)                               | NO   | MUL |                   |                             |
| 
Proc_priv
    | set('Execute','Alter Routine','Grant') | NO   |     |                   |                             |
| 
Timestamp
    | timestamp                              | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
+--------------+----------------------------------------+------+-----+-------------------+-----------------------------+
8 rows in set (0.00 sec)
```







## 用户的权限分配规则

\(1\)**Mysql的权限分配是按照user表---&gt;db表---&gt;tables\_priv表---&gt;columns\_priv表的顺序进行分配的**  
\(2\)如果user表中某一权限的值为Y，就不需要检查往后的表了  
\(3\)如果user表中某一权限的值为N，则依次往后检查每一张表  
  








## Mysql的各种权限

![](https://images2017.cnblogs.com/blog/821187/201710/821187-20171011145738605-275677926.jpg)





## 权限管理操作

### 1. 授权

> Methods-1
>
> &gt; grant            \[权限list\]                      ——&gt; 参考上表  
> on                 \[datebase-name.table-name\]  
>  to                  \['username'@'hostname'\]  
>   
> identified by  \['password'\]                  ——&gt; 如果是新创建的用户可以设置密码  
> with              \[options\];                      ——&gt; with关键字之后有5个可选项  
>   
> options：  
> 　　grant option：表示被授权的用户可以将这些权限赋予给别的用户  
> 　　max\_queries\_per\_hour \[count\]:设置每小时可以允许执行count次查询  
> 　　max\_updates\_per\_hour \[count\]:设置每小时可以允许执行count次更新  
> 　　max\_connections\_per\_hour \[count\]:设置每小时可以建立count连接  
> 　　max\_user\_connections \[count\]:设置单个用户可以同时具有的count个连接数
>
>
>
> Methods-2 授予一个用户全部权限
>
> **&gt; grant   all privileges  on  \[database-name.table-name\]  to  'username'@'hostname'  with grant option;**

### 2. 查看权限

> Methods-1
>
> **&gt; show  grants  for  'hostname'@'hostname' \G**
>
>
>
> Methods-2　　
>
> **&gt; select  \*  from  mysql.user  where  User='username'  and  Host='hostname' \G**

### 3. 取消权限

> &gt;revoke  \[权限list\]  
> on         \[datebase-name.table.name\]  
> from     \['username'@'hostname'\];
>
>
>
> 取消全部权限：
>
> &gt;revokeall privileges,grant option  
> from     'username'@'hostname';

### 4. 刷新\(加载\)权限

> &gt; flush privileges;



