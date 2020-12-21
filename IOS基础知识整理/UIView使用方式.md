# UIView使用方式

​        ![img](https://uploader.shimo.im/f/iXi7EdxVvVMG6KnL.png!thumbnail)      

>   注意:当你发现通过代码无法修改控件的位置或者尺寸的时候,应该去掉storyboard里面的autolayout功能,
>
>   代码会覆盖掉在storyboard当中设置的重复属性

​        ![img](https://uploader.shimo.im/f/pwcnpa6mml3AVWj9.png!thumbnail)      

>   常见设置autolayout的方式是通过masonry布局进行设置

## 如何修改控件状态

如何修改控件的状态呢？方法很简单：

每一个UI控件都是一个对象修改UI控件的状态，其实就是修改控件对象的属性

比如修改UILabel显示的文字，

就修改UILabel对象的text属性即可比如修改UIImageView显示的图片，

就修改UIImageView对象的image属性即可不难想到，

每一个UI控件肯定都有很多属性，

比如：UIProgressView进度条控件有progress属性（进度值）UILabel和UITextField都有text属性（显示文字）……

虽然，每一个UI控件都有自己的独特属性，但是有些属性是每个UI控件都具备的，比如每一个UI控件都有自己的位置和尺寸、都有自己的父控件、子控件。

于是，所有的UI控件最终都继承自UIView，UI控件的公共属性都定义在UIView中，比如：frame ：位置和尺寸 center ：中心点位置 

……



## UIView的常见属性

```objective-c
@property(nonatomic,readonly) UIView *superview;//获得自己的父控件对象 
@property(nonatomic,readonly,copy) NSArray *subviews;//获得自己的所有子控件对象  
@property(nonatomic) NSInteger tag; //控件的ID(标识)，父控件可以通过tag来找到对应的子控件 
@property(nonatomic) CGAffineTransformtransform; //控件的形变属性(可以设置旋转角度、比例缩放、平移等属性)
@property(nonatomic) CGRect frame;//控件所在矩形框在父控件中的位置和尺寸(以父控件的左上角为坐标原点)可以定义控件的位置(origin)和大小(size)
@property(nonatomic) CGRect bounds; //控件所在矩形框的位置和尺寸(以自己左上角为坐标原点，所以bounds的x、y一般为0)注意:如果没有设置位置的情况下,把自己的左上角放在控制器的中心点可以定义控件的大小W H(size)
@property(nonatomic) CGPoint center; //控件中点的位置(以父控件的左上角为坐标原点)可以定义控件的位置X Y(center)
```



​        ![img](https://uploader.shimo.im/f/1Yny4KQTQn8y7UHA.png!thumbnail)      



## UIView的常见方法

```objective-c
- (void)addSubview:(UIView *)view; //添加一个子控件view

-(void)removeFromSuperview;//从父控件中移除

-(UIView *)viewWithTag:(NSInteger)tag; //根据一个tag标识找出对应的控件（一般都是子控件）
```

​        ![img](https://uploader.shimo.im/f/Ba181upXptSgERn9.png!thumbnail)      

>   总结：
>
>   1.监听:监视我们的触摸按下(点击)哪个按钮(可以被点击的东西)事件(系统调用的是那个方法)
>
>   2.storyboard : 描述软件界面的, 目前理解: 应用程序一启动就会加载这个文件
>
>    3.文字 是使用UILabel 按钮使用 UIButton 创建对象 
>
>    3.控制器 : 控制器就是继承自UIViewController  控制器就是管理"当前的软件界面"的所有事件(点击,拖拽)  
>
>   4.IBAction 返回值相当于void  能让storyboard中的控件跟(方法)代码进行连线 
>
>   5.  连线:   why:当按钮需要"控制器"处理某些事件的时候,那么这个处理就应该交给控制器的方法来做  what:只有在方法返回类型是IBAction的情况下,才能连线  how:首先 方法的返回值必须是IBAction 按xcode上右上角的双环按钮,直接脱线 
>   6.   常见错误   reason: '[<ViewController 0x7fc07ae36380> setValue:forUndefinedKey:]: this class is not keyvalue coding-compliant for the key label.'  这个情况一般是: 属性(label)之间连线,属性不存在,但是线还在    其他情况 kvc:   reason: '-[ViewController blueClick]: unrecognized selector sent to instance 0x7ffd63828e90'  找不到按钮的方法(不可识别的方法)(blueClick)  
>   7.  拷贝的时候会直接把线也一起copy(注意点) 
>   8.   分类和类扩展   类扩展:可以扩展属性,成员变量,方法,一般情况下属性都是放在类扩展内,苹果官方也是这么建议的(没必要让外界知道封装性)  分类:只能扩展方法
>   9.    storyboard 应用程序已启动就会加载main.storyboard , 创建箭头指向的控制器,初始化控制器内部的所有UI(UI   进阶会讲app的启动原理)   注意点:类型不匹配不能连线 
>   10.  superview: 获取当前的控件的父控件 (获取到调用者(谁调用的谁就是调用者)父控件)   self.btn.superview  self.btn 就是调用者  
>   11.   subviews :获取当前控件的所有子控件, 返回一个装满控件的数组
>   12.   获取调用者的所有子空间        self.purpleView.subviews  self.purpleView 就是调用者 
>   13.  addSubView: 添加一个子控件,添加到调用者身上(谁调用这个方法那么谁就是调用者)  
>   14.  removeFromSuperview:从父控件把当前(调用者)控件移除[xiaojian removeFromSuperview][liuqi addSubView:xiaojian] 
>   15.   viewWithTag: 根据调用内部子空间的tag找出对应的子控件         1. 直接子控件         2. 按顺序找子空间内部的所有自控,然后找后面自控件的所有子控件
>   16.  frame 既能设置尺寸也能设置位置   设置控件在父控件中的尺寸和位置 
>   17.   bounds 只能设置尺寸   设置控件在父控件中的尺寸 如果没有位置的话,那么使用自己的中心放在左上角 
>   18.  center 只能设置位置   设置控件的中心点在父控件中的位置
>   19.   OC 不允许直接修改对象的 结构体的 成员的 属性   解决的方案  command + ]     来一个临时的frame 存储 对象的frame     CGRect tempFrame = self.myBtn.frame;     直接改变临时的frame     tempFrame.origin.y -= 10;    赋值给对象的frame     self.myBtn.frame = tempFrame; 

##   layoutSubviews   

1.  一般都是设置子控件的frame  
2.  只要父控件的frame发生改变就会调用
3.   在initWithFrame之后也会默认调用一次这个方法(尝试着给子控件赋值) 