# 不得不说的原型与原型链：prototype与__proto__

## 1、纸面意思
啥是原型，啥是原型链？<br/>
原型：一个属性，属性名叫prototype，只有构造函数有，比如Foo.prototype;<br/>
原型链：一个属性，属性名叫__proto__，万物皆有，链状相连，最后归宗到Object.prototype上，Object.prototype的__proto__值为null；<br/>

## 2、尝试理解一下这两个点，记住一定配合控制台去打印。
* 访问一个实例的属性，JS会先在实例内部寻找，找不到的话，沿着原型链继续找下去。
```javascript
    function Foo(){this.name='我是构造函数Foo'} //一个构造函数Foo
    
    var foo = new Foo(); //通过new Foo()，得到实例化的foo
    
    console.log(foo.bar); //访问foo的bar属性，控制台打印一下，得到undefined
    
    Foo.prototype.bar = '我是bar属性，在Foo.prototype(Foo的原型)里';  //给Foo.prototype添加bar属性
    
    console.log(foo.bar); //再次访问foo的bar属性，控制台打印一下看看。结果验证了"沿着原型链找下去"这句话。
    
    Object.prototype.examAttr = '我是examAttr属性，在Object.prototype(Object的原型)里'; //给Object.prototype添加examAttr属性
    
    console.log(foo.examAttr); //访问foo的examAttr属性，控制台打印一下看看。结果验证了"一直找到Object.prototype"这句话。
```
    
    配合之前写的纸面意思，回味一下上面的代码。
* 每个构造函数都有一个原型，这个原型的constructor属性就是这个构造函数。
```javascript
    function Foo(){this.name='我是构造函数Foo'} //一个构造函数Foo
    
    console.log(Foo.prototype); //打印结果可以看到一个Object对象，即Foo的原型，里面有一个constructor属性，属性值即为Foo函数。
    
    var foo = new Foo(); //实例化
    
    console.log(foo.constructor); //foo中没有constructor属性，沿着原型链找到Foo的原型(即上面打印的结果)，得到Foo原型的constructor属性值，即Foo函数。
```

    以上代码解释了为什么通过查看实例的constructor属性可以得到实例的构造函数。重点在于“沿着原型链找”。
## 3、从老生常谈的JS实现new运算符过程来剖析
```javascript
//写一个构造函数，定义其prototype
function Foo(name) {
    this.name = name;
}
Foo.prototype = {
    constructor:Foo, //由于重新定义了prototype，咱们把constructor属性补上。
    say: function () {
        console.log('My name is ' + this.name);
    }
};
//JS实现new的方法generate
function generate(Fun,arguments) {
    var foo = {}; //新建一个空对象
    Fun.apply(foo, arguments); //利用apply改变this指向，现在运行Fun时，内部this指向foo空对象，那么给this.name赋值就变成了给foo.name赋值。
    foo.__proto__ = Fun.prototype; //把foo的__proto__属性指向Fun.prototype。
    return foo;
}
//执行generate，模拟new
var foo = generate(Foo,["Terry"]); //相当于 var foo = new Foo("Terry")
//验证实例方法
foo.say();
//查看foo的构造函数
console.log(foo.constructor);
```
#### 上面的代码完全实现了new运算符的逻辑，所以说，new运算符就是上面这段代码的语法糖而已。

    下面这张图想必很多人都很熟悉，我截取了小上半部分，
    不要考虑看不见的导线，只关心f1、Foo、Foo.prototype三者的关系就够了。
    配合下面这张图，再回头看一下JS实现new的过程：
<img src="https://github.com/TerryBeanX2/Dive-Into-JS/raw/master/pics/top.jpg" />

###### 理不清没关系，我们来解析一下：

    图中表示，f1由new Foo而来，而f1的__proto__连接着Foo.prototype，
    这说明Foo的实例f1的__proto__(原型链)是指向Foo.prototype(原型)的，
    你再回头去看我们用JS实现new所封装的方法generate，有这么一句：
    foo.__proto__ = Fun.prototype;
    那不就是手动把foo(图中的f1)的原型链指向Fun(图中的Foo)的原型吗！
    
## 4、ES5面向对象中常用的混合模式

    在封装代码/插件的时候，使用面向对象的混合模式来写，代码结构是这样的：
    
```javascript
function MyPlugin(name){
    this.name = name; //每个实例都不一样的属性，写在构造函数里。
}
MyPlugin.prototype = {
    publicFun:function(){
        console.log('我是公共方法，所有实例共用'); //每个实例都调用一样逻辑的代码，封装成方法写进构造函数的原型里
        console.log(this.name+'使用了插件'); 
    }
}
//使用插件
var myPlugin = new MyPlugin('小明');
myPlugin.publicFun(); //实例并没有publicFun方法，但是JS从myPlugun.__proto__中找到了public。
```

通过找“点”大法(前面教程有说过)，可以发现publicFun内部的this指向myPlugin，<br/>
也就验证了为什么[1、理解this指向的小技巧](https://github.com/TerryBeanX2/Dive-Into-JS/tree/master/this)中小结里的第三点：<br/>
“明确区分函数是[构造函数]还是[普通函数]，[构造函数]内的this指向实例化后的对象；”
###### 这回知道为什么插件可以这么写，并且实例化后的插件可以直接调用写在prototype里的方法了吧？

## 小结
* 关于原型与原型链，没有什么“一句话来说”，仔细通读这篇教程吧，尤其是那张图。

欢迎转载，需要注明原址。如果帮到你，希望得到你的Star~<br/>
PS:教程之间紧密联系，不懂的地方，请好好看下[目录](https://github.com/TerryBeanX2/Dive-Into-JS#提供给有一定js基础开发者进阶知识点大白话解读)，有没有你不懂的那个关键字在里面。
    
