1.UIView与CALayer关系

CALayer主要作用是提供可视内容，处理内容几何信息，位置、大小、变换，用于屏幕显示内容

1.UIView包含一个CALayer层，其中UIView除了显示视图之外还要处理响应事件，CALayer提供内容的绘制和显示

2.一个Layer的frame它的 anchorPoint,position,bounds,和 transform 共同决定的，而一个 View 的 frame 只是简单的返回 Layer的 frame，同样 View 的 center和 bounds 也是返回 Layer 的一些属性。

3.UIView主要是对显示内容的管理而 CALayer 主要侧重显示内容的绘制

UIView继承了CALayerDelegate的代理

4.CALayer 是默认修改属性支持隐式动画的，在给 UIView 的 Layer 做动画的时候，View 作为 Layer 的代理，Layer  通过 actionForLayer:forKey:向 View请求相应的 action(动画行为)

二、事件传递与响应者链:

IOS事件分为三类:触摸时间、加速事件、远程控制事件

### UIResponder

IOS当中只有以下继承自UIResponder的，所以都能接收并处理事件。

-   UIApplication
-   UIViewController
-   UIView

UIResponder当中有4个对象来处理触摸事件

```objective-c
UIResponder内部提供了以下方法来处理事件触摸事件
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event;
- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event;
- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event;
- (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event;
加速计事件
- (void)motionBegan:(UIEventSubtype)motion withEvent:(UIEvent *)event;
- (void)motionEnded:(UIEventSubtype)motion withEvent:(UIEvent *)event;
- (void)motionCancelled:(UIEventSubtype)motion withEvent:(UIEvent *)event;
远程控制事件
- (void)remoteControlReceivedWithEvent:(UIEvent *)event;
```

### 事件的处理

下面以UIView为例来说明触摸事件的处理。

```objective-c
// UIView是UIResponder的子类，可以覆盖下列4个方法处理不同的触摸事件
// 一根或者多根手指开始触摸view，系统会自动调用view的下面方法
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
// 一根或者多根手指在view上移动，系统会自动调用view的下面方法（随着手指的移动，会持续调用该方法）
- (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event
// 一根或者多根手指离开view，系统会自动调用view的下面方法
- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event
// 触摸结束前，某个系统事件(例如电话呼入)会打断触摸过程，系统会自动调用view的下面方法
- (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event
// 提示：touches中存放的都是UITouch对象
```

需要注意的是：以上四个方法是由系统自动调用的，所以可以通过重写该方法来处理一些事件。

### 2.1.1.UITouch对象

-   当用户用一根手指触摸屏幕时，会创建一个与手指相关的UITouch对象
-   一根手指对应一个UITouch对象
-   如果两根手指同时触摸一个view，那么view只会调用一次touchesBegan:withEvent:方法，touches参数中装着2个UITouch对象
-   如果这两根手指一前一后分开触摸同一个view，那么view会分别调用2次touchesBegan:withEvent:方法，并且每次调用时的touches参数中只包含一个UITouch对象

## iOS中的事件的产生和传递

### 事件的产生

1.  发生触摸事件后，系统会将该事件加入到一个由UIApplication管理的事件队列中,先进先出可以让先进入的事件最先处理
2.  UIApplication会从事件队列中取出最前面的事件，并将事件分发下去以便处理，通常，先发送事件给应用程序的主窗口（keyWindow）
3.  主窗口会在视图层次结构中找到一个最合适的视图来处理触摸事件，这也是整个事件处理过程的第一步。

## 事件的传递

-   触摸事件的传递是从父控件传递到子控件
-   也就是UIApplication->window->寻找处理事件最合适的view

>   `注 意`: 如果父控件不能接受触摸事件，那么子控件就不可能接收到触摸事件，也就是说如果父视图设置不可设置触摸事件，子视图也无法获取点击事件

##### 应用如何找到最合适的控件来处理事件？

1.首先判断主窗口（keyWindow）自己是否能接受触摸事件

2.判断触摸点是否在自己身上

3.子控件数组中从后往前遍历子控件，重复前面的两个步骤（所谓从后往前遍历子控件，就是首先查找子控件数组中最后一个元素，然后执行1、2步骤）

4.view，比如叫做fitView，那么会把这个事件交给这个fitView，再遍历这个fitView的子控件，直至没有更合适的view为止。

