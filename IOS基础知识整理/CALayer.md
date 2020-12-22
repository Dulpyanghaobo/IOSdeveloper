# CALayer

在iOS中，你能看得见摸得着的东西基本上都是UIView，比如一个按钮、一个文本标签、一个文本输入框、一个图标等等，这些都是UIView

**其实UIView之所以能显示在屏幕上，完全是因为它内部的一个图层**

在创建UIView对象时，UIView内部会自动创建一个图层(即CALayer对象)，通过UIView的layer属性可以访问这个层

```objective-c
@property(nonatomic,readonly,retain) CALayer *layer;  
```

当UIView需要显示到屏幕上时，会调用drawRect:方法进行绘图，并且会将所有内容绘制在自己的图层上，绘图完毕后，系统会将图层拷贝到屏幕上，于是就完成了UIView的显示

 换句话说，UIView本身不具备显示的功能，是它内部的层才有显示功能

## CALayer的基本使用

```
通过操作CALayer对象，可以很方便地调整UIView的一些外观属性，比如：
阴影
圆角大小
边框宽度和颜色
… …
 
还可以给图层添加动画，来实现一些比较炫酷的效果
```

## CALayer的属性

```objective-c
//宽度和高度
@property CGRect bounds; 
//位置(默认指中点，具体由anchorPoint决定)
@property CGPoint position; 
//锚点(x,y的范围都是0-1)，决定了position的含义
@property CGPoint anchorPoint; 
//背景颜色(CGColorRef类型)
@property CGColorRef backgroundColor; 
//形变属性
@property CATransform3D transform; 
//让图片显示固定区域(比如可以让一个图片只显示上半部分或者下半部分甚至更小)
@property CGRect contentsRect; 
 
//边框颜色(CGColorRef类型)
@property CGColorRef borderColor; 
 
//边框宽度
@property CGFloat borderWidth; 
 
//圆角半径
@property CGFloat cornerRadius; 
 
//内容(比如设置为图片CGImageRef)
@property(retain) id contents; 
```

## CALayer的阴影属性

```objective-c
//阴影颜色
@property CGColorRef shadowColor; 
 
//阴影不透明(0.0 ~ 1.0)
@property float shadowOpacity; 
 
//阴影偏移位置
@property CGSize shadowOffset; 
```

