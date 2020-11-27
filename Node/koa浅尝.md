### 官网地址 （https://koa.bootcss.com/）
### github （https://github.com/koajs）

#### 创建koa2工程 
> demo:(https://www.liaoxuefeng.com/wiki/1022910821149312/1099752344192192)

#### 利用koa-generator快速生成koa2框架目录结构
> koa-generator 地址（https://www.npmjs.com/package/koa-generator）

#### 开发时候 热更新
1. 安装  npm install nodemon --save 
2. 修改packjson > start > = 'nodemon node bin/www'

#### 数据库操作 第三方 插件 Sequelize（https://itbilu.com/nodejs/npm/sequelize-docs-v5.html#data-retrieval---finders）

#### post请求参数解析
> 对于post请求处理，koa2没有封装轻便的方法获取参数，需要通过解析上下文context中的原生node.js请求对象req来获取。

>可安装第三方插件 koa-bodyparser插件处理 步骤如下

1、 安装koa-bodyparser
````javascript 1.8
cnpm i koa-bodyparser --save
````
2、 引入bodyparser模块(该模块的引入要在router引入之前)
````javascript 1.8
const bodyParser = require('koa-bodyparser');
app.use(bodyParser());
````
3、 接收post请求（ajax或者表单提交）参数
````javascript 1.8
ctx.request.body
````