5.如果没有符合条件的子控件，那么就认为自己最合适处理这个事件，也就是自己是最合适的view

UIView不能接收触摸事件的三种情况：

-   **不允许交互**：userInteractionEnabled = NO

-   **隐藏**：如果把父控件隐藏，那么子控件也会隐藏，隐藏的控件不能接受事件

-   **透明度**：如果设置一个控件的透明度<0.01，会直接影响子控件的透明度。alpha：0.0~0.01为透明。

>   `注 意`:默认UIImageView不能接受触摸事件，因为不允许交互，即userInteractionEnabled = NO。所以如果希望UIImageView可以交互，需要设置UIImageView的userInteractionEnabled = YES。

### 总结一下

1.点击一个UIView或产生一个触摸事件A，这个触摸事件A会被添加到由UIApplication管理的事件队列中（即，首先接收到事件的是UIApplication）。
 2.UIApplication会从事件对列中取出最前面的事件（此处假设为触摸事件A），把事件A传递给应用程序的主窗口（keyWindow）。
 3.窗口会在视图层次结构中找到一个最合适的视图来处理触摸事件。（至此，第一步已完成）

![img](https://upload-images.jianshu.io/upload_images/1055199-48ec84d3d176374e.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

>   如果想让某个view不能处理事件（或者说，事件传递到某个view那里就断了），那么可以通过刚才提到的三种方式。比如，设置其userInteractionEnabled = NO;那么传递下来的事件就会由该view的父控件处理。
>    例如，不想让蓝色的view接收事件，那么可以设置蓝色的view的userInteractionEnabled = NO;那么点击黄色的view或者蓝色的view所产生的事件，最终会由橙色的view处理，橙色的view就会成为最合适的view。
>    所以，不管视图能不能处理事件，只要点击了视图就都会产生事件，关键在于该事件最终是由谁来处理！也就是说，如果蓝色视图不能处理事件，点击蓝色视图产生的触摸事件不会由被点击的视图（蓝色视图）处理！
>    注意：如果设置父控件的透明度或者hidden，会直接影响到子控件的透明度和hidden。如果父控件的透明度为0或者hidden = YES，那么子控件也是不可见的！

## 3.3.（重难点）如何寻找最合适的view

应用如何找到最合适的控件来处理事件？
 1.首先判断主窗口（keyWindow）自己是否能接受触摸事件
 2.触摸点是否在自己身上
 3.从后往前遍历子控件，重复前面的两个步骤（首先查找数组中最后一个元素）
 4.如果没有符合条件的子控件，那么就认为自己最合适处理

>   详述：1.主窗口接收到应用程序传递过来的事件后，首先判断自己能否接手触摸事件。如果能，那么在判断触摸点在不在窗口自己身上
>    　　2.如果触摸点也在窗口身上，那么窗口会从后往前遍历自己的子控件（遍历自己的子控件只是为了寻找出来最合适的view）
>    　　3.遍历到每一个子控件后，又会重复上面的两个步骤（传递事件给子控件，1.判断子控件能否接受事件，2.点在不在子控件上）
>    　　4.如此循环遍历子控件，直到找到最合适的view，如果没有更合适的子控件，那么自己就成为最合适的view。
>    找到最合适的view后，就会调用该view的touches方法处理具体的事件。所以，只有找到最合适的view，把事件传递给最合适的view后，才会调用touches方法进行接下来的事件处理。找不到最合适的view，就不会调用touches方法进行事件处理。
>    注意：之所以会采取从后往前遍历子控件的方式寻找最合适的view只是为了做一些循环优化。因为相比较之下，后添加的view在上面，降低循环次数。

### 3.3.1.寻找最合适的view底层剖析

两个重要的方法：
**hitTest:withEvent:**方法
**pointInside**方法

#### 3.3.1.1.hitTest：withEvent：方法

**什么时候调用？**

 **作用**

-   寻找并返回最合适的view(能够响应事件的那个最合适的view)

>   `注 意`：不管这个控件能不能处理事件，也不管触摸点在不在这个控件上，事件都会先传递给这个控件，随后再调用hitTest:withEvent:方法

**拦截事件的处理**

-   正因为hitTest：withEvent：方法可以返回最合适的view，所以可以通过重写hitTest：withEvent：方法，返回指定的view作为最合适的view。
-   不管点击哪里，最合适的view都是hitTest：withEvent：方法中返回的那个view。
-   通过重写hitTest：withEvent：，就可以拦截事件的传递过程，想让谁处理事件谁就处理事件。

>   事件传递给谁，就会调用谁的hitTest:withEvent:方法。
>    `注 意`：如果hitTest:withEvent:方法中返回nil，那么调用该方法的控件本身和其子控件都不是最合适的view，也就是在自己身上没有找到更合适的view。那么最合适的view就是该控件的父控件。

**所以事件的传递顺序是这样的**

产生触摸事件->UIApplication事件队列->[UIWindow hitTest:withEvent:]->返回**更合适**的view->[子控件 hitTest:withEvent:]->返回**最合适**的view

>   **事件传递给窗口或控件的后，就调用hitTest:withEvent:方法寻找更合适的view。所以是，先传递事件，再根据事件在自己身上找更合适的view。**
>    **不管子控件是不是最合适的view，系统默认都要先把事件传递给子控件，经过子控件调用子控件自己的hitTest:withEvent:方法验证后才知道有没有更合适的view。即便父控件是最合适的view了，子控件的hitTest:withEvent:方法还是会调用，不然怎么知道有没有更合适的！即，如果确定最终父控件是最合适的view，那么该父控件的子控件的hitTest:withEvent:方法也是会被调用的。**

**技巧：**想让谁成为最合适的view就重写谁自己的父控件的hitTest:withEvent:方法返回指定的子控件，或者重写自己的hitTest:withEvent:方法 return self。但是，***建议在父控件的hitTest:withEvent:中返回子控件作为最合适的view！\***

**原因**在于在自己的hitTest:withEvent:方法中返回自己有时候会出现问题。因为会存在这么一种情况：当遍历子控件时，如果触摸点不在子控件A自己身上而是在子控件B身上，还要要求返回子控件A作为最合适的view，采用返回自己的方法可能会导致还没有来得及遍历A自己，就有可能已经遍历了点真正所在的view，也就是B。这就导致了返回的不是自己而是触摸点真正所在的view。所以还是建议在父控件的hitTest:withEvent:中返回子控件作为最合适的view！
 **例如：**whiteView有redView和greenView两个子控件。redView先添加，greenView后添加。如果要求无论点击那里都要让redView作为最合适的view（把事件交给redView来处理）那么只能在whiteView的hitTest:withEvent:方法中return self.subViews[0];这种情况下在redView的hitTest:withEvent:方法中return self;是不好使的！

```objective-c
// 这里redView是whiteView的第0个子控件
#import "redView.h"

@implementation redView
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event{ 
   return self;
}
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event{ 
   NSLog(@"red-touch");
}@end
// 或者
#import "whiteView.h"

@implementation whiteView
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event{ 
   return self.subviews[0];
}
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event{ 
   NSLog(@"white-touch");
}
@end
```

#### 3.3.1.2.pointInside:withEvent:方法

pointInside:withEvent:方法判断点在不在当前view上（方法调用者的坐标系上）如果返回YES，代表点在方法调用者的坐标系上;返回NO代表点不在方法调用者的坐标系上，那么方法调用者也就不能处理事件。

## 事件的响应

##### 触摸事件处理的整体过程

1.  用户点击屏幕后产生的一个触摸事件，经过一系列的传递过程后，会找到最合适的视图控件来处理这个事件
2.  找到最合适的视图控件后，就会调用控件的touches方法来作具体的事件处理touchesBegan…touchesMoved…touchedEnded…
3.  这些touches方法的默认做法是将事件顺着响应者链条向上传递（也就是touch方法默认不处理事件，只传递事件），将事件交给上一个响应者进行处理

### 响应者链条示意图

**响应者链条：**在iOS程序中无论是最后面的UIWindow还是最前面的某个按钮，它们的摆放是有前后关系的，一个控件可以放到另一个控件上面或下面，那么用户点击某个控件时是触发上面的控件还是下面的控件呢，这种先后关系构成一个链条就叫“响应者链”。也可以说，响应者链是由多个响应者对象连接起来的链条。在iOS中响应者链的关系可以用下图表示：

![img](https://upload-images.jianshu.io/upload_images/1055199-2a49a16e1e483b5c.png)

**响应者对象：**能处理事件的对象，也就是继承自UIResponder的对象

**作用：**能很清楚的看见每个响应者之间的联系，并且可以让一个事件多个对象处理。

**如何判断上一个响应者**

1.   如果当前这个view是控制器的view,那么控制器就是上一个响应者
2.  如果当前这个view不是控制器的view,那么父控件就是上一个响应者

**响应者链的事件传递过程:**

-   1>如果当前view是控制器的view，那么控制器就是上一个响应者，事件就传递给控制器；如果当前view不是控制器的view，那么父视图就是当前view的上一个响应者，事件就传递给它的父视图
-   2>在视图层次结构的最顶级视图，如果也不能处理收到的事件或消息，则其将事件或消息传递给window对象进行处理
-   3>如果window对象也不处理，则其将事件或消息传递给UIApplication对象
-   4>如果UIApplication也不能处理该事件或消息，则将其丢弃

**事件处理的整个流程总结：**
 　1.触摸屏幕产生触摸事件后，触摸事件会被添加到由UIApplication管理的事件队列中（即，首先接收到事件的是UIApplication）。
 　2.UIApplication会从事件队列中取出最前面的事件，把事件传递给应用程序的主窗口（keyWindow）。
 　3.主窗口会在视图层次结构中找到一个最合适的视图来处理触摸事件。（至此，第一步已完成)
 　4.最合适的view会调用自己的touches方法处理事件
 　5.touches默认做法是把事件顺着响应者链条向上抛。
 touches的默认做法：

```objc
#import "WSView.h"
@implementation WSView 
//只要点击控件,就会调用touchBegin,如果没有重写这个方法,自己处理不了触摸事件
// 上一个响应者可能是父控件
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event{ 
// 默认会把事件传递给上一个响应者,上一个响应者是父控件,交给父控件处理
[super touchesBegan:touches withEvent:event]; 
// 注意不是调用父控件的touches方法，而是调用父类的touches方法
// super是父类 superview是父控件 
}
@end
```

**事件的传递与响应：**

1、当一个事件发生后，事件会从父控件传给子控件，也就是说由UIApplication -> UIWindow -> UIView -> initial view,以上就是事件的传递，也就是寻找最合适的view的过程。

2、接下来是事件的响应。首先看initial view能否处理这个事件，如果不能则会将事件传递给其上级视图（inital view的superView）；如果上级视图仍然无法处理则会继续往上传递；一直传递到视图控制器view controller，首先判断视图控制器的根视图view是否能处理此事件；如果不能则接着判断该视图控制器能否处理此事件，如果还是不能则继续向上传 递；（对于第二个图视图控制器本身还在另一个视图控制器中，则继续交给父视图控制器的根视图，如果根视图不能处理则交给父视图控制器处理）；一直到 window，如果window还是不能处理此事件则继续交给application处理，如果最后application还是不能处理此事件则将其丢弃

3、在事件的响应中，如果某个控件实现了touches...方法，则这个事件将由该控件来接受，如果调用了[supertouches….];就会将事件顺着响应者链条往上传递，传递给上一个响应者；接着就会调用上一个响应者的touches….方法

**如何做到一个事件多个对象处理：**

因为系统默认做法是把事件上抛给父控件，所以可以通过重写自己的touches方法和父控件的touches方法来达到一个事件多个对象处理的目的。

```objective-c
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event{ 
// 1.自己先处理事件...
NSLog(@"do somthing...");
// 2.再调用系统的默认做法，再把事件交给上一个响应者处理
[super touchesBegan:touches withEvent:event]; 
}

```

**事件的传递和响应的区别：**
事件的传递是从上到下（父控件到子控件），事件的响应是从下到上（顺着响应者链条向上传递：子控件到父控件。

# UI图像显示原理

CPU和GPU都和总线连接。CPU拿到的是位图，会在适当的时机通过总线交给GPU,GPU拿到位图后会做响应的图层渲染，然后把结果放在贞缓冲区（Frame Buffer）中,再由视图控制器根据VSync信号在适应的时机内在贞缓冲区内提取响应显示的内容。最终显示在手机的屏幕上。

![img](https://user-gold-cdn.xitu.io/2019/10/19/16de4a4f278fd9b2?imageslim)

接下来看一下CPU和GPU都做了哪些事。

首先我们创建一个UIView视图后，它的显示部分是由CALayer来负责的。而CALater上的一个属性contents就是我们要最终绘制在屏幕上的位图。比如我们创建的一个UILable，文字是Hello world ，那么contents里边存放的就是一个存有Hello world 的位图。然后系统会在合适的时机回掉给我们一个drawRect:的方法。然后我们就可以去绘制一些自定义的一些内容。绘制好的内容会经由Core Animation 这个框架来交给OpenGL(ES)渲染管线，还进行一些绘制，比如纹理的合成等，最终渲染到我们的屏幕上。

![img](https://user-gold-cdn.xitu.io/2019/10/19/16de4a58636733a1?imageslim)

在接下来看下CPU和GPU 的工作承担

CPU工作。主要负责视图的布局Layout（ui的布局和文本的计算，其实就是我们平常设计一个label的frame已经文字大小设置）Display过程(包括显示或者绘制比如我们的drawRect:方法就发生在这个过程中)之后呢做一些准备的工作（准备工作比如我们设置了UIImageView的图片，但是这个图片是不能直接显示的，需要图片的编解码），再把位图提交到GPU上面。

![img](https://user-gold-cdn.xitu.io/2019/10/19/16de4a5e33342a65?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

GPU工作：GPU的渲染管线其实就是OpenGL的渲染管线。首先会进行顶点着色(就是对位图的处理)，然后就是图元装配->光栅化->片段着色->片段处理。这个几步都完成后就会把处理好的像素点放在贞缓冲区中。

![img](https://user-gold-cdn.xitu.io/2019/10/19/16de4a634fa7b9e0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

大致的流程就这么多，针对后边的GPU渲染流程，详细的渲染流程我会在后边的文章中更新。

# UI卡顿、掉帧的原因

![WX20181206-160621@2x.png](https://user-gold-cdn.xitu.io/2018/12/6/16783eb3cc81da82?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

在显示器中是固定的频率，比如iOS中是每秒60帧（60FPS），即每帧16.7ms

从上图中可以看出，每两个VSync信号之间有时间间隔（16.7ms），在这个时间内，CPU主线程计算布局，解码图片，创建视图，绘制文本，计算完成后将内容交给GPU，GPU变换，合成，渲染（详细可学习 OpenGL相关课程），放入帧缓冲区

**假如16.7ms内，CPU和GPU没有来得及生产出一帧缓冲，那么这一帧会被丢弃，显示器就会保持不变，继续显示上一帧内容，这就将导致导致画面卡顿**

所以无论CPU,GPU，哪个消耗时间过长，都会导致在16.7ms内无法生成一帧缓存

### 卡顿、掉帧优化方案切入点

**CPU** CPU在准备下一帧的所做的工作非常多导致耗时，基于减轻CPU工作时长和压力来达到一个优化效果 1、部分对象的创建、调整和销毁可以放到子线程去做 2、预排版（ 布局计算、文本计算），这些计算也可以放到子线程去做，这样主线程也可以有更多的时间去响应用户的交互 3、预渲染（文本等异步绘制、图片编解码等）

**GPU** 1、**纹理渲染**：假如说我们触发了**离屏渲染**，例如我们设置圆角时对maskToBounds的设置，包括一些阴影、蒙层等都会触发GPU层面的离屏渲染，对于这种情况下，GPU对于纹理渲染的工作量就会非常的大，我们可以基于此对GPU进行优化，就是**尽量减少离屏渲染**，我们也可以**通过CPU的异步绘制来减轻GPU的压力**

2、**视图混合**： 比如说我们视图层级比较复杂，视图之间层层叠加，那么GPU就要做每一个视图的合成，合成每一个像素点的像素值，如果我们可以**减少视图的层级**，也是可以减轻GPU的压力，我们也可以**通过CPU的异步绘制机制来达到一个提交的位图本身就是一个层级比较少的位图**

### UIView的绘制原理

##### 流程图

![QQ20181206-211905@2x.png](https://user-gold-cdn.xitu.io/2018/12/6/16783eb3cd1e3681?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

在CALayer内部会先创建backing store，我可以理解为CGContext，我们一般在drawRect:方法中通过上下文堆栈当中取出栈顶的context,也就是上下文

然后这个layer会判断是否有代理，如果没有代理，那么就会调用[CALayer drawInCotext:]；**如果有代理，会调用代理的drawLayer:inContext:方法，然后做当前视图的绘制工作**（**这一步是发生在系统内部的**），然后在一个合适的时机给与我们这个十分熟悉的[UIView drawRect:]方法的回调，[UIView drawRect:]这个方法默认是什么都不做，，系统给我们开这个口子是为了让我们可以再做一些其他的绘制工作

然后无论是哪个分支，**最终都会由CALayer上传对应的backing store(可以理解为位图)给GPU**，然后就结束了系统默认的绘制流程

### 那么问题来了，我们如何进行异步绘制呢

**实际上我们就需要借用系统给开的这个口子，即[layer.delegate displayLayer:]**

-   在这个异步绘制过程中就需要代理负责生成对应的bitmap(位图)
-   同时设置bitmap作为layer.contents属性的值

**国际惯例，流程图走一波（原谅我画图能力实在有限TT）**



![QQ20181206-220620@2x.png](https://user-gold-cdn.xitu.io/2018/12/6/16783eb3ce69f9a4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



-   假如说我们在某一个时机调用了[view setNeedsDisplay]这个方法，系统会在当前runloop将要结束的时候调用[CALyer display]方法，然后如果我们这个layer的代理实现了[view displayLayer]这个方法
-   然后会通过子线程的切换，我们在子线程中去做一个位图的绘制，主线程可以去做一些其他的操作
-   在子线程中第一步先通过CGBitmapContextCreate()方法来创建一个位图的上下文，然后我们通过CoreGraphic API可以做当前UI控件的一些绘制工作，最后我们再通过CGBitmapContextCreateImage()这个函数来根据当前所绘制的上下文来生成一张CGImage图片
-   最后回到主线程来提交这个位图，设置layer的contents属性，这样就完成了一个UI控件的异步绘制过程

### 离屏渲染 （便于理解视图卡顿、掉帧中对GPU的开销）

**离屏渲染指的是GPU在当前屏幕缓冲区以外开辟了一个缓冲区进行渲染操作**

当前屏幕渲染不需要额外创建新的缓存，也不需要开启新的上下文，相对于离屏渲染性能更好。但是受当前屏幕渲染的局限因素限制(只有自身上下文、屏幕缓存有限等)，当前屏幕渲染有些情况下的渲染解决不了的，就使用到离屏渲染

**离屏渲染对性能的的代价是很高的，主要体现在：**

-   创建了新的缓冲区
-   上下文的频繁切换

**导致产生离屏渲染的原因：**

-   shouldRasterize（光栅化）
-   masks（遮罩）
-   shadows（阴影）
-   edge antialiasing（抗锯齿）
-   group opacity（不透明）
-   复杂形状设置圆角等
-   渐变

# UI滑动优化方案

#### 1.多个透明元素重叠显示的性能问题。

-   解决方案：合并成一张图显示
-   原理：CPU方面，减少了UIKit的创建消耗，GPU方面，避免了合成渲染产生的消耗。
-   AsyncDisplayKit（[现在叫Texture](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2FTextureGroup%2FTexture)），针对多个透明元素的重叠，预合并无点击响应，不改变动画的图层。
-   Texture的保持流畅的原理：UIKit不是线程安全的，所以必须在主线程改动。Texture利用中间变量存储改动，保证线程安全，在合适的机会将并发操作同步到主线程。
-   暂时不用Texture的原因：需要用Texture Node Container替换UIKit元素，成本较大。

#### 2.静态cell、多图待加载的优化

-   解决方案：合并成一张图显示；
-   原理：提升I/O速度，一个大文件的读取速度，通常比多个小文件要快。

#### 3.展示适合界面尺寸图片，不进行拉伸缩放。

-   解决方案：从服务器拉取合适尺寸的图片（例如七牛的服务就带裁剪/压缩参数）；
-   原理：过大图片对内存消耗巨大（图片占用内存 = 图像高×图像宽×像素位数）；不符合UIImageView尺寸的图片，进行重新缩减/放大尺寸的消耗是非常巨大的。

#### 4.imageNamed和imageWithContentsOfFile

这个知道的人比较多，因为缓存图片的消耗通常是肉眼可见的多。
 常用的元素例如icon之类的，采用imageNamed:，系统会有缓存。
 如果是较大或者不常用的图片资源，采用imageWithContentsOfFile:。

#### 5.获取文件大小

-   解决方案：不要使用NSFileManager，用C的stat来获取文件信息。
-   实例：获取一个目录下所有文件大小，进行多次递归计算，stat几乎瞬间完成，NSFileManager耗时较长。

#### 6.NSDateFormatter产生较大消耗

-   解决方案：.缓存NSDateFormatter结果，不多次创建，及时释放。
-   做过类似日历的同学应该都懂😂

#### 7.图片解码：

-   解决方案：CALayer 被提交到 GPU 前，CGImage 中的数据才会得到解码，GPU执行，卡主线程。常见的做法是在后台线程先把图片绘制到 CGBitmapContext 中，然后从 Bitmap 直接创建图片。
-   SDWebImage/YYImage等图片库都是这么做的，有兴趣的同学可以去看下源码。如果你是自己做图片下载，就要考虑到相关优化。

### 二.cell高度预计算/缓存

-   一般情况下，不要用estimatedRowHeight，不然容易鬼畜；
-   systemLayoutSizeFittingSize:这个方法，就是大部分cell布局库采用的方法，只要从上至下布局全部生效，就能计算高度，不要多次调用；
-   由于UITableView绘制过程中多次调用绘制，所以缓存高度计算结果，可以有效的增加滑动流畅度；
-   当cell高度改变，记得及时替换缓存；

### 三.离屏渲染

触发条件：CALayer 的 border、圆角、阴影、遮罩（mask），CASharpLayer 的矢量图形显示。
 主要问题：GPU占满，CPU空闲
 解决方法：
 1.开启CALayer.shouldRasterize ，转嫁到CPU上；
 2.粗暴画图/截图实现border和圆角；
 3.砍死设计师。

最近拖延症有点厉害，这篇文章想写了很久都没写出来，我先摘要一部分思路出来。



![img](https:////upload-images.jianshu.io/upload_images/256780-3410344c59a4d904.png?imageMogr2/auto-orient/strip|imageView2/2/w/881/format/webp)

拖延症晚期患者.png

曾经的需求是这样的：



![img](https:////upload-images.jianshu.io/upload_images/256780-cdc776e220cd1690.png?imageMogr2/auto-orient/strip|imageView2/2/w/704/format/webp)

注意需求的圈和头像之间是有空隙的.png

初期方案是图片下载完成后，裁成圆形，然后外面用贝塞尔画一个圈，根据不同的UI缓存不同多个带圈儿的图；
 然而...神奇的产品第二版给我整出了无数个各种不同大小、间距、透明度的圈。这样就意味着我得缓存无数带着各色圈儿的图。

![img](https:////upload-images.jianshu.io/upload_images/256780-569e79697a8f453c.png?imageMogr2/auto-orient/strip|imageView2/2/w/194/format/webp)

此时的心情.png

后来突然灵光一闪，单独设layer的圆角貌似不引发离屏渲染（有说法是iOS8之后）。所以后来方案变成，UIView嵌套一个UIImageView，只缓存裁剪过的图片。

但是这样的话，其实UIKit的创建要比读取画好的图更耗性能。所以这是个终极的问题，时间/空间，究竟选哪个。

### 四.图片的处理

#### 1.SDWebImage的外层干了啥

![img](https:////upload-images.jianshu.io/upload_images/256780-991dc3e3797c2c09.png?imageMogr2/auto-orient/strip|imageView2/2/w/485/format/webp)

喜闻乐见的拖延症晚期



这篇文章写了1/4的样子，详细的会在这篇文章里，十分心疼自己。下面简单说说SDWebImage的外层都干了啥：

-   重用cell的时候，cancel之前下载operation；
-   二级缓存：memory/disk；
-   合并回调，多次调用只回调一次；

#### 2.项目中实际遇到的问题

我们做了个类似SDWebImage的东西，来实现神奇的需求，过程中遇到了一些问题：

-   从disk读取需要时间，在memory没有的情况下，导致image = nil的一瞬间闪动：我把SDWeb的demo改了改，左边按钮ReloadData，右边按钮清除内存（memory）缓存，在reload的一瞬间，展示了placeHolder的图。

    ![img](https:////upload-images.jianshu.io/upload_images/256780-c890317c881d4473.gif?imageMogr2/auto-orient/strip|imageView2/2/w/619/format/webp)

    SDWebFlash.gif

-   UICollectionView, reloadData 迷之移位闪烁问题：当reloadData的时候，重用的cell神奇的变换了次序，此时memory里只要一图片不存在，就会有一瞬间的闪动。

-   上面两个问题的解决方法有：

    -   尽量避免reloadData，可以找到visibleCells，一一更换换图片；
    -   优化细节，在发现disk里面有图的时候，image不设为nil，避免闪动；
    -   保证memory缓存的大小和数量，能满足界面需求；

-   请求没有合并/回调没有合并；
     这个比较好解决，就是请求之前判断该请求是否在执行，若在执行，则将回调暂存到该请求下，等完成后一并调用。

### 五.ScrollView滑动优化

#### 1.滑动时的代理

-   scrollViewDidScroll：实际上就是contentOffset的KVO
-   scrollViewWillBeginDragging:
-   scrollViewWillEndDragging: withVelocity: （points/millisecond这实际上是个速度的参数）targetContentOffset:(这是一个可以传值的指针，可以控制最后的减速动画)
-   scrollViewDidEndDragging: willDecelerate:（拖动结束，如果仍有速度，会执行后面两个方法）
-   scrollViewWillBeginDecelerating:
-   scrollViewDidEndDecelerating:
-   需要注意scrollView的dragging属性在decelerate的过程中仍然为YES

#### 2.特殊情况

drag完，正decelerating时（didEndDecelerating尚未调用），强行再次drag（单指停止滑动，双指连环滑）

-   单指停止滑动：没有decelerate ，willBeginDecelerating不会被调用，但前一次留下的 didEndDecelerating 会被调用（后面会结合VVWeibo的例子讲述这里怎么处理）
-   双指连环滑动：willBeginDecelerating会先于didEndDecelerating调用，就是说这种情况didEndDecelerating会在你手指离开屏幕且屏幕停止的情况下调用。

#### 3.VVWeibo的做法

-   scrollViewWillEndDragging: withVelocity: targetContentOffset:时，可以从targetContentOffset判断即将加载的那一页cell，从而预先加载，UITableView有传入rect返回cells的方法，UICollectionView得强行取两个点获得这两个点cell的IndexPath，然后得到cells。
-   遇到前文单指停止的处理，VVWeibo是在UITableView的子类捕获了touchEvent，然后reloadData，我就没有做子类了。最后做了一系列神奇的判断，然后reload。但是仍然遇到了 reloadData 迷之移位闪烁问题；后来我加入了速度的判断，这个已经不会触发了，我就暂时注销掉了，等待下一步优化。

#### 4.最迷的问题

UICollectionViewLayout的prepareLayout调用了过多次数，是因为shouldInvalidateLayoutForBoundsChange:这个方法灾难的调用了多次，newBounds的x和y实际上随着滑动一直在变，return YES的话就一直重新布局，最后用magicNumber存他的size，当size变化才返回YES，就很强行的解决了。

### 六.魔鬼般的视频播放

这里涉及业务逻辑过多，我也不方便多写，就写一些过程中遇到的问题：

-   scrollViewDidEndDecelerating的VisibleItems为nil。换个线程/延时去取，就能取到。
-   缩小播放区域，跟前面的取点找目标cell的操作类似，找出首尾点，中间的cell，即是需要播放的cell。
-   多个等待播放的AVPlayerItemVideoOutput，会导致一部分失效，内存越小的机子上越明显。
     解决方法：播放前再把url赋给playerItem，一定程度避免过多playerItem失败的问题；
-   出入页面找到所有playerItem并干掉，避免影响其他播放；
-   GLKView的reuse在狂滑的时候十分耗内存，而不reuse的话，重用的时候，会显示上一个页面的残影，解决方法是先用图片盖住残影，在播前，清理上一次播放的残余；
-   加入一个Timer，通过记录偏移量来控制滑动速度，高速度的时候，不绘制/下载图片。这样也解决了双指狂滑的时候，无法很好的判断当前是否绘制的问题。