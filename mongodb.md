## mongo常用函数

1. db.col.stats();
2. db.col.getIndexes();
3. db.col.ensureIndex({key: 1});
4. db.getCollectionNames();
5. db.col.find().limit(n);
6. db.col.ensureIndex({key: 1}, {background: 1});
7. db.runCommand({distinct: 'access_log', key: 'userid', query: {startTime: {$gte: 1486080000000, $lte: 1486166400000}}}).values.length;


## 只获取某字段的结果
`find()`的第二个参数可以指定获取哪些值。
`_id`是默认查出来的，可以通过{"_id": 0}，取消。
例子：`{ "id" : 1, "name" : { "first" : 2, "last" : 3 }, "age": 23 }`

1. 查`age`，`db.col.find({},{"age": 1})`。
2. 查`name.first`，`db.col.find({},{"name.first": 1})`

## jsAPI的用法
1. `collection.find().toArray(function (err, results){});`，要用`toArray`，`results`才是结果集的数组。
