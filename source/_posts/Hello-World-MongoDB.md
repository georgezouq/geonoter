---
title: Hello World MongoDB
date: 2016-06-27 19:45:58
tags:
---
# Hello World MongoDB

本篇文章是 MongoDB 的入门级文章，介绍 MongoDB 的基本命令和语法。

## DB Shell 数据操作

shell 命令操作语法和 JavaScript 很类似，其实控制台底层的查询语句都是用 JavaScript 脚本完成的。

### 数据库:

1.Help 查看命令提示

```shell
help
db.help();
db.yourColl.help();
db.yourColl.find().help();
```

2.切换、创建 数据库

```shell
use yourDB
```

当创建一个集合(table)的时候会自动创建当前数据库

3.查询所有数据库
```shell
show dbs;
```

4.删除当前使用数据库
```
db.dropDatabase();
```

5.从指定主机上克隆数据库
```
db.cloneDatabase("127.0.0.1");
```

6.从指定机器上复制指定数据库到某个数据库
```
db.copyDatabase("mydb","temp","127.0.0.1");
```

7.修复当前数据库
```
db.repaireDatabase();
```

8.查看当前使用的数据库
```
db.getName()
```

9.显示当前db状态
```
db.stats();
```

10.当前db版本
```
db.version()
```

11.查看当前db链接地址
```
db.getMongo()
```

#### Collection 聚合集合

1.创建一个聚合集合 (table)
```
db.createCollection("collName",{site:20,capped:5,max:100});
```

2.得到指定名称的聚合集合(table)
```
db.getCollection("account");
```

3.得到当前db的所有聚合集合
```
db.getCollectionNames();
```

4.显示当前db所有聚集索引的状态
```
db.getCollectionNames();
```

#### 用户相关

1.添加一个用户
```
db.addUser("name")
db.addUser("userName","pwd123",true);
```
添加用户、设置密码、是否只读

2.数据库认证、安全模式
```
db.auth("userName","123456")
```

3.显示当前所有用户
```
show users;
```

4.删除用户
```
db.removeUser("userName");
```

#### 其他

1.查询之前的错误信息
```
db.getPrevError();
```

2.清除错误记录
```
db.resetError();
```

### Collection 聚集集合操作

#### 查看聚集集合基本信息

1.查看帮助
```
db.yourColl.help();
```

2.查询当前集合的数据条数:
```
db.yourColl.count();
```

3.查看数据空间大小
```
db.userInfo.dataSize();
```

4.得到当前聚集集合所在的db
```
db.userInfo.getDB();
```

5.得到当前聚集的状态
```
db.userInfo.stats();
```

6.得到聚集集合总大小
```
db.userInfo.storageSize();
```

7.聚集集合重命名
```
db.userInfo.renameCollection("user");
```
将userInfo 重命名为 users

8.Shard版本信息
```
db.userInfo.getShardVersion();
```

9.删除当前聚合集合
```
db.userInfo.drop();
```

#### 聚合集合查询

1.查询所有记录
```
db.userInfo.find();
```
相当于：select * from userInfo;
默认每页显示20条记录，当显示不下的情况下，可以用it迭代命令查询下一页数据。注意：键入 `it` 命令不能带 `;` 但是你可以设置每页显示数据的大小，用 `DBQuery.shellBatchSize = 50;` 这样就显示 50条记录了。

2.查询去掉后的当前聚集集合中的某列的重复数据
```
db.userInfo.distinct("name");
```
会过滤掉 `name` 中的相同数据，相当于 `select distict name from userInfo; `

3.数字条件查询
```
//age == 22
db.userInfo.find({"age":22});

//age > 22
db.userInfo.find({age:{$gt:22}});

//age < 22
db.userInfo.find({age:{$lt:22}});

//age >= 25
db.userInfo.find({age:{$gte:25}});

//age <= 22
db.userInfo.find({age:{$lte:22}});

//age >= 23 and age <=26
db.userInfo.find({age:{$gte:23,$lte:26}});
```

4.字符串条件查询
```
//1.name 中包含 mongo 开头的数据
db.userInfo.find({name:/mongo/});
//相当于 select *gtom userInfo where name like '%mongo%'

//2.查询指定列 name,age 数据
db.userInfo.find({},{name:1,age:1});

//3.查询指定列 name，age 的数据，age > 25
db.userInfo.find({age:{$gt:25}},{name:1,age:1});
相当于:select name,age from userInfo where age > 25;

//4.按照年龄排序
升序：db.userInfo.find().sort({age:1});
降序：db.userInfo.find().sort({age:-1});

//5.查询 name = zhangsan,age=22 的数据
db.userInfo.find({name:'zhangsan',age:22});

//6.查询前5条数据
db.userInfo.find().limit(5);

//7.查询10条以后的数据
db.userInfo.find().skip(10);

//8.查询5-10之间的数据
db.userInfo.find().limit(10).skip(5);

//9.or 与查询
db.userInfo.find({$or:[{age:22},{age:25}]});

//10.查询第一条数据
db.userInfo.findOne();
db.userInfo.find().limit(1);

//11.查询某个结果集的记录条数
db.userInfo.find({age:{$gte:25}}).count();

//12.求sex字段不为空的 查询结果总数
db.userInfo.find({sex:{$exists:true}}).count()

```

