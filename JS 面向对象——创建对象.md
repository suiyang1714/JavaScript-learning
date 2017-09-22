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
在 *JavaScript* 中，判断一个变量的类型常常会用 *typeof* 运算符，在使用 *typeof* 运算符时采用引用类型存储值会出现一个问题，无论引用是什么类型的对象，它都返回 *“object”*。 *ECMAScript* 引入另一个 *Java* 运算符 *instanceof* 来解决这个问题。
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

这样解决了两个实例对象共享在全局作用域定义的同一个 *sayname()* 函数。 但这样的问题就又尴尬了，挂在全局作用域的函数只能被某个函数调用，这让全局作用域有点名不副实。(能解决就好，看起来不雅观) 总的来说，这样定义n个全局函数，无法突出自定义引用类型的封装性，所以又催生出另一种模式来解决——原型模式。
## 原型模式
所谓的原型也就是函数的一个属性，每一个函数都有这么个属性 *prototype* (原型)，这个属性是一个指针，他指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。说白了它可以共享属性和方法。
```
    function Person(){
        
    };
    
    Person.prototype.name = 'winter';
    Person.prototype.age = '23';
    Person.prototype.sayname = function(){
        console.log(this.name);
    }
    
    var person1 = new Person();
    person1.sayname(); //'winter'
    
    var person2 = new Person();
    person2.sayname(); //'winter'
```

### 理解原型对象
前面也说了，任何一个函数都有一个 *prototype* 属性。无论什么时候，只要创建一个新函数，就会根据一组特定的规则为该函数创建一个 *prototype* 属性，这个属性指向函数的原型对象。   

默认情况下，所有的原型对象都会自动获得一个 *constructor* （构造函数）属性，这个属性是一个指向 *prototype* 属性所在函数的指针。

通过构造函数 *new* 出来的新实例，本身和构造函数没有任何关系（构造函数本身为空,通过原型来添加的属性和方法），新实例本身也都不包含属性和方法，但是可以调用 *person1.sayname()*。这是通过查找对象属性的过程来实现的。

### isPrototypeOf() & Object.getPrototypeOf()
两者都是用来确定实例与原型对象之间的关系。
* **isPrototypeOf()：** 从本质上讲，如果 [[ prototype ]] 指向调用 *isPrototypeOf()* 方法的对象(Person.prototype),那么这个方法就返回 *true* 。  
     ```
     console.log(Person.prototype.isPrototypeOf(person1)); //true
     console.log(Person.prototype.isPrototypeOf(person2)); //true
     ```
* **Object.getPrototype()：** ECMAScript 5增加的新方法，在所有支持的实现中，这个方法返回[[ prototype ]]的值  
     ```
     console.log(Object.getPrototypeOf(person1) == Person.prototype); //true
     console.log(Object.getPrototypeOf(person2).name); //'Nicholas'
     ```
当代码读取到某个对象的属性时，它会先从实例本身开始，在实例中中找到给定名字的属性，则返回该属性值；如果没找到，就会继续搜索指针指向的原型对象，在原型对象中搜索该属性。  
这样可以说原型对象中共享属性可以被 ***改写*** ，这里的改写只是修改实例本身的值，因为上面也讲到了，如果实例本身有该属性，就会屏蔽原型上的属性，但这样是不能修改实例原型中的属性。
```
    function Person(){
        
    };
    
    Person.prototype.name = 'winter';
    Person.prototype.age = '23';
    Person.prototype.sayname = function(){
        console.log(this.name);
    }
    
    var person1 = new Person();
    var person2 = new Person();
    person1.name = "sun";
    
    console.log(person1.name); // "sun" ——来自实例
    console.log(person2.name); // "winter" ——来自原型
```

当然，通过 *delete* 操作符可以完全删除实例属性，从而可以继续访问到原型中的属性。
```
    console.log(person1.name); // "sun" ——来自实例
    
    delete person1.name
    console.log(person1.name); // "winter" ——来自原型
```

### hasOwnProperty()
使用 *hasOwnProperty()* 方法可以检测一个属性是存在于实例中，还是存在于原型中。这个方法只在给定属性存在于对象实例中时，才会返回 *true* 。

### 原型与 in 操作符
单独使用 *in* 操作符会在通过对象能够访问给定属性时返回 *true* 无论该属性存在于实例还是原型中。  
```
    function hasPrototypeProperty(object,name) {
        return ！object.hasOwnProperty(name) && (name in object)
    }
```

通过 *in* 与 *hasOwnProperty()* 对属性的检测可写一个方法来检测该属性到底属于原型还是实例。

### 原型语法改写成一个以对象字面量形式创建的新对象
```
    function Person() {}
    Person.prototype = {
        name: 'Winter',
        age: '19'
    }
```
这样的最终实现效果与前面的写的一致，但是有个地方是不同的，就是创建的实例的 *constructor* 属性不再指向 *Person* 了。原因是因为在这里使用的语法，本质上完全重写了默认的 *prototype* 对象，因此 *constructor* 属性也就变成了新对象的 *constructor* 属性（指向 *Object* 构造函数），不再指向 *Person* 函数。尽管此时 *instranceof* 操作符还能返回正确的结果，但是通过 *constructor* 已经无法确定对象的类型了。
```
    var  friend = newer Person();
    
    console.log(friend instranceof Object);  //true
    console.log(friend instranceof Person);  //true
    console.log(friend.constructor == Object);  //true
    console.log(friend.constructor == Person);  //false
    
```
当然 *constructor* 属性值在创建原型的的时候也可以设置回适当的值。
```
    function Person() {}
    Person.prototype = {
        constructor: Person,
        name: 'Winter',
        age: '19'
    }
```
### 原型的动态性
前面也说了原型中查找值的过程，本质山上是一次搜索，因此我们对原型对象任何修改都能够立即从实例反应出来——即使是先创建了实例后修改原型也照样如此。
```
    
    var person3 = new Person();
    
    Person.prototype.sayname = function(){
        console.log('ni hao');
    }
    
    console.log(person3.sayname); // 'ni hao' 
```
原因可以归结为实例与原型之间的松散连接关系。当我调用 *person3.sayname()* 的时候，它会先在实例中搜索名为 *sayname* 的属性，在没有找到的情况下，会继续搜索原型。因为实例与原型之间连接的只不过是一个指针，而非一个副本，因此就可以在原型中找到新的 *sayname* 属性并返回保存在那里的函数。

