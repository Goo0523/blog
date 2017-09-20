# retina屏幕图片高清适配的解决方案

以下摘抄自维基百科[retina屏幕](https://zh.wikipedia.org/wiki/Retina%E6%98%BE%E7%A4%BA%E5%B1%8F)

>以MacBook Pro with Retina Display为例，工作时显卡渲染出2880x1800个像素，其中每四个像素一组，输出原来屏幕的一个像素显示的大小区域内的图像。这样一来，用户所看到的图标与文字的大小与原来的1440x900分辨率显示屏相同，但精细度是原来的4倍，但对于特殊元素，如视频与图像，则以一个图片像素对应一个屏幕像素的方式显示。故不会产生Windows中分辨率提升使屏幕文字与图像变小，造成阅读困难的问题。这样在设计软件时只需将所有的UI元素的精细度都提高到原来的4倍就可以既保持了观看舒适度，又提高了显示效果。

这也是往往很多网站，用retina屏幕看的时候，文字很锐利，但是图片很模糊的原因。
所以要解决高清屏幕图片模糊的问题，可以根据使用场景选择矢量图，或者使用等比例放大后的图片。
	1. Iconfont：通常是作为图标来使用，同时向下兼容到IE6，不存在兼容性的问题。但是由于不支持多色，所以使用场景有一定限制；
	2. SVG：支持多色彩，体积小巧。但是仅支持到IE9，向下兼容可以使用如下代码
```
<svg width="96" height="96">
	<image xlink:href="pic.svg" src="pic.png" width="96" height="96" />
</svg>
```
虽然这种方法解决了兼容性的问题，但是在IE11以下浏览器，会把svg和兼容的png资源同时下载下来。作为背景图的形式插入可以解决双下载的问题，代码如下：
```
div {
  background-image: url(pic.png);
  background-image: url(pic.svg), none;
}
```
这里使用了一个小技巧：支持css3多背景属性的浏览器均支持svg
3.img 不管是icofont还是svg适用场景都是不复杂的图形，常规的图片依然不适用。做高清适配依旧是多倍图的形式，通常做法是利用media查询的形式进行替换多倍图：
```
div{
	background-image: url('pic.jpg')
}
@media
only screen and (-webkit-min-device-pixel-ratio: 2),
only screen and (device-pixel-ratio: 2){
	div{
		background-image: url('pic@2x.jpg')
	}
}
// 还有一种比较简便的写法，利用image-set属性
// background-image: image-set( "pic.png" 1x,
//                           "pic@2x.png" 2x );
// 但是image-set属性是css4的属性，浏览器的支持还有些问题
```
图片的形式插入利用srcset属性，主流浏览器均支持：
```
	<img src="image-128.png"
		srcset="image-128.png 128w, image-256.png 256w, image-512.png 512w"
		sizes="(max-width: 360px) 340px, 128px" />

```
		
		