#### 索引

1.创建索引
```
db.userInfo.ensureIndex({name:1});
db.userInfo.ensureIndex({name:1,ts:-1});
```

2.查询当前聚集集合所有索引
```
db.userIndex.getIndexes();
```

3.查询总索引记录大小
```
db.userIndex.getIndexes();
```

4.读取当前集合的所有 index 信息
```
db.users.reIndex();
```

5.删除指定索引
```
db.users.dropIndex("name_1");
```

6.删除所有索引
```
db.users.dropIndexes();
```

#### 修改 添加 删除集合数据

1.添加
```
db.users.save({name:'zhangsan',age:25,sex:true});
```
添加的数据的数据列没有固定，根据添加的数据为准。

2.修改
```
db.users.update({age:25},{$set:{name:'changeName'},false,true})
//相当于 update users set name='changeName' where age=25;

db.users.uodate({name:'Lisi'},{$inc:{age:50}},false,true);
//相当于：update users set age = age + 50 where name='Lisi';

db.users.update({name:'Lisi'},{$inc:{age:50},$set:{name:'hoho'}},false,true);
//相当于：update users set age = age+50,name='hoho' where name='Lisi';
```

3.删除
```
db.users.remove({age:132});
```

4.查询修改删除
```
db.users.findAndModify({
    query:{age:{$gte:25}},
    sort:{age:-1},
    update:{$set:{name:'a2'},$inc:{age:2}},
    remove:true
  });
```

update 或 remove 其中一个是必选参数，其他参数可选

1. query   查询过滤条件    {}

2. sort    如果多个文档符合查询条件，将以该参数指定的排序方式选择出排在首位的对象，该对象讲被操作 {}

3. remove    若为true。将返回修改后的对象而不是原始对象，在操作中该参数被忽略。   false

4. new      若为true 将返回修改后的对象而不是原始对象，在删除操作中，该参数被忽略。   false

5.fields 参见[Retrieving a Subset of Fields](http://www.mongodb.org/display/DOCS/Retrieving+a+Subset+of+Fields) (1.5.0+)

6.upsert    若查询结果为空创建新对象。   false

#### 语句块操作

1. 简单 Hello World
```
print("Hello World");
```
这种写法调用了 print 函数，和直接写入"Hello World"的效果是一样的

2. 将一个对象转换为 JSON
```
tojson(new Object());
tojson(new Object('a'));
```

3. 循环添加数据
```
for(var i = 1;i < 30; i++ ){
  db.users.save({name:"u_"+i,age:22 + i,sex:i % 2});
}
```

这样就循环添加了30条数据，同样可以省略括号的写法

```
for(var i = 0;i < 30; i++ )
  db.users.save({name:"u_" + i,age:22 + i,sex:i % 2});
```
当你用db.users.find()查询的时候，显示多条数据而无法一页显示的情况下，可以用it查看下一页的信息；

4. find 游标查询
```
var cursor = db.users.find();
while(cursor.hasNext()){
  printjson(cursor.next());
}
```
这样就查询所有的 users 信息。

5. forEach 迭代循环
```
db.users.find().forEach(printjson);
```
forEach中必须传递一个函数来处理每条迭代的数据信息

6. 将find游标当数组处理

```
var  cursor = db.users.find();
cursor[4];
```

取得下标索引为 4 的那条数据，既然可以当做数组处理，那么就可以获得他的长度：`cursor.length()` 或 `cursor.count();`

```
for (var i = 0, len = c.length(); i < len; i++) printjson(c[i]);
```

7、将find游标转换成数组

```
var arr = db.users.find().toArray();
printjson(arr[2]);
```

用toArray方法将其转换为数组

8、定制我们自己的查询结果

只显示age <= 28的并且只显示age这列数据

```
db.users.find({age: {$lte: 28}}, {age: 1}).forEach(printjson);

db.users.find({age: {$lte: 28}}, {age: true}).forEach(printjson);

//排除age的列
db.users.find({age: {$lte: 28}}, {age: false}).forEach(printjson);
```

9、forEach传递函数显示信息

```
db.things.find({x:4}).forEach(function(x) {print(tojson(x));});
```

上面介绍过forEach需要传递一个函数，函数会接受一个参数，就是当前循环的对象，然后在函数体重处理传入的参数信息。

[原文链接](http://www.cnblogs.com/hoojo/archive/2011/06/01/2066426.html)
感谢原作者
