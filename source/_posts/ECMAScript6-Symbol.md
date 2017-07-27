---
title: 'ECMAScript6  Symbol '
date: 2017-07-27 09:57:46
tags: node.js
---
es6之前所有的属性名都是字符串类型，容易造成属性名的冲突。所以在es6新增了一种数据类型Symbol，表示**独一无二**的值。Symbol并不是一个对象，所以不能通过new的方法进行创建，同时也不能添加属性。
<!--more-->

``` javascript
let s=Symbol();
console.log(typeof s)//out:symbol
```

Symbol与字符串处理

``` js
var s1=Symbol('foo');通过字符串进行构造
var s2=Symbol('bar');
console.log(s1)//out:symbol(foo)
console.log(s2)//out:symbol(bar)
console.log(s1.toString())
console.log(s2.toString())
```

Symbol函数的参数只表示对当前Symbol值的描述，即使两个Symbol的参数相同也并不相等.

``` js
var s1=Symbol();
var s2=Symbol();
console.log(s1==s2);//out:flash

var s1=Symbol("foo");
var s2=Symbol("foo");
console.log(s1==s2);//out:flash
```
Symbol通过Object.getOwnPropertySymbols来返回一个数组
Reflect.ownKeys方法可以返回所有类型的键名，包括常规键名和Symbol键名。

**Symbol.for(name)**，接受一个字符串，搜索是否存在着为name的Symbol的对象，如果有返回这个Symbol值，否则创建一个以该字符串为名称的Symbol字符。

```js
var s1 = Symbol.for('foo');
var s2 = Symbol.for('foo');

s1 === s2 // true
```

**利用Symbol创建单例模式**

``` js 
//mod.js文件
function A(){
    this.foo='hello';
}
if(!global._foo){
    global._foo=new A();
}
module.exports=global._foo;
```
因为global._foo是可写的对象，一旦其他改写了该对象就会使其他的加载mod.js模块失效

``` js
//main.js
var mod=require('./mod.js');
console.log(mod.foo);
```

下面采用Symbol的方式FOO_KEY本身不可必定，通过Symbol.for可能保证获取到的foo对应的Symbol对象是唯一的。但同样存在着一个问题就是用户一旦清空缓存的时候会重新加载致使该模块失效 。

``` js
//mod2.js
const FOO_KEY=Symbol.for('foo');
function A(){
    this.foo='hello';
}
if(!global[FOO_KEY]){
    global[FOO_KEY]=new A();
}
module.exports=global[FOO_KEY];

//main2.js
var a=require('./mod2.js');
global[Symbol.for('foo')]=123;
console.log(global[Symbol.for('foo')]);
```
#### 属性及方法
>属性  
**Symbol.length**  
长度属性值为1。  
**Symbol.name**  
仅Chrome/v8。返回符号描述。  
**Symbol.prototype**  
描述符号构造函数的原型。  
众所周知的符号  
除了你自己的符号，JavaScript还内建了一些在ECMAScript 5 之前没有暴露给开发者的符号，它们代表了内部语言行为。这些符号可以使用以下属性访问：  
迭代 symbols  
**Symbol.iterator**  
一个返回一个对象默认迭代器的方法。使用 for...of.  
正则表达式 symbols  
**Symbol.match**  
一个用于对字符串进行匹配的方法，也用于确定一个对象是否可以作为正则表达式使用。使用 String.prototype.match().  
**Symbol.replace**  
一个替换匹配字符串的子串的方法. 使用 String.prototype.replace().    
**Symbol.search**  
一个返回一个字符串中与正则表达式相匹配的索引的方法。使用String.prototype.search().  
**Symbol.split**  
一个在匹配正则表达式的索引处拆分一个字符串的方法.。使用 String.prototype.split().  
其他 symbols  
**Symbol.hasInstance**  
一个确定一个构造器对象识别的对象是否为它的实例的方法。使用 instanceof.  
**Symbol.isConcatSpreadable**  
一个布尔值，表明一个对象是否应该flattened为它的数组元素。使用Array.prototype.concat().  
**Symbol.unscopables**  
拥有和继承属性名的一个对象的值被排除在与环境绑定的相关对象外。   
**Symbol.species**  
一个用于创建派生对象的构造器函数。.  
**Symbol.toPrimitive**    
一个将对象转化为基本数据类型的方法。    
**Symbol.toStringTag**  
用于对象的默认描述的字符串值。使用Object.prototype.toString().
方法  
**Symbol.for(key)**   
使用给定的key搜索现有符号，如果找到则返回符号。否则将得到一个新的使用给定的key在全局符号注册表中创建的符号。  
**Symbol.keyFor(sym)**  
为给定符号从全局符号注册表中检索一个共享符号键。