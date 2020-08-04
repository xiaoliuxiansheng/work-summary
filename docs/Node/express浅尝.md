### 官网地址（https://www.expressjs.com.cn/）
### github(https://github.com/expressjs/expressjs.com)
```javascript 1.8
$ npm install express --save
```
### express 热更新方法（express 框架不自带热更新）
npm install -g node-dev或npm install node-dev -D

然后在package.json里加上"dev": "node-dev ./bin/www"
### 路由
```javascript 1.8
var express = require('express');
var router = express.Router();
````
router.get()处理GET请求和router.post() 处理POST请求 router.all() 处理所有HTTP方法，并使用router.use() 将中间件指定为回调函数
#### 简单封装路由
将所有的路由单独提取成一个router.js文件
引入express中提供的Router
将router替换成express中的router
最后采用module.exports的方式将router导出
> router.js
```javascript 1.8
var express = require('express');
var router = express.Router();

/* GET users listing. */
router.get('/users/:userId/books/:bookId', function(req, res, next) {
  res.send(req.params);
});

router.get('/users', function(req, res, next) {
  res.send("userss");
});

router.post('/test', function(req, res, next) {
  res.send('test22222xx2');
});
module.exports = router;

```
> app.js
```javascript 1.8
var usersRouter = require('./router');
app.use(usersRouter);
```
#### 常用的返回方式
1. res.json([status|body], [body])  以json的形式返回数据

2. res.render(view [, locals] [, callback])  返回对应的view和数据，此方法可以有回调函数，以处理可能出现的异常

3. res.send([body|status], [body])  返回自定义的数据，比如json或者404等状态

4. res.redirect([status,] path)  这个方法其实不是返回，而是跳转到另外一个url上

### 连接数据库
### 示例 mysql
1. 安装依赖 
```javascript 1.8
npm i mysql --save
```
2. 连接数据库 进行操作
````javascript 1.8
let mysql = require('mysql')

//连接数据库
let db=mysql.createConnection({host: "localhost",
  port: "3306",
  user: "root",
  password: "123456",
  database: "mysql"});
db.connect();
let sql = 'select * from websites'

let str = " ";
db.query(sql, function (err,result) {
  if(err){
    console.log('[SELECT ERROR]:',err.message);
  }
  str = result;
  console.log(str);
});
````
3. token（验证） https://www.npmjs.com/package/jsonwebtoken
>  jsonwebtoken

