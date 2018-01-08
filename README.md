
# 常见的JS页面特效

### 冒泡排序

数组中每相邻的两项进行比较，若果是升序排序，前一项大于后一项，就将彼此的值交换

- 一般写法：


	var array = [56,32,50,16,96,47,4];
	
	for (var j = 0; j < array.length - 1; j++) {
	 for (var i = 0; i < array.length - 1 - j; i++) {
	    if (array[i] > array[i + 1]) {
	      num = array[i];
	      array[i] = array[i + 1];
	      array[i + 1] = num;
	    }
	  }
	}
	console.log(array);
- 优化算法

~~~
var num = 0;
var m = 0;
var bool = 0;
for (var j = 0; j < array.length-1; j++) {
  bool = true;
  for (var i = 0; i < array.length-1-j; i++) {
    if (array[i] > array[i + 1]) {
    num = array[i];
    array[i] = array[i + 1];
    array[i + 1] = num;
    bool = false;
    }
  }
  if (bool) {//如果内循环没有执行就跳出循环
 	break;
  }
  m++;
}
console.log(m);
console.log(array);
~~~



### 快速排序

假设数组中的第一项为最大值，将最大值与数组的每一项进行比较，遇到比最大值还大的项，交换彼此的值

~~~
var arr = [32,26,66,78,12,54]
var temp

for (var j = 0; j < arr.length; j++) {
  var max = arr[j]
  for (var i = j; i < arr.length; i++) {
    if (arr[i] > max) {
      temp = max
      max = arr[i]
      arr[i] = temp
    }
    arr[j] = max
  }
}
console.log(arr)
~~~



### 拖拽

- `offsetWidth(Height) ` --- 自身的宽（高）

- `offsetLeft(Top)` --- 与定位的父级元素的距离

  ​

- `clientWidth(Height)` --- 可视宽（高）

- `clientLeft(Top)`  ---  鼠标距离可是区域的距离

  ​

- `scrollWidth(Height)` --- 实际内容宽（高）

- `scrollLeft`(Top)  ---  滚动条卷曲的距离

鼠标距离盒子的距离不变，利用`盒子left值 = 鼠标的位置 - 鼠标距离盒子的距离` 实现拖拽

~~~
var box = document.getElementById('box')

box.onmousedown = function(e) {
  var that = this
  var x = e.clientX
  var y = e.clientY
  var dirX = x - this.offsetLeft  //  鼠标距离盒子左边的距离 = 鼠标距离浏览器的距离 - 盒子的偏移距离
  var dirY = y - this.offsetTop

  document.onmousemove = function(e) {
    e = e || event
    that.style.left = e.clientX - dirX + 'px'
    that.style.top = e.clientY - dirY + 'px'
  }

  document.onmouseup = function() {
  	document.onmousemove = null
  }
}
~~~



### 事件冒泡机制

- 事件冒泡就是当一个元素触发某个事件后，会向他的父籍元素传递该事件
- 应用：通过给动态生成的元素的父级绑定某些事件，完成一些功能，例如删除功能

~~~
<div>
    心里软
    <p>小白</p>
</div>

var box = document.getElementsByTagName('div')[0]
var p = document.getElementsByTagName('p')[0]
var flag = true

box.onclick = function() {
  if (flag) {
  	this.style.left = 0
  flag = false
  } else {
    this.style.left = '-200px'
    flag = true
  }
}
~~~



### innerHTML性能问题--擦出图片

先在内存中利用字符串的拼接功能动态的创建好标签，再通过innerHTML渲染至页面

~~~
//1. 动态生成 100 个小 div 
var str = ''
var width = parseInt(getComputedStyle(document.getElementsByClassName('box')[0],null).width)
var height = parseInt(getComputedStyle(document.getElementsByClassName('box')[0],null).height)

for (var i = 0; i < 100; i++) {
	var l = i%10*(width+1)
	var h = parseInt(i/10)*(height+1)
	str += '<div class="box" style="left: '+l+'px;top: '+h+'px;background-position: '+-l+'px '+-h+'px;"></div>'	
}

//2. 渲染至页面
document.body.innerHTML = str
var aDiv = document.getElementsByTagName('div')
//添加事件
for (var i = 0; i < aDiv.length; i++) {
	aDiv[i].onmouseover = function() {
		this.style.opacity = 1
	}
}
~~~



### layout -- 布局

- 两栏自适应

  1.  定位实现

     左边盒子觉对定位 ，右边盒子自适应

     ~~~
     .left {
       width: 50px;
       height: 100%;
       background: #0fc;
       position: absolute;
       left: 0;
       top: 0;
     }
     .right {
       width: 100%;
       height: 100%;
       background: #f0c;
       margin-left: 50px;
     }
     ~~~

  2. BFC 机制

     左边盒子左浮动，右边盒子形成 BFC 机制

     BFC，块级格式化上下文，一个创建了新的BFC的盒子是独立布局的，盒子里面的子元素的样式不会影响到外面的元素；在同一个BFC中的两个毗邻的块级盒在垂直方向（和布局方向有关系）的margin会发生折叠。 

     ~~~
     .f {
       float: left;
       width: 100px;
       height: 100%;
       background: red;
     }
     .r {
       overflow: auto;
       height: 100%;
       background: yellow;
     }
     ~~~



### photo 相册切换

通过 src 属性实现图片的切换

~~~
var oImagegallery = document.getElementById('imagegallery');
var aA = oImagegallery.getElementsByTagName('a');
var oImage = document.getElementById('image');
var aDiv = document.getElementsByTagName('div')[1];
for (var i = 0; i < aA.length; i++) {
  aA[i].onclick = function () {
    oImage.src = this.href;
    aDiv.innerText = this.title;
    return false;
  }
}

~~~



###  select 全选

点击全选按钮，所有按钮被选中，取消，所有按钮都不选中；当且仅当所有按钮都被选中时，全选按钮才被选中

~~~
var aInp = document.getElementsByTagName('input');
var total = aInp[0]; 

// 当点击 total 时， 所有的 input 的选中状态与 total 一样
total.onclick = function () {
  for(var i = 1;i < aInp.length; i++) {
  	aInp[i].checked = this.checked;
  }
} 

// 当所有的 input 被选中时 total 也被选中 
for (var i = 1; i < aInp.length; i++) {
  aInp[i].onclick = function () {
    var flag = true;
    for (var i = 1; i < aInp.length; i++) {
      if (aInp[i].checked == false) {
      	flag = false;
      }
    total.checked = flag;
    }
  }
}
~~~



### Tab选项卡

点击按钮让对应内容显示

- 普通方法

  ~~~
  var li = document.getElementsByTagName('li');
    //添加索引
    for (var i = 0; i < 3; i++) {
      li[i].index = i
      li[i].onclick = function() {
        for (var i = 0; i < li.length; i++) {
        	li[i].className = ''
        }
        this.className = 'now'
        li[this.index+3].className = 'now'
      }
    }
  }
  ~~~

- 闭包

  ~~~
  var li = document.getElementsByTagName('li');
  //闭包方法
  for (var i = 0; i < 3; i++) {
    (function(m) {
      li[m].onclick = function() {
        for (var i = 0; i < 3; i++) {
          li[i].className = ''
          // li[i+3].style.display = 'none'
          li[i+3].className = ''
        }
        li[m].className = 'now'
        li[m+3].className = 'now'
      }
    })(i)
  }
  ~~~
