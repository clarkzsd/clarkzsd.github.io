---
title: 一个Express MVC实例
date: 2016-09-25 15:45:43
tags: [node.js,express]
categories: Tech
description: Express 是最流行的 Node.js 框架，它在 Node.js 之上扩展了 Web 应用所需的基本功能。Fast, unopinionated, minimalist web framework for Node.js

---

## 什么是 Express ?

Express 是最流行的 Node.js 框架，它在 Node.js 之上扩展了 Web 应用所需的基本功能。

> Fast, unopinionated, minimalist web framework for Node.js

详情： [Express](http://expressjs.com/)

随着ES6的推出，Express 开发团队又根据 ES6 新特性发布了下一代 Node.js web 框架：[Koa](http://koajs.com/)

## MVC模式 ?

MVC 模式是程序架构模式中的一种。MVC是三个单词的首字母缩写，它们是Model（模型）、View（视图）和Controller（控制）。
这个模式将程序分成三层：

> 1.最上面的一层，是直接面向最终用户的”视图层”（View）。它是提供给用户的操作界面，是程序的外壳。
>
> 2.最底下的一层，是核心的”数据层”（Model），也就是程序需要操作的数据或信息。
>
> 3.中间的一层，就是”控制层”（Controller），它负责根据用户从”视图层”输入的指令，选取”数据层”中的数据，然后对其进行相应的操作，产生最终结果。

参考：[谈谈MVC模式](http://www.ruanyifeng.com/blog/2007/11/mvc.html)

## 使用 Yeoman 搭建一个 Express MVC项目

使用官方的`express-generator`生成的项目并没有清晰地体现MVC思想，这里我们使用 Yeoman 搭建。

### 安装 Yeoman 和 generator-express

```
npm install -g yo
npm install -g generator-express

```

### 新建一个项目目录，并用generator生成项目模板

```
mkdir ExpressMVC && cd ExpressMVC
yo express

```

在项目生成过程中，yeoman会让我们选择是否使用MVC模式。

## 看懂项目目录

- ExpressMVC
  - app
    - controllers // 控制层
    - models // 数据层
    - views // 视图层
  - config // 用来存放配置文件
    - config.js // 配置数据库、端口等
    - express.js // 配置express项目相关
  - public // 存放静态资源
    - js
    - img
    - css
    - components
  - app.js // 入口文件

### app.js 入口文件

```javascript
var express = require('express'),
  config = require('./config/config'), // 引入配置文件
  glob = require('glob'),              // glob模块允许你使用 *等符号,获取匹配对应规则的文件.
  mongoose = require('mongoose');

mongoose.connect(config.db);          // 连接数据库
var db = mongoose.connection;

// 处理 error 事件
db.on('error', function () {
  throw new Error('unable to connect to database at ' + config.db);
});

var models = glob.sync(config.root + '/app/models/*.js'); // 引入所有model
models.forEach(function (model) {
  require(model);
});
var app = express();       // 生成一个express实例

require('./config/express')(app, config);

// 监听3000端口
app.listen(config.port, function () {
  console.log('Express server listening on port ' + config.port);
});
```

### config/express.js 配置express相关

```javascript
var express = require('express');
var glob = require('glob');

var favicon = require('serve-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');
var compress = require('compression');
var methodOverride = require('method-override');

module.exports = function(app, config) {
  var env = process.env.NODE_ENV || 'development';
  app.locals.ENV = env;
  app.locals.ENV_DEVELOPMENT = env == 'development';

  app.set('views', config.root + '/app/views'); // 配置视图层路径
  app.set('view engine', 'ejs');                // 配置模板引擎为 ejs

  // app.use(favicon(config.root + '/public/img/favicon.ico'));  配置网站favicon
  app.use(logger('dev'));       // 加载日志中间件
  app.use(bodyParser.json());   // 加载解析json的中间件

  // 加载解析urlencoded请求体的中间件
  app.use(bodyParser.urlencoded({
    extended: true
  }));

  // 加载解析cookie的中间件
  app.use(cookieParser());

  // 加载压缩中间件进行 Express 应用程序中的 gzip 压缩，
  // 有助于显著降低响应主体的大小，从而提高 Web 应用程序的速度
  app.use(compress());

  // 配置静态资源的存放路径
  app.use(express.static(config.root + '/public'));
  // 为了支持put、delete等HTTP方法
  app.use(methodOverride());

  // 配置Controller
  var controllers = glob.sync(config.root + '/app/controllers/*.js');
  controllers.forEach(function (controller) {
    require(controller)(app);
  });

  app.use(function (req, res, next) {
    var err = new Error('Not Found');
    err.status = 404;
    next(err);
  });

  // 当发生错误时，进行错误处理并路由到error页面
  if(app.get('env') === 'development'){
    app.use(function (err, req, res, next) {
      res.status(err.status || 500);
      res.render('error', {
        message: err.message,
        error: err,
        title: 'error'
      });
    });
  }

  app.use(function (err, req, res, next) {
    res.status(err.status || 500);
      res.render('error', {
        message: err.message,
        error: {},
        title: 'error'
      });
  });

};
```

### models/article.js 数据层

```javascript
var mongoose = require('mongoose'),
  Schema = mongoose.Schema;

// 定义 Schema
var ArticleSchema = new Schema({
  title: String,
  url: String,
  text: String
});

ArticleSchema.virtual('date')
  .get(function(){
    return this._id.getTimestamp();
  });

mongoose.model('Article', ArticleSchema);   // 创建一个名叫 Article 的Model
```

关于[mongoose](http://mongoosejs.com/docs/index.html)

[mongoose4.5 中文文档](https://www.gitbook.com/book/xiaoxiami/mongoose/details)

### controllers/home.js 控制层

```javascript
var express = require('express'),
  router = express.Router(),
  mongoose = require('mongoose'),
  Article = mongoose.model('Article'); // 引入 Article 这个 model

module.exports = function (app) {
  app.use('/', router);
};

router.get('/', function (req, res, next) {
  Article.find(function (err, articles) {
    if (err) return next(err);
    // 渲染到 视图 层
    res.render('index', {
      title: 'Generator-Express MVC',
      articles: articles
    });
  });
});
```

这里的Controller做的工作正是我们平时所说的路由，Controller不但要处理get请求，也要处理post。

### views/index.ejs 视图层

```ejs
<% include header %>

  <h1><%-title %></h1>
  <p>Welcome to <%-title %></p>

<% include footer %>
```

### 访问网站根目录效果

![img](http://o6ljw8wcq.bkt.clouddn.com/QQ%E5%9B%BE%E7%89%8720160925160903.png)