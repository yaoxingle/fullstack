#### div基础：

1）div没有任何自带样式属性

2）div必须配合css样式属性使用

3）margin:auto 表示让容器相对于父容器水平居中

而text-align:center 表示让容器内的内容水平居中

4）div在默认情况下、<u>是独占一行的（行级元素）</u>，如果需要让多个div位于同一行、则必须使用

```css
float
```

属性设置div

任何意见问题，最好只有一种解决方案（Python创始人）

5）网页布局上、已定不能出现水平滚动条（用户体验最基本的要求）

6）响应式布局：Responsive Layout（根据不同的电脑分辨率，进行不同的css样式处理）必须依赖于DIV+CSS

```css
margin: 20px; /*DIV与DIV之间的外边距*/
padding: 50px; /*DIV与内容之间内边距 会撑开DIV*/
line-height:400px; /*一行内容的行高 如果多行则无法居中*/ 
 /*一行文字的高度=字体大小 + 4像素*/
用于垂直高度
```

div默认宽度是浏览器宽度、高度是内容高度

#### div常用CSS样式：

1、width、height

2、backgroud-color、backgourd-image、backgroud-size、background-repeat

3、margin、padding、border：-top,-right,-bootm-left

4、line-height、text-align

5、color、font-size、font-family、font-weight、font-style

6、overflow





#### 补充知识：

##### 关于高度的百分比问题：相对的绝对值

```css
html,body {
    height:100%
    margin:0px;
}
div {
    height:50%;
    border: solid 0px red
    margin:auto
}
```

##### 关于灵活可控的垂直居中问题

#align {
	width: 80%;
	height:auto;
	border:solid 2px red;
	margin:auto;
	text-align: center;
	pading: 20px auto;
}
<div id="align">
    关于灵活可控的垂直居中问题<br>
    关于灵活可控的垂直居中问题<br>
    关于灵活可控的垂直居中问题
</div>

