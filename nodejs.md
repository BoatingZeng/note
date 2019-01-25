# nodejs

## pm2

传入参数

```
node --expose-gc bin/www arg1 arg2 arg3
```

```
pm2 start bin/www --node-args="--expose-gc" -- arg1 arg2 arg3
```

node参数放到--node-args里，普通参数放到--后面

## log4js

和pm2使用时，要`pm2 install pm2-intercom`，config里设置`pm2`为`true`，详见https://nomiddlename.github.io/log4js-node/faq.html

## koa

### 错误处理和404处理

和express相反，koa的这两个处理中间件要放在最开头。404和错误处理谁先谁后关系不大，因为这两个中间件都是要保证不能出错(或许错误处理应该放在404前面，以便捕获404中间件可能出现的错误)。后面的中间件可以通过`ctx.throw(555, 'my error');`抛出有状态码的错误，以便错误中间件识别。koa的机制是，如果中间件没有改变状态码，`ctx.response.status`就一直是404，所以404中间件就是利用这个特性来判断后面的路由是否命中。

```js
app.use(async (ctx, next) => {
    try{
        await next();
    } catch(e) {
        ctx.response.status = e.status || e.statusCode || 500;
        console.log('error', e);
        console.log('error status', e.status);
        console.log('status', ctx.response.status);
        ctx.response.body = 'my error page';
    }
});

app.use(async (ctx, next) => {
    await next();
    console.log('404 mid', ctx.response.status);
    if(parseInt(ctx.response.status) === 404) ctx.response.body = 'my 404';
});
```

## Promise
nodejs原生的Promise，UnhandledPromiseRejectionWarning的打印不详细，不具体到哪个文件哪一行，所以使用bluebird会更好。使用Promise是，当你then里出现错误时，就会报UnhandledPromiseRejectionWarning。

## sequelize
这个库的坑很多，记得保持更新。旧版本遇到一个bug，设置unique就给你创建两个一样的唯一索引。

### associations用例

belongsTo和hasMany。下面的例子理解为，一个company有很多个user，但是user只属于一个company。

```js
// 定义User模型
var User = sequelize.define('user', {
	id:{type: Sequelize.BIGINT(11), autoIncrement:true, primaryKey : true },
	name: { type: Sequelize.STRING },
	sex: { type: Sequelize.INTEGER, allowNull: false, defaultValue: 0 },
	isManager: { type: Sequelize.BOOLEAN, field: 'is_manager', allowNull: false, defaultValue: false }
});

// 定义Company模型
var Company = sequelize.define('company', {
	id:{ type:Sequelize.BIGINT(11), autoIncrement:true, primaryKey : true },
	name: { type: Sequelize.STRING, unique: true, allowNull: false }
});

// 定义User-Company关联关系
User.belongsTo(Company, {as: 'company', foreignKey: 'company_name', targetKey: 'name'});
Company.hasMany(User, { as: 'users', foreignKey:'company_name', sourceKey: 'name'});
```

foreignKey是建立外键的那个表的字段，这个字段不需要在define里创建，定义association时会给你创建。在belongsTo和hasMany里，targetKey或者sourceKey是指被指向的表里的字段，这里指向的就是company表的name字段。

关于hasOne，目前版本4.42.0，它没有sourceKey选项，所以hasOne只能用主键，github上说5.0.0版本后支持sourceKey选项。

同步后会得到下面的表

```sql
CREATE TABLE `company` (
  `id` bigint(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `name` (`name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `user` (
  `id` bigint(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `sex` int(11) NOT NULL DEFAULT '0',
  `is_manager` tinyint(1) NOT NULL DEFAULT '0',
  `company_name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `company_name` (`company_name`),
  CONSTRAINT `user_ibfk_1` FOREIGN KEY (`company_name`) REFERENCES `company` (`name`) ON DELETE SET NULL ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
