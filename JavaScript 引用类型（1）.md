## 引子
> 跑一遍概念。

对象在 JavaScript 中被称为引用类型的值，而且有一些内置的引用类型可以用来创建特定的对象。

也就是说：引用类型的值(对象)是**引用类型**的一个实例。
## Object 类型

大部分遇到的引用类型值都是 Object 类型的实例，而且 Object 也是 ECMAScript 中使用最多的一个类型。

## Array 类型

Array 类型也是很常用的。   
ECMAScript  数组的每一项可以保存任何类型的数据。

Array 构造函数使用时也可以省略 new 操作符。

### 检测数组——Array.isArray()

前面提到过，在JavaScript 中检测某个对象是不是数组用的是 instanceof 操作符。

但这只是在同一个全局环境下可以完美解决该判断，但是在多个全局对象下，不同的全局对象拥有不同的全局对象，从而拥有不同的内置类型构造函数。   
这可能会引发一些问题。比如，表达式 ***[] instanceof window.frames[0].Array 会返回false*** ，因为 ***Array.prototype !== window.frames[0].Array.prototype*** ，因此你必须使用 ***Array.isArray(myObj)*** 或者 ***Object.prototype.toString.call(myObj) === "[object Array]"*** 来判断myObj是否是数组。

其中 *Array.isArray()* 是 ECMAScript 5新增的方法，它的目的是最终确定某个值到底是不是数组。

### 转换方法

如前所述，所有对象都具有 toLocaleString()、toString（）和 valueOf() 方法。   
toString() 方法返回由数组中每个值的字符串形式拼接而成的一个以逗号分隔的字符串。   
而 valueOf() 返回的还是数组。

join() 方法，可以使用不同的分隔符来构建字符串。

如果数组中某一项的值是 null 或者 undefined ，那么该值在 以上四种方法返回的是空字符串表示。

### 栈方法

栈是一种 **LIFO（Last-In-First-Out 后进先出）** 的数据结构，也就是最新添加的项最早被移除。而栈中项的插入(叫做推入)和移除（叫做弹出），只发生在一个位置——栈的顶部。ECAMScript 为数组专门提供了 push（）和 pop（）方法，以便实现类似栈的行为

push() 方法 可以接受任意数量的参数，把他们逐个添加到数组的末尾，并返回修改后数组的长度。
 
pop（）方法则从数组末尾移除最后一项，减少数组的 length 值，然后返回移除的项。

### 队列方法

栈数据结构的访问规则是 **LIFO**（后进先出），而队列数据结构的访问规则是 **FIFO**（First-In-First-Out 先进先出）。队列在列表的末端添加项，从列表的前端移除项。

shift() 和 unshift() 在数组的第一项添加或者删除一项，并将数组的长度-1.

结合使用 shift() 和 push() 方法，可以像是用队列一样使用数组；同样使用 unshift() 和 pop() 方法，可以相反的方向来模拟队列，即在数组前端添加项，在数组的末尾移除项。

### 重排序方法

数组中可以直接用来排序的方法有两个： reverse() sort()。

reverse() 方法会反转数组。

sort()方法：
如果没有指明 compareFunction ，那么元素会按照转换为的字符串的诸个字符的Unicode位点进行排序。例如 "Banana" 会被排列到 "cherry" 之前。数字比大小时，9 出现在 80 之前，但这里比较时数字会先被转换为字符串，所以 "80" 比 "9" 要靠前。

如果指明了 compareFunction ，那么数组会按照调用该函数的返回值排序。即 a 和 b 是两个将要被比较的元素：

* 如果 compareFunction(a, b) 小于 0 ，那么 a 会被排列到 b 之前；
* 如果 compareFunction(a, b) 等于 0 ， a 和 b 的相对位置不变。
* 如果 compareFunction(a, b) 大于 0 ， b 会被排列到 a 之前。
* compareFunction(a, b) 必须总是对相同的输入返回相同的比较结果，否则排序的结果将是不确定的。


```
var numbers = [4, 2, 5, 1, 3];
numbers.sort(function(a, b) {
  return a - b;
});
console.log(numbers);

// [1, 2, 3, 4, 5]
```
### 操作方法

***contact()*** 方法    
用于合并两个或多个数组。此方法不会更改现有数组，而是返回一个新数组。

***slice()*** 方法   
返回一个从开始到结束 **(不包括结束)** 选择的数组的一部分 **浅拷贝** 到一个新数组对象。原始数组不会被修改。

```
arr.slice();
// [0,end]

arr.slice(begin);
// [begin, end];

arr.slice(begin, end);
// [begin, end]

```
* 结束位置大于数组长度， slice 也会一直提取到原数组末尾。
* 结束位置小于起始位置，则返回空数组。


***splice()*** 方法   
通过删除现有元素和/或添加新元素来更改一个数组的内容。

