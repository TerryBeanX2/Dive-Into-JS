# 理解JS中this指向的小技巧

在看他人写的js文件时，会看到许多this，对this不熟悉的人很容易蒙圈，这里就说明如何用最简单的方法去找this指向谁。

#### 切记！这里说的找“点”大法中的“.”，都是在调用的语句里找，本教程的全局对象为window！

## 1、找“点”大法：你找不到“.”的函数调用，this指向一般是window：

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

##### ㈠当函数/匿名函数作为参数时，你是找不到“.”的，这种情况下，函数内部的this指向window。
```javascript
function foo(callback){
    callback(); //调用其实在这里，你是找不到“.”的
}
foo(function(){
    console.log(this);  //自己去运行代码看this指向谁
})
```
    这个例子就是，匿名函数内部打印了this，它作为参数，内部的this指向window。
    
## 2、找“点”大法：有“.”的函数调用，this指向一般是最后一个“.”左侧的那个对象：
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
    
##### ㈡如果发现你找到的“.”左侧是prototype，那么再往左找一个“.”，这个“.”左侧的对象是this指向。原理在[不得不提的原型链](https://github.com/TerryBeanX2/Dive-Into-JS/tree/master/proto)中给出。


## 3、面向对象中的this：

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

    当Foo()时，Foo被当做[普通函数]，那么遵循找“点”大法，Foo内部的this是指向window的;
    
    当Foo.prototype.bar()时，Foo还是被当做[普通函数]，遵循找“点”大法，按照2-2，发现找到了prototype，转而遵循㈡，再向左找，发现this指向Foo；
    
    当new Foo()时，Foo作为[构造函数]被实例化，Foo内部的this指向实例化后的Foo，也就是我声明的foo；
    
    当foo.bar时，遵循找“点”大法，按照2-1，发现this指向foo；
    
    当foo.funcWithParam(匿名函数)时，匿名函数前没有“.”，匿名函数作为参数，所以遵循㈠，发现其内部this指向window；
    


#### 4、call和apply会改变this指向，在[巧妙理解call、apply](https://github.com/TerryBeanX2/Dive-Into-JS/tree/master/call-apply)单独详解。



## 小结：

* 找不到“.”的函数调用，其内部的this一般指向window象；
* 找得到“.”的函数调用，其内部的this一般指向最后一个“.”左侧的那个对象，如果左侧是prototype，再向左找一个；
* 明确区分函数是[构造函数]还是[普通函数]，[构造函数]内的this指向实例化后的对象；
* 函数作为参数传递，被调用时其内部的this一般指向window。
* call和apply会改变this指向，参阅[巧妙理解call、apply](https://github.com/TerryBeanX2/Dive-Into-JS/tree/master/call-apply)。
* ES6/7的箭头函数也会改变this指向，这个很简单，我就不多讲啦~

#### 一句话来说，就是“谁调的我(普通函数)，我内部的this就指向谁；new我一下(构造函数)，我内部的this就指向我的实例化”


欢迎转载，需要注明原址。如果帮到你，希望得到你的Star~







