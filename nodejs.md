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
