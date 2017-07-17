# css

## 基本选择器
1. #id id选择器
2. .class 类选择器
3. [attribute] 属性选择器
4. :link 伪类或伪元素

## 组合
1. 两个选择器间没有空格，表示交集
2. 空格，表示后代(包括后代的后代)，多次空格，则是指定后代的后代
3. >，表示直接后代，多个>，则是直接后代的直接后代
4. +，表示同辈的后一个元素。p+p:与前一个p同辈的后一个p。
5. ~，选择同辈的后面的所有元素。p~p，与前p同辈的后面所有p。

# SQL

## 常用函数

1. substring_index(str,delim,count)

      str:要处理的字符串

      delim:分隔符

      count:计数

http://blog.csdn.net/wolinxuebin/article/details/7845917

2. concat('a','b','c') = 'abc';
3. `IFNULL(fieldName, value)`，如果这个`fieldName`字段的值是`null`，则用`value`代替
4. `DATE_FORMAT(date, format)`，格式化时间。
5. `COUNT()`，`COUNT(column_name)`会计数`column_name`不为`NULL`的行，`COUNT(*)`会计数所有行。

## 导出导入

1. 导出整个数据库
    mysqldump -u 用户名 -p 数据库名 > 导出的文件名
    mysqldump -u root -p test > test.sql
2. 导出一个表
　　mysqldump -u 用户名 -p 数据库名 表名 > 导出的文件名
　　mysqldump -u root -p test users > test_users.sql 
3. 导出一个数据库结构
　　mysqldump -u root -p -d --add-drop-table smgp_apps_wcnc > d:\wcnc_db.sql
　　-d 没有数据 --add-drop-table 在每个create语句之前增加一个drop table
4. 导入数据库
　　常用source 命令
　　进入mysql数据库控制台，
　　如mysql -u root -p
　　mysql>use 数据库  **一定要记得先选择数据库**
　　然后使用source命令，后面参数为脚本文件(如这里用到的.sql)
　　mysql>source d:\wcnc_db.sql

远程： 加 --host
http://dev.mysql.com/doc/refman/5.7/en/mysqldump.html

## 基本

### 语句
**SQL语句的顺序问题。**
http://www.cnblogs.com/qanholas/archive/2010/10/24/1859924.html
1. `USE dbName`选择`dbName`数据库
2. `SELECT tb.fieldName FROM tableName tb`选择`tableName`表的`fieldName`字段，`tb`用于给`tableName`起别名。
3. `IN`，`WHERE column_name IN (value1,value2)`，`IN`表示可能的取值，即相当于`WHERE column_name=value1 OR column_name=value2`
4. `LIKE`，`WHERE column_name LIKE pattern`，`LIKE`用于指定模式，`pattern`就是模式
5. `AS`，`origin AS new`，用于起别名，用`new`表示`origin`，`AS`可以省略，中间用空格
6. `UNION`，用于合并两个或多个`SELECT`语句的结果，`UNION`选取不同的值，`UNION ALL`允许选择重复的值，结果集中的列名总等于第一个`SELECT`语句的列名
7. `JOIN`，跟平时用的两个表`WHERE`结合查询是一个意思，会先生成一个临时表。`JOIN`的时候，表应该从小到大排列，在`FROM`里也是，大表放在结合操作的右侧。`JOIN`操作应该在过滤操作前面。

### Stored Procedure (存储过程)

1. 执行`loopinsert`就可以循环插入

```sql
create procedure loopinsert() 
begin 
declare num int; 
set num=1; 
    while num < 10 do 
    insert into t_tag(tag_name, tag_desc) values(concat("tag", num), concat("tag", num)); set num=num+1;
    end while;
end
```


## 关于SELECT 0
语句`SELECT 0 AS x, field1 FROM tb`， tb表中是没有`x`这个字段的，但是希望查询结果中有这个字段，所以就通过`SELECT 0 AS x`来添加了，并且结果中的`x`字段会是`0`值。

具体例子：http://stackoverflow.com/questions/13219472/why-SELECT-0-instead-of-SELECT
。这个例子是为了确认`B`表中到底有没有`a=1`和`a=2`的行。

