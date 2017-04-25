## 理解JS中this指向的小技巧

在看他人写的js文件时，会看到许多this，对this不熟悉的人很容易蒙圈，这里就说明如何用最简单的方法去找this指向谁。

### 切记！这里说的找“点”，都是在调用的语句里找，不要去声明函数的语句里找！

#### 1、找“点”大法：多数你找不到“.”的函数调用，this指向一般是windows

#### 栗子
先搞一个方法，内部打印this
```javascript
function foo(){
    console.log('我是直接声明的foo')
    console.log(this)
}
```
在你看到的/你写的js代码中间，出现过这么几个调用方式<br/>

⑴、foo前面没有“.”：
不用怀疑，也不用犹豫，找不到任何“.”，this基本就指向window了。
以后见到直接调用的foo，自动脑补成window.foo()，因为在这种情况下，这两种写法是一样的：
```javascript
foo();    //this指向ndow
//脑补：
window.foo();   //this指向window，运行结果与直接调用相同
```
⑵、foo前面有“.”：
```javascript
bar.foo();  //this指向bar
```
看到这个，我们找到了“.”的存在，“.”前面是bar，那么bar是个啥？bar肯定是个对象啦！
