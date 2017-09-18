## 引子
> 什么也不说了，我只是个3000分CV前端手。

先通过几种创建方式来慢慢了解JS中第一座大山。
## 工厂模式
```
    function createPerson(name,age){
        var o = new Object();
        o.name = name ;
        o.age = age;
        
        return o;
    }
    var person1 = creatPerson('winter',23);
    var person2 = creatPerson('sun',21);
```
工厂模式，科科...了解下就好了，按照书中所言，js不像是java那样创建类，所以老一辈的大佬们创造出了这种函数。  
被淘汰的原因呢，也比较直白，无法解决对象识别问题，怎么知道一个对象的类型，因为工厂模式里所有的函数都只是Object的实例。  
所以，又诞生了一种新的设计模式——构造函数模式。
## 构造函数模式
构造函数可以用来创建指定的类型的对象，类似Object和Array这样的原生构造函数，档次瞬间拉开了工厂模式。
```
    function Person(name,age){
        
        this.name = name ;
        this.age = age;
        

    }
    var person1 = new Person('winter',23);
    var person2 = new Person('sun',21);
```
这种模式和之前的模式的不同之处也比较明显：
* 没有显式地创建对象；
* 直接的将属性和方法付给了 *this* 对象；
* 没有 *return* 语句
* 按照惯例，构造函数始终都应该以一个大写字母开头，而非构造函数则以一个小写字母开头。  

这种模式也很好理解，每次创建一个新实例，必须使用 *new* 操作符。然而以这种方式调用构造函数实际上会经历一下4个步骤：  
（1） 创建一个新对象；  
（2） 将构造函数的作用域赋给新对象(因此 *this* 就指向了这个对象)；  
（3） 执行构造函数中的代码(为这个新对象添加属性);  
（4） 返回新对象。  
*new* 出来的 *person1* 和 *person2* 分别就是 *Person* 的新实例，这两个对象都有个 *constructor* (构造函数)属性来说明他们与 *Person*的联系，该属性指向 *Person*。
```
    console.log(person1.constructor == Person);  //true
    console.log(person2.constructor == Person);  //true
```
按照书上的解释，原本对象的 *constructor* 属性是最初来标识对象类型的。但是，提到监测对象类型，还是 *instanceof* 操作符更靠谱一些。  

### instanceof 运算符
在 *JavaScript* 中，判断一个变量的类型常常会用 *typeof* 运算符，在使用 *typeof* 运算符时采用引用类型存储值会出现一个问题，无论引用是什么类型的对象，它都返回 *“onject”*。 *ECNAScript* 引入另一个 *Java* 运算符 *instanceof* 来解决这个问题。
*instanceof* 运算符与 *typeof* 运算符相似，用于识别正在处理的对象的类型。与 *typeof* 方法不同的是， *instanceof* 方法要求开发者明确确认对象为某指定类型。
```
var oStringObject = new String("hello world"); 
console.log(oStringObject instanceof String);   // 输出 "true"

// 判断 foo 是否是 Foo 类的实例
function Foo(){} 
var foo = new Foo(); 
console.log(foo instanceof Foo)//true
```
这里就简单的再次了解一下 *instanceof* ，它还有很多复杂的用法以及一些机制，在以后的继承那里在深入探索一下。  
回到正文。

---
前面也说了通过构造函数可以将它的实例标识为一种指定的类型；而这也是构造函数优越于工厂模式的地方。
### 构造函数也是函数
通过上文的构造函数 *Person* 可以看出，构造函数本质上也是一种函数，不存在特殊语法。可以说，任何函数，只要通过 *new* 操作符来调用，那它就可以作为构造函数;而任何函数，如果不通过 *new* 操作符来调用，那它和普通函数没什么两样。
### 构造函数的缺点
构造函数的不足主要体现在每一个方法都要在每个实例上重新创建一遍。
```
    function Person(name,age){
        
        this.name = name ;
        this.age = age;
        this.sayname = function(){ 
        
            console.log(this.name);
            
        };
        

    }
    var person1 = new Person('winter',23);
    var person2 = new Person('sun',21);
    person1.sayname == person2.sayname; // false
```
上述代码证明了不同实例上的同名函数是不相等的。然而，创建两个完成同样任务的 *Function* 实例是完全没播要的；况且有 *this* 对象在，根本不用在执行代码前就把函数绑到特定对象上面。所以也就可以将该函数拿出来，放在全局作用域。  

这样解决了两个实例对象共享在全局作用域定义的同一个 *sayname()* 函数。 但这样的问题就又尴尬了，挂在全局作用域的函数只能被某个函数调用，这让全局作用域有点名不副实。(能解决就好，看起来不雅观)为了tuchule