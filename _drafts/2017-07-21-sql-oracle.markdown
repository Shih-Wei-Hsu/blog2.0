---
layout: post
title:  "oracle表空间与用户相关知识"
date:   2017-07-21 16:00:00 +0800
categories: xsw sql
description: "oracle表空间,oracle用户,oracle"
keywords: oracle表空间,oracle用户,oracle
---

查看当前用户的缺省表空间

　　SQL>select username,default_tablespace from user_users;

　　查看当前用户的角色

　　SQL>select * from user_role_privs;

　　查看当前用户的系统权限和表级权限

　　SQL>select * from user_sys_privs;

　　SQL>select * from user_tab_privs;

　　查看用户下所有的表

　　SQL>select * from user_tables;

　　1、用户

　　查看当前用户的缺省表空间

　　SQL>select username,default_tablespace from user_users;

　　查看当前用户的角色

　　SQL>select * from user_role_privs;

　　查看当前用户的系统权限和表级权限

　　SQL>select * from user_sys_privs;

　　SQL>select * from user_tab_privs;

　　显示当前会话所具有的权限

　　SQL>select * from session_privs;

　　显示指定用户所具有的系统权限

　　SQL>select * from dba_sys_privs where grantee='GAME';

　　2、表

　　查看用户下所有的表

　　SQL>select * from user_tables;

　　查看名称包含log字符的表

　　SQL>select object_name,object_id from user_objects

　　where instr(object_name,'LOG')>0;

　　查看某表的创建时间

　　SQL>select object_name,created from user_objects where object_name=upper('&table_name');

　　查看某表的大小

　　SQL>select sum(bytes)/(1024*1024) as "size(M)" from user_segments

　　where segment_name=upper('&table_name');

　　查看放在Oracle的内存区里的表

　　SQL>select table_name,cache from user_tables where instr(cache,'Y')>0;

　　3、索引

　　查看索引个数和类别

　　SQL>select index_name,index_type,table_name from user_indexes order by table_name;

　　查看索引被索引的字段

　　SQL>select * from user_ind_columns where index_name=upper('&index_name');

　　查看索引的大小

　　SQL>select sum(bytes)/(1024*1024) as "size(M)" from user_segments

　　where segment_name=upper('&index_name');

　　4、序列号

　　查看序列号，last_number是当前值

　　SQL>select * from user_sequences;

　　5、视图

　　查看视图的名称

　　SQL>select view_name from user_views;

　　查看创建视图的select语句

　　SQL>set view_name,text_length from user_views;

　　SQL>set long 2000; 说明：可以根据视图的text_length值设定set long 的大小

　　SQL>select text from user_views where view_name=upper('&view_name');

　　6、同义词

　　查看同义词的名称

　　SQL>select * from user_synonyms;

　　7、约束条件

　　查看某表的约束条件

　　SQL>select constraint_name, constraint_type,search_condition, r_constraint_name

　　from user_constraints where table_name = upper('&table_name');

　　SQL>select c.constraint_name,c.constraint_type,cc.column_name

　　from user_constraints c,user_cons_columns cc

　　where c.owner = upper('&table_owner') and c.table_name = upper('&table_name')

　　and c.owner = cc.owner and c.constraint_name = cc.constraint_name

　　order by cc.position;

　　8、存储函数和过程

　　查看函数和过程的状态

　　SQL>select object_name,status from user_objects where object_type='FUNCTION';

　　SQL>select object_name,status from user_objects where object_type='PROCEDURE';

　　查看函数和过程的源代码

　　SQL>select text from all_source where owner=user and name=upper('&plsql_name');

 

-------------------------------------------------------------------------------------------------------------------------------------

 

oracle：


----常用数据字典

USER_   
  记录用户对象的信息，如user_tables包含用户创建的所有表； user_views,user_constraints等;
ALL_   
记录用户对象的信息及被授权访问的对象信息；
DBA_
  记录数据库实例的所有对象的信息，如DBA_USERS包含数据库实例中
所有用户的信息，DBA的信息包含user和all的信息；
V$	当前实例的动态视图，包含系统管理和优化使用的视图；
GV_	分布环境下所有实例的动态视图，包含系统管理和优化使用的视图，	这里的GV表示 Global v$的意思；
 如当前分布环境下启动多个实例：
set line 160 (每行显示160个字符)
select inst_id,sid,lmode from gv$lock where inst_id = 1 or inst_id=2;

oracle常用数据字典，oracle10增加了不少数据字典10g r1 有1713个，r2有1870个：
1 基本的数据字典：
DBA_TABLES 所有用户的所有表的信息；
DBA_TAB_COLUMNS 所有用户的表的列(字段)信息；
DBA_VIEWS 所有用户的所有视图信息；
DBA_SYNONYMS 所有用户同义词信息；
DBA_SEQUENCES 所有用户序列信息；
DBA_CONSTRAINTS 所有用户的表约束信息；
DBA_INDEXES 所有用户索引的简要信息；
DBA_IND_COLUMNS 所有用户索引的列信息；
DBA_TRIGGERS 所有用户触发器信息 ；
DBA_SOURCE所有用户存储过程源代码信息；
DBA_PROCEDUS 所有用户存储过程；
DBA_SEGMENTS 所有用户段（表，索引，Cluster）使用空间信息；
DBA_EXTENTS 所有用户段的扩展段信息；
DBA_OBJECTS 所有用户对象的基本信息（包括素引，表，视图，序列等）；
CAT 当前用户可以访问的所有的基表 ；
TAB 当前用户创建的所有基表，视图，同义词等；
DICT 构成数据字典的所有表的信息；


