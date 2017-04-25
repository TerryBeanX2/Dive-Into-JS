# 巧妙理解call和apply
想当年笔者还是个小白的时候，看到call和apply，那都是一脸懵逼啊！<br/>
再加上参数内部this，arguments什么的，虐的我不要不要的，一度产生厌学心理。<br/>
的确，这俩方法对初学者不够友好...<br/>

但是！作为半个老鸟，现在看到call啊什么apply啊什么的，也就微微一笑了。<br/>
想当年茅塞顿开的时候，那心里叫一个痛快，现在就把开窍的过程分享出来。<br/>

## 1、call和apply的区别
先说一下call和apply的区别，你在完全不懂俩函数是干嘛的情况下，你只要记住：<br/>
call和apply的功能是完全一样的，只是第二个参数不一样；<br/>
call可以接收无限多个参数，apply只接收俩参数，并且第二个参数只能是argument。<br/>
好了，现在，你只需要记住这么多，不要多想，看下面的例子。<br/>

## 2、举个基本不相关的栗子
比如我们要搞一个装饰器，使之前写的函数运行前先运行我们添加的代码：<br/>
生动的具体化一下：

    先前陈海写的的代码：

```javascript
    function foo(){
        console.log('我是陈海，我拍床戏去了');
    }
```

    现在侯亮平接手的反贪局接管了代码，
    需求是，不改变陈海写的代码的情况下，在每次调用陈海写的代码时先打印一些话。
    林华华自告奋勇，用一段代码帮侯局长完成了需求：
    
```javascript
    function beforeFoo(num){
        console.log('侯亮平知道陈海有床戏,一共'+num+'场');
    }
    var fooOld = foo;
    foo = function(num){
        beforeFoo(num);
        fooOld();
    }
    foo(30); //运行一下看看效果
```

    陆亦可觉得这个代码复用性太低，每次改变beforeFoo的参数，还要一同修改下面的代码，于是改进了一下：

```javascript
    function beforeFoo(num){
        console.log('侯亮平知道陈海有床戏,一共'+num+'场');
    }
    var fooOld = foo;
    foo = function(){
        beforeFoo.apply(this,arguments);
        fooOld();
    }
    foo(30); //运行一下看看效果
```
