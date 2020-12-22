# Quartz2D

### 什么是Quartz2D

> ### 注意:学好了可以抛弃一切UI控件
>
> Quartz 2D是一个二维绘图引擎，
>
> 同时支持iOS和Mac系统
>
>  Quartz 2D能完成的工作绘制图形 :
>
>  线条\三角形\矩形\圆\弧等 
>
> 绘制文字
>
> 绘制\生成图片(图像)
>
> 读取\生成PDF
>
> 截图\裁剪图片
>
> 自定义UI控件

#### Quartz2D在iOS开发中的价值

为了便于搭建美观的UI界面，iOS提供了UIKit框架，里面有各种各样的UI控件

UILabel：显示文字

UIImageView：显示图片

UIButton：同时显示图片和文字（能点击）

… …

利用UIKit框架提供的控件，拼拼凑凑，能搭建和现实一些简单、常见的UI界面

但是，有些UI界面极其复杂、而且比较个性化，用普通的UI控件无法实现，这时可以利用Quartz2D技术将控件内部的结构画出来，自定义控件的样子

其实，iOS中大部分控件的内容都是通过Quartz2D画出来的

> 因此，Quartz2D在iOS开发中很重要的一个价值是：自定义view（自定义UI控件）

​        ![img](https://uploader.shimo.im/f/P58VzDDtMEzDNrvV.png!thumbnail)      

![img](https://uploader.shimo.im/f/i1xe11zDyfAbAxjO.png!thumbnail)      

![img](https://uploader.shimo.im/f/RSa3QkcRreL72pto.png!thumbnail)      

![img](https://uploader.shimo.im/f/RBCoy2c41b1oijw9.png!thumbnail)      

![img](https://uploader.shimo.im/f/zCiw2Br1lMJnbOh3.png!thumbnail)      

```objective-c
Quartz2D提供了以下几种类型的Graphics Context：
Bitmap Graphics Context
PDF Graphics Context
Window Graphics Context
Layer Graphics Context
Printer Graphics Context
```

​        ![img](https://uploader.shimo.im/f/jeebZR8vIjJzrcrp.png!thumbnail)      

## 自定义UI控件

如何利用Quartz2D自定义UI控件？

如何利用Quartz2D绘制东西到view上？

首先，得有图形上下文，因为它能保存绘图信息，并且决定着绘制到什么地方去

其次，那个图形上下文必须跟view相关联，才能将内容绘制到view上面

自定义UI控件的步骤

1.新建一个类，继承自UIView

2.必须实现- (void)drawRect:(CGRect)rect方法，然后在这个方法中，可以：

取得跟当前view相关联的图形上下文

绘制相应的图形内容，绘制时产生的线条称为路径。 路径由一个或多个直线段或曲线段组成。

利用图形上下文将绘制的所有内容渲染显示到view上面

也可以：利用UIKit封装的绘图函数直接绘图

### drawRect:

为什么要实现drawRect:方法才能绘图到view上？

因为在drawRect:方法中才能取得跟view相关联的图形上下文

drawRect:方法在什么时候被调用？

当view第一次显示到屏幕上时（被加到UIWindow上显示出来）

调用view的setNeedsDisplay或者setNeedsDisplayInRect:时

​        ![img](https://uploader.shimo.im/f/M4VetcfYj95cbvwv.png!thumbnail)      

## Quartz2D须知

Quartz2D的API是纯C语言的 

Quartz2D的API来自于Core Graphics框架 

数据类型和函数基本都以CG作为前缀(看前缀可以得知这些数据类型和函数是哪个框架)

```
CGContextRef
CGPathRef
CGContextStrokePath(ctx);
```

## drawRect:中取得的上下文

在drawRect:方法中取得上下文后，就可以绘制东西到view上

View内部有个layer（图层）属性，drawRect:方法中取得的是一个Layer Graphics Context，因此，绘制的东西其实是绘制到view的layer上去了

View之所以能显示东西，完全是因为它内部的layer

### Quartz2D绘图的代码步骤

获得图形上下文

CGContextRef ctx = UIGraphicsGetCurrentContext(); 

拼接路径（下面代码是搞一条线段）

CGContextMoveToPoint(ctx, 10, 10); 

CGContextAddLineToPoint(ctx, 100, 100); 

绘制路径

CGContextStrokePath(ctx); // CGContextFillPath(ctx); 

​        ![img](https://uploader.shimo.im/f/UAgOcRYZ3jtHr8Mx.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/gCpsGCP3FvNyMO9L.png!thumbnail)      

## 常用拼接路径函数

```objective-c
//新建一个起点
void CGContextMoveToPoint(CGContextRef c, CGFloat x, CGFloat y) 
//添加新的线段到某个点
void CGContextAddLineToPoint(CGContextRef c, CGFloat x, CGFloat y) 
//添加一个矩形
void CGContextAddRect(CGContextRef c, CGRect rect) 
//添加一个椭圆
void CGContextAddEllipseInRect(CGContextRef context, CGRect rect) 
//添加一个圆弧
void CGContextAddArc(CGContextRef c, CGFloat x, CGFloat y, 
  CGFloat radius, CGFloat startAngle, CGFloat endAngle, int clockwise) 
```

## 常用绘制路径函数

```objective-c
//Mode参数决定绘制的模式
void CGContextDrawPath(CGContextRef c, CGPathDrawingMode mode) 
 
//绘制空心路径
void CGContextStrokePath(CGContextRef c) 
 
//绘制实心路径
void CGContextFillPath(CGContextRef c) 
```

> 注意:实心路径和空心路径就一个单词不同提示：一般以CGContextDraw、CGContextStroke、CGContextFill开头的函数，都是用来绘制路径的

## 图形上下文栈的操作

将当前的上下文copy一份,保存到栈顶(那个栈叫做”图形上下文栈”)

void CGContextSaveGState(CGContextRef c) 

将栈顶的上下文出栈,替换掉当前的上下文

void CGContextRestoreGState(CGContextRef c) 

## 矩阵操作

> 利用矩阵操作，能让绘制到上下文中的所有路径一起发生变化

```objective-c
缩放
void CGContextScaleCTM(CGContextRef c, CGFloat sx, CGFloat sy) 
 
旋转
void CGContextRotateCTM(CGContextRef c, CGFloat angle) 
 
平移
void CGContextTranslateCTM(CGContextRef c, CGFloat tx, CGFloat ty) 
```

## Quartz2D的内存管理

使用含有“Create”或“Copy”的函数创建的对象，使用完后必须释放，否则将导致内存泄露 

使用不含有“Create”或“Copy”的函数获取的对象，则不需要释放

 如果retain了一个对象，不再使用时，需要将其release掉

 可以使用Quartz 2D的函数来指定retain和release一个对象。例如，如果创建了一个CGColorSpace对象，则使用函数CGColorSpaceRetain和CGColorSpaceRelease来retain和release对象。

 也可以使用Core Foundation的CFRetain和CFRelease。注意不能传递NULL值给这些函数

​        ![img](https://uploader.shimo.im/f/Kf63xSR9hE71yi2O.png!thumbnail)      

## 图片水印

有时候，在手机客户端app中也需要用到水印技术

比如，用户拍完照片后，可以在照片上打个水印，标识这个图片是属于哪个用户的

实现方式：利用Quartz2D，将水印（文字、LOGO）画到图片的右下角

```objective-c
//核心代码
//开启一个基于位图的图形上下文
void     UIGraphicsBeginImageContextWithOptions(CGSize size, BOOL opaque, CGFloat scale) 
 
//从上下文中取得图片（UIImage）
UIImage* UIGraphicsGetImageFromCurrentImageContext(); 
 
//结束基于位图的图形上下文
void     UIGraphicsEndImageContext(); 
```

​        ![img](https://uploader.shimo.im/f/ehBgIVaITDeoZar9.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/12Jud3nTqEr7R6L0.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/uMzvWRRY2TM8XEqU.png!thumbnail)      

## 图片裁剪

​        ![img](https://uploader.shimo.im/f/OH6znZRytt2VHb7g.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/en6rHGsunYio3cwr.png!thumbnail)      

## Quartz 2D绘图的基础元素——路径

```
路径定义了一条或者或多条形状或子路径
子路径可以包含一条或者多条直线或曲线
子路径也可以是一些简单的形状，例如线、圆形、矩形或者星型等
子路径还可以包含复杂的形状，例如地图轮廓或者涂鸦等
路径可以是开放的，也可以是封闭的
路径主要使用在定义视图运动轨迹
```

​        ![img](https://uploader.shimo.im/f/VceF8al8SyHcxzuY.png!thumbnail)      