# UIScrollView

## UIScrollView基本使用

UIScrollView的常见属性

 UIScrollView的常用代理方法

UIScrollView的缩放

UIScrollView和UIPageControl的分页

NSTimer的使用

## 什么是UIScrollView

>   移动设备的屏幕大小是极其有限的，因此直接展示在用户眼前的内容也相当有限 当展示的内容较多，超出一个屏幕时，用户可通过滚动手势来查看屏幕以外的内容普通的UIView不具备滚动功能，不能显示过多的内容**UIScrollView是一个能够滚动的视图控件**，可以用来展示大量的内容，并且可以通过滚动查看所有的内容举例：手机上的“设置”、其他示例程序.

## UIScrollView的基本使用

UIScrollView 的用法很简单

将需要展示的内容添加到UIScrollView中

设置UIScrollView的contentSize属性，告诉UIScrollView所有内容的尺寸，也就是告诉它滚动的范围（能滚多远，滚到哪里是尽头）

​        ![img](https://uploader.shimo.im/f/ZLc4Rvqnf1odT2CK.png!thumbnail)      

## UIScrollView的常见属性

```objective-c
@property(nonatomic) CGSize contentSize;// 这个属性用来表示UIScrollView内容的尺寸，滚动范围（能滚多远）
@property(nonatomic) CGPoint contentOffset; //(又叫内容偏移量:内容左上角减去scoreView左上角差的值,用户只要稍微动一下,图片就会再次弹回原始的位置) 这个属性用来表示UIScrollView滚动到的位置
@property(nonatomic) UIEdgeInsets contentInset; //这个属性能够在UIScrollView的4周增加额外的滚动区域(也可以把这个理解成周围的厚度) 
@property(nonatomic) BOOL bounces; //设置UIScrollView是否需要弹簧效果
 
@property(nonatomic,getter=isScrollEnabled) BOOL scrollEnabled;  //设置UIScrollView是否能滚动
 
@property(nonatomic) BOOL showsHorizontalScrollIndicator; 
//是否显示水平滚动条

@property(nonatomic) BOOL showsVerticalScrollIndicator; 
//是否显示垂直滚动条
tracking
//当 touch 后还没有拖动的时候值是YES，否则NO 
 
zoomBouncing
//当内容放大到最大或者最小的时候值是 YES，否则 NO 
 
zooming
//当正在缩放的时候值是 YES，否则 NO 
 
decelerating
//当滚动后，手指放开但是还在继续滚动中。这个时候是 YES，其它时候是 NO 
 
decelerationRate
//设置手指放开后的减速率
 
maximumZoomScale
//一个浮点数，表示能放最大的倍数
 
minimumZoomScale
//一个浮点数，表示能缩最小的倍数
 
pagingEnabled
//当值是 YES 会自动滚动到 subview 的边界。默认是NO 
 
scrollEnabled
//决定是否可以滚动
```

​        ![img](https://uploader.shimo.im/f/zhKzDbPRTRLV07Kp.png!thumbnail)      

>   如果scrollView不是通过storyboard、xib创建，那么一开始的subviews里面没有子控件这就是和手动创建的最大的区别
>
>   在scoreView里面在水平滚动条和垂直滚动条没有设置为NO的情况下,它也算做ScrollView的一部分(在scoreView里面删除子控件的时候要格外的注意,但是可以把这两个滚动条给禁止设置为NO)  

```objective-c
意思是需要动画  但是时间是苹果自己定义的 第一个参数是你要滚动到的位置  第二个参数是是否要动画效果! 
        [self.scrollView setContentOffset:CGPointMake(0, self.scrollView.contentOffset.y) animated:YES]; 
        是否有弹簧效果 
        self.scrollView.bounces = NO; 
    
        不管有没有contentSize，都能拥有弹簧效果(一般适用于下拉刷新功能，上拉刷新) 
        self.scrollView.alwaysBounceVertical = YES; (弹簧效果) 
 
        self.scrollView.alwaysBounceHorizontal = YES;(水平效果可以滚来滚去) 
    
        是否要显示滚动条 
        self.scrollView.showsHorizontalScrollIndicator = NO; 
        self.scrollView.showsVerticalScrollIndicator = NO; 
    
        self.scrollView.indicatorStyle = UIScrollViewIndicatorStyleWhite; 
    
        NSLog(@"%@", self.scrollView.subviews); 
    
        [[self.scrollView.subviews firstObject] removeFromSuperview]; 
 
    /** 
     UIControlStateNormal;
     UIControlStateHighlighted;
     UIControlStateDisabled; 禁止状态// 只能通过enabled=NO达到这个状态 **/
```

​        ![img](https://uploader.shimo.im/f/jPK0VHk2xFZ8vhxP.png!thumbnail)      

## UIScrollView的代理（delegate）

代理三部曲:作用

1.代理一般都是控制器对象

2.代理一般都是id类型 (注意代理是弱指针,如果是强指针会内存泄露) 

3.代理协议的格式:控件类名Delegata

4.代理方法:方法名一般都是以控件名开头,比如UIScrollView的代理方法就是ScrollViewDelegata

5.如何监听控件的行为

### 通过addTarget(只有继承自UIControl的控件,才有这个功能)

通过Delegate(只有拥有delagate的属性的控件,才有这个功能,有的控件两个功能都有)(比如让控制器监听ScoreView的行为)

**注意:给其设置背景色的时候只有运行的时候才能看到** 

1.设置scorollView的deledate(代理)为控制器对象

scorollView.deledata = 控制器

 2.控制器要遵守协议 <UIScrollViewDelegata>  

3.控制器要实现UIscoreView协议里面的代理方法 

很多时候，我们想在UIScrollView正在滚动 或 滚动到某个位置 或者 停止滚动 时做一些特定的操作要想完成上述功能，前提条件就是能够监听到UIScrollView的整个滚动过程当UIScrollView发生一系列的滚动操作时， 会自动通知它的代理（delegate）对象，给它的代理发送相应的消息，让代理得知它的滚动情况

也就是说，要想监听UIScrollView的滚动过程，就必须先给UIScrollView设置一个代理对象，然后通过代理得知UIScrollView的滚动过程

​        ![img](https://uploader.shimo.im/f/T3xOzV8sk8eD45gL.png!thumbnail)      

   仔细看这个方法

```objective-c

UIImageView *imageView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"minion"]];
//这个方法的意思是通过一张图片来初始化这个UIImageView,并且UIImageView的尺寸就是图片的尺寸!
[scrollView addSubview:imageView]; 
scrollView.contentSize = imageView.frame.size;
设置代理（监听scrollView的各种行为）
scrollView.delegate = self;
#pragma mark - <UIScrollViewDelegate> 代理方法   只要scrollView在滚动，就会调用这个方法（监听scrollView的滚动）
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
NSLog(@"scrollViewDidScroll");
}  
//用户即将开始拖拽scrollView，就会调用这个方法
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView {
NSLog(@"scrollViewWillBeginDragging");
}  
//用户即将停止拖拽scrollView，就会调用这个方法
- (void)scrollViewWillEndDragging:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity targetContentOffset:(inout CGPoint *)targetContentOffset { 
NSLog(@"scrollViewWillEndDragging");
} 
//scrollView已经停止减速，就会调用这个方法（停止滚动）
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView {  
NSLog(@"scrollView减速完毕，停止滚动，完全静止");
}  
//用户已经停止拖拽scrollView，就会调用这个方法
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate { 
if (decelerate == NO) 
{     
NSLog(@"scrollView停止滚动，完全静止");
} else { 
NSLog(@"用户停止拖拽，但是scrollView由于惯性，会继续滚动，并且减速");   
}}
//textField的代理方法/** * 当textField的输入文字发生改变时，就会调用这个方法  * * @param string  用户当时输入的文字 (NSRange暂且不要管)  * * @return YES：允许用户输入；NO：禁止用户输入 */
- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string { 
if ([string isEqualToString:@"0"])
return NO;
return YES;
}
```

## UIScrollView和delegate的通信

​        ![img](https://uploader.shimo.im/f/SX6pLHdTNLZSBKN3.png!thumbnail)      

### 成为delegate的条件

UIScrollView将delegate需要实现的方法都定义在了UIScrollViewDelegate协议中，因此要想成为UIScrollView的delegate，必须遵守UIScrollViewDelegate协议，然后实现协议中相应的方法，就可以监听UIScrollView的滚动过程了

​        ![img](https://uploader.shimo.im/f/JtG2gehiXO96yqFc.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/avvRUIl2ry7QBTTC.png!thumbnail)      

### UIScrollView和控制器

一般情况下，就设置UIScrollView所在的控制器 为 UIScrollView的delegate  设置控制器为UIScrollView的delegate有2种方法：

通过代码（self就是控制器）

self.Edison.delegate = self;  

通过storyboard拖线（右击UIScrollView）

​        ![img](https://uploader.shimo.im/f/Dl1WGOpJQn9CEuxF.png!thumbnail)      

```objective-c
4. scrollView的代理方法
scrollView 正在滚动的时候调用 偏移量一直变化
- (void)scrollViewDidScroll:(UIScrollView *)scrollView;     scrollView正在缩放
- (void)scrollViewDidZoom:(UIScrollView *)scrollView
//开始拖拽的时候调用 
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView;     //即将停止拖拽的时候调用
- (void)scrollViewWillEndDragging:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity targetContentOffset:(inout CGPoint *)targetContentOffset NS_AVAILABLE_IOS(5_0);     
//已经停止拖拽调用     
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate;     scrollView即将开始减速
- (void)scrollViewWillBeginDecelerating:(UIScrollView *)scrollView;
//scrollView 停止减速 停止
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView;
//scrollView 停止滚的的动画
- (void)scrollViewDidEndScrollingAnimation:(UIScrollView *)scrollView;
//在scrollView中 哪些控件是需要缩放的
- (UIView *)viewForZoomingInScrollView:(UIScrollView *)scrollView; //    scrollView即将开始缩放 
- (void)scrollViewWillBeginZooming:(UIScrollView *)scrollView withView:(UIView *)view NS_AVAILABLE_IOS(3_2);//     scrollView结束缩放
- (void)scrollViewDidEndZooming:(UIScrollView *)scrollView withView:(UIView *)view atScale:(CGFloat)scale;     //scrollView 即将滚动到顶部
- (BOOL)scrollViewShouldScrollToTop:(UIScrollView *)scrollView;
//scrollView 已经滚动到顶部 
- (void)scrollViewDidScrollToTop:(UIScrollView *)scrollView;
```

## UIScrollView和控制器

​        ![img](https://uploader.shimo.im/f/7sthDBJQOk0RXEdw.png!thumbnail)      

### 内容缩放

###          ![img](https://uploader.shimo.im/f/tgB9cwy11TeUiXLd.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/5UVkhwhG9EuyBdAS.png!thumbnail)      

### 缩放实现步骤

```objective-c
跟缩放相关的其他代理方法准备开始缩放的时候调用
- (void)scrollViewWillBeginZooming:(UIScrollView *)scrollView withView:(UIView *)view//  正在缩放的时候调用
- (void)scrollViewDidZoom:(UIScrollView *)scrollView
//核心代码:设置内容大小    contentSize.height == 0
设置为0仅仅代表竖直方向上不能滚动
self.scrollView.contentSize = CGSizeMake(count * w, 0);      分页    每一页的尺寸都是跟scrollView的frame.size一样的
self.scrollView.pagingEnabled = YES;
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
--四舍五入为整数的方法-- (int)(小数 + 0.5)    0.3 -> (int)(0.3 + 0.5) == 0    0.4 -> (int)(0.4 + 0.5) == 0    1.5 -> (int)(1.5 + 0.5) == 2    0.7 -> (int)(0.7 + 0.5) == 1
小数加上0.5再转换成整数,这样页码显示的就是正确的页数(仔细想)
int page = (int)(scrollView.contentOffset.x / scrollView.frame.size.width + 0.5);
self.pageControl.currentPage = page;
} 
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView {
int page = scrollView.contentOffset.x / scrollView.frame.size.width; 
self.pageControl.currentPage = page;
}
```

### 分页

>   只要将UIScrollView的pageEnabled属性设置为YES，UIScrollView会被分割成多个独立页面，里面的内容就能进行分页展示
>
>   一般会配合UIPageControl增强分页效果，UIPageControl常用属性如下 

```objective-c
//一共有多少页
@property(nonatomic) NSInteger numberOfPages; 
//当前显示第几页
@property(nonatomic) NSIntegercurrentPage;  
//只有一页时，是否需要隐藏页码指示器
@property(nonatomic) BOOL hidesForSinglePage;  
//其他页码指示器的颜色
@property(nonatomic,retain) UIColor *pageIndicatorTintColor; 
//当前页码指示器的颜色
@property(nonatomic,retain) UIColor *currentPageIndicatorTintColor; 
```

### NSTimer

>   注意:(它现在是弱指针,NSTimer的内部会有一个强指针引用着它)NSTimer叫做“定时器”，它的作用如下在指定的时间执行指定的任务每隔一段时间执行指定的任务调用下面的方法就会开启一个定时任务
>
>   + (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)ti target:(id)aTarget selector:(SEL)aSelector userInfo:(id)userInfo repeats:(BOOL)yesOrNo; 每隔ti秒，调用一次aTarget的aSelector方法，yes Or No决定了是否重复执行这个任务 通过invalidate方法可以停止定时器的工作，一旦定时器被停止了，就不能再次执行任务。只能再创建一个新的定时器才能执行新的任务

```objective-c
//停止定时器:
- (void)invalidate; 
//当用户即将开始拖拽scrollView时，停止定时器 
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView 
{
    [self stopTimer];//  这个stopTimer方法是自己定义的,并不是系统的! 
}
 //当用户已经结束拖拽scrollView时，开启定时器
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate 
{
    [self startTimer]; //这个startTimer方法是自己定义的,并不是系统的! 
}
    //返回一个自动开始执行任务的定时器 
    self.timer = [NSTimer scheduledTimerWithTimeInterval:2.0 target:self selector:@selector(nextPage:) userInfo:@"123" repeats:YES]; 
    
     //修改NSTimer在NSRunLoop中的模式：NSRunLoopCommonModes 
     //主线程不管在处理什么操作，都会抽时间处理NSTimer 
    [[NSRunLoop mainRunLoop] addTimer:self.timer forMode:NSRunLoopCommonModes]; 
```

###  总结：

 

```objective-c
 1. 常见属性   设置只能在一个方向上滚动
   self.scrollView.directionalLockEnabled = YES;
//内容在scrollView四周的弹簧效果
  self.scrollView.bounces = NO;
//实现下拉刷新    不管有没有contentSize 都可以水平 垂直方向的拉
self.scrollView.alwaysBounceVertical = YES;
self.scrollView.alwaysBounceHorizontal = YES;
//分页效果   
self.scrollView.pagingEnabled = YES;
UIButton *btn ;    
btn.enabled = NO; 
btn.userInteractionEnabled = NO;
//设置scrollView能不能滚动
self.scrollView.scrollEnabled = NO; 
//任何继承自UIView的子类都能使用以下属性设置与用户是否能交互 
self.scrollView.userInteractionEnabled = NO;
//滚动条在scrollView的subview中
NSLog(@"------subviews = %@",self.scrollView.subviews);   //设置水平方向的滚动条  
self.scrollView.showsHorizontalScrollIndicator = NO;   self.scrollView.showsVerticalScrollIndicator = NO; 
//subviews中没有滚动条
NSLog(@"----2--subviews = %@",self.scrollView.subviews); 
//滚动条的样式
self.scrollView.indicatorStyle = UIScrollViewIndicatorStyleBlack;
//惯性停下来的速度 靠自己的手验证 
self.scrollView.decelerationRate = 0.1f; 
//2. 重点常见属性    设置内容尺寸
self.scrollView.contentSize = imageView.image.size;
//设置内容的偏移量   
self.scrollView.contentOffset = CGPointMake(200, 200);    //内边距   
self.scrollView.contentInset = UIEdgeInsetsMake(20, 30, 40, 50);
3.delegate 代理(assign 弱引用) 
```

>    面试题:代理的好处:可以实现监听的思想,假如让一个对象A监听另一个对象B的状态,这个时候可以用代理通知的思想:一个对象B的状态发生了改变(做了某些事情),想通知另一个对象A,这就是通知   1. 遵守协议   2. 成为代理   

3.  实现代理方法    一般: 代理的名称一般是控件的名称 + Delegate       UITextFieldDelegate,UITableViewDelegate,UIScrollViewDelegate,UIAlertViewDelegate   

4.    代理需要实现的的方法一般都是代理名称 - UI - delegate 

    ```objective-c
    - (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string;
    - (void)tableView:(UITableView *)tableView didDeselectRowAtIndexPath:(NSIndexPath *)indexPath; 
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex; 
    ```

    **成为代理**

    一般需要在A类中写代理方法,那么就让A类成为代理

    遵守协议 - 写在.h文件中(如果是需要当前控制器,那么就.m中遵守协议就可以了) 

    实现(你需要和必须实现的)代理方法

     成为代理 **scrollView.delegate(scrollView 的属性) = self** 

    图片的缩放

    .遵守协议  

    成为代理   

    实现代理方法

    4.  在- (UIView *)viewForZoomingInScrollView:(UIScrollView *)scrollView 返回一个需要缩放的控件

    5.  设置scrollView的最大和最小缩放比例 

    6.  用到的代理方法       以下方法中 view 就是正在缩放的控件             scale 缩放比例 

        ```objective-c
        -(void)scrollViewWillBeginZooming:(UIScrollView *)scrollView withView:(UIView *)view 
        -(void)scrollViewWillBeginZooming:(UIScrollView *)scrollView withView:(UIView *)view缩放前
        ```

        :        ![img](https://uploader.shimo.im/f/hyaLtzQbgcMwQHqL.png!thumbnail)     

        放后:        ![img](https://uploader.shimo.im/f/z6DkwmVPKRfuoqFA.png!thumbnail)    

### UITextField 的代理方法 

#### 常见设置

数字键盘->textField.keyboardType = UIKeyboardTypeNumberPad; 

改变键盘的右下角return 按钮的名称->textField.returnKeyType = UIReturnKeyDone; 

#### 代理方法 

```objective-c
 //textField : A输入框发送改变 就把A输入框传入代理方法中      range : 其实就是光标的位置 长度就是代表需要替换哪些字符串      string : 输入的是什么就是什么

-(BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string
```



### 图片轮播器

1.  往scrollView 中添加imageView (5个),imageView的尺寸保持根scrollView的尺寸一致 
2.  设置分页 pagingEnabled = YES
3.   pageControl 在storyboard中 根 scrollView 同级   
4.  设置pageControl的一些属性  numberOfPages currentPage...
5.   在scrollViewDidScroll代理方法中设置currentPage     使用x偏移量 / scrollView的宽度 + 0.5 "记得打印偏移量"
6.  nextPage
           1. 获取pageControl的当前页 + 1 (跳到下一个) = index 
           2.  判断index 是不是为 5 (如果是 index = 0)(循环切换)   
           3.  设置偏移量(使用偏移量切换到下一个imageView)       index *width(scrollView) 

### 定时器 

   1. performSector

   2. NSTimer

      ```objective-c
       self.timer = [NSTimer scheduledTimerWithTimeInterval:2.0f target:self selector:@selector(nextPage:) userInfo:@"ceshi" repeats:YES]; 
      ```

      ​      timer是一次性 , 用了就不能再用了

3.  添加timer到主线程

​       

```objective-c
[[NSRunLoop mainRunLoop] addTimer:self.timer forMode:NSRunLoopCommonModes];
```

效果展示:         ![img](https://uploader.shimo.im/f/indwNO4H9aT95TP9.png!thumbnail)               ![img](https://uploader.shimo.im/f/6TZfOVGAcPLVK6lR.png!thumbnail)    

7.  #### 封装图片轮播器

1.  类方法加载xib
2.   先把 awakeFromNib 
3.  pageControl的一次性操作
4.   重写图片数组set方法
5.  将控制器中viewDidLoad里面所有的代码除了 pageControl的一次性操作   
6.  copy控制器中所有的方法
7.  将报错的地方补上
8.  使用scrollView做购物车
9.  设置contentSize     拿出scrollView中的最后一个控件     算出这个空间的bottom = (控件的高 + 控件的y)     bottom 就是contentSize 的height
10.  设置contentOffset     首先判断最后一个控件的bottom 是不是比 scrollView的高度大     如果大 设置偏移量height 就 = bottom 