那么问题就来了，如果重写整个原型会发生？  
我们都知道，调用构造函数的时候会为实例添加一个指向最初原型的指针（prototype）,而把原型修改为另一个对象就等于切断了构造函数与最初原型之间的联系。 *实例中呃指针仅指向原先，而不指向构造函数。*
```
    
    var person4 = new Person();
    
     Person.prototype = {
        constructor: Person,
        name: 'Winter',
        age: '19'
    }
    
    console.log(person4.name); // error
```
简单的来说，person4指向的原型中并没有以该名字命名的属性，所以报错。

### 原型对象的问题
原型对象第一个问题便是省略了构造函数传递初始化参数这一环节，结果所有实例在默认情况下都将取得相同的属性值。这也不是最大的问题，最大问题是它的属性共享导致的一些情况。

原型中所有属性是被很多实例共享的，这种贡献对于函数非常合适。对于那些包含基本值的属性来说也可以通过给实例添加同名属性隐藏原型对应的属性。然而对于引用类型值的属性，问题就会被无限放大。
```
    function Person() {};
    
     Person.prototype = {
        constructor: Person,
        friends:['job','neo'],
        name: 'Winter',
        age: '19'
    }
    
   var person1 = new Person();
   var person2 = new Person();
   
   person1.friends.push('van');
   
    console.log(person1.friends); // ['job','neo','van']
    console.log(person2.friends); // ['job','neo','van']
```

通过实例直接修改引用的数组，这样直接会改变原型中的数组，从而造成该原型所实例化的对象都将受到影响，如果本身是这样的初衷，那么没问题，但是很多时候一个实例都是需要属于自己全部的属性。所以我们基本看不到单独使用原型模式的原因所在。

## 组合模式——构造函数模式和原型模式
这种组合模式结合两种模式长处，构造函数模式用于定义实例的属性，而原型模式用于定义方法和共享的属性。
```
    function Person(name,age){
        this.name = name;
        this.age= age;
        this.friends = ['Snow','Bob']
    }
    
    Person.prototype = {
        constructor: Person,
        sayname: function(){
            console.log(this.name);
        }
    }
    
    var person1 = new Person('sun','23');
    var person2 = new Person('Winter','23');
    
    person1.friends.push('Van');
    console.log(person1.friends);  // "Snow,Bob,Van"
    console.log(person2.friends);  // "Snow,Bob"
    console.log(person1.age == person2.age);  // false
    console.log(person1.sayname == person2.sayname);  // true
```
## 动态原型模式
```
    function Person(name,age){
        this.name = name;
        this.age= age;
        
        if(typeof this.sayname != 'function'){
            Person.prototype.sayname = function(){
                console.log(this.name);
            }
    
        }
    }
    var person1 = new Person('sun','23');
    person1.sayname(); //sun
```
动态原型模式，第一遍看了之后说实话还真没看懂，第二遍细读的时候才慢慢回味过来，第三遍再读发现有那么一丢丢道理。

按照开发者的思路研发出这个方案，它把所有的信息都封装进构造函数中，通过构造函数初始化原型。这里用 *if* 是来通过检查某个方法是否有效，来添加原型方法。

第二遍大概了解了 *typeof this.sayname*。
```
    var person1 = new Person('sun','23');
    var person2 = new Person('Winter','23');
    
    console.log(typeof person1.friends);  // undefined
    console.log(typeof person2.friends);  // "function"
```
第三遍读的时候因为想到了原型的动态性（前面讲述的很清楚），初始化实例后，再次添加原型方法也会立即生效。

这里需要注意的是，不能对象用字面量重写原型，原因前面也解释了，解决方法也写了=。


## 寄生构造函数模式
简单的说就是为原生的构造函数添加特殊的属性。
```
    function SpecialArray() {
        var values = new Array();
        values.push.apply(values, arguments);
        values.toPipedString = function() {
            return this.join('|');
        };
    
        return values;
    }
    
    var colors = new SpecialArray('red', 'black', 'white');
```
像上面的例子中，SpecialArray()返回了一个对象values，这个values与Array“唯一”的区别就是比Array多了一个自定义的方法。。如果直接在Array中定义新的方法，会污染其它的数组对象甚至造成不必要的麻烦。

#### 参考
> *JavaScript高级程序设计*
---
> 写在最后，每天看一丢丢，写一点点，我也不清楚什么时候再把 *JavaScript高级程序设计* 认真的再拜读一遍。一遍有一遍的的效果吧，更何况现在有点基础，读起来可能更加容易理解一些概念。
 *ES6 标准入门* 也弃书两周了，心塞。
