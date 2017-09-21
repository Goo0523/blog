# IE6 常见兼容性问题及解决方案

页面如果需要兼容IE6 等低版本浏览器，需要考虑的只有样式问题，因为js方面有 jQuery1.x 嘛。
页面对IE6的兼容，对前端来说是一件很痛苦而且时间成本很高的事情。需要兼容到IE6的话，只能是渐进的形式，毕竟允许我们使用到布局方面的属性并不多，如果是兼容到IE9，可以是降级的形式进行布局。
在低版本的浏览器下，主体功能不能出问题，样式偏差不能太大，同时给用户浏览器升级提示。

**如下列出常见的IE6等样式差异及解决方案：**

 1. 不支持`position:fixed`，需要时刻计算滚动的高度，再加上`position:absolute`；
 2.当在浮动中使用margin时，会产生一个双倍边距的BUG，尽量避免出现margin，float同时出现，可以多嵌套一层盒子，使用padding的形式代替边距；
 3.不支持：after、：before伪类；
 4.不支持max-height/min-height ，只能用确定尺寸，或者js动态计算
 5.假如某个盒子设置了一个高度，当内容超出时，没有`overflow:hidden;`时，IE6默认会撑开，有时候会出现莫名布局错乱；
 6.不能使用.class.class2的形式作为css选择器，应避免这种形式或者改用.class-class2的写法；
 7.盒子模型解析不一致，在Quirks Mode中，盒子的宽度计算与Standards Mode的不同，在 IE 盒模型中， `box width = content width + padding left + padding right + border left + border right， box height = content height + padding top + padding bottom + border top + border bottom`， 而在 W3C 标准的盒模型中，box 的大小就是content 的大小。 
 8.设置的文字高度超出盒模型内容区域设置的高度时会影响布局。给超出高度的标签设置overflow：hidden；或者将文字的行高line-height设置为小于块的高度；
 9.img外部有a标签，即img标签有链接时默认有一个border。CSSreset设置img边框border:0; 
 10.每个img之后敲了回车，图片会默认有间距。为img设置float的浮动布局方式； 
 11.浮动块元素与未浮动块元素处于同一行，有默认的3px间距。设置非浮动元素浮动；
 12.清除浮动的时候，有些人会采取一种清浮动的方法，使用一个空的div，然后为这个div设置`{clear：both}`。div即使是空的，也会存在默认行高。设置其高度为0，并设置`overflow:hidden`；
 13.hover只支持a标签的使用，不支持一切其它标签使用；
 14.IE6中table设置属性border-color无效； 
 15.仅支持png8格式的透明，即要么opcity为0，要么为1； 
 16.不支持透明rgba与opacity属性，使用IE6当中的滤镜filter替代掉，如：`{opacity:0.6;filter:alpha(opacity=60)；}`（需注意：所以子元素均会透明）
 17.不支持E>F子选择器
 18.纵向居中，IE6不支持`display:table-cell`；
 19.cursor不支持pointer属性，可以使用hand替代。`{cursor:pointer ; cursor:hand;}`； 
 20.子标签无法撑开父标签的高度  处理方法：方法1：在子标签最后添加清除浮动的设置`<div style='height:0;clear:both'></div>`;方法2：为父标签添加`{overflow:hidden;}`的样式；方法3：为父标签设置固定高度。 
 21.img图片下部高度多余5px，可以将图片转化为块级对象，即`display:block`；
 22.多个浮动元素中间夹杂HTML注释语句，浮动元素宽度设置为100%；则在下一行多显示一个上一行的最后一个字符。需要删除注释；
 23.IE6当中，在同一组CSS属性中，`!important`不起作用。

**如下是一些hack的写法：**

 1. IE6 css hack：
```
1. *html Selector {} /* Selector 表示 css选择器 下同 */ 
2. Selector { _property: value; } /* property: value 表示 css 的属性名: 属性值 下同 */ 
3. Selector { _property/**/: /**/value; } 
4. Selector { -property: value; } /*IE6 css hack常用习惯推荐使用下划线_ */ 
```
 2. IE7 css hack：
```
1. *+html Selector {} 
2. *:first-child+html Selector {} 
```
 3. IE8 css hack：
```
Selector { 
    property: value1; /* W3C MODEL */ 
    property: value2\0; /* IE 8+ */ 
    property: value1\9\0; /* IE 9+ */ 
} 
```
 4. IE6、IE7、IE8共有的css hack：
```
Selector { property: value\9; } 
```
 5. IE6、IE7共有的css hack：
```
1. Selector { *property: value; } 
2. Selector { #property: value; } 
3. Selector { +property: value; } 
```
 6. IE8+ css hack：
```
Selector { property: value\0; } 
```
 7. IE9+ css hack：
```
Selector { property: value\9\0; } 

```
 8. 条件注释：
```
<!--[if IE]> Only IE <![endif]-->
所有的IE可识别

<!--[if gte IE 6]> Only IE 6/+ <![endif]-->
IE6以及IE6以上都可识别

<!--[if lte IE 7]> Only IE 7/- <![endif]-->
IE7及ie7以下版本可识别 
 
<!--[if (gte IE 9)|!(IE)]><!--><html> <!--<![endif]--> 
大于等于ie9或者非ie浏览器 
```