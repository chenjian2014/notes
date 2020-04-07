# shell操作

### 启动

```shell
mongo --username alice --password --authenticationDatabase admin --host mongodb0.examples.com --port 28015
```

### 常用操作

**db**

```javascript
db  // 查看当前库
use foobar  // 创建新库或者切换到指定库
show dbs   // 查看所有库(必须有数据才能看到)
db.dropDatabase()  // 删除当前 db
```

**集合**

```javascript
db.createCollection("runoob")  // 创建集合, 插入数据会自动创建集合
show collections  // 查看已经创建的集合
db.runoob.drop()  //删除 runoob 集合
```

**文档**

```javascript
db.blog.insert({a: 1})  // 插入
db.blog.batchInsert([]) //批量插入
db.blog.findOne()  // 查询
db.blog.find()  // 查询
db.blog.remove({a:10}) // 删除
db.blog.update({a: 1}, {a:10}) // 更新

// 修改器
db.blog.update({e: 1}, {$inc: {e: 3}})  // e + 3 = 4
db.blog.update({e: 1}, {$set: {e: 100}}) // 更新 e = 100
db.blog.update({e: 1}, {$push: {list: 1}}) // 向 list 增加元素 1, 如果 list 不存在则创建
db.blog.update({e: 1}, {$push: {list2: {$each: [1,2,3,4,5]}}}) // 一次推送多个元素

```

### 查询语法

```javascript
db.blog.find({a: 1, b: 2})  // 查询a==1 and b==2 的结果
db.blog.find({a: 1, b: 2}, {a:1})  // 返回字段 a
db.blog.find({a: 1, b: 2}, {a:0})  // 不返回字段 a

db.blog.find({a: {$lt: 1}})  // a < 1
db.blog.find({a: {$lte: 1}})  // a <= 1
db.blog.find({a: {$gt: 1}})  // a > 1
db.blog.find({a: {$gte: 1}})  // a >= 1
db.blog.find({a: {$ne: 1}})  // a != 1

db.blog.find({$or: [{a: 1}, {b: 2}]})  // a == 1 or b == 2
db.blog.find({$or: [{a: 1, b: 1}, {b: 2}]})  // (a == 1 and b == 1) or b == 2
db.blog.find({c: 1, $or: [{a: 1}, {b: 2}]})  // c == 1 and (a == 1 or b == 2)
db.blog.find({a: {$in: [1,2,3,4]}})  // a in [1,2,3,4]
db.blog.find({a: {$nin: [1,2,3,4]}})  // a not in [1,2,3,4]
db.blog.find({a: {$not: {$in: [1,2,3,4]}}}) // $not 针对给定条件取反

```

