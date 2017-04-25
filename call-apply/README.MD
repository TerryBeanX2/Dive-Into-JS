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
##### 上栗子

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
    找到的“.”左边是obj1，说明是obj1调用了add，this应该指向obj1啊！为啥算出来的结果都是obj2里的numA与numB之和呢？
    因为用了call和apply啊！不是刚说完嘛，它们改变了this的指向啊，指向谁啊？第一个参数啊！第一个参数是谁啊？obj2啊！
    所以你写obj1.add.call(obj2)，add方法中this就变成了obj2，就打印出了obj2.numA和obj2.numB的和。
    就起到了“借”的效果。
    
#### 带参数的栗子

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


    

## 3、再说个特殊的apply的栗子
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
##### 教你个apply转换大法： 

    a.apply(b,arguments) ES6的新写法就是 b.a(...arguments)
    ...arguments是ES6语法，解构参数数组，用白话来说就是JS帮你在调用方法时把参数按逗号分开传进去。
    你可以在谷歌控制台里验证一下，ES6写法搞出来的代码，和原本的代码，效果是一样的。
##### 接着分析陆亦可的代码：

    那么 beforeFoo.apply(this,arguments) 就可以转换成 this.beforeFoo(...arguments)；
    而this上文说过指向window，即window.beforeFoo(...arguments) ；
    window是全局环境，不写window也没问题，所以就变成了beforeFoo(...arguments);
    于是不管你修改beforeFoo为带多少个参数，运行的时候JS会通过arguments对象在调用方法时帮你传参，无论参数多少个。
    所以陆亦可的代码就这样借助apply完成了代码可用性的提高。
    最后，侯亮平把这些代码封装起来...搞好了事情。
    所以说，ES6还是很不错的，可以在这个功能点上替代apply写法；
    但是你在实际情况下依然会见到许多apply的代码，所以你还是得懂他为何这么写。










