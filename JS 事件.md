## 引子
> 关于事件最早听过的一句话就是：JavaScript 与 HTML 之间的交互式通过 *事件* 来实现的。对于事件系统来说，说简单也简单，说复杂也是很复杂的，因为对于高版本浏览器都已经实现了 *“DOM2级事件”* ，但是对于IE8以下仍然使用其专有的事件系统。还有浏览器对象BOM支持的一些事件，DOM3级的出现，使得事件更加繁琐。

## 事件流

事件流描述的是从页面接受事件的顺序。  
对于 *IE* 和 *Netscape* 两个开发团队提出截然相反的事件流的概念，也是加大了学习成本。

*IE* 的事件流是事件冒泡流，而 *Netscape Communicator* 的事件流是事件捕获流。

### 事件冒泡
事件开始由最具体的元素（文档中嵌套层次最深的那个节点）接受，然后逐级向上传播到较为不具体的节点（文档）。

### 事件捕获

事件捕获的思路是不太具体的节点最先接收到事件，而最具体的节点应该最后接收到事件。

### DOM 事件流

*“DOM2级事件”* 规定的事件流包括三个阶段：事件捕获阶段、处于目标阶段和事件冒泡阶段。

## 事件处理程序

事件就是用户或浏览器自身执行的某种动作。诸如 *click*、*load* 和 *mouseover* ，都是事件的名字。而相应某个时间的函数就叫做 *事件处理程序*（或*事件侦听器、事件处理函数、事件句柄*）。   
而为事件指定处理程序的方式有好几种。

### HTML 事件处理程序

简单地说就是：
**事件直接加在html元素上。**

然鹅这种方式已经过时了。因为动作(javascript代码)和内容(html代码)紧密耦合，修改时即要修改html也要修改js。   
常用在写小demo中，我的PHP、java同事有时候会用这种方法，但大多数会用更简单的 *Jquery*。


```
// 直接在html中定义事件处理程序及包含的动作。

<input type="button" value="click me!" onclick="alert('clicked!')"/>

// html中定义事件处理程序，执行的动作则调用其他地方定义的脚本。

<input type="button" value="click me!" onclick="showMessage()"/>
<script>
function showMessage(){
    alert("clicked!");
}
</script>
```


> 1. 通过event变量可以直接访问事件本身，比如onclick="alert(event.type)"会弹出click事件。  
> 2. this值等于事件的目标元素，这里目标元素是input。比如 onclick="alert(this.value)"可以得到input元素的value值。

### DOM0级事件处理程序

**把一个函数赋值给一个事件处理程序属性。**

这种方法简单而且跨浏览器，但是只能为一个元素添加一个事件处理函数。

因为这种方法为元素添加多个事件处理函数，则后面的会覆盖前面的。


```
// myBtn.onclick=null;
var myBtn=document.getElementById("myBtn");
myBtn.onclick=function(){
    alert("clicked!");
}
// 删除事件处理程序
    
myBtn.onclick=null;
```

### DOM2级事件处理程序

DOM2级事件处理程序可以为一个元素添加多个事件处理程序。其定义了两个方法用于添加和删除事件处理程序：*addEventListener()* 和 *removeEventListener()* 。

**所有的DOM节点都包含这2个方法。**

这两个方法都需要3个参数：事件名，事件处理函数，布尔值。

这个布尔值为true,在捕获阶段处理事件，为false，在冒泡阶段处理事件，默认为false。


```
    // 添加事件处理程序
    var myBtn=document.getElementById("myBtn");
    myBtn.addEventListener("click",function(){
        alert("hello");
    },false);
    myBtn.addEventListener("click",function(){
        alert("world");
    },false);
    

```
**删除事件处理程序**：通过 *addEventListener* 添加的事件处理程序必须通过 *removeEventListener* 删除，且参数一致。

通过 *addEventListener* 添加的匿名函数将无法删除。

```
var handler=function(){
        alert("hello");
    }
    myBtn.addEventListener("click",handler,false);
    myBtn.removeEventListener("click",handler,false);
```

### IE事件处理程序

