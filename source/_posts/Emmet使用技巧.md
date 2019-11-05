---
title: Emmet使用技巧
tags:
  - Emmet
  - 前端
categories: []
date: 2018-03-05 13:07:01
---

**使用">"号来生成子元素**
> div>ul>li <Tab键>
```
<div>
  <ul>
    <li></li>
  </ul>
</div>
```
<!-- more -->
**使用“+”来生成同级元素**：
> div+p+dq<Tab键>
```
<div></div>
<p></p>
<dq></dq>
```
**使用“^”返回父层元素来添加新的元素**
> div>p>span+em^bq<Tab键>
```
<div>
  <p><span></span><em></em></p>
  <blockquote></blockquote>
</div>
```
返回两级
> div>p>span+em^^bq<Tab键>
```
<div>
  <p><span></span><em></em></p>
</div>
<blockquote></blockquote>
```
**生成重复元素**
> ul>li*5

```
<ul>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
  <li></li>
</ul>
```
**组合操作符**
>div>(header>ul>li*2>a)+footer>p

```
<div>
  <header>
    <ul>
      <li><a href=""></a></li>
      <li><a href=""></a></li>
    </ul>
  </header>
  <footer>
    <p></p>
  </footer>
</div>
```
**添加ID和CLASS**
> div#header+div.page+div#footer.class1.class2.class3<Tab键>

```
<div id="header"></div>
<div class="page"></div>
<div id="footer" class="class1 class2 class3"></div>
```
**自定义属性**
> d[title="Hello world!" colspan=3]<Tab键>
如果不包含空格的话，你不需要使用引号: td[title=hello colspan=3] 这样也同样好使
```
<td title="hello gbtags" colspan="5"></td>
```
**自动生成连续编号**
> ul>li.item$*5
```
<ul>
  <li class="item1"></li>
  <li class="item2"></li>
  <li class="item3"></li>
  <li class="item4"></li>
  <li class="item5"></li>
</ul>
```
> ul>li.item$$$*5
```
<ul>
  <li class="item001"></li>
  <li class="item002"></li>
  <li class="item003"></li>
  <li class="item004"></li>
  <li class="item005"></li>
</ul>
```
**生成标签内容**
> a{更多信息}<Tab键>
```
<a href="">更多信息</a>
```
**使用的格式相关注意事项**
> Emmet中需要注意不要添加额外的空格
以下可以正常工作
(header>ul.nav>li*5)+footer
但是这行不能正常工作
(header > ul.nav > li*5) + footer
**标签模糊输入**
> .wrap>.content等价于 div.wrap>div.content
 ul>.item*3 等价于	 ul>li.item*3

**快速生成占位文字**
> lorem<Tab键>

```
//生成一段占位文字
Lorem ipsum dolor sit amet, consectetur adipisicing elit. Debitis hic dolorem recusandae culpa officiis ullam doloribus aut voluptatibus. Dicta aliquam dolorum nesciunt at adipisci dignissimos vitae iure perspiciatis modi illum.
```
>生成重复文字
p*4>lorem

**生成针对不同浏览器带有特定前缀的CSS**
> -bdrs<Tab键>
```
//生成
-webkit-border-radius: ;
-moz-border-radius: ;
border-radius: ;
```
>　@kf<Tab>
```
//生成
@-webkit-keyframes identifier {
	from {  }
	to {  }
}
@-o-keyframes identifier {
	from {  }
	to {  }
}
@-moz-keyframes identifier {
	from {  }
	to {  }
}
@keyframes identifier {
	from {  }
	to {  }
}
```
>  -chrorme-foo<Tab>
```
//生成
-webkit-chrorme-foo: ;
-moz-chrorme-foo: ;
-ms-chrorme-foo: ;
-o-chrorme-foo: ;
chrorme-foo: ;
```
**快速生成CSS渐变代码**
> lg(left, #CCC 30%, orange)<Tab键>
```
//生成
  background-image: -webkit-gradient(linear, 0 0, 100% 0, color-stop(0.3, #CCC), to(orange));
  background-image: -webkit-linear-gradient(left, #CCC 30%, orange);
  background-image: -moz-linear-gradient(left, #CCC 30%, orange);
  background-image: -o-linear-gradient(left, #CCC 30%, orange);
  background-image: linear-gradient(left, #CCC 30%, orange);
```
**模糊搜索快速生成CSS代码**
> ov:h<Tab键>
```
//生成
overflow:hidden
```
> d:b<Tab键>
```
//生成
display: block;
````