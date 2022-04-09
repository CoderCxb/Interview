# CSS

#### CSS3新特性
过度、动画、形变、弹性布局(flex)、网格布局(grid)、word-wrap、渐变、媒体查询、盒模型

#### 盒模型
标准盒模型和IE盒模型的区别是: 设置的宽高是否包含padding和border
标准盒模型不包含:盒子的实际尺寸 = 内容(设置的宽/高) + 内边距 + 边框
IE 盒模型包含:盒子的实际尺寸 = 设置的宽/高 = 内容 + 内边距 + 边框

CSS3添加的属性box-sizing 
border-box: 标准盒模型
content-box: IE盒模型

#### BFC·
块格式化上下文(Block Formatting Context,BFC): 为CSS概念,可以理解为一个独立容器,决定了其中元素的排版逻辑。

PS: 还有IFC,IFC内部元素是水平排版

特点: 
  - 内部元素垂直排版
  - 垂直方向上的margin会发生重叠
  - 子元素不会影响外部元素
  - BFC的区域不会与float元素重叠
  - 计算BFC的高度时,浮动元·素也会参与计算
·
常见触发条件: 
  - float不为none
  - position: absolute或者fixed
  - display: inline-block或者flow-root
  - overflow不为visible

用途:
  - 清除浮动
  - margin重叠问题

#### marigin重叠问题
垂直方向上设置的margin不会进行叠加,而是重叠,解决方式为,为其中一个元素触发BFC
```html
<head>
  <style>
    .top{
      width: 100px;
      height: 100px;
      background: yellow;
      margin-bottom: 50px;
    }
    .bfc{
      overflow: hidden;
    }
    .center{
      margin-top: 50px;
      width: 100px;
      height: 100px;
      background: red;
    }
  </style>
</head>
<div>
  <div class="top"></div>
  <div class="bfc">
    <div class="center"></div>
  </div>
</div>
```

#### 浮动

###### 清除浮动
1. 伪元素 + clear:both + display:block(最推荐的写法)
```css
/* 兼容处理 */
.clear{
  zoom:1;
}
/*  */
.clear:after{
  content: '';
  clear:both;
  display:block;
  /* 前三行就可以清除浮动了, 后三行优化 */
  height:0;
  overflow:hidden;
  visibility:hidden;
}
```

2. overflow: hidden
```html
<style>
  .box{
    background: yellow;
    overflow: hidden;
  }
  .float{
    width: 100px;
    height: 100px;
    background: red;
    float: left;
  }
</style>

<div class="box">
  <div class="float"></div>
</div>
```

#### flex布局
注意,设为 Flex 布局以后,子元素的float、clear和vertical-align属性将失效
  - display: flex | inline-flex;
  - flex-direction: 决定主轴的方向,row、row-reverse、column、column-reverse
  - flex-wrap: 如果一条轴线排不下如何换行,nowrap | wrap | wrap-reverse;
  - flex-flow: flex-direction || flex-wrap 的简写形式
  - justify-content: 项目在主轴上的对齐方式,flex-start | flex-end | center | space-between | space-around
  - align-items: 项目在交叉轴上如何对齐,flex-start | flex-end | center | baseline | stretch
  - flex-grow: 项目的放大比例,默认为0,即如果存在剩余空间,也不放大
  - flex-shrink:项目的缩小比例,默认为1,即如果空间不足,该项目将缩小
  - flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
  - flex: flex-grow, flex-shrink 和 flex-basis的简写,默认值为0 1 auto
  - align-self: 允许单个项目有与其他项目不一样的对齐方式,可覆盖align-items属性。默认值为auto,表示继承父元素的align-items属性,如果没有父元素,则等同于stretch


#### Grid布局
flex可以看成是一维的,而Grid是二维的
  - fr: 设置比例 1fr,2fr, 后一个元素是前一个的两倍
  - repeat(3, 150px): 相当于 150px,150px,150px, 第一个参数如果是auto-fill会尽可能填充
  - display: grid | inline-grid
  - grid-template-rows: 设置网格行数和高度,可以使用百分比和固定单位
  - grid-template-columns: 设置网格列数和宽度,可以使用百分比和固定单位
  - grid-row-gap: 行之间的间距
  - grid-column-gap 列之间的间距
  - grid-gap: grid-row-gap和grid-column-gap的简写


#### 定位 - position
  - static: 默认定位,一般用于取消继承来的定位
  - relative: 相对定位,相对原本的位置进行定位
  - absolute: 绝对定位, 相对最近的非static的上级元素,脱离标准流
  - fixed: 固定定位,相对于body进行定位,脱离标准流
  - sticky: 当要移出视口时,变成固定定位,相对于父元素


#### 过渡
过渡:  属性 时长 时间曲线 延时
transition: property duration timing-function delay;

