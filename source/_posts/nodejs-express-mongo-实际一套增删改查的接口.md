---
title: nodejs+express+mongo 实际一套增删改查的接口
date: 2017-07-26 14:59:40
tags: node.js
---

 通过提交的类型来执行相应的动作 delete 删除对象 put 更新对象 get 获取对象 post 创建对象
 <!--more-->
# nodejs+mongoose+express 实现数据的增删改查


#### 定义数据模型
``` js
//bear.js
var mongoose=require('mongoose');
var Schema=mongoose.Schema;
var BearSchema=new Schema({
    name:String
});
module.exports=mongoose.model('Bear',BearSchema);
```
#### package.json

``` json
{
    "name":"node-mongo-express",
    "version":"1.0.0",
    "description":"实现数据的增删改查",
    "main":"server.js",
    "license":"MIT",
    "dependencies":{
        "express":"~4.0.0",
        "mongoose":"~3.6.13",
        "body-parser":"~1.0.1"
    }
}

```
#### 实例代码

``` js
var express = require('express');
var app = express();
var bodyParser = require('body-parser');
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/myapp')
app.use(bodyParser.urlencoded({ extended: true }));
var Bear = require('./app/models/bear');
app.use(bodyParser.json());
var port = process.env.PORT || 8080;
var router = express.Router();
router.use(function (req, res, next) {//增加
    console.log('触发中间件.');
    next();
});
router.route('/bears').post(function (req, res) {//post请求来增加一条数据
    var bear = new Bear();
    bear.name = req.body.name;
    bear.save(function (err) {
        if (err)
            res.send(err);
        res.json({ message: 'Bear created!' });
    });
})
router.route('/bears/:bear_id')
.get(function (req, res) {//根据id来查找对应的数据
    console.log(req.params.bear_id);
    Bear.findById(req.params.bear_id, function (err, bear) {
        if (err)
            res.send(err);
        res.json(bear);
    });
})
.put(function (req, res) {//通过put请求来更新数据
    Bear.findById(req.params.bear_id, function (err, bear) {
        if (err)
            res.send(err);
        bear.name = req.body.name;
        bear.save(function (err) {
            if (err)
                res.send(err);
            res.json({ message: 'Bear updated!' });
        });
    });
})
.delete(function (req, res) {//删除数据
    Bear.remove({
        _id: req.params.bear_id
    }, function (err, bear) {
        if (err)
            res.send(err);
        res.json({ message: 'Successfully deleted' });
    })
})
router.get('/', function (req, res) {//访问首页
    res.json({ message: 'hooray!welcome to our api!' });
});
app.use('/api', router);


app.listen(port);
console.log("Magic happens on port " + port);
```
