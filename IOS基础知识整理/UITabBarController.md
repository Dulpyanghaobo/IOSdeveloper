# UITabBarController

​        ![img](https://uploader.shimo.im/f/1i1lDukEHWcN9AVq.png!thumbnail)      

## 自定义UITabBarController和TabBar

如何自定义TabBar

首先要自定义UITabBarController：新建一个类，继承自UITabBarController

新建一个类，继承自UIView，用来做TabBar，封装内部的按钮

在自定义的UITabBarController中创建自定义的TabBar，添加到默认的UITabBar上面

## 导航栏主题

为了保证整个项目的导航栏样式一样，可以统一设置导航栏的主题

主要是取得导航栏的appearance对象，操作它就设置导航栏的主题

```
UINavigationBar *navBar = [UINavigationBar appearance]; 
```

常用主题设置

导航栏背景

```
- (void)setBackgroundImage:(UIImage *)backgroundImage forBarMetrics:(UIBarMetrics)barMetrics; 
```

标题

```
@property(nonatomic,copy) NSDictionary *titleTextAttributes; 
```

 字典中能用到的key在UIStringDrawing.h中 

最新版本的key在UIKit框架的NSAttributedString.h中

iOS7返回按钮的箭头样式

```
@property(nonatomic,retain) UIColor *tintColor; 
```

## 导航栏按钮主题

```objective-c
//导航栏的左上角和右上角都是UIBarButtonItem对象，为了统一样式，也可以设置的
//UIBarButtonItem主题
UIBarButtonItem *item = [UIBarButtonItem appearance]; 
 
//设置主题的方法
//背景
- (void)setBackgroundImage:(UIImage *)backgroundImage forState:(UIControlState)state barMetrics:(UIBarMetrics)barMetrics; 
 
//文字
- (void)setTitleTextAttributes:(NSDictionary *)attributes forState:(UIControlState)state; 
 
//导航栏返回按钮背景
- (void)setBackButtonBackgroundImage:(UIImage *)backgroundImage forState:(UIControlState)state barMetrics:(UIBarMetrics)barMetrics; 
```

## 导航栏背景的出图规格

```
iOS6导航栏背景的出图规格
非retina：320x44 px 
retina：640x88 px 
 
iOS7导航栏背景的出图规格
retina：640x128 px 
```

## 自定义导航控制器

```
自定义导航控制器的步骤：新建一个类，继承自UINavigationController
 
自定义导航控制器的价值
重写push方法就可以拦截所有压入栈中的子控制器，统一做一些处理
- (void)pushViewController:(UIViewController *)viewController animated:(BOOL)animated; 
 
重写pop方法就可以拦截所有子控制器的出栈
- (UIViewController *)popViewControllerAnimated:(BOOL)animated; 
```

## 琐碎知识点

为了在push控制器时隐藏UITabBar，需要做以下设置

```
viewController.hidesBottomBarWhenPushed = YES; 
```

initailize、load方法的区别

initailize、load都是类方法

当一个类被装载进内存时，就会调用一次load方法（当时这个类还不可用）

当第一次使用这个类时，就会调用一次initailize方法

## 状态栏的设置

​        ![img](https://uploader.shimo.im/f/DuQttfQuLi8cjBqU.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/ks6UQbr02LiD7XrS.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/DgOephGftEva54eF.png!thumbnail)      