---
layout: post
title: "独特高效的数据存储——SQLite数据库"
excerpt: "《Android开发进阶：从小工到专家》第5章读书笔记"
tags: [读书笔记，从小工到专家]
comments: true
---

# 1、SQLite3的基本介绍 #

SQLite的优点：

* 无需安装和配置
* 储存在单一磁盘文件中的一个完整的数据库
* 数据库文件可以在不同字节顺序的机器间自由共享
* 支持数据库大小至2TB
* 足够小，全部源代码大概3万行C代码，250KB
* 比目前流行的大多数数据库对数据的操作要快
* 开源

# 2、SQLite中的SQL语句 #

略

# 3、Android中的数据库开发 #

Android中封装的相关主要类有：SQLiteOpenHelper,SQLiteDatabase,Cursor。本质：构建Sql语言，提交到数据库中执行。

## 3.1、数据库基本类型与接口 ##

SQLiteOpenHelper,封装了数据库的创建与升级等工作，也可以通过其获取到实际操作数据库的SQLiteDatabase对象。其中的onCreate()、onUpgrade（）方法分别在创建、升级数据库时调用。在onCreate()中通过SQL语句创建表，在onUpgrade()时升级数据库。

SQLiteDateabase是SQLite数据库的管理类，提供了对SQLite数据库操作的接口，例如增删改查，事务，执行原生SQL等

* insertWithOnConflicit(String table,String nullColumnHack,ContentValues initialValues,int conflictAlgorithm),插入数据
* delete(String table,String whereClause,String[] whereArgs),删除数据
* updateWithOnConflict(String table,ContentValues values,String whereClause,String[] whereArgs,int conflictAlgorithm),修改数据
* Cursor query(String table,String[] columns,String selection,String[] selectionArgs,String groupBy,String having,String orderBy,String limit),查询数据

当执行查询语句时，返回的数据类型是Cursor，它是一个提供了随机读写访问数据库查询结果集的接口。（Cursor线程不安全）。

Android中使用数据库事务可以大幅提升速度：
不适用事务：插入1000条数据，执行过程重复1000次；
使用事务：创建事务->执行1000条语句->提交，一次完成。
缺点是，同一事务内的所有修改要么都完成，要么都不做，一条修改失败，就会自动回滚使所有修改不生效。