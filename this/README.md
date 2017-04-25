## 理解JS中this指向的小技巧

在看他人写的js文件时，会看到许多this，对this不熟悉的人很容易蒙圈，这里就说明如何用最简单的方法去找this指向谁。

### 切记！这里说的找“点”，都是在调用的语句里找，不要去声明函数的语句里找！

#### 1、找“点”大法：你找不到“.”的函数调用，this指向一般是windows：
一个方法foo，内部打印this
```javascript
function foo(){
  console.log(this)
}
```
###### 调用
```javascript
foo();    //自己去运行代码看this指向谁
//脑补：
window.foo();   //自己去运行代码看this指向谁
```
不用怀疑，也不用犹豫，找不到任何“.”，this指向window。
以后见到直接调用的foo，自动脑补成window.foo()，因为在这种情况下，这两种写法是一样的。

##### 当函数/匿名函数作为参数时，你是找不到“.”的，这种情况下，函数内部的this指向window。
```javascript
function foo(callback){
    callback();
}
foo(function(){
    console.log(this);  //自己去运行代码看this指向谁
})
```
这个例子就是，匿名函数内部打印了this，它作为参数，内部的this指向window。

#### 2、找“点”大法：找得到“.”的函数调用，this指向一般是最后一个“.”左侧的那个对象：
##### ·调用语句里只能找到一个“.”：<br/>

```javascript
var bar = {name:'我是bar'};
bar.foo = function(){
  console.log(this)
};
```
###### 调用
```javascript
bar.foo();  //自己去运行代码看this指向谁
```
这个例子，我们找到了“.”的存在，“.”左侧是bar，指向是bar。

##### ·调用语句里能找到多个“.”：<br/>

```javascript
var obj = {name:'我是obj'};
obj.bar = {name:'我是bar'};
obj.bar.foo = function(){
  console.log(this)
};

```
###### 调用
```javascript
obj.bar.foo();  //自己去运行代码看this指向谁
```
这个例子，我们找到了俩“.”，最后一个“.”左侧的对象是bar，那么指向就是bar。