## 例子

### 例1
```sql
SELECT tcpr.rel_id, tcpr.seq, tp.code, tp.title, tp.creation_date, tp.lASt_update_date
FROM t_product tp, t_category_product_relation tcpr
WHERE tp.code=tcpr.product_code AND tcpr.category_code='cy_yinyue'
ORDER BY tcpr.seq ASC
```

这个语句查询了两个表，`t_product`和`t_category_product_relation`，选择了
`tcpr`表的`rel_id`、`seq`字段，`tp`表的`code`、`title`、`creation_date`、`lASt_update_date`字段。

关于`WHERE`的部分，是先从`tcpr`表获取`category_code='cy_yinyue'`的部分，然后从`tp`表获取`tp.code=tcpr.product_code`的部分。
关于`ORDER BY`部分，结果按照`tcpr.seq`升序排序。

(注:`tp.code`和`tcpr.product_code`记录的数据是一样的，就是`pcode`，下面的例子也是)

### 例2
```sql
SELECT tp.code, tp.title, tp.creation_date, tp.lASt_update_date
FROM t_product tp 
WHERE tp.code NOT IN (
    SELECT product_code FROM t_category_product_relation
    WHERE  category_code = 'cy_huihua'
)
AND tp.code LIKE '通配'
ORDER BY tp.code
```

先从`t_category_product_relation`表中选择符合`category_code = 'cy_huihua'`的记录的`product_code`字段，这些`product_code`用作`IN`所需的数组。这样就排除了`category_code = 'cy_huihua'`的`tp.code`。

### 例3
```sql
    SELECT resource_code, seq, 0 AS i 
    FROM t_product_resource_relation t
    WHERE t.product_code = 'pcode' 
    AND t.seq = (SELECT CASE seq WHEN 0 THEN - 1 ELSE seq - 1 END
        FROM t_product_resource_relation
        WHERE product_code = 'pcode'
        AND resource_code = 'rcode')
UNION
    SELECT resource_code, seq, 1 AS i 
    FROM t_product_resource_relation t
    WHERE t.product_code = 'pcode'
    AND t.seq = (SELECT seq + 1 
        FROM t_product_resource_relation
        WHERE product_code = 'pcode'
        AND resource_code = 'rcode')
```

查询上/下一条资源记录，查询结果的`0`和`1`应该就是分别表示上一集和下一集。先用`pcode`和`rcode`查到当前集，然后当前集的`seq`减一和加一分别就是上一集和下一集的`seq`，然后用用上一集和下一集的`seq`找到它们的`rcode`。

### 例4
```sql
    SELECT tpr.resource_code, t.product_code, t.seq, 0 AS i
    FROM t_category_product_relation t, t_product_resource_relation tpr
    WHERE t.product_code = tpr.product_code
    AND t.category_code = 'ccode'
    AND t.seq = (SELECT CASE seq WHEN 0 THEN - 1 ELSE seq - 1 END
        FROM t_category_product_relation
        WHERE category_code = 'ccode'
        AND product_code = 'pcode')
UNION
    SELECT tpr.resource_code, t.product_code, t.seq, 1 AS i
    FROM t_category_product_relation t, t_product_resource_relation tpr
    WHERE t.product_code = tpr.product_code
    AND t.category_code = 'ccode'
    AND t.seq = (SELECT seq + 1
        FROM t_category_product_relation
        WHERE category_code = 'ccode'
        AND product_code = 'pcode')
```

根据分类编码查询上/下一条资源记录，适用于查询每个作品仅包含1集资源的情况。为什么只有一集还要去查找上一集和下一集？？？

```sql
select * from post where content like '%$中文%' and date > '两个月内时间'
```
由于***id是自增***的，用下面的方式效率应该更高点
```sql
select p.* 
from post as p
     ,(select id from post where  date > '两个月内时间'  order by id limit 1) as t
where content like '%$中文%' and p.id>=t.id 
order by p.id desc limit 0,30;
```
先取ID，然后用ID做顺序扫描的写法是个好方法。
当然前提是 自增ID 和 date是单调递增的。

