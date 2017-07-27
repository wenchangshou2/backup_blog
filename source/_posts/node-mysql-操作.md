---
title: node-mysql 操作
date: 2016-07-26 15:01:05
tags: node.js
---
 node.js mysql操作
 <!--more-->
  


#### 安装node-mysql
在node.js采用npm的方式来进行模块的安装。node.js安装的话有两种方式可以通过-g的方式进行全局的安装，在这种模式下安装的模块可以被整个node.js的程序所引用。如果不加-g的话nodejs会在当前的目录下新建一个node_modules文件夹，并且将下载的模块放置在该文件夹下相应的目录.
>npm install mysql -g //全局安装
>npm install mysql --save //下载至当前的目录下

#### 运行第一个mysql 查询
为了方便我将mysql的root密码设置为123456，在node数据库当中新建了一张名为users的表,建表的语句与如所示。

``` sql

SET NAMES utf8;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
--  Table structure for `users`
-- ----------------------------
DROP TABLE IF EXISTS `users`;
CREATE TABLE `users` (
  `name` varchar(255) DEFAULT NULL,
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `password` varchar(256) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=latin1;

-- ----------------------------
--  Records of `users`
-- ----------------------------
BEGIN;
INSERT INTO `users` VALUES ('zs', '1', '123456'), ('ls', '2', '12345');
COMMIT;

SET FOREIGN_KEY_CHECKS = 1;

```

``` javascript
var mysql=require('mysql');
var connection=mysql.createConnection(
    {
        host:'localhost',
        user:'root',
        password:'123456',
        database:'node'
    }
);
connection.connect();
var queryString='SELECT * FROM users';
connection.query(queryString,function(err,rows,fields){
    if(err) throw err;
    for(var i in rows){
        console.log('user name',rows[i].name);
    }
});
```
同样的我们在创建createConnection连接时可以使用字符串来替代对象的方式

``` js
var connection=mysql.createConnection('mysql://user:pass@host/node'）;
```
通过遍历查询的结果我们可以查询到每个字段（在上面的用例当中我们显示的是name)
>console.log('user name',rows[i].name);

query用于mysql的查询，在这里面我们使用了匿名函数的方式进行操作，一旦触及到查询的操作时会自动回调到该匿名事件当中，其中err,fields和rows分别用于显示相应的状态。

对于 上面的操作是将查询所有的行之后再进行回调，倘若我们需要对其中的每一行进行处理的话，我们可以采用下面的方式进行。

``` js
var mysql=require('mysql');

var connection=mysql.createConnection(
    {
        host:'localhost',
        user:'root',
        password:'a63621375',
        database:'node'
    }
);
connection.connect();
var queryString='SELECT * FROM users';
var query=connection.query('select * from users');
query.on('error',function(err){
    throw err;
});
query.on('fields',function(fields){
    console.log(fields);
});
query.on('result',function(row){
    console.log(row.name);
});
```
有的时候我们需要对某一行进行处理时，为了不影响其他的数据，我们不得不对查询进行暂停进行相应的处理之后再进行恢复。

``` js
query.on('result', function(row) {
    connection.pause();
    // 指定处理的代码
    console.log(row);
    connection.resume();
});
```

#### 通过条件来查询值
有的时候我们需要采用指定查询的条件来过滤不需要的数据，同时我们要避免出现sql注入，在node-mysql当中有两种方式进行条件查询，第一种采用？来显示

``` js

connection.connect();
 
var key = 'zs'; 
var queryString = 'SELECT * FROM users WHERE names = ?';
 
connection.query(queryString, [key], function(err, rows, fields) {
    if (err) throw err;
 
    for (var i in rows) {
        console.log(rows[i]);
    }
});
 
connection.end();
```
另一种方式是采用conection.escape()的方式 

```
connection.connect();
 
var key = 'zs'; 
var queryString = 'SELECT * FROM users WHERE name = ' + 
                   connection.escape(key);
 
connection.query(queryString, function(err, rows, fields) {
    if (err) throw err;
 
    for (var i in rows) {
        console.log(rows[i]);
    }
});
```
#### 数据插入
``` js
var user={name:"ll",password:"1234adsf"};
var query=connection.query('insert into users set ?',user,function(err,result){

});
console.log(query.sql);
```

>insert into users set `name` = 'll', `password` = '1234adsf' //输出的query.sql语句

#### 关闭连接
在操作完成之后我们需要对mysql进行关闭，同时我们必须要对一些非正常关闭进行相应的处理。

``` js
//不管是正常或者非正常的关闭都会触发close这个回调事件，
//我们可以在触发close操作之后检查关闭的原因从而进行相应的处理
connection.on('close', function(err) {
  if (err) {
    //倘若出现非正常关闭我们重新进行连接 
    connection = mysql.createConnection(connection.config);
  } else {
    console.log('正常关闭.');
  }
});
``` 
