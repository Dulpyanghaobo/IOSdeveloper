# loadView

1.什么时候被调用？

每次访问UIViewController的view(比如controller.view、self.view)而且view为nil，loadView方法就会被调用。

2.有什么作用？

loadView方法是用来负责创建UIViewController的view

3.默认实现是怎样的？

默认实现即[super loadView]里面做了什么事情。

1> 它会先去查找与UIViewController相关联的xib文件，通过加载xib文件来创建UIViewController的view 

如果在初始化UIViewController指定了xib文件名，就会根据传入的xib文件名加载对应的xib文件

[[MJViewController alloc] initWithNibName:@"MJViewController" bundle:nil];

如果没有明显地传xib文件名，就会加载跟UIViewController同名的xib文件

[[MJViewController alloc] init]; // 加载MJViewController.xib 

2> 如果没有找到相关联的xib文件，就会创建一个空白的UIView，然后赋值给UIViewController的view属性，大致如下 

self.view = [[[UIView alloc] initWithFrame:[UIScreen mainScreen].applicationFrame] autorelease];

// applicationFrame的值是：{{x = 0, y = 20}, {width = 320, height = 460}}

[super loadView]里面就大致完成1>和2>中叙述的内容

4.怎样正确使用这个方法？

大家都知道UIViewController的view可以通过xib文件来创建，但是在某些情况下，xib不是那么地灵活，所以有时候我们想通过代码来创建UIView，比如什么：

self.view = [[[UIWebView alloc] initWithFrame:[UIScreen mainScreen].applicationFrame] autorelease];

 如果想通过代码来创建UIViewController的view，就要重写loadView方法，并且不需要调用[super loadView]，因为在第3点里面已经提到：若没有xib文件，[super loadView]默认会创建一个空白的UIView。我们既然要通过代码来自定义UIView，那么就没必要事先创建一个空白的UIView，以节省不必要的开销。正确的做法应该是这样：

-(void)loadView {
     self.view = [[[UIWebView alloc] initWithFrame:[UIScreen mainScreen].applicationFrame] autorelease]; 
}

不需要调用[super loadView]，你调用了也不会出错，只是造成了一些不必要的开销。

总结一句话，苹果设计这个方法就是给我们自定义UIViewController的view用的 

# viewDidLoad

1.什么时候被调用？

无论你是通过xib文件还是重写loadView方法创建UIViewController的view，在view创建完毕后，最终都会调用viewDidLoad方法

2.有什么作用？

一般我们会在这里做界面上的初始化操作，比如往view中添加一些子视图、从数据库或者网络加载模型数据装配到子视图中。例如：

```objective-c
- (void)viewDidLoad
{
			[super viewDidLoad];   
     // 添加一个按钮 
     UIButton *button = [UIButton buttonWithType:UIButtonTypeContactAdd]; 
     [button addTarget:self action:@selector(click) forControlEvents:UIControlEventTouchUpInside]; 
     [self.view addSubview:button]; 
 } 
```

## viewDidUnload

1.什么时候被调用？

iOS设备的内存是极其有限的，如果应用程序占用的内存过多的话，系统就会对应用程序发出内存警告。UIViewController就会收到didReceiveMemoryWarning消息。didReceiveMemoryWarning方法的默认实现是：如果当前UIViewController的view不在应用程序的视图层次结构(View Hierarchy)中，即view的superview为nil的时候，就会将view释放，并且调用viewDidUnload方法

2.有什么作用？

上面说到，发出内存警告且view被释放的时候就会调用viewDidUnload方法，所以一般在释放资源，主要是释放界面元素相关的资源，将相关的实例都赋值为nil

```objective-c
 - (void)viewDidUnload {
     [super viewDidUnload]; 
     self.name = nil; 
     self.pwd = nil; 
 }
```

3.dealloc也是用来释放资源的，那跟viewDidUnload有什么关系？

当发出内存警告调用viewDidUnload方法时，只是释放了view，并没有释放UIViewController，所以并不会调用dealloc方法。即viewDidUnload和dealloc方法并没有任何关系，dealloc方法只会在UIViewController被释放的时候调用

## 三个方法的关系

1.第一次访问UIViewController的view时，view为nil，然后就会调用loadView方法创建view
2.view创建完毕后会调用viewDidLoad方法进行界面元素的初始化
3.当内存警告时，系统可能会释放UIViewController的view，将view赋值为nil，并且调用viewDidUnload方法
4.当再次访问UIViewController的view时，view已经在3中被赋值为nil，所以又会调用loadView方法重新创建view
5.view被重新创建完毕后，还是会调用viewDidLoad方法进行界面元素的初始化

