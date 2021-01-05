---
title: vue 监听键盘回车事件
date: 2020-03-08 16:31:33
---



### 基本用法

```html
<!-- vue运行为v-on在监听键盘事件时，添加了特殊的键盘修饰符：13代表回车键 -->
<input v-on:keyup.13="submit">

<!-- vue还非常贴心地给出了常用按键的别名，这样就不必去记keyCode ~ ~ -->
<!-- 上面代码，还可以在这样写： -->

<input v-on:keyup.enter="submit">
<input @keyup.enter="submit">
```



### 按回车触发search() 方法

```html
<input @keyup.enter="search()"></input>
```



### element组件使用

<!-- more -->

```html
<!-- 如果用了封装组件的话，比如element，这个时候使用按键修饰符需要加上.native -->
<el-input v-model="account" placeholder="请输入账号" @keyup.enter.native="search()"></el-input>
```



### 常用键盘别名：

- .enter
- .tab
- .delete (捕获 “删除” 和 “退格” 键)
- .esc
- .space 空格键
- .up
- .down
- .left
- .right



#### 还有一些特殊或者组合按键：

- .ctrl
- .alt
- .shift
- .meta(window系统下是window键，mac下是command键)



```html
<!-- Alt + C : -->
<input @keyup.alt.67=“doSth”>

<!-- Ctrl + Click : -->
<div @click.ctrl="doSth">点我</div>
```



