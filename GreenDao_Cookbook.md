# GreenDAO 学习笔记

索引：

- [介绍](#介绍)
- [安装](#安装)
- [使用](#使用)


## 介绍

GreenDAO:

    GreenDAO是一个轻量级&快速的Android平台上的ORM，他的主要作用是将Java里面的对象映射到SQLite里面的。可以大大简化Android开发时对SQLite的操作流程。

ORM

    Object Relational Mapping，即对象关系映射。

---

PS : 我个人是第一次接触ORM这个东西，我以一个新手的角度来理解ORM吧。

众所周知，SQL类的数据库操作起来很繁琐，当你想要插入一个raw数据的时候，你还需要新建一个与之对应的table，这个代码写出来非常繁琐，而且对于程序员的心智要求比较高，耗时耗力。我最开始也发现了这个问题，但是我的选择是转战NoSQL数据库去了，没有想过要自己写一个框架来实现SQL数据库操作的简化。所以我认为ORM诞生的目的就是为了简化SQL数据库的操作的。

## 安装

GreenDAO安装起来很简单

1.在你的Android Studio项目里的build.gradle(Project:YourProjectName)文件里面，添加一句

     classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2' 

添加之后是这个样子：

``` gradle
buildscript {
    repositories {
        jcenter()
        mavenCentral() // add repository
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.1.1'
        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2' // add plugin
    }
}
```

2.在build.gradle(module:app)里面添加这几句代码：
```gradle
apply plugin: 'com.android.application'
apply plugin: 'org.greenrobot.greendao' // apply plugin
 
dependencies {
    implementation 'org.greenrobot:greendao:3.2.2' // add library
}
```

## 使用

新建一个App类：
```java
//App.java
public class App  {
    private final Context context;
    private DaoMaster.DevOpenHelper mHelper;
    private SQLiteDatabase db;
    private DaoMaster mDaoMaster;
    private DaoSession mDaoSession;
    //静态单例
    public static App instances;

    public App(Context c){
        context=c;
        if (instances == null) {
            instances=this;
            setDatabase();
        }
    }
    /**
     * 设置greenDao
     */
    private void setDatabase() {
        // 通过 DaoMaster 的内部类 DevOpenHelper，你可以得到一个便利的 SQLiteOpenHelper 对象。
        // 可能你已经注意到了，你并不需要去编写「CREATE TABLE」这样的 SQL 语句，因为 greenDAO 已经帮你做了。
        // 注意：默认的 DaoMaster.DevOpenHelper 会在数据库升级时，删除所有的表，意味着这将导致数据的丢失。
        // 所以，在正式的项目中，你还应该做一层封装，来实现数据库的安全升级。
        mHelper = new DaoMaster.DevOpenHelper(context, "sport-db", null);
        db = mHelper.getWritableDatabase();
        // 注意：该数据库连接属于 DaoMaster，所以多个 Session 指的是相同的数据库连接。
        mDaoMaster = new DaoMaster(db);
        mDaoSession = mDaoMaster.newSession();
    }

    public DaoSession getDaoSession() {
        return mDaoSession;
    }
    public SQLiteDatabase getDb() {
        return db;
    }
}
```

然后创建你想要的数据结构

```java
//Note.java
@Entity
public class Note {

    @Id
    private Long id;

    @NotNull
    private String text;
    private Date date;
}
```

然后点击Android Studio里面菜单Build->Make Project，他就会开始自动生成DAO(Data Access Object)类。

然后就可以在项目代码里面使用了：

```java
//MainActivity.java
public class MainActivity extends AppCompatActivity {

    private NoteDao noteDao;
    private String TAG=this.getClass().getName();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //创建Note
        Note note = new Note();
        note.setText("asd");
        note.setDate(new Date());
        note.setId(273687648122l);
        DaoSession daoSession = new App(this).getDaoSession();
        NoteDao noteDao=daoSession.getNoteDao();
        //插入Note到SQLite数据库中
//        noteDao.insert(note);
        //查找Note
        List<Note> ns = noteDao.queryRaw("where TEXT = ?","asd");
        Log.d(TAG, "onCreate: ns.size() = "+ns.size());
    }
}
```