通过主键顺序搜索映射到磁盘上就是顺序读。10万行1k的数据相当于100M，基本是秒级完成。
再加上like的匹配，相对于作业类型的SQL属于可接受范围。

## 事务
```sql
start transaction;
INSERT INTO `test`.`repo_table` (`repo_id`, `repo_name`) VALUES ('17', 'kl');
INSERT INTO `test`.`repo_table` (`repo_id`, `repo_name`) VALUES ('18', 'mn');
commit; //或者rollback
```
相当于上保险，如果其中有语句ERRO，则执行`rollback`，则所有语句都不实际生效。但是一旦执行`commit`，则语句都会生效。

### mysql.js中的用法
```js
connection.beginTransaction(function(err) {
  if (err) { throw err; }
  connection.query('INSERT INTO posts SET title=?', title, function(err, result) {
    if (err) {
      return connection.rollback(function() {
        throw err;
      });
    }

    var log = 'Post ' + result.insertId + ' added';

    connection.query('INSERT INTO log SET data=?', log, function(err, result) {
      if (err) {
        return connection.rollback(function() {
          throw err;
        });
      }  
      connection.commit(function(err) {
        if (err) {
          return connection.rollback(function() {
            throw err;
          });
        }
        console.log('success!');
      });
    });
  });
});
```
其实就是如果query抛出错误，就rollback，这样之前的query都不会执行。都没问题才commit。

## KEY
http://stackoverflow.com/questions/10908561/mysql-meaning-of-primary-key-unique-key-and-key-when-used-together

在MYSQL中Key就是Index。不完全相同，但是创建Key一定会创建Index。比如要创建PK就不能用create index。对于InnoDB，创建表时，如果不指定PK，就会创建隐藏的PK。
Unique Key 就是 Unique Index。Unique约束和Unique Key、Unique Index的结果是一样的。不过PK不能通过INDEX创建。

### PRIMARY （主键）

如果你用InnoDB，而且不需要特殊的聚簇索引，一个好的做法就是使用代理主键(surrogate key)——独立于你的应用中的数据。最简单的做法就是使用一个AUTO_INCREMENT的列，这会保证记录按照顺序插入，而且能提高使用primary key进行连接的查询的性能。应该尽量避免随机的聚簇主键，例如，字符串主键就是一个不好的选择，它使得插入操作变得随机。

### FOREIGN KEY （外键）
http://www.cnblogs.com/mydomain/archive/2011/11/10/2244233.html
建立外键的表叫子表，被reference的表叫父表。注意`ON UPDATE`和`ON DELETE`事件的设置，默认是`RESTRICT`。默认状态下，子表不可添加父表中没有关联的记录，父表不能删除与子表关联的记录。**也就是子表中有的，父表一定要有。**

# mongodb

### 只获取某字段的结果
`find()`的第二个参数可以指定获取哪些值。
`_id`是默认查出来的，可以通过{"_id": 0}，取消。
例子：`{ "id" : 1, "name" : { "first" : 2, "last" : 3 }, "age": 23 }`

1. 查`age`，`db.col.find({},{"age": 1})`。
2. 查`name.first`，`db.col.find({},{"name.first": 1})`

### jsAPI的用法
1. `collection.find().toArray(function (err, results){});`，要用`toArray`，`results`才是结果集的数组。

## mongo常用函数

1. db.col.stats();
2. db.col.getIndexes();
3. db.col.ensureIndex({key: 1});
4. db.getCollectionNames();
5. db.col.find().limit(n);
6. db.col.ensureIndex({key: 1}, {background: 1});
7. db.runCommand({distinct: 'access_log', key: 'userid', query: {startTime: {$gte: 1486080000000, $lte: 1486166400000}}}).values.length;


# 树数组生成json
```js
 function fn(data, pid) {
    var result = [], temp;
    for (var i = 0; i < data.length; i++) {
        if (data[i].pid == pid) {
            var obj = {"text": data[i].name,"id": data[i].id};
            temp = fn(data, data[i].id);
            if (temp.length > 0) {
                obj.children = temp;
            }
            result.push(obj);
        }
    }
    return result;
}
```

