## 理解JS中this指向的小技巧

在看他人写的js文件时，会看到许多this，对this不熟悉的人很容易蒙圈，这里就说明如何用最简单的方法去找this指向谁。

### 切记！这里说的找“点”，都是在调用的语句里找！

#### 1、找“点”大法：你找不到“.”的函数调用，this指向一般是windows：

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

##### ①当函数/匿名函数作为参数时，你是找不到“.”的，这种情况下，函数内部的this指向window。
```javascript
function foo(callback){
    callback(); //调用其实在这里，你是找不到“.”的
}
foo(function(){
    console.log(this);  //自己去运行代码看this指向谁
})
```
这个例子就是，匿名函数内部打印了this，它作为参数，内部的this指向window。

#### 2、找“点”大法：找得到“.”的函数调用，this指向一般是最后一个“.”左侧的那个对象：
##### 2-1、调用语句里只能找到一个“.”：<br/>

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

##### 2-2、调用语句里能找到多个“.”：<br/>

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
这个例子，我们找到了俩“.”，最后一个“.”左侧的对象是bar，那么this指向就是bar。
##### ②如果！！！你找完了，发现左侧是prototype，那么特殊处理：再往左找一个“.”，这个“.”左侧的对象是this指向。原理会在后期教程中给出。

#### 3、面向对象中的this：

```javascript
function Foo(){
    console.log(this);
}
Foo.prototype.bar = function(){
    console.log(this);
}
Foo.prototype.funcWithParam = function(fn){
    fn();
}
```
###### 调用
```javascript
Foo();  //自己去运行代码看this指向谁

Foo.prototype.bar();  //自己去运行代码看this指向谁

var foo = new Foo(); //自己去运行代码看this指向谁

foo.bar(); //自己去运行代码看this指向谁

foo.funcWithParam(function(){ 
    console.log(this);  //自己去运行代码看this指向谁
});

```
当Foo()时，Foo被当做[普通函数]，那么遵循找“点”大法，Foo内部的this是指向window的;<br/>
当Foo.prototype.bar()时，Foo还是被当做[普通函数]，遵循找“点”大法，发现prototype，转而遵循②，再向左找，发现this指向Foo；<br/>
当new Foo()时，Foo作为[构造函数]被实例化，Foo内部的this指向实例化后的Foo，也就是我声明的foo；<br/>
当foo.bar时，遵循找“.”大法，按照2-1，发现this指向foo；<br/>
当foo.funcWithParam(匿名函数)时，因为内部打印this的[匿名函数]作为参数，所以遵循①，发现this指向window；










