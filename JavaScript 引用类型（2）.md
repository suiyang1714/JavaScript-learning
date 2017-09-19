## 引子
> go on

## RegExp 类型

关于正则这一块我在前面的文章也有总结，传送门 [ES6——正则的扩展](http://blog.suiyangdadi.com/detail/595a14ec3784ef08d76c683b)

## Function 类型

每一个函数都是 Function 类型的实例，而且都与其他引用类型一样具有属性和方法。


```
 var sum = new Function("num1","num2","return num1 + num2");
```

从技术角度来看，这是一个函数表达式，但是一般我们都不用这种方法来定义函数，因为这样的语法会导致解析两次代码（第一次是解析常规 ECMAScript 代码，第二次是解析传入构造函数中的字符串），从而影响性能，所以这种方法不推荐使用。

但上述语法对于理解“函数是对象，函数名是指针”
的概念更加直观。

对于函数没有重载问题，前面也说过了，将函数名想象为指针，也更容易理解。

## 基本包装类型

为了方便操作基本类型值，ECMAScript还提供了 3 个特殊的引用类型， Boolean、 Number、String。
每当读取一个基本类型值的时候，后台就会创建一个对应的基本包装类型的对象，从而让我的们能够调用一些方法来操作这些数据。

后台具体处理过程：
1. 创建 String 类型的一个实例；
2. 在实例上调用指定的方法；
3. 销毁这个实例。

转换成 ECMAScript 代码如下：

```
var s1 = new String("some text");
s1.subString(2);
s1 = null;
```

上面三个步骤同样适用于 Boolean 和 Number。

如果自己要添加私有属性和方法，必须显式地调用 Boolean 、 Number 和 String 来创建基本包装类型的对象。
否则：

```
var s1 = "some text";
s1.color = "red"; //执行完毕后销毁

console.log(s1.color); //新创建的 String 对象并没有该属性
```

对于实例化的 以上三种类型，都是不推荐使用的，了解一下就可以。


## 单体内置对象

本身 ECMAScript 就存在着一些不必显式地实例化的内置对象，因为它们本身在 ECMAScript 程序执行之前已经存在了。向之前所说的 OBject、Array、String。
除此之外还有两个单体内置对象： Global 和 Math。

### Global 对象
在 ECMAScript 中，不管是对那种类型的一些方法和属性都有一些是属于 全局对象的方法。比如说：isNaN()、isFinite()、parseInt()这些，实际上全都属于 Global 对象的方法。

当然在 ECMAScript 6 中逐渐开始讲这些全局方法一直到相应对象上

比如说 **parseInt() 和 parseFloat()** 这两个方法，在ES 6 中就被移植到 Number对象上： Number.parseInt() 和 Number.parseFloat()。功能完全一样。

***URL的编码方法***

全局对象对 URL的编码方法
* encodeURL()
* encodeURLComponent()
这两个方法我到现在都没遇到过，所以了解有印象即可。

***eval() 方法***

说实话这个方法我到现在都没用过。

除了上次面试，问过我这个方法我也是一脸懵逼。

言归正传，mdn上给出的描述是：  
*eval()* 函数会将传入的字符串当做J avaScript 代码进行执行。


> 在 eval() 中创建的任何变量或函数都不会被提升，因为在解析代码的时候，它们被包含在一个字符串中；它们只在 eval() 执行的时候创建。   
> 在严格模式下，外部访问不到 eval() 中创建的任何变量或函数；同样，在严格模式下，为 eval() 赋值也会到时错误


```
eval("var msg = 'hello world!'")
console.log(msg);  // "hello world!"

"use strict";

eval("var msg = 'hello world!'")
console.log(msg);  // error

eval = "hi" //causes error
```

***Global 对象的属性***

除了特殊值 undefined 、NaN 以及 Infinity 都是Flobal 对象属性外，所有原生引用类型的构造函数也都是 Global 对象的属性。

ECMAScript 5 明确禁止给 undefined、 NaN 和 Infinity 赋值，这样做即使在非严格模式下也会导致错误。

### Math 对象

对于 Math 对象常用的属性：
* Math.min()
* Math.max()
* Math.ceil()
* Math.floar()
* Math.round()
* Math.random()
* 其他

对于 Math.min() 和 Math.max() 求最小最大值

Math.ceil() 执行向上舍入，即它总是将数值向上舍入为最接近的整数；

Math.floar() 执行向下舍入，即它总是将数值向下舍入为最接近的整数；

Math.round() 执行标准舍入，即它总是将数值四舍五入为最接近的整数。

Math.random() 返回一个大于等于 0 小于 1 的一个随机数。

可以利用 Math.random() 从某个整数范围内随机选择一个值


```
值 = Math.floor(Math.random() * 可能值的总数 + 第一个可能值) 

var num = Math.floor(Math.random() * 10 +1);

// 1-10
```