#### 动画
动画:  时长 时间曲线 延时 是否应该轮流反向播放动画 动画名称
animation: name duration timing-function delay iteration-count direction;
```html
<style>
  @keyframes drop
  {
    from {
      top: 0px;
    }
    to {
      top: 200px;
    }
  }
  /*  div不断下落 */
  div{
    width: 100px;
    height: 100px;
    position: absolute;
    background: red;
    animation: drop 3s linear 1s infinite alternate ;
  }
</style>

</head>
<body>
  <div class="box">
  </div>
</body>
```


定义动画 
```css
@keyframes drop
{
  from {
    top: 0px;
  }
  to {
    top: -200px;
  }
}

div{
  animation: drop 3s 
}

```

#### 伪类和伪元素
伪类: 在选择器的基础上增加条件, 操作的依旧是该选择器选择的元素

伪元素: 在选择器的基础上增加元素, 操作的是该元素

当悬停div时,设置div的样式
div:hover{} 

在div后添加一个元素并设置该元素的样式
div::after{}


#### 垂直水平居中

###### 水平居中
1. 文本居中
text-align: center;

2. 块级元素
width: 100%;
margin: 0 auto; // 设置两边的margin,使其居中

3. 任意元素 - flex布局
display: flex; 
justify-content: center;

4. 任意元素 - 子绝父相 + transfrom
position: absolute;
transform: translate(-50%, 0);
left: 50%;

###### 垂直居中

1. 单行文本/元素
line-height: 100%;

2. 任意元素 - flex布局
display: flex; 
align-items: center;

3. 任意元素 - 子绝父相 + transfrom
position: absolute;
transform: translate(0, -50%);
top: 50%;

#### 单位 px、em、rem
px: 一个像素点
em: 元素的font-size大小
rem: 跟元素的font-size大小
vw、vh: 视口

#### 文本超出...
```css
text-overflow: ellipsis;
white-space: nowrap;
overflow: hidden;
```
#### 多行文本超过...
```css


```

#### 隐藏元素的方式和区别
1. display: none; 移出文档流,不会响应绑定的监听事件
2. opacity: 0; 仅修改透明度, 依旧可以响应绑定的事件
3. visibility: hidden; 修改透明度, 不会响应绑定的监听事件
4. content-visibility: hidden; 将内容移出文档流,当本元素还在
5. position: absolute:通过使用绝对定位将元素移除可视区域内,以此来实现元素的隐藏。
6. z-index: 负值:来使其他元素遮盖住该元素,以此来实现隐藏。
7. transform: scale(0,0):将元素缩放为 0,来实现元素的隐藏。这种方法下,元素仍在页面中占据位置,但是不会响应绑定的监听事件。

#### CSS选择器有哪些？
CSS选择器: id选择器(#id)、类选择器(.class)、标签选择器(div)、相邻选择器(div + span)、子元素选择器(ul > li)、后代选择器(div span)、属性选择器(a[rel="external"])、通配符选择器(*)、伪类选择器(div:hover)

#### 选择器的权重
权重优先级: !important(∞) > 行内样式(1000) > id (100) > class和伪类(10) > 标签 > * 和 继承(0)  

相同权重时: 行内样式 > 内联style > 外联style

#### 移动端如何实现1px的
物理像素: 设备上的物理像素点
逻辑像素:  css的px
像素密度:  
原因: 由于在不同的手机屏幕中, css的1px对应的物理像素可能是1、2、3,在高清屏(Retina)上的1px其实对应了2~3个物理像素,看起来比较宽
实现:
1. 通过伪类 + transform的scale进行缩放
2. 使用阴影, 模拟出0.5px的效果
3. 使用渐变色, 不需要中间过渡

#### 如何画一个三角形
1. 通过border
```css
.triangle{
    border: transparent solid 50px;
    border-top: red solid 50px;
    box-sizing: border-box;
    width: 100px;
    height: 100px;
}
```

2. 通过canvas画
3. 通过渐变色画
```css
.triangle{
  width: 100px;
  height: 100px;
  background-image: linear-gradient(45deg, red 50%, transparent 50%);
}
```

#### link和@import的区别
1. link是XHTML标签,除了可以加载CSS,还可以加载其他类型资源,而@import属于CSS范畴,只能加载CSS
2. link引用CSS时,在页面载入时同时加载；@import需要页面网页完全载入以后加载
3. link是XHTML标签,无兼容问题；@import是在CSS2.1提出的,低版本的浏览器不支持

#### 什么是回流(重排)和重绘以及其区别？
  - 回流(重排)reflow: 当render tree中的一部分(或全部)因为元素的规模尺寸,布局,隐藏等改变时而需要重新构建,回流必定引发重绘
  - 重绘(repaint):当render tree中的一些元素需要更新属性,而这些属性只影响元素的外观,风格,而不会影响布局时,称其为重绘,例如颜色改变等。


#### 媒体查询
针对不同的屏幕大小设置不同的样式,实现响应式的布局
```css
/* 当屏幕小于等于300px时, 设置body背景颜色为red */
@media screen and (max-width: 300px) {
    body {
        background-color:red;
    }
}
```