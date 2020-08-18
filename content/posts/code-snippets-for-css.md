---
title: "深入 Web 开发： Code snippets for CSS"
date: '2017-08-30'
---

#### .5px

```html
<div class="line"></div>
```

```css
.line {
  position: relative;
}
.line::after {
  position: absolute;
  content: '';
  width: 100%;
  left: 0;
  bottom: 0;
  height: 1px;
  background-color: red;
  transform: scale(1,.5);
  transform-origin: center bottom
}
```

1. *白树, 《移动web点5像素的秘密》, https://www.cnblogs.com/PeunZhang/p/4709822.html, 2015 年 08 月 19 日更新*

#### 自定义滚动条（Custom scrollbar）

```html
<div class="custom-scrollbar">
  <p>
    Lorem ipsum dolor sit amet consectetur adipisicing elit.<br />
    Iure id exercitationem nulla qui repellat laborum vitae, <br />
    molestias tempora velit natus. Quas, assumenda nisi. <br />
    Quisquam enim qui iure, consequatur velit sit?
  </p>
</div>
```

```css
.custom-scrollbar {
  height: 70px;
  overflow-y: scroll;
}

.custom-scrollbar::-webkit-scrollbar {
  width: 8px;
}

.custom-scrollbar::-webkit-scrollbar-track {
  background: #1E3F20;
  border-radius: 12px;
}

.custom-scrollbar::-webkit-scrollbar-thumb {
  background: #4A7856;
  border-radius: 12px;
}
```

#### 三角形（Triangle）

使用纯 CSS 创建三角形。

```html
<div class="triangle"></div>
```

```css
/*Top*/
.triangle {
  width: 0;
  height: 0;
  border-bottom: 20px solid #333;
  border-left: 20px solid transparent;
  border-right: 20px solid transparent;
}

/*Bottom*/
.triangle {
  width: 0;
  height: 0;
  border-top: 20px solid #333;
  border-left: 20px solid transparent;
  border-right: 20px solid transparent;
}

/*Left*/
.triangle {
  width: 0;
  height: 0;
  border-right: 20px solid #333;
  border-top: 20px solid transparent;
  border-bottom: 20px solid transparent;
}

/*Right*/
.triangle {
  width: 0;
  height: 0;
  border-left: 20px solid #333;
  border-top: 20px solid transparent;
  border-bottom: 20px solid transparent;
}
```

#### 圆形（Circle）

```html
<div class="circle"></div>
```

```css
.circle {
  border-radius: 50%;
  width: 2rem;
  height: 2rem;
  background: #333;
}
```

#### 清除浮动（Clearfix）

```html
<div class="clearfix">
  <div class="floated">float a</div>
  <div class="floated">float b</div>
  <div class="floated">float c</div>
</div>
```

```css

.clearfix:after {
  content: '';
  display: block;
  clear: both;
}

.floated {
  float: left;
}
```
