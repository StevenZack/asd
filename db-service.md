# 
db-service这个模块主要是对SQLite数据库的一些常用的操作进行封装，方便使用。

# db-service 模块的代码目录结构
- [daoService/](#daoservice)
- [dbHelper/](#dbhelper)
- [repository/](#repository)
- [AtworkDatabaseHelper.java](#atworkdatabaseHelperjava)
- [BaseDbService.java](#basedbservicejava)
- [DbThreadPoolExecutor.java](#dbthreadpoolexecutorjava)

## __daoService/__

对数据库进行一些异步操作，相当于在repository里面的操作变成异步的

这个目录里面只包含一个FileDaoService类，这个类是继承自BaseDbService的，主要是实现“插入最近文件”功能的异步操作。

（我个人认为这个文件不应该放在这里，本应该和UserDaoService一起放在app模块的db.daoService目录下面的。）

## __dbHelper/__

数据结构的一些基础操作，主要是方便repository来解析数据的。

典型函数:  User fromCursor(Cursor cursor)

这个目录里面的所有类都是DBHelper这个接口的实现。DBHelper这个接口是实现了两个函数：onCreate(SQLiteDatabase db)函数、和onUpgrade(SQLiteDatabase db,int oldVersion.int newVersion)函数。

onCreate函数，用于创建相应的table。比如UserDBHelper类就实现了这个接口，他的onCreate函数里面执行的操作就是在数据库中创建一个User的table，这个table里面要包含我们需要的属性（user_id、avatar等）。

onUpgrade函数，用于更新数据库中的数据版本（个人理解，代码没太看懂）。第二个参数是int oldVersion，第三个参数是int newVersion

## __repository/__

各个table的增删改查等常用的基础操作。

典型函数: User queryUserByUserId(String userid)

## AtworkDatabaseHelper.java

对所有的DBHelper进行批量操作

## BaseDbService.java

一个包含DbThreadPoolExecutor实例的抽象类。主要是方便所有的DaoService访问DbThreadPoolExecutor

## DbThreadPoolExecutor.java

实现一个自己的线程池，方便来进行频繁的数据库异步操作。