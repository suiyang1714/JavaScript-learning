## 引子
> 到目前为止还没接触过类似的项目，想必以后遇到也是查 API ，不如提早再温习一下，距离上次看这个也是两年前的事情了。

## 拖放事件
实现拖放最关键的在于确定哪里发生了拖放事件，有些事件是在被拖动的元素上触发的，有些事件是在放置目标上触发的。

### 作用于被拖动元素

拖动某元素时，将依次触发的事件是：

1. dragstart
2. drag
3. dragend

按下鼠标键并开始移动时，会在被拖放的元素上触发 *dragstart* 事件。此时光标变成“不能放”符号，表示不能把元素放到自己身上。

触发 *dragstart* 事件后，随机会触发 *drag* 事件，而且在元素被拖动期间会持续触发该事件。类似 *mouseover* 。当拖动停止时（无论是把元素放到了有效的放置目标上，还是放到了无效的放置目标上），会触发 *dragend* 事件。

### 作用于放置目标元素

当某个元素被拖动到有效的放置目标上时，会依次触发的事件为：
1. dragenter
2. dragover
3. dragleave 或 drop

被拖动元素进入放置目标上，会触发 *dragenter* 事件（类似mouseover事件），紧随其后的是 *dragover* 事件，而且在被拖动的元素还在放置目标的范围内移动时，会持续触发该事件。如果元素被拖出了放置目标，触发的是 *dragleave*，放到了目标元素上，触发的是 *drop* 事件。

## dataTransfer 对象

该对象有两个主要方法： getData() 和 setData()。

一个取值，一个保存值。

```
 e.dataTransfer.setData('PlaneID',this.id);//保存数据--该img元素的id
 
  var id = e.dataTransfer.getData('PlaneID');//得到数据--id值
```
## 相关实例


```
<!-- HTML -->
<h3>拖动元素到垃圾桶后从DOM树中删除子元素</h3>
<img id="trash" src="1.png">
<hr/>
<img id="p3" class="src" src="1.png">
<img id="p4" class="src" src="1.png">
<img id="p5" class="src" src="1.png">

<!-- CSS -->
body {
    text-align: center;
}
#trash {
    opacity: .2;
    margin: 15px;
}
        
<!-- JS -->
<script>

    //为源对象添加事件监听 —— 记录拖动了哪一个源对象
    var srcList = document.querySelectorAll('.src');//找到全部img元素
    for(var i=0; i<srcList.length; i++){ //遍历img元素
        var p = srcList[i];
        p.ondragstart = function(e){ //开始拖动源对象
            e.dataTransfer.setData('PlaneID',this.id);//保存数据--该img元素的id
        }
        p.ondrag = function(){}
        p.ondragend = function(){}
    }

    //为目标对象添加事件监听 —— 删除拖动的源对象
    trash.ondragenter = function(){ //源对象进入目标对象
        console.log('drag enter');
        trash.style.opacity = "1"; //将透明度变成1
    }
    trash.ondragleave= function(){  //源对象离开目标对象后
        console.log('drag leave');
        trash.style.opacity = ".2"; //将透明度变为0.2
    }
    trash.ondragover= function(e){  //源对象在悬停在目标对象上时
        e.preventDefault();  //阻止默认行为，使得drop可以触发
    }
    trash.ondrop= function(e){ //源对象松手释放在了目标对象中
        console.log('drop');
        trash.style.opacity = ".2"; //将透明度变为0.2
        //删除被拖动的源对象
        var id = e.dataTransfer.getData('PlaneID');//得到数据--id值
        var p = document.getElementById(id); //根据id值找到相关的元素
        p.parentNode.removeChild(p);  //从父元素中删除子节点
    }


</script>
```
