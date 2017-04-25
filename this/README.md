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
调用
```javascript
foo();    //自己去运行代码看this指向谁
//脑补：
window.foo();   //自己去运行代码看this指向谁
```
不用怀疑，也不用犹豫，找不到任何“.”，this基本就指向window了。
以后见到直接调用的foo，自动脑补成window.foo()，因为在这种情况下，这两种写法是一样的。

#### 2、找“点”大法：找得到“.”的函数调用，this指向一般是最后一个“.”左侧的那个对象：
一个对象bar，bar里有个属性是方法foo，foo内部打印this
```javascript
function foo(){
  console.log(this)
}
var bar = {}；
bar.foo = foo;
```
调用
```javascript
bar.foo();  //自己去运行代码看this指向谁
```
这个例子，我们找到了“.”的存在，“.”左侧最近的是bar，那么bar是个啥？bar肯定是个对象啦！指向就是bar了。

那你再看看这个呢？
一个对象obj，obj有个属性是对象bar，bar里面属性是方法foo，foo内部打印this
```javascript
function foo(){
  console.log(this)
}
var obj = {};
obj.bar = {};
obj.bar.foo = foo;
```
调用
```javascript
obj.bar.foo();  //自己去运行代码看this指向谁
```
这个例子，我们找到了俩“.”，最后一个“.”左侧的对象是bar，那么指向就是bar。