2 与数据库组件相关的数据字典：
数据库：
  V$DATABASE 同义词 V_$DATABASE,记录系统的运行情况；
表空间：  
DBA_TABLESPACES 记录系统表空间的基本信息；
DBA_DATA_FILES 记录系统数据文件及表空间的基本信息；
DBA_FREE_SPACE 记录系统表空间的剩余空间的信息；
控制文件：
V$CONTROLFILE 记录系统控制文件的路径信息；
V$PARAMETER 记录系统各参数的基本信息；
v$CONTROLFILE_RECORD_SECTION 记录系统控制运行的基本信息；
数据文件：
DBA_DATA_FILES 记录系统数据文件及表空间的基本信息；
v$DATAFILE 记录来自控制文件的数据文件信息；
v$FILESTAT 记录数据文件读写的基本信息 ；




    
--查看VGA信息：

show sga;

select * from v$sgastat;

--可以通过以下几个动态性能视图查看信息：
V$sysstat 系统统计信息
V$sesstat 用户会话统计信息  
V$pgastat 显示内存使用统计信息
V$sql_workarea SQL游标所用工作区的信息
V$ sql_workarea_active 当前系统工作区的信息

--在V$process动态性能视图中可以查询到每个Oracle进程的PGA分配的内存和已使用的内存情况，
--其中PGA_used_mem表示已使用的，pag_alloc_mem表示已分配的，pga_max_men表示PGA的最大值。

SQL> select pid,pga_used_mem,pga_alloc_mem,pga_max_mem from v$process;

--查看后台进程：

SELECT * FROM v$bgprocess WHERE paddr <> '00';

--查看所有的表空间；

SQL> select tablespace_name from dba_data_files order by tablespace_name;

--查看表空间的名字及大小:
SQL> select t.tablespace_name, round(sum(bytes/(1024*1024)),0) ts_size   
from dba_tablespaces t, dba_data_files d where t.tablespace_name = d.tablespace_name   
group by t.tablespace_name;

--创建表空间的语法是：
CREATE TABLESPACE tablespacename
DATAFILE 'filename' [SIZE integer [K|M]]  
[AUTOEXTEND [OFF|ON]];
--创建有多个数据文件的表空间：
SQL> create tablespace SALES
datafile 'd:/sales/SALES_DATA01.dbf' size 10m autoextend on next 10m maxsize 100m,
'd:/sales/SALES_DATA02.dbf' size 10m autoextend on next 10m maxsize
unlimited,'d:/sales/SALES_DATA03.dbf' size 10m;

--查看表空间的大小；

SQL> SELECT TABLESPACE_NAME,SUM(BYTES)/1024/1024 MB FROM DBA_FREE_SPACE GROUP BY TABLESPACE_NAME;

--查看表空间中数据文件存放的路径：

SQL> SELECT TABLESPACE_NAME, BYTES/1024/1024 FILE_SIZE_MB, FILE_NAME FROM DBA_DATA_FILES;


删除表空间：
SQL> drop tablespace worktbs including contents;
表空间已丢弃。

再次查询表空间会发现已经没有worktbs表空间了。
如果我们再次删除表空间会出现什么后果？
删除一个不存在的表空间：
SQL> drop tablespace worktbs including contents;
drop tablespace worktbs including contents
*
ERROR 位于第 1 行:
ORA-00959: 表空间'WORKTBS'不存在
o	为表空间增加数据文件
SQL> alter tablespace sales add datafile 'd:/oracle/oradata/test/testtablespace/
sales_data04.dbf' size 10m autoextend on next 10m maxsize 100m,
'c:/oracle/oradata/test/testtablespace/sales_data05.dbf' size 10m autoextend on next 10m maxsize unlimited, 'c:/oracle/oradata/test/testtablespace/sales_data06.dbf' size 10m;

表空间已更改。
o	修改数据文件的大小
o	在数据文件中的可用空间未使用完时，允许对数据文件进行压缩，当压缩空间大小超过未使用空间大小时，将会产生错误。
SQL> alter database datafile 'c:/oracle/oradata/test/testtablespace/SALES_DATA04.dbf' resize 30m;

数据库已更改。
o	关闭表空间数据文件的自动扩展属性
o	alter database
o	datafile 'c:/SALES_DATA04.dbf' ,
o	'c:/SALES_DATA05.dbf',
o	'c:/SALES_DATA06.dbf'
o	autoextend off;
o	打开表空间数据文件的自动扩展属性
o	alter database
o	datafile 'c:/SALES_DATA04.dbf' ,
o	'c:/SALES_DATA05.dbf',
o	'c:/SALES_DATA06.dbf'
o	autoextend on;

o	修改表空间属性(离线)
o	alter tablespace sales offline;   
o	修改表空间属性(在线)
o	alter tablespace sales online;
o	修改表空间属性(只读)
o	alter tablespace sales read only;
o	修改表空间属性(读写)
o	alter tablespace sales read write;
o	如下系统表空间不得设置为 offline 或者 read only
o	system ， temp，undo ，undotbs  

o	移动表空间的数据文件
o	1. 使表空间脱机（系统表空间不得脱机，故不得移动）
o	2. 修改数据文件的名称
o	3. 执行 alter tablespace rename datafile 命令
o	4. 使表空间联机
o	第一步：alter tablespace sales offline；   
o	第二步：物理移动数据文件到目的地(可以是表空间的
o	部分数据文件,可以修改数据文件的名称)
o	第三步: 逻辑移动，修改控制文件的内容

o	alter tablespace sales rename  
o	datafile 'c:/sales01.dbf' to 'd:/sales02.dbf'
o	--可以有多个数据文件，但是源文件
o	要在to的左边，目的文件要在to 右边,文件名称之间逗号分割.
o	第四步: 将表空间联机
o	alter tablespace sales online;
o	第五步：查询dba_data_files确认

o	创建用户，指定默认表空间，磁盘配额
o	create user rose identified by rose default
o	tablespace sales quota 10m on sales;
o	给用户授权
o	grant connect,resource,dba to rose;
o	用户登录
o	connect rose/rose
o	创建表
o	create table emp(eid number)  
o	该表默认放在表空间 sales 中
o	查询用户的存储限额dba_ts_quotas  
o	删除表空间(如果该表空间为空)
o	drop tablespace sales ;
o	删除表空间的同时删除数据文件(不为空)
o	drop tablespace sales including contents and datafiles;
o	创建表的同时指定该表的存储位置
o	create table mytab(tid int ) tablespace sales;

o	删除表空间(如果该表空间为空)
o	drop tablespace sales ;
o	删除表空间的同时删除数据文件(不为空)
o	drop tablespace sales including contents and datafiles;
o	创建表的同时指定该表的存储位置
o	create table mytab(tid int ) tablespace sales;


o	查看当前用户每个表占用空间的大小：
o	select segment_name,sum(bytes)/1024/1024 from user_extents group by segment_name  
o	查看每个表空间占用空间的大小：
o	select tablespace_name,sum(bytes)/1024/1024 from dba_segments group by tablespace_name  


只有用合法的用户帐号才能访问Oracle数据库
Oracle 有几个默认的数据库用户   
Scott/tiger

创建一个名称为 martin 的用户，其密码为 martinpwd
CREATE USER MARTIN IDENTIFIED BY martinpwd
DEFAULT TABLESPACE USERS
TEMPORARY TABLESPACE TEMP;

GRANT 命令可用于为用户分配权限或角色；
CONNECT角色允许用户连接至数据库，并创建数据库对象。
GRANT CONNECT TO MARTIN;  

RESOURCE角色允许用户使用数据库中的存储空间。
GRANT RESOURCE TO MARTIN;  

此系统权限允许用户在当前模式中创建序列，此权限包含在CONNECT角色中。
GRANT CREATE SEQUENCE TO MARTIN;  

GRANT CREATE SESSION TO MARTIN;

GRANT CREATE TABLE TO MARTIN;

GRANT CREATE VIEW TO MARTIN;

GRANT CREATE SEQUENCE TO MARTIN;



授予用户 MARTIN 操作emp表对象的权限 ：
允许用户查询 TEST 表的记录
GRANT SELECT ON EMP TO MARTIN;  

允许用户更新 TEST 表中的记录
GRANT UPDATE ON EMP TO MARTIN;  

允许用户插入、删除、更新和查询 TEST 表中的记录
GRANT ALL ON EMP TO MARTIN;  


ALTER USER 命令可用于更改口令：
修改 MARTIN 用户的密码：
ALTER USER MARTIN IDENTIFIED BY martinpass;
DROP USER 命令用于删除用户：  
删除 MARTIN 用户模式：
DROP USER MARTIN CASCADE;


alter session set nls_date_format='yyyy-mm-dd';



1开始创建数据库：

----------------使用工具创建 （步骤省略）

2登陆新数据库  
run/sqlplus sys/sys@ACCP
(此时如果登陆老数据库使用 sqlplus sys/sys@orac9i)
2创建表空间：sales (用来保存accp数据库的数据)
create tablespace TestTBS datafile 'd:/sales/sales001.dbf' size 10m autoextend on next 10m maxsize 30m,'d:/sales/sales002.dbf' size 10m autoextend on next 10m maxsize 30m
3创建用户accp并授予权限
grant connect to accp;
grant resource to accp;
4以accp用户身份登陆accp数据库

create table person(pid number(5),pname varchar2(20)) tablespace testtbs;
insert into person values(1001,'TOM');
commit;
select table_name,tablespace_name from user_tables;
select * from person;

