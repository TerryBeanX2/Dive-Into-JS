## 理解JS中this的小技巧

#### 1、找“点”大法：多数你找不到“.”的函数，this指向一般是windows
##### 栗子
先搞一个方法，内部打印this
```javascript
function foo(){
        console.log(this)
    }
```
在你看到的/你写的js代码中间，出现过这么几个调用方式<br/>
(1)foo前面没有“.”
```javascript
  foo();
```
不用怀疑，也不用犹豫，找不到任何“.”，this基本就指向window了。
以后见到直接调用的foo，自动脑补成window.foo()，因为在这种情况下，这两种写法是一样的：
```javascript
  foo();
  window.foo();
```