​        ![img](https://uploader.shimo.im/f/LMEUOqVRxh3B7BBu.png!thumbnail)      

## 关于CALayer的疑惑

首先

CALayer是定义在QuartzCore框架中的(Core Animation)

CGImageRef、CGColorRef两种数据类型是定义在CoreGraphics框架中的

UIColor、UIImage是定义在UIKit框架中的

其次

QuartzCore框架和CoreGraphics框架是可以跨平台使用的，在iOS和Mac OS X上都能使用

但是UIKit只能在iOS中使用

为了保证可移植性，QuartzCore不能使用UIImage、UIColor，只能使用CGImageRef、CGColorRef

## UIView和CALayer的选择

通过CALayer，就能做出跟UIView一样的界面效果

既然CALayer和UIView都能实现相同的显示效果，那究竟该选择谁好呢？其实，对比CALayer，UIView多了一个事件处理的功能。也就是说，CALayer不能处理用户的触摸事件，而UIView可以

所以，如果显示出来的东西需要跟用户进行交互的话，用UIView；如果不需要跟用户进行交互，用UIView或者CALayer都可以当然，CALayer的性能会高一些，因为它少了事件处理的功能，更加轻量级

## position和anchorPoint

CALayer有2个非常重要的属性：position和anchorPoint

图层的position就是view的center

```objective-c
@property CGPoint position; 
```

用来设置CALayer在父层中的位置

以父层的左上角为原点(0, 0)

```objective-c
@property CGPoint anchorPoint; 
```

称为“定位点”、“锚点”

决定着CALayer身上的哪个点会在position属性所指的位置

以自己的左上角为原点(0, 0)

它的x、y取值范围都是0~1，默认值为（0.5, 0.5），意味着锚点在layer的中间

## anchorPoint(示意图)

​        ![img](https://uploader.shimo.im/f/WfOVvrnGLoTSNi4W.png!thumbnail)      

## position和anchorPoint

​        ![img](https://uploader.shimo.im/f/IN6vnr90Wh6w5p3B.png!thumbnail)      

## 隐式动画

每一个UIView内部都默认关联着一个CALayer，我们可用称这个Layer为Root Layer（根层）

所有的非Root Layer，也就是手动创建的CALayer对象，都存在着隐式动画

#### 什么是隐式动画？

当对非Root Layer的部分属性进行修改时，默认会自动产生一些动画效果

而这些属性称为Animatable Properties(可动画属性)

列举几个常见的Animatable Properties：

bounds：用于设置CALayer的宽度和高度。修改这个属性会产生缩放动画

backgroundColor：用于设置CALayer的背景色。修改这个属性会产生背景色的渐变动画

position：用于设置CALayer的位置。修改这个属性会产生平移动画

可以通过动画事务(CATransaction)关闭默认的隐式动画效果

```objective-c
[CATransaction begin]; 
[CATransaction setDisableActions:YES]; 
self.myview.layer.position = CGPointMake(10, 10); 
[CATransaction commit]; 
```

## CALayer使用

**一、添加一个简单的图层**

```objective-c
 1 CALayer *myLayer = [CALayer layer];
 2 // 设置层的宽度和高度（100x100） 
 3 myLayer.bounds = CGRectMake(0, 0, 100, 100);
 4 // 设置层的位置 
 5 myLayer.position = CGPointMake(100, 100);
 6 // 设置层的背景颜色：红色 
 7 myLayer.backgroundColor = [UIColor redColor].CGColor;
 8 // 设置层的圆角半径为10 
 9 myLayer.cornerRadius = 10;
10
11 // 添加myLayer到控制器的view的layer中 
12 [self.view.layer addSublayer:myLayer];
```

\* 第1行创建了一个自动释放的CALayer对象，你也可以使用经典的alloc和init方法来创建 

* 第12行将创建好的层添加到控制器的view的层中 

​        ![img](https://uploader.shimo.im/f/R5xFesWeShosmBO3.png!thumbnail)      

#### **二、添加一个显示图片的图层**

```objective-c
 1 CALayer *myLayer = [CALayer layer];
 2 // 设置层的宽度和高度（100x100） 
 3 myLayer.bounds = CGRectMake(0, 0, 100, 100);
 4 // 设置层的位置 
 5 myLayer.position = CGPointMake(100, 100);
 6 // 设置需要显示的图片 
 7 myLayer.contents = (id)[UIImage imageNamed:@"lufy.png"].CGImage;
 8 // 设置层的圆角半径为10 
 9 myLayer.cornerRadius = 10;
10 // 如果设置了图片，需要设置这个属性为YES才有圆角效果 
11 myLayer.masksToBounds = YES;
12
13 // 添加myLayer到控制器的view的layer中 
14 [self.view.layer addSublayer:myLayer];
```

\* 在第7行设置需要显示的图片，注意，这里用的是UIImage的CGImage属性，是一种CGImageRef类型的数据 

​        ![img](https://uploader.shimo.im/f/goQgqmHSLnWOlZTM.png!thumbnail)      

#### **三、为什么CALayer中使用CGColorRef和CGImageRef这2种数据类型，而不用UIColor和UIImage？

首先要知道：CALayer是定义在QuartzCore框架中的；CGImageRef、CGColorRef两种数据类型是定义在CoreGraphics框架中的；UIColor、UIImage是定义在UIKit框架中的 

其次，QuartzCore框架和CoreGraphics框架是可以跨平台使用的，在iOS和Mac OS X上都能使用，但是UIKit只能在iOS中使用 

因此，为了保证可移植性，QuartzCore不能使用UIImage、UIColor，只能使用CGImageRef、CGColorRef 

不过很多情况下，可以通过UIKit对象的特定方法，得到CoreGraphics对象，比如UIImage的CGImage方法可以返回一个CGImageRef 

#### **四、UIView和CALayer的选择**

细心的朋友不难发现，其实前面的2个效果不

仅可以通过添加层来实现，还可以通过添加UIView来实现。

比如，第1个红色的层可以用一个UIView来实现，第2个显示图片的层可以用一个UIImageView来实现。

 既然CALayer和UIView都能实现相同的显示效果，那究竟该选择谁好呢？

* 其实，对比CALayer，UIView多了一个事件处理的功能。也就是说，CALayer不能处理用户的触摸事件，而UIView可以
*  所以，如果显示出来的东西需要跟用户进行交互的话，用UIView；如果不需要跟用户进行交互，用UIView或者CALayer都可以
*  当然，CALayer的性能会高一些，因为它少了事件处理的功能，更加轻量级 

#### 五、UIView和CALayer的其他关系

* UIView可以通过subviews属性访问所有的子视图，类似地，CALayer也可以通过sublayers属性访问所有的子层

* UIView可以通过superview属性访问父视图，类似地，CALayer也可以通过superlayer属性访问父层
* 下面再看一张UIView和CALayer的关系图： 

​        ![img](https://uploader.shimo.im/f/o9fOdewORjDmOINu.png!thumbnail)      

如果两个UIView是父子关系，那么它们内部的CALayer也是父子关系。

#### [**CALayer3-**](http://www.cnblogs.com/mjios/archive/2013/04/14/3020291.html)[**层的属性**](http://www.cnblogs.com/mjios/archive/2013/04/14/3020291.html)

