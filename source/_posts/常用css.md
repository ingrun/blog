---
title: CSS 的一些常用操作
date: 2019-03-012
---



## span 小手

```css
cursor: pointer;
```



## 虚线边框

```css
  p{
    border-bottom: 1px dashed  #ccc;
  }
```



### span不换行

```css
  span{
    white-space:nowrap;
  }
```

<!-- more -->

### span居中

```css
span{
   vertical-align:middle;
}
```



## 圆角边框

```css
border-radius: 10px;
```



## 强制生效

```css
margin-bottom: 15px !important;
```



## 文字溢出隐藏

```css
#多行
div {
    width: 100px;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 3; //行数
    -webkit-box-orient: vertical;
}

#单行
div {
    width: 100px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;   
}
```



## 鼠标悬停显示边框

```css
//js
$(".div").hover( 
    function () {
        $(this).addClass("hover");
    },
    function () {
        $(this).removeClass("hover");
});
#css
.hover{
    border: 1px solid;
}
```



## ajax

```js
$.ajax({
    type: "post",
    url: "/updUser",
    dataType: "json",
    data:{
        id : $("#id").text(),
        username: $("#username").val(),
        phone: $("#phone").val(),
        address: $("#address").val(),
    },
    success: function (data) {
        if (data.code == 1) {
            alert("成功")
        } else {
            alert(data.message)
        }
    },
    error: function () {
        alert("发生错误");
    }
})
```



## 隐藏元素

```html
<p hidden id="id">3</p>
```



## span 文字上划线

```css
span{
	text-decoration: line-through;
}    
```



## hover 伪类

```css
// 鼠标悬浮
button:hover{
	color: red;
}
```



## CSS border-style 属性

```css
	border-style:dotted solid double dashed; 
    // 上边框是点状
    // 右边框是实线
    // 下边框是双线
    // 左边框是虚线
```



## 渐变色

```css
background: linear-gradient(to left, white, blue);
```



## 边框不占用宽度

```css
	box-sizing: border-box;
```



## 字体小于12px

```css
	transform: scale(0.9);
```