IE8及以下版本浏览器实现了与DOM中类似的两个方法：attachEvent()和detachEvent()。

这两个方法都需要两个参数：**事件处理程序名称和事件处理程序函数**。由于IE8及更早版本只支持事件冒泡，所以通过attachEvent()添加的事件处理程序都会被添加到冒泡阶段。注意是事件处理程序名称而不是事件名称，所以要加上on，是onclick而不是click。


**IE11只支持addEventListener！**

**IE9，IE10对attachEvent和addEventListener都支持！**

**TE8及以下版本只支持attachEvent！**


```
 var handler= function () {
        alert("hello");
    }
    myBtn.attachEvent("onclick",handler);
    myBtn.detachEvent("onclick",handler);
```

在 IE 中使用 attachEvent（）与使用 DOM0 级方法的主要区别在于事件处理程序的作用域。在使用 DOM0 级 方法的情况下，事件处理程序会在其所属元素的作用域内运行；在使用 attachEvent（）方法的情况下，事件处理程序会在全局作用域下运行，因此 this 等于 windows。


```
var handler= function () {
        alert(this === window); //true
    }
    myBtn.attachEvent("onclick",handler);
```

IE8以下浏览器事件执行顺序和DOM中事件触发顺序相反。

IE9及以上浏览器事件执行顺序和DOM中事件触发顺序相同。

### 兼容版本的事件处理程序


```
var EventUtil = {
    addHandler: function(element,type,handler){
        if(element.addEventListener){
            element.addEventListener(type,handler,false)
        }
        else if(element.attachEvent){
            element.attachEvent('on'+type,handler)
        }
        else{
            element["on" + type ] = handler;
        }
    },
    removeHandler: function (element,type,handler){
        if(element.removeEventListener){
            element.removeEventListener(type,handler,false)
        }
        else if(element.attachEvent){
            element.detachEvent('on'+type,handler)
        }
        else{
            element["on" + type ] = null;
        }
    }
}
```
## 事件对象

在触发 DOM 上某个事件时，会产生一个事件对象 event ，该对象包含着所有与事件有关的信息。

### DOM中的事件对象

DOM0 级 和 DOM2 级 事件处理程序都会把event作为参数传入。

DOM中事件对象重要属性和方法。

```
var btn=document.getElementById("btn");
    btn.onclick=function(event){
        console.log(event.type);    //click
    }
    btn.addEventListener("click", function (event) {
        console.log(event.type);    //click
    },false);
```

**属性：**

* type属性，用于获取事件类型
* target属性 用户获取事件目标 事件加在哪个元素上。（更具体target.nodeName）

**方法：**

* stopPropagation()方法 用于阻止事件冒泡
* preventDefault()方法 阻止事件的默认行为 

### IE中的事件对象

通过DOM0级方法添加事件处理程序时，event对象作为window对象的一个属性存在。

```
btn.onclick= function () {
        var event=window.event;
       console.log(event.type); //click
    }
```

通过attachEvent()添加的事件处理程序，event对象作为参数传入。


```
btn.attachEvent("onclick", function (type) {
        console.log(event.type);    //click
    })
```

**属性：**
* type属性，用于获取事件类型(一样)
* srcElement属性 用户获取事件目标 事件加在哪个元素上。（更具体target.nodeName）   
    ```
    //兼容性处理
    function showMsg(event){
        event=event||window.event;  //IE8以前必须是通过window获取event，DOM中就是个简单的传参
        var ele=event.target || event.srcElement; //获取目标元素，DOM中用target,IE中用srcElement
        alert(ele);
     }
    ```
* cancelBubble属性 用于阻止事件冒泡 IE中cancelBubble为属性而不是方法，true表示阻止冒泡。
* returnValue属性 阻止事件的默认行为 false表示阻止事件的默认行为

---
## 总结

在这里我只记录一下关于事件的基础知识，对于 *JavaScript* 来说，事件是最主要的主题之一，所以还有很庞大的内容没有详细展开的细说，对于那些知识点的运用我准备放在实战中展开来说，这里就不细讲了。

漫漫前端道路，我只开了个头。
