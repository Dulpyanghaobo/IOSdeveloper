# 帧动画/plist

## 掌握

- UIImageView帧动画的使用

- UIImage的2种加载方式

- 重复代码的封装抽取

- 文档注释的写法

   UIImageView帧动画相关属性和方法

  ```objective-c
  @property(nonatomic,copy) NSArray *animationImages;// 需要播放的序列帧图片数组（里面都是UIImage对象，会按顺序显示里面的图片）
  @property(nonatomic) NSTimeInterval animationDuration; //帧动画的持续时间
  @property(nonatomic) NSInteger animationRepeatCount;// 帧动画的执行次数（默认是无限循环）
  - (void)startAnimating;//开始执行帧动画
  - (void)stopAnimating;//停止执行帧动画
  - (BOOL)isAnimating;//是否正在执行帧动画
  ```

  

  ##  UIImage的2种加载方式

  ### 方式一：有缓存（图片所占用的内存会一直停留在程序中）

  

  ```objective-c
  + (UIImage *)imageNamed:(NSString *)name; //name是图片的文件名 
  ```

  ### 方式二：无缓存（图片所占用的内存会在一些特定操作后被清除）

  ```objective-c
  + (UIImage *)imageWithContentsOfFile:(NSString *)path
    - (id)initWithContentsOfFile:(NSString *)path; //path是图片的全路径 重复代码的封装抽取当一份代码重复出现在程序的多处地方，就会造成程序又臭又长，当这份代码的结构要修改时，每一处出现这份代码的地方都得修改，导致程序的扩展性很差 因此，要将重复出现的代码抽取到某个方法中，在需要这份代码的地方调用方法即可 抽取代码的思路将相同的代码放到一个方法中将不同的值当做方法参数传进来掌握按钮的多功能使用@2x的含义应用程序图标、启动图片的添加状态栏在iOS7中，状态栏默认情况下归控制器管理，比如状态栏的样式、状态栏的是否可见控制器通过重写以下方法来控制状态栏设置状态栏的样式
  ```

  ```objective-c
  -(UIStatusBarStyle)preferredStatusBarStyle;
  //其中UIStatusBarStyleLightContent是白色样式 设置状态栏的可见性
  - (BOOL)prefersStatusBarHidden; 
  ```

  ###         ![img](https://uploader.shimo.im/f/3wFFZdHtKnJcZS0H.png!thumbnail)               ![img](https://uploader.shimo.im/f/xU7JhTp7qv0FdQ90.png!thumbnail)       

  > 应用程序启动图片一个app在启动过程中会全屏显示叫做Default.png的图片 不同规格Default的使用场合Default.png：非retina-iPhone屏幕，320x480Default@2x.png：retina-iPhone屏幕，640x960Default-568h@2x.png：4inch的retina-iPhone屏幕，640x1136Default-Portrait~ipad.png：非retain-iPad竖屏屏幕，768x1024Default-Portrait~ipad@2x.png：retain-iPad竖屏屏幕，1536x2048Default-Landscape~ipad.png：非retain-iPad横屏屏幕，1024x768Default-Landscape~ipad@2x.png：retain-iPad横屏屏幕，2048x1536UIButtonUIButton有很多种状态，它提供了一些便捷属性，可以直接获取当前状态下的文字、文字颜色、图片等

  ```objective-c
  @property(nonatomic,readonly,retain) NSString*currentTitle; @property(nonatomic,readonly,retain) UIColor *currentTitleColor;     @property(nonatomic,readonly,retain) UIImage *currentImage;       @property(nonatomic,readonly,retain) UIImage *currentBackgroundImage;       
  ```

  ![img](https://uploader.shimo.im/f/OQ4SFXYEUySjqsLX.png!thumbnail)

  >    **UIButton和UIImageView相同点** ：都能显示图片不同点UIButton默认情况就能监听点击事件，而UIImageView默认情况下不能UIButton可以在不同状态下显示不同的图片UIButton既能显示文字，又能显示图片如何选择UIButton：需要显示图片，点击图片后需要做一些特定的操作UIImageView：仅仅需要显示图片，点击图片后不需要做任何事情UIImage:父类为NSObject,不是一个控件,只是一个图片NSArray和NSDictionary的使用当图片内容非常多时，“根据index来设置内容”的代码就不具备扩展性，要经常改动 为了改变现状，可以考虑将图片数据线保存到一个数组中，数组中有序地放着很多字典，一个字典代表一张图片数据，包含了图片名、图片描述
  >
  > @property (strong, nonatomic) NSArray *images;  由于只需要初始化一次图片数据，因此放在get方法中初始化 将属性放在get方法中初始化的方式，称为“懒加载”\”延迟加载” 

  ## 什么是Plist文件：

  > 直接将数据直接写在代码里面，不是一种合理的做法。如果数据经常改，就要经常翻开对应的代码进行修改，造成代码扩展性低 因此，可以考虑将经常变的数据放在文件中进行存储，程序启动后从文件中读取最新的数据。如果要变动数据，直接修改数据文件即可，不用修改代码  
  >
  > 一般可以使用属性列表文件存储NSArray或者NSDictionary之类的数据，这种属性列表文件的扩展名是plist，因此也成为“Plist文件”   

     ![img](https://uploader.shimo.im/f/KuW7rBJ0hic9I0dU.png!thumbnail)       解析Plist文件接下来通过代码来解析Plist文件中的数据获得Plist文件的全路径

  ```objective-c
  NSBundle *bundle = [NSBundle mainBundle];
   NSString *path = [bundle pathForResource:@"imageData" ofType:@"plist"];  //加载plist文件
  images = [NSArray arrayWithContentsOfFile:path]; 
   - (NSArray *)images {  
     if (_images == nil) {    
       NSBundle *bundle = [NSBundle mainBundle];     
  NSString *path = [bundle pathForResource:@"imageData" ofType:@"plist"];     images = [NSArray arrayWithContentsOfFile:path];   
                                              }
                        return _images;
   }    
  ```

  ​     ![img](https://uploader.shimo.im/f/OQgXUx9qEaXWklM0.png!thumbnail)      