# Linux命令
1.  查看进程：ps -ef | grep mysql
2.  杀进程：kill -s 9 1827
3.  查看文件尾：tail file.log -n 300

## unbuntu开机启动sh
svn为例：
1.  在/etc/init.d写个脚本startsvn.sh
```
    #!/bin/bash
    svnserve -d -r /home/svn
```
2. chmod 777 startsvn.sh  设置启动权限
3. update-rc.d startsvn.sh defaults


# Nginx

#### 简单配置示例
```
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    
    default_type  application/octet-stream;

    proxy_cache_path  cache/cache2  levels=1:2 keys_zone=cache2:100m inactive=10m max_size=1g;

    server {
        listen       80;
        server_name  localhost;
      
        add_header X-Cache $upstream_cache_status;

        location / {
            proxy_pass http://localhost:18189;
        }

        location /home {
            proxy_pass http://localhost:18189;

            proxy_cache cache2;

            proxy_cache_key $host$uri$is_args$args;

            proxy_cache_valid 200 1m;
        }
        
        location /category {
            proxy_pass http://localhost:18189;

            proxy_cache cache2;

            proxy_cache_key $host$uri$is_args$args;

            proxy_cache_valid 200 1m;
        }
        
        location /assets/ {
            root public;
        }
    }
}
```

##### proxy_cache_path的说明：
```
proxy_cache_path  cache/cache2  levels=1:2 keys_zone=cache2:100m inactive=10m max_size=1g;
```
1. `cache/cache2`：缓存目录
2. `levels=1:2`：缓存目录结构
3. `inactive=10`：10分钟内没被访问的缓存会被删除
4. `max_size=1g`：缓存最大1g
5. `keys_zone=cache2:100m`：`cache2`这个名字下面会用到，这个名字是自己起的。`100m`的说明参考文档。


##### location /home块的说明：

和`location /category`一样，它们会匹配以`/home`和`/category`开头的url，比如：`/category/sub?ccode=story`和`/category/ajax?ccode=story`

```
proxy_cache cache2;
```
表示使用cache2这个缓存

```
proxy_cache_key $host$uri$is_args$args;
```
请参考文档。带`$`前缀的是Nginx变量，上面这个`$host$uri$is_args$args`会变成类似`localhost:18189/home?query=test`或`localhost:18189/category`。

**注意：** 下面几个URL会命中不同缓存。

1. `localhost:18189/home?query=test`
2. `localhost:18189/home?query=test2`
3. `localhost:18189/home?query=test`
4. `localhost:18189/home`

```
proxy_cache_valid 200 1m;
```
请参考文档。这样设置的话，第一次请求`/home`，会走node，1分钟内再请求不走node，1分钟后会再走node。

##### location /assets/块的说明：

这是静态文件的配置。

把项目的public移到了Nginx目录下，也就是原项目没有public目录了。这样，访问`localhost:18189`是获取不到静态文件的，页面显示不正常，`18189`是node项目的端口。访问`80`，即Nginx，是可以获取静态文件，页面正常显示。

详细可参考：

http://freeloda.blog.51cto.com/2033581/1288553

https://www.nginx.com/blog/nginx-caching-guide/

http://nginx.org/en/docs/

#### 详细配置示例

```
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;

    default_type  application/octet-stream;

    proxy_cache_path  cache/cache_dm  levels=1:2 keys_zone=cache_dm:100m inactive=1m max_size=1g;

    server {
        listen       80;
        server_name  localhost;
      
        add_header X-Cache $upstream_cache_status;
        
        location /assets {
            root F:/work/com/dongshi/shanghai/iptv-4k-dongman/public;
        }

        location / {
            proxy_pass http://localhost:18189;
        }

        location /home {
            proxy_pass http://localhost:18189;

            proxy_cache cache_dm;

            proxy_cache_key $host$uri$is_args$args;

            proxy_cache_valid 200 1m;
        }

        location /category {
            proxy_pass http://localhost:18189;

            proxy_cache cache_dm;

            proxy_cache_key $host$uri$is_args$args;

            proxy_cache_valid 200 1m;
        }

        location /search {
            proxy_pass http://localhost:18189;

            proxy_cache cache_dm;

            proxy_cache_key $host$uri$is_args$args;

            proxy_cache_valid 200 1m;
        }

        location /search/do_search {
            proxy_pass http://localhost:18189;
        }

        location /product {
            proxy_pass http://localhost:18189;

            proxy_cache cache_dm;

            proxy_cache_key $host$uri$is_args$args;

            proxy_cache_valid 200 1m;
        }

        location /product/userInfo {
            proxy_pass http://localhost:18189;
        }
    }
}
```

##### 需要调整的参数

1. `cache/cache_dm`：缓存文件路径
2. `keys_zone=cache_dm:100m`：缓存区名字和Key大小，100m应该够了
3. `inactive=1m`：不活跃缓存被删除的时间
4. `max_size=1g`：缓存最大大小
5. `proxy_cache cache_dm`：这个参数填缓存区名字
6. `proxy_cache_valid 200 1m`：200是响应码，只对200的设置应该就够了，1m是缓存1分钟内可用
7. `proxy_pass http://localhost:18189`
8. `add_header X-Cache $upstream_cache_status`：用来在响应头里标识有没有命中缓存的，不用更改
9. 静态文件的配置

##### 关于inactive和proxy_cache_valid

inactive的时间表示一个文件在指定时间内没有被访问过，就从存储系统中移除，不管你proxy_cache_valid里设置的时间是多少。而proxy_cache_valid在保证inactive时间内被访问过的前提下，最长的可用时间。proxy_cache_valid定义的其实是一个绝对过期时间(第一次缓存的时间+配置的缓存时间)，到了这个点，对象就被认为是过期，然后去后端重取数据，尽管它被访问的很频繁(即所谓的inactive时间内)。expires呢，它不在这个过期控制体系内，它用在发给客户端的响应中，添加"Expires"头。

##### 关于缓存的储存


缓存用文件的形式储存在磁盘内。

# JAVA

## Class<T>
Class<T>：表示T这个类的Class对象。T.class。

https://segmentfault.com/q/1010000002615805

## 匿名内部类使用的参数

JAVA编程思想P198、P527提及：如果定义一个匿名内部类，并且希望它使用一个在其外部定义的对象，那么编译器会要求其参数引用的是final的。但是在JAVA8中测试，不是final也没问题。

## 并发

线程(Thread)和任务(Runnable)不是同一个概念(p669 21.2.10)

# Vue

## Vue admin 里用到的模块

* axios：用来发http请求，Promise形式
* vue-axios：封装axios，应该跟vue-resource差不多，vue-resource好像有点问题，所以我也换了
* vue-nprogress：用来做进度条
* vue-auth：权限管理，限制路由，使用jwt，并且会检测http请求和响应，获取jwt或者写入token到header
* vuex-router-sync：统一管理路由状态

# WebPack

## 问题
* UglifyJs好像不支持es6，用typescript的es6选项时，uglify会出错。vue-cli默认就开了babel-loader。
* error TS2686: 'XXX' refers to a UMD global, but the current file is a module. Consider adding an import instead。想把THREE作为全局使用，但是在编译时会出现这样的问题。***暂时设置ts-loader的happyPackMode来解决***
* sass模块的引用，当使用alias时，要在设定的alias前面加~。也就是说用alias时，要以~开头。

## 模块的引用

模块A，如果几个模块(B、C、D)同时引用模块A，它们所用的其实是同一个。模块A里的局部变量(var 声明的)，B、C、D里访问的A的局部变量，是指向相同的变量，而不是复制。所以在不同模块里操作A，对A的影响是跨模块的。

# GIT

## post-receive

```
#!/bin/sh
unset GIT_DIR

deployPath='/home/user/your_repo'

cd $deployPath
git pull origin master
```

**unset GIT_DIR 这句不能省略**
