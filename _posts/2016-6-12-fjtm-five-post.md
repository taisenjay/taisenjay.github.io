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

## 3.3、Android中的数据库升级 ##

修改数据表，比如给原来的表增加一个字段，必须修改版本号（往上升级）。版本号变大后，onUpgrade才会被调用。

# 4、数据库框架ActiveAndroid

* 在AndroidManifest中配置数据库名和版本号
* 在Application类中初始化ActiveAndroid

使用示例：
{% highlight java %}

	@Table (name = "students")
	public class Student extends Model{
		@Column(name="sid",unique=true)
		public long id;
		@Column
		public String name
		@Column
		public String tel_no
		@Column
		public int cls_id;
	}

{% endhighlight %}

调用Model对象的save方法就会将实体类中的数据存储到表中。

ActiveAndroid实际的核心就是通过注解与反射将Model类型中的字段与SQL语句建立一个双向关系，然后在查询时将数据从Cursor映射到Model的字段中，而在插入是则将Model中的字段值转换为SQL语句，最后提交到数据库中执行。但是ActiveAndorid通过运行时注解与反射的形式实现数据库引擎效率并不是最高，更高效的实现是通过编译时注解，在编译时生成辅助类，通过这些辅助类完成数据库，表的创建以及Model与SQL之间的映射。利用编译时注解的数据库开源框架有GreenDao以及DBFlow.