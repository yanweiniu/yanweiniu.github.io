---
title: css碎碎念
tags:
  - css
categories:
  - CSS
date: 2017-10-25 22:58:44
---

---
**实现底部footer**
1. 外部有一个wrapper容器，要min-height:100%;撑满页面高度。
2. footer参考wrapper绝对定位，用bottom:0定在底部
3. main用来放置页面主体内容，padding-bottom与footer高度一致，用于页面长的时候给footer撑出高度

**文本不换行省略效果**
```
white-space: nowrap
overflow:hidden
text-overflow:ellipsis
```


**瀑布流原理**
>　
瀑布流布局要求要进行布置的元素等宽，然后计算元素的宽度与浏览器宽度之比，得到需要布置的列数。
创建一个数组，长度为列数，里面的值为已布置元素的总高度（最开始为0）
然后将未布置的元素依次布置到高度最小的那一列，就得到了瀑布流布局。

**transition一个速记法：transition: css属性 动画持续时间 动画类型  动画延迟时间；** 


**两列布局一列宽度固定，一列宽度不固定**
```
方法一：把side改写为绝对定位
.side{    
    position:absolute;left:0;top:0;    
    width:200px;height:200px;    
    background:red;    
}    
.main{    
    margin-left:210px;    
    background:blue;    
    height:200px;    
}  
方法二：把side改写为浮动
.side{    
    width:200px;    
    height:200px;    
    float:left;    
    background:red;    
}    
   
.main{    
    margin-left:210px;    
    background:blue;    
    height:200px;    
} 
方法三：flex布局
.parent {
	display:flex; 
}       
.side{    
   width:200px;    
   height:200px;    
    background:red;    
    margin-right:10px;    
}    
.main{    
    background:blue;    
    height:200px;    
    flex:1;    
}
方法四：利用BFC不与浮动元素重叠的特性

.side {    
  width: 200px;    
  height: 100px;    
  float: left;    
  background: red;    
  margin-right: 10px;    
/* 这里没有flex才能按自身设定的宽度渲染 */
/* 如果有了flex则宽度设定无效 */
}    
.main {    
  /* 创建BFC   */   
  overflow: hidden;    
  background: blue;    
  height: 100px;    
}  
```
**如何进行水平垂直居中(行内块元素)**
```
方案一：用display: table-cell;
内部元素如果是display:block;则无效
.box{
	display: table-cell;
	vertical-align:middle;/*垂直居中*/
	text-align:center;/*水平居中*/
}
方案二：单纯flex布局
.box{
    display: flex;
    justify-content:center;/*水平居中*/
    align-items:center;/*垂直居中*/
}
方案三：flex与margin:auto;结合
.box{
    display: flex;
}
button{margin: auto;}
方案四：绝对定位与0
.box{
    position:relative;/*需要父元素定位*/
}
button{
  overflow: auto; 
  margin: auto; 
  position: absolute; 
  top: 0; left: 0; bottom: 0; right: 0; 
}
```

**三栏水平布局，其中 left 、 right 分别位于左右两边， left 宽度为 200px ， right 宽度为 300px ， main 处在中间，宽度自适应。**

```
/*三栏结构大致如下*/
<divclass="container">
    <div class="main">main</div>
    <div class="left">left</div>
    <div class="right">right</div>
</div>
 //Flex 布局
 .container {
    display:flex;
}
.main{
    flex: 1;
    background-color:#eee;
}
.left{
    flex-basis:200PX;
    order:-1;
    background-color: lightblue;
}
.right {
    flex-basis: 300px;
    background-color:lightblue;
}
//方法二：绝对定位布局
    .container {
        position: relative;
    }
    .main {
        margin-left: 200px;
        margin-right: 300px;
    }
    
    .left {
        position: absolute;
        top: 0;
        /* 外层设置 padding 时才需要 */
        /*left: 0;*/
    }
    
    .right {
        position: absolute;
        top: 0;
        right: 0;
    }
    /* 圣杯布局 */
    .container {
        /* 这里不能使用 overflow: hidden; 清除浮动 */
        /* 不然会使 aside(.left & .right) 被 hidden */
        margin-left: 200px;
        margin-right: 160px;
    }
    
    .container::after {
        /* 借助伪元素清除浮动 */
        clear: both;
        display: block;
        content: '\200b';
        width: 0;
        height: 0;
    }
    
    .main {
        float: left;
        width: 100%;
    }
    
    .left {
        float: left;
        margin-left: -100%;
        position: relative;
        right: 200px;
    }
    
    .right {
        float: left;
        margin-right: -160px;
    }


    /*三栏的 HTML 结构大致如下：*/
    <main>
        <div class="content-wrapper">
            <div class="content"></div>
        </div>
        <aside class="left"></aside>
        <aside class="right"></aside>
    </main>
    /* 双飞翼布局 */
    main {
        /* 这里可以使用 overflow: hidden; 清除浮动 */
        overflow: hidden;
    }
    
    .content-wrapper {
        float: left;
        width: 100%;
    }
    
    .content {
        margin-left: 200px;
        margin-right: 160px;
    }
    
    .left {
        float: left;
        margin-left: -100%;
    }
    
    .right {
        float: left;
        margin-left: -160px;
    }
    /*三栏的 HTML 结构大致如下：*/
    <main>
        <aside class="left"></aside>
        <aside class="right"></aside>
        <div class="content"></div>
    </main>
    /* 流体布局 */
    .left {
        float: left;
    }

    .right {
        float: right;
    }

    .content {
        margin-left: 200px;
        margin-right: 160px;
    }
/* BFC布局 */
    .left {
        float: left;
    }
    
    .right {
        float: right;
    }
    
    .content {
        overflow: hidden;
    }
                
              
```


**三栏平分布局**
```
考查box-sizing属性
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8">
       <style type="text/css">
       #main{
       	display: flex;
       }
		#left{width:100%;display:inline-block;flex:1;box-sizing:border-box}
		#center{width:100%;display:inline-block;flex:1;box-sizing:border-box}
		#right{width:100%;display:inline-block;flex:1;box-sizing:border-box}
       </style>
    </head>
    <body>
	    <div id="main">
		    <div id="left">left</div>
			<div id="center">center</div>
			<div id="right">right</div>
		</div>
    </body>
</html>
```

**手写清除浮动的几种方式**
```
方法一：添加空div标签 
clear:both
方法二：父级标签定义伪类after
.float-div::after{
            display: block;
            clear: both;
            content: '';
            height: 0px;
        }
上一级父元素：zoom：1；//解决IE67兼容问题
方法三：父级标签overflow:hidden
 .float-div{
            overflow:hidden;
        }
```
**Meta标签的使用**
```
meta标签的使用
meta标签共有两个属性：http-equiv和name;
name属性
    name属性主要用于描述网页，与之对应的属性值为content，content中的内容主要是便于搜索引擎机器人查找信息和分类信息用的。其中name属性主要有以下几种参数:
Keywords(关键字)
举例：＜meta name ="keywords" content="science, education,culture,politics,ecnomics，relationships, entertaiment, human"＞
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
http-equiv属性
    http-equiv顾名思义，相当于http协议中文件头的作用，它可以向浏览器传回一些有用的信息，以帮助正确和精确地显示网页内容，与之对应的属性值为content。
其中http-equiv属性主要有以下几种参数：
content-Type(显示字符集的设定)
用法：＜meta http-equiv="content-Type" content="text/html; charset=gb2312"＞
Expires(期限)
用法：＜meta http-equiv="expires" content="Fri, 12 Jan 2001 18:18:18 GMT"＞
注意：必须使用GMT的时间格式。
Pragma(cache模式)
说明：禁止浏览器从本地计算机的缓存中访问页面内容。
用法：＜meta http-equiv="Pragma" content="no-cache"＞
Refresh(刷新)
说明：自动刷新并指向新页面。
用法：＜meta http-equiv="Refresh" content="2; URL=http://www.root.net"＞
注意：其中的2是指停留2秒钟后自动刷新到URL网址。
Set-Cookie(cookie设定)
说明：设置cookie, 如果网页过期，那么存盘的cookie将被删除。
用法：＜meta http-equiv="Set-Cookie" content="cookievalue=xxx; expires=Friday, 12-Jan-2001 18:18:18 GMT； path=/"＞
注意：必须使用GMT的时间格式。
Window-target(显示窗口的设定)
说明：强制页面在当前窗口以独立页面显示。
用法：＜meta http-equiv="Window-target" content="_top"＞
注意：用来防止别人在框架里调用自己的页面。
```
**媒体查询**
```
@media screen and (min-width:600px) {
  nav {
    float: left;
    width: 25%;
  }
  section {
    margin-left: 25%;
  }
}
@media screen and (max-width:599px) {
  nav li {
    display: inline;
  }
}
```