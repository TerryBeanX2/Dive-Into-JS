# 不得不说的原型与原型链：prototype与__proto__

## 1、纸面意思
啥是原型，啥是原型链？<br/>
原型：一个属性，属性名叫prototype，只有构造函数有，比如Foo.prototype;<br/>
原型链：一个属性，属性名叫__proto__，万物皆有，链状相连，最后归到Object上，Object的__proto__值为null；<br/>

## 2、从老生常谈的JS实现new运算符谈起
```javascript
//写一个构造函数，定义其prototype
function Foo(name) {
    this.name = name;
}
Foo.prototype = {
    say: function () {
        console.log('My name is ' + this.name);
    }
};
//JS实现new的方法
function generate(Fun,arguments) {
    var foo = {};
    Fun.apply(foo, arguments);
    foo.__proto__ = Fun.prototype;
    return foo;
}
//开始模拟new
var foo = generate(Foo,["Terry"]); //相当于 var foo = new Foo("Terry")
//验证实例
foo.say();
```
#### 配合下面这张图，再回头看一下JS实现new的过程：
