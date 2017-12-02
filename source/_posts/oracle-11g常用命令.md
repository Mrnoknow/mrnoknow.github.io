title: oracle 11g常用命令
date: 2016-04-14 17:08:27
categories: sql
tags: 
- other
- 读书笔记
---
```
drop table t1;（t1是表的名字）删除表空间

flashback table t1 to before drop；（t1是表的名字）找回删除的表空间

alter table student add year int 添加列
create table student(
name varchar(255),
year int
)       创建表空间
alter table student drop column year;   删除列
delete from student where name='fubiao';  删除列中的name 为fubiao的数据
delete from student 在不删除表空间的情况下，删除所有列
```