---
title: "Canvas 和 SVG "
date: 2024-03-20 13:30:33
categories:
- HTML&CSS
tags:
- HTML&CSS
toc: true # 是否显示目录
---

> Canvas 和 SVG  

<!-- more -->

## Canvas
### 介绍
Canvas，画布！很好理解就是用来画画的。那具体怎么“画”咱们就得看一下具体的 API 文档了。下面是对 canvas 的一些概述：
* canvas 是一个可以使用脚本(通常为JavaScript)来绘制图形的 HTML 元素.
* Canvas API 提供了一个通过JavaScript 和 HTML的Canvas元素来绘制图形的方式。它可以用于动画、游戏画面、数据可视化、图片编辑以及实时视频处理等方面。
* Canvas API 主要聚焦于2D图形。而同样使用Canvas元素的 WebGL API 则用于绘制硬件加速的2D和3D图形。

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
	</head>
	<body>
		<canvas id="canvas" width="400" height="300"></canvas>
	</body>
</html>
 
<script>
    var canvas = document.getElementById('canvas');	
    var context = canvas.getContext('2d');			
    
    context.moveTo(10,100);		
    context.lineTo(30,10);		
    context.lineTo(50,100);		
    context.lineTo(70,10);		
    context.lineTo(90,100);		
    context.stroke();			
</script>
```
### 应用
* 首先是可视化数据的应用，比如说：百度的ECharts、阿里的G2等图表可视化插件。  
* 其次是游戏的应用，Canvas 在基于Web的图像显示方面比 Flash 更加立体、精巧，且Canvas游戏在流畅度和跨平台方面更牛。  
* 还有图形编辑器的应用，比如可视化组态编辑工具HT，它完全基于Canvas绘制。未来Photoshop能够基于Canvas在web端100%实现。  
* 最后模拟器的应用，模拟器产品可以完全由 canvas 来实现，视觉效果上更加逼真模拟实际生活中的工具，进一步优化物联网环境下的使用体验，降低使用者的学习成本。  
未来Canvas的应用只会越来越普遍，所以具备Canvas这门技术是必不可缺的。

## SVG
SVG 英文全称为 Scalable Vector Graphics，意思为可缩放的矢量图。
* HTML5 支持内联 SVG。
* HTML `<svg>` 元素是 SVG 图形的容器。
* SVG 有多种绘制路径、框、圆、文本和图形图像的方法。

```html
<!-- 绘制矩形：使用 rect 标签，主要属性：
x/y：绘图位置，
width/height：矩形长宽，
fill：填充颜色，默认黑色，
stroke：描边的颜色。
stroke-width：描边的宽度。
rx/ry：描边的圆角。 -->
<svg width="500" height="500">
	<rect x="100" y="100" width="150" height="100" fill="red" stroke="green" stroke-width="2" rx="10"></rect>
</svg>
```

## 区别
* SVG 是一种使用 `XML` 描述 2D 图形的语言。
* Canvas 通过 `JavaScript` 来绘制 2D 图形。
* SVG 基于 XML，这意味着 SVG DOM 中的每个元素都是可用的。您可以为某个元素附加 JavaScript 事件处理器。
* 在 SVG 中，每个被绘制的图形均被视为对象。如果 SVG 对象的属性发生变化，那么浏览器能够自动重现图形。
* Canvas 是逐像素进行渲染的。在 Canvas 中，一旦图形被绘制完成，它就不会继续得到浏览器的关注。如果其位置发生变化，那么整个场景也需要重新绘制，包括任何或许已被图形覆盖的对象。
* Canvas 依赖分辨率； 不支持事件处理器； 弱的文本渲染能力； 能够以 .png 或 .jpg 格式保存结果图像； 最适合图像密集型的游戏，其中的许多对象会被频繁重绘。
* SVG 不依赖分辨率； 支持事件处理器； 最适合带有大型渲染区域的应用程序（比如谷歌地图）； 复杂度高会减慢渲染速度（任何过度使用 DOM 的应用都不快）； 不适合游戏应用。