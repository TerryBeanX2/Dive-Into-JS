# 巧妙理解call和apply
想当年我还是个小白的时候，看到call和apply，那都是一脸懵逼啊！<br/>
再加上参数内部this，arguments什么的，虐的我不要不要的，一度产生厌学心理。<br/>
的确，这俩方法对初学者不够友好...<br/>

但是！作为半个老鸟，现在看到call啊什么apply啊什么的，也就微微一笑了。<br/>
想当初茅塞顿开的时候，那心里叫一个痛快，现在就把开窍的过程分享出来。<br/>

## 1、call和apply的区别
先说一下call和apply的区别，你在完全不懂俩函数是干嘛的情况下，你只要记住：<br/>
call和apply的功能是完全一样的，只是第二个参数不一样；<br/>
call可以接收无限多个参数，apply只接收俩参数，并且第二个参数只能是argument。<br/>
“而它们同样的第一个参数，就是新的this指向！”你先不用管引号里的话说明了什么，脑子里默记下这句话就行。<br/>
好了，现在，不要多想，往下看。<br/>


## 2、call、apply会改变this指向
    
    我在实际应用中，最常用的就是用call、apply去“借”另一个对象的方法来用，其实是call、apply改变了this指向。
#### 上最简单的栗子

    我写了个对象obj1，内部三个属性，两个数字numA、numB、还有个方法add，可以打印numA和numB之和：
    
```javascript
var obj1 ={
    numA:1,
    numB:2,
    add:function(){
         console.log(this.numA + this.numB)
    }
}
obj1.add(); //打印出obj1.numA和obj1.numB的和，即3
```

    现在我写了个对象obj2，内部有只两个属性数字numA和数字numB，没有计算器，但也想求和，怎么办？
    管obj1借啊！怎么借？call、apply啊！
    上代码

```javascript
var obj2 = {
    numA:3,
    numB:4
}
//用call借：
obj1.add.call(obj2); //打印出obj2.numA和obj2.numB的和，即7;
//用apply借：
obj1.add.apply(obj2); //打印出obj2.numA和obj2.numB的和，即7;
```

    有意思吧？明明是obj1的add方法里出现了this，按照《理解JS中this指向的小技巧》中的思路，
    找到的“.”左边是obj1，说明是obj1调用了add，add方法内部的this应该指向obj1啊！为啥算出来的结果都是obj2里的numA与numB之和呢？
    因为用了call和apply啊！不是刚说完嘛，它们改变了this的指向啊，指向谁啊？第一个参数啊！第一个参数是谁啊？obj2啊！
    所以你写obj1.add.call(obj2)，add方法内部的this指向就变成了obj2，就打印出了obj2.numA和obj2.numB的和。
    就起到了obj2向Obj1“借”了方法add的效果。
#### 带参数的栗子
这个栗子是面向对象的栗子，对面向对象不够了解的同学，请尽量读懂[不得不提的原型/原型链](https://github.com/TerryBeanX2/Dive-Into-JS/tree/master/proto)<br/>

    我写了个构造函数Obj1，内部三个属性，两个数字numA、numB、还有个方法add，可以打印numA和numB之和：
    
```javascript
function Obj1(numA,numB){
    this.numA = numA;
    this.numB = numB;
}
Obj1.prototype.add = function(){
         console.log(this.numA + this.numB)
}
var obj1 = new Obj1(1,2);
obj1.add(); //打印出obj1.numA和obj1.numB的和，即3
```

    现在我写了个构造函数Obj2，内部有只两个属性数字numA和数字numB，没有计算器，但也想求和，怎么办？
    管obj1借啊！怎么借？call、apply啊！
    上代码

```javascript
function Obj2(numA,numB){
    this.numA = numA;
    this.numB = numB;
}
var obj2 = new Obj2(3,4);
//用call向实例obj1借：
obj1.add.call(obj2,3,4); //打印出obj2.numA和obj2.numB的和，即7;
//用apply向实例obj1借：
obj1.add.apply(obj2,[3,4]); //打印出obj2.numA和obj2.numB的和，即7;
//用call向构造函数Obj1借：
Obj1.prototype.add.call(obj2, 3, 4); //打印出obj2.numA和obj2.numB的和，即7;
//用apply向构造函数Obj1借：
Obj1.prototype.add.apply(obj2, [3, 4]); //打印出obj2.numA和obj2.numB的和，即7;
```

    这个栗子恰好说明了带参数的情况怎么“借”另一个对象的方法，也把apply和call的不同解释明白了，就是个传参不同。
    看这个 Obj1.prototype.add.call(obj2, 3, 4) ，眼熟吗？
    像不像 Array.prototype.forEach.call(xxx) ？就是这么来的，xxx想借用Array.prototype的forEach方法完成遍历。
## 3、特殊栗：在第一个参数为this并且this指向window的情况下，apply的应用
比如有个需求，需要做到每次调用先前别人写好的方法时，先在前面运行我们添加的代码：<br/>
下面的代码不一定是最好的实现本需求的代码，但可以演示apply的应用。<br/>
生动的具体化一下：

###### 先前陈海写的的代码：

```javascript
    function foo(){
        console.log('我是陈海，我拍床戏去了');
    }
    foo();
```

    现在侯亮平接手的反贪局接管了代码，
    需求是，不改变陈海写的代码的情况下，在每次调用陈海写的代码时先打印一些话。
    
###### 林华华自告奋勇，用一段代码帮侯局长完成了需求：
    
```javascript
    function beforeFoo(num){
        console.log('侯亮平知道陈海有床戏,一共'+num+'场');
    }
    var fooOld = foo;
    foo = function(num){
        beforeFoo(num); //这里将会被陆亦可修改
        fooOld();
    }
    foo(30); //运行一下看看效果
```

###### 陆亦可觉得这个代码复用性太低，每次beforeFoo的参数个数有变化，还要一同修改下面的代码，于是改进了一下：

```javascript
    function beforeFoo(num,text){
        console.log('侯亮平知道陈海有床戏,一共'+num+'场,',text);
    }
    var fooOld = foo;
    foo = function(){
        beforeFoo.apply(this,arguments); //陆亦可修改了这里
        fooOld();
    }
    foo(30,'醒不过来'); //运行一下看看效果
```

    刹车！陆亦可在她的代码里用到了apply！
    我们来分析一下她干了啥，完成了啥功能：
    修改：把beforeFoo(num)改成beforeFoo.apply(this,arguments)；
    完成功能：beforeFoo可以任意修改参数个数，不必再修改后续代码。

##### 是不是挺神奇，我们来分析一下：？

    首先来看看beforeFoo.apply(this,arguments)中的this：
    1、this出现在新foo的内部；
    2、foo的调用语句是foo(30，'醒不过来')，是全局直接调用，找不到“.”；
    根据我上一篇this教程，通过这两点，不难发现this指向window；

    那么，根据本片文章前面提到过的，apply即“借”，beforeFoo.apply(this,arguments)，
    也就是this借用了beforeFoo方法，向谁借的？beforeFoo左边没有“.”，是全局调用，原来是向window借的！
    而刚刚说过，此this指向window，这就好玩了：window向window借用了beforeFoo方法！

    你说，那不就是beforeFoo直接调用吗，绕一圈干嘛？别忘了还有arguments参数呢！
    这么绕了一圈，在绕圈调用的过程中，JS会解析arguments参数，自动用“，”帮你把参数分开传入beforeFoo方法，
    以后无论你如何修改beforeFoo方法的参数个数，都不用再改剩余的代码了。
    陆亦可利用这一点，巧妙的借助apply完成了代码的可用性提高。

    PS：ES6新出的拓展符可以完成一样的效果：before.apply(this,arguments)可以写成before(...arguments);
    请细细品味，发现道理都是想通的，有趣吧。

    最后，侯亮平风骚的封装了代码，以后陈海再也不怕不知道自己会演多少场床戏了。
    
## 小结
* 看到call、apply出现，遵循着“借”的思想，再配合“改变this指向”，<br/>
* XX.call(YY)，那么这个“XX”就是被借的方法；<br/>
* YY就是借方法的那个对象，this指向它；<br/>
* XX是谁的？谁调用就是谁的，XX左边没有“.”，说明是全局调用，那就是window的。<br/>
* 一定要做到“不找出到底是向谁借的就不罢休”。<br/>

#### 当你终于找到物主(到底是“借”的谁的方法)，接着理清this指向，你也就透彻的明白call和apply了。

欢迎转载，需要注明原址。如果帮到你，希望得到你的Star~