* **删除：** 可以删除任意数量的项，只需要指定 2 个参数： 要删除的第一项位置和要删除项数。例如： splice(0,2) 会删除数组中前两项。
* **插入：** 可以向指定位置插入任意数量的项，只需要提供 3 个参数： 起始位置、0(要删除的项数)和要插入的项。
* **替换：** 可以向指定位置插入任意数量的项，且同时删除人数量的项。

splice() 方法始终都会返回一个数组，该数组中包含从原始数组中删除的项(如果没有任何删除项，则返回一个空数组)。

### 位置方法


```
var array = [2, 5, 9];
array.indexOf(2);     // 0
array.indexOf(7);     // -1
array.indexOf(9, 2);  // 2
array.indexOf(2, -1); // -1
array.indexOf(2, -3); // 0

var array = [2, 5, 9, 2];
var index = array.lastIndexOf(2);
// index is 3
index = array.lastIndexOf(7);
// index is -1
index = array.lastIndexOf(2, 3);
// index is 3
index = array.lastIndexOf(2, 2);
// index is 0
index = array.lastIndexOf(2, -2);
// index is 0
index = array.lastIndexOf(2, -1);
// index is 3
```

**indexOf()** 方法   
返回在数组中可以找到一个给定元素的第一个索引，如果不存在，则返回-1。

**lastIndexOf()** 方法     
返回指定元素（也即有效的 JavaScript 值或变量）在数组中的最后一个的索引，如果不存在则返回 -1。从数组的后面向前查找，从 fromIndex 处开始。

在比较第一个参数与数组中的每一项时，会使用全等操作符。

### 迭代方法

***arr.every(callback()):***  
对数组中的每一项运行给定的函数，如果该函数对每一项都返回 true ，则返回 true

*callback* 被调用时传入三个参数：元素值，元素的索引，原数组。

***arr.some(callback()):***  
对数组中的每一项运行给定的函数，如果该函数对任一项返回 true ，则返回 true

every() 和 some() 相似，它们都用于查询数组中的项是不是满足某个条件。对 every() 来说，传入的函数必须对每一项都返回 true ，这个方法才返回 true ；否则它就返回 false。而 some() 方法则是只要传入的函数对数组中某一项返回 true ，就会返回 true。


```
var  numbers = [1,2,3,4,5];

var result1 = numbers.erery(function(item,index,array){
    return item > 2
})

console.log(result1) //false;

var result2 = numbers.some(function(item,index,array){
    return item > 2
})

console.log(result2) //true;
```

***filter():***   
对数组中的每一项运行给定的函数，返回该函数会返回 true 的项组成的数组。


```
var resulte3 = numbers.filter(function(item,index,array){
    return item > 2;
})

console.log(result3); // [3,4,5]

```
***forEach():***   
对数组中的每一项运行给定的函数。该方法没有返回值。
没法提前跳出 forEach() 循环，除了抛出一个异常，然鹅这样是错误的写法。

### 归并方法

ECMAScript 5 新增两个归并数组方法： reduce() 和 reduceRight() 。这两个方法都会迭代数组的所有项，然后构建一个最终返回的值。

reduce() 方法从数组的第一项开始，逐个遍历到最后。而 reduceRight() 则从数组的最后一项开始，向前遍历到第一项。


```
var total = [0, 1, 2, 3].reduce(function(sum, value) {
  return sum + value;
}, 0);
// total is 6

var flattened = [[0, 1], [2, 3], [4, 5]].reduce(function(a, b) {
  return a.concat(b);
}, []);
// flattened is [0, 1, 2, 3, 4, 5]
```
两个方法都有两个参数： 一个是 callback()，一个是初始值。

* **callback** 执行数组中每个值的函数，包含四个参数
    * **accumulator** 
上一次调用回调返回的值，或者是提供的初始值（initialValue）
    * **currentValue**
    数组中正在处理的元素
    * **currentIndex**
    数据中正在处理的元素索引，如果提供了 initialValue ，从0开始；否则从1开始
    * **array**
    调用 reduce 的数组
* **initialValue**
可选项，其值用于第一次调用 callback 的第一个参数。如果没有设置初始值，则将数组中的第一个元素作为初始值。空数组调用reduce时没有设置初始值将会报错。

如果数组为空并且没有提供 initialValue ， 会抛出 TypeError  。如果数组仅有一个元素（无论位置如何）并且没有提供 initialValue ， 或者有提供 initialValue 但是数组为空，那么此唯一值将被返回并且 callback 不会被执行。

提供 initialValue 通常更安全


## Date 类型

创建 Date 实例用来处理日期和实践。Date 对象基于1970年1月1日 （世界标准时间）起的毫秒数。

[Date文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date)



---
## 参考资料

* [Date文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date)