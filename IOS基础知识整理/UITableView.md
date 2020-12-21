# UITableView

### 掌握

-   设置UITableView的dataSource、delegate
-   UITableView多组数据和单组数据的展示
-   UITableViewCell的常见属性
-   UITableView的性能优化（cell的循环利用）
-   自定义Cell->什么是UITableView? 能看到各式各样的表格数据就是UITableView        

### ![img](https://uploader.shimo.im/f/KexbB2yS0A0n10Pb.png!thumbnail)              ![img](https://uploader.shimo.im/f/zI0MAyCYP1UX0GCg.png!thumbnail)      

### 如何展示数据

UITableView需要一个数据源(dataSource)来显示数据

>   注意:但是如果删掉原先控制器自动配置的View,用自己定义的UITableView,系统是默认遵守数据源和协议,不用设置!

 UITableView会向数据源查询一共有多少行数据以及每一行显示什么数据等 没有设置数据源的UITableView只是个空壳凡是遵守UITableViewDataSource协议的OC对象，都可以是UITableView的数据源 

```objective-c
/**   * dataSource是UITableViewDataSource类型，主要为UITableView提供显示用的数据(UITableViewCell)，指定UITableViewCell支持的编辑操作类型(insert，delete和reordering)，并根据用户的操作进行相应的数据更新操作，如果数据没有更具操作进行正确的更新，可能会导致显示异常，甚至crush。     delegate是UITableViewDelegate类型，主要提供一些可选的方法，用来控制tableView的选择、指定section的头和尾的显示以及协助完成cell的删除和排序等功能。     提到UITableView，就必须的说一说NSIndexPath。UITableView声明了一个NSIndexPath的类别，主要用来标识当前cell的在tableView中的位置，该类别有section和row两个属性，前者标识当前cell处于第几个section中，后者代表在该section中的第几行。    UITableView只能有一列数据(cell)，且只支持纵向滑动，当创建好的tablView第一次显示的时候，我们需要调用其reloadData方法，强制刷新一次，从而使tableView的数据更新到最新状态。   */    
```

​    ![img](https://uploader.shimo.im/f/NiDVR5Oois74voSy.png!thumbnail)      

```objective-c
//tableView展示数据的过程调用数据源的下面方法得知一共有多少组数据

-(NSInteger)numberOfSectionsInTableView:(UITableView *)tableView; 
// 调用数据源的下面方法得知每一组有多少行数据
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;
// 调用数据源的下面方法得知每一行显示什么内容
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;  
```



​       ![img](https://uploader.shimo.im/f/Mwq6yfKAD0VjMCy4.png!thumbnail)       

### 初始MVC

>   MVC是一种设计思想，贯穿于整个iOS开发中，需要积累一定的项目经验，才能深刻体会其中的含义和好处

MVC中的三个角色M：

Model，模型数据

V：View，视图（界面）

C：Control，控制中心

>   注意:首先控制器会加载这个数据,然后根据这个数据的个数去创建对应的view 

MVC的几个明显的特征和体现：View上面显示什么东西，取决于Model

只要Model数据改了，View的显示状态会跟着更改

Control负责初始化Model，并将Model传递给View去解析展示

### Cell简介

>   以及属性UITableView的每一行都是一个UITableViewCell，通过dataSource的tableView:cellForRowAtIndexPath:(此方法就是UITableViewCell重用机制的一个初始化Cell的一个系统提供的一个方法)方法来初始化每一行UITableViewCell内部有个默认的子视图:contentView，contentView是UITableViewCell所显示内容的父视图，可显示一些辅助指示视图accessoryType属性:

```objective-c
//辅助指示视图的作用是显示一个表示动作的图标，可以通过设置UITableViewCell的accessoryType(其属性)来显示，默认是UITableViewCellAccessoryNone(不显示辅助指示视图)，其他值如下:属性accessoryType就是设置指示器,也就是手机右边的一个箭头告诉用户有的是可以点击的,以下皆是其枚举值
UITableViewCellAccessoryDisclosureIndicator    箭头 
UITableViewCellAccessoryDetailDisclosureButton 按钮 + 箭头 
UITableViewCellAccessoryCheckmark            对勾 
UITableViewCellAccessoryNone                    没有 
UITableViewCellAccessoryDetaiButton              叹号按钮 
```

### Cell的常见属性 

```objective-c
设置cell的背景颜色 backgroundColor优先级低于backgroundView backgroundView使用方法:
//创建一个View
UIView *bg = [[UIView alloc] init];  //设置view的颜色,然后简介的给cell赋值
bg.backgroundColor = [UIColor redColor];
//再设置cell背景的颜色
cell.backgroundView = bg;
selectedBackgroundView 
//使用方法:  创建一个View(也可以设置UIImageView作为cell的背景颜色)  
UIView *selectedView = [[UIView alloc] init]; 
//设置view的颜色,然后间接的赋值给cell 
selectedView.backgroundColor = [UIColor redColor];
//还可以通过cell的accessoryView属性来自定义辅助指示视图（比如往右边放一个开关）注意:这里的cell的指的是通过UITableViewCell创建的一个对象的accessoryView属性来自定义辅助的视图
Cell.accessoryView = UITableViewCellAccessoryCheckmark; 
cell.accessoryView = [UIbutton alloc]init];(可以在右边自定义控件可以放一个按钮或者一个开关)     
```

​     ![img](https://uploader.shimo.im/f/q608p1vhkGEtWSpi.png!thumbnail)               ![img](https://uploader.shimo.im/f/s87lZJuiVQSpo6Sl.png!thumbnail)      注意:以上cell的属性包含了4个属性

### Cell的重用原理

>   iOS设备的内存有限，如果用UITableView显示成千上万条数据，就需要成千上万个UITableViewCell对象的话，那将会耗尽iOS设备的内存。要解决该问题，需要重用UITableViewCell对象

### 重用原理：

-   当滚动列表时，部分UITableViewCell会移出窗口，UITableView会将窗口外的UITableViewCell放入一个对象池中，等待重用。
-   当UITableView要求dataSource返回UITableViewCell时，dataSource会先查看这个对象池，如果池中有未使用的UITableViewCell，dataSource会用新的数据配置这个UITableViewCell，然后返回给UITableView，重新显示到窗口中，从而避免创建新对象
-   还有一个非常重要的问题：有时候需要自定义UITableViewCell(用一个子类继承UITableViewCell)，而且每一行用的不一定是同一种UITableViewCell，所以一个UITableView可能拥有不同类型的UITableViewCell，对象池中也会有很多不同类型的UITableViewCell，那么UITableView在重用UITableViewCell时可能会得到错误类型的UITableViewCell

### 解决方案：

-   UITableViewCell有个NSString *reuseIdentifier属性，可以在初始化UITableViewCell的时候传入一个特定的字符串标识来设置reuseIdentifier(一般用UITableViewCell的类名)。
-   当UITableView要求dataSource返回UITableViewCell时，先通过一个字符串标识到对象池中查找对应类型的UITableViewCell对象，
    -   如果有，就重用，
    -   如果没有，就传入这个字符串标识来初始化一个UITableViewCell对象

**Cell的重用代码**

```objective-c
-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

// 注意:重用机制是根据相同的标识符来重用cell,标识符不同的cell不能彼此重用 

//1.定义一个cell的标识为ID

   static NSString *ID = @"mjcell"; 

//     2.从缓存池中取出cell 

   UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID];

//      3.如果缓存池中没有cell 

   if (cell == nil) {  

   cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier:ID];  

 }    

// 4.设置cell的属性... 

return cell;

 }

方法2:注册 另一中性能优化的写法:

 0.先确定cell的重用标识

1.注册带重用标识的cell 

 2.从缓存池中取是否有带重用标识的cell(如果没有,系统会根据注册自动创建一个相应的cell返回给我们) 

3.覆盖cell上面的数据

说明:当cell离开屏幕的时候,就会放到tableView的缓存池中,这时候缓存池才有数据

3.0 先确定cell的重用标识:

命名规范:cell类型+ID 

static NSString *ID = @"carID"; // static修饰局部变量,局部变量从执行后始终存在, 但不能被其它函数使用, 当再次进入该函数时, 将保存上次的结果。其它与局部变量一样.

static BOOL isReged = NO; 

3.1 先注册带ID的cell

if (isReged == NO) { 

//warning registerCell需要注意的3点,面试可能会问到  

//该方法是伴随着UICollectionView出现的,也就是iOS6出现的

//采用注册创建出来的cell,默认是Default样式,所以一般注册大部分都用在自定义cell的时候

//需要注意的是

 [tableView registerNib:<#(UINib *)#> forCellReuseIdentifier:<#(NSString *)#>] 是iOS5出现的  编码规范:xib自定义cell一般用注册          [tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:ID];          isReged = YES; 

 }  

//3.2 查看缓存池中是否有带重用标识的cell 

//缓存池方法中封装了,如果缓存池中没有,就根据注册创建新的cell,然后返回给我们一个带ID的cell

UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID];

//3.3 覆盖cell上的数据

 XMGCar *car = self.cars[indexPath.row];             cell.textLabel.text = [NSString... 

 //3.4返回cell 
                                                                            return cell;  

  }
```

>   #### 方法3:storyboard 
>
>   ​         另一中性能优化的写法(storyboard): 
>
>   ​         0.先确定cell的重用标识
>
>   ​         1.将storyboard中的tableView中的cell的重用标志赋值  
>
>   ​       2.从缓存池中取是否有带重用标识的cell 
>
>   ​        (2.1 如果没有,系统会根据注册自动创建一个相应的cell返回给我们;          2.2 如果也没有注册过,系统会根据storyboard中写好的带重用标志的cell来自动创建,然后返回)         
>
>    3.覆盖cell上面的数据 
>
>   ​        说明:当cell离开屏幕的时候,就会放到tableView的缓存池中,这时候缓存池才有数据 
>
>    3.返回每行内容:该方法,只有当cell显示在屏幕上的时候才会调用,是一种懒加载的思想 warning 如果有注册,就不会触发storyboard的创建新cell的机制.只有没有注册过,并且storyboard中没有标记相应cell 的时候,dequeueReusableCellWithIdentifier才会返回nil 

```objective-c
-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath    {
//3.0 先确定cell的重用标识:命名规范:数据的模型+ID 
static NSString *ID = @"carID";
//static修饰局部变量,局部变量只会分配一次内存地址
//3.1 查看缓存池中是否有带重用标识的cell
//缓存池方法中封装了,如果缓存池中没有,就根据注册创建新的cell,然后返回给我们一个带ID的cell
//后面还封装了一层,如果也没有注册呢,会根据storyboard中是否标记了在重用cell,来创建一个新的cell然后返回
UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID]; 
//3.3 覆盖cell上的数据
XMGCar *car = self.cars[indexPath.row];         cell.textLabel.text = [N...           return cell; 
} 
//3.修改控件属性(一半在cell的创建方法中设置initWithStyle) 
cell.imageView.layer.cornerRadius = 22; 
//设置圆角为正方形边长一半(内切圆半径)   cell.imageView.layer.masksToBounds = YES; 
//设置将位于imageView之下的layer都遮盖住(默认是NO) 等效代码是:
cell.imageView.clipsToBounds
//设置字体以及文字颜色
cell.textLabel.textColor = [UIColor brownColor];  
cell.detailTextLabel.font = [UIFont systemFontOfSize:12.0];   
cell.detailTextLabel.textColor = [UIColor grayColor];
```

//设置辅助视图

   cell.accessoryType = UITableViewCellAccessoryDetailButton; 

**使用xib封装一个view的步骤**

-   新建一个xib文件描述一个view的内部结构(假设叫做MJTgCell.xib)
-   新建一个自定义的类
-   (自定义类需要继承自系统自带的view, 继承自哪个类,  取决于xib根对象的Class) 新建类的类名最好跟xib的文件名保持一致(比如类名就叫做MJTgCell)将xib中的控件 和 自定义类的.m文件 进行连
-   提供一个类方法返回一个创建好的自定义view(屏蔽从xib加载的过程)
-   提供一个模型属性让外界传递模型数据
-   重写模型属性的setter方法,在这里将模型数据展示到对应的子控件上面

### Delegate的使用场合

对象A内部发生了一些事情,想通知对象B

对象B想监听对象A内部发生了什么事情

 对象A想在自己的方法内部调用对象B的某个方法,并且对象A不能对对象B有耦合依赖

对象A想传递数据给对象B

……

 以上情况,结果都一样:对象B是对象A的代理(delegate)

### 使用delegate的步骤

先搞清楚谁是谁的代理(delegate)

定义代理协议,协议名称的命名规范:控件类名 + Delegate

定义代理方法

代理方法一般都定义为@optional

代理方法名都以控件名开头

代理方法至少有1个参数,将控件本身传递出去

设置代理(delegate)对象 (比如myView.delegate = xxxx;) 

代理对象遵守协议

代理对象实现协议里面该实现的方法

 在恰当的时刻调用代理对象(delegate)的代理方法,通知代理发生了什么事情(在调用之前判断代理是否实现了该代理方法)

### 通过代码自定义cell(cell的高度不一致)

1.新建一个继承自UITableViewCell的类

>   注意:一个是初始化方法 一个设置子控件的方法一个是设置数据的对象的set方法  
>

2.重写initWithStyle:reuseIdentifier:方法

>   添加所有需要显示的子控件(不需要设置子控件的数据和frame, 子控件要添加到contentView中

进行子控件一次性的属性设置(有些属性只需要设置一次, 比如字体\固定的图片等等但是设置frame不行)  

3.提供2个模型数据模型: 存放文字数据\图片数据 (字典转模) frame模型: 存放数据模型\所有子控件的frame\cell的高度

4.cell拥有一个frame模型(不要直接拥有数据模型)

5.重写frame模型属性的setter方法: 在这个方法中设置子控件的显示数据和frame

6.frame模型数据的初始化已经采取懒加载的方式(每一个cell对应的frame模型数据只加载一次)

### UITextField

通过UITextField的代理方法能够监听键盘最右下角按钮的点击

成为UITextField的代理

```objective-c
self.textField.delegate = self;

//遵守UITextFieldDelegate协议,实现代理方法
- (BOOL)textFieldShouldReturn:(UITextField *)textField;
//在UITextField左边放一个view
self.textField.leftView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 8, 0)]; self.textField.leftViewMode = UITextFieldViewModeAlways;  ***************************笔记********************************************************************************tableView常见属性   修改cell的高度,也就是行高
self.tableView.rowHeight = 60;
//修改组头.组尾的高度(Plain样式下生效): 在Group样式下,组头组尾高度都是系统默认的
self.tableView.sectionHeaderHeight = 2;   self.tableView.sectionFooterHeight = 222;
//设置tableView的表头表尾视图    一般用于做下拉刷新,banner 
self.tableView.tableHeaderView = [UIButton buttonWithType:UIButtonTypeContactAdd]; 
self.tableView.tableFooterView = [[UISwitch alloc] init]; 
设置分割线颜色:
[UIColor clearColor]//如果设置成透明色就表示分割线取消显示 warning 在tableView上,尽量避免使用透明色
self.tableView.separatorColor = [UIColor orangeColor]; 
UITableViewCellSeparatorStyleNone 取消分割线的显示 warning UITableViewCellSeparatorStyleNone->UITableViewCellSelectionStyleNone  
self.tableView.separatorStyle = UITableViewCellSeparatorStyleNone;
//隐藏状态栏
- (BOOL)prefersStatusBarHidden{  return YES; }
//设置cell的属性
//设置cell的背景颜色 backgroundColor优先级低于backgroundView 
cell.backgroundColor = [UIColor colorWithRed:arc4random_uniform(255)/255.0 green:arc4random_uniform(255)/255.0 blue:arc4random_uniform(255)/255.0 alpha:1];     UIView *bg = [[UIView alloc] init];
bg.backgroundColor = [UIColor grayColor];   cell.backgroundView= bg;
//一般用于UIIMageView展示公司logo
typedef NS_ENUM(NSInteger, UITableViewCellAccessoryType){  UITableViewCellAccessoryNone, 
//don't show any accessory view 默认没有
UITableViewCellAccessoryDisclosureIndicator,
//regular chevron. doesn't track 尖尖   UITableViewCellAccessoryDetailDisclosureButton,
//info button w/ chevron. tracks 圈! + 尖尖 
UITableViewCellAccessoryCheckmark,
//checkmark. doesn't track 对号    
UITableViewCellAccessoryDetailButton NS_ENUM_AVAILABLE_IOS(7_0) // info button. tracks 圈!        设置辅助视图    accessoryView优先级高
NSUInteger row = arc4random_uniform(255);
if (row % 2) {
cell.accessoryView = [[UISwitch alloc] init];
}else  { 
cell.accessoryType = UITableViewCellAccessoryDetailDisclosureButton; 
} 
//tableView如何展示数据?
mutiSectionsTableView tableView如何展示数据?- 通过数据源 
@property (nonatomic, assign)  id <UITableViewDataSource> dataSource;

/* 设置数据源
1.在viewDidLoad方法中设置tableView.dataSource = self;
2.在storyboard中拖线 * 遵守数据源协议<UITableViewDataSource> * 实现数据源方法   多少组数据*/
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView; 
//每一组有多少行数据
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;
//每一行显示什么内容(note:必须是UITableViewCell或者其子类)
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath; 
//indexPaht.section 第几组 indexPath.row   第几行  每一组的头部section 代表行数
- (NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section;
//每一组的尾部
- (NSString *)tableView:(UITableView *)tableView titleForFooterInSection:(NSInteger)section
//为什么要数据独立? 新需求再添加一组数据时候,需要修改全部数据源方法! 思路:将所有需要修改的数据集中到一处 - 创建一个新的数组属性
@property (nonatomic, strong) NSArray *groups;
/**< group模型数组 */
//- 通过懒加载为数组赋值  懒加载
- (NSArray *)groups{
if (!_groups) { 
XMGGroup *g0 = [[XMGGroup alloc] init];
g0.header = @"adc";
g0.footer = @"物理输出脆皮";
g0.heros = @[[XMGHero heroWithName:@"艾希" Icon:@"hanbing"],
[XMGHero heroWithName:@"崔斯特娜" Icon:@"xiaopao"],  
[XMGHero heroWithName:@"金克斯" Icon:@"金克斯"]]; 
XMGGroup *g1 = [[XMGGroup alloc] init]; 
g1.header = @"apc";   
g1.footer = @"法术输出脆皮";
g1.heros = @[[XMGHero heroWithName:@"阿狸" Icon:@"ali"],  
[XMGHero heroWithName:@"火男" Icon:@"huonan"], 
[XMGHero heroWithName:@"球女" Icon:@"qiunv"]];

XMGGroup *g2 = [[XMGGroup alloc] init];
g2.header = @"上单";    
g2.footer = @"上单爸爸不好惹"; 
g2.heros = @[[XMGHero heroWithName:@"大保健" Icon:@"dabaojian"], 
];  
_groups = @[g0, g1, g2];
} 
return _groups;
} 
//这样再添加数据,只需要对懒加载中的数据进行操作即可 实际开发中,所用的数据也是从服务器拿到后展示 分析模型的创建- 组头 - 组尾 - cell上的内容(数组)* 每个cell上的内容分为name和icon * 创建hero模型  单组数据展示 
- 数据源方法:返回多少组可以不实现
- cell上自带3个展示信息的子控件 
cell.textLabel.text = [car.name](http://car.name/);
//系统自带的cell的非value2样式并且有数据才展示
cell.imageView.image = [UIImage imageNamed:car.icon];
//系统自带cell的非default样式并且有数据时候才展示
cell.detailTextLabel.text = car.money;
- 复习简单plist文件解析 
1.拿到plist的文件路径 
NSString *filePath = [[NSBundle mainBundle] 
pathForResource:@"cars.plist" ofType:nil]; 
2.创建对应的JSON数据  NSArray *dicts = [NSArray arrayWithContentsOfFile:filePath]; 
3.JSON->Model  NSMutableArray *arrayM = [NSMutableArray arrayWithCapacity:dicts.count];
for (NSDictionary *dict in dicts) {
3.1 Dict->Model 
XMGCar *obj = [XMGCar carWithDict:dict];
[arrayM addObject:obj];} - Dict->
Model
+ (instancetype)carWithDict:(NSDictionary *)dict{
XMGCar *car = [[self alloc] init];
[car.name](http://car.name/) = dict[@"name"]; 
car.icon = dict[@"icon"];  
car.money = dict[@"money"]; 
kvc 相当于上面3行
[car setValuesForKeysWithDictionary:dict];
return car;
} 
//pragma mark - cell常见属性 方法抽取的技巧就是将变量写成参数传进来 相同业务逻辑的的代码尽量抽取成方法
- (void)setupCellProperty:(UITableViewCell *)cell row:(NSInteger)row{
//cell的常见属性      设置cell的背景颜色  
cell.backgroundColor = (row % 2)? [UIColor yellowColor]: [UIColor orangeColor]; 
//设置cell的背景视图,并且backgroundView的优先级比backgroundColor高一些  
UIView *cellBg = [[UIView alloc] init]; 
cellBg.backgroundColor = [UIColor grayColor];  
cell.backgroundView = cellBg; 
/*    UITableViewCellAccessoryNone,          // don't show any accessory view  
UITableViewCellAccessoryDisclosureIndicator,  // 尖尖 regular chevron. doesn't track    UITableViewCellAccessoryDetailDisclosureButton, 
// 圈i + 尖尖info button w/ chevron. tracks      UITableViewCellAccessoryCheckmark, 
// 小对勾 checkmark. doesn't track    UITableViewCellAccessoryDetailButton 
NS_ENUM_AVAILABLE_IOS(7_0) // 圈i info button. tracks    */   cell的辅助视图样式 
cell.accessoryType = UITableViewCellAccessoryDisclosureIndicator;
//cell的辅助视图,作用优先级高于accessoryType   cell.accessoryView = [[UISwitch alloc] init];
/*    UITableViewCellSelectionStyleNone, // 设置没有选中样式  
UITableViewCellSelectionStyleBlue, // 在iOS6之前,选中背景色变蓝,iOS7之后,和gray相同     
UITableViewCellSelectionStyleGray, // 默认样式 
UITableViewCellSelectionStyleDefault 
NS_ENUM_AVAILABLE_IOS(7_0) // 默认样式    */  
cell.selectionStyle = UITableViewCellSelectionStyleGray; 
在UITableViewCellSelectionStyleNone样式中,设置selectedBackgroundView是无效的     /*    在    UITableViewCellSelectionStyleBlue,
// 在iOS6之前,选中背景色变蓝,iOS7之后,和gray相同      UITableViewCellSelectionStyleGray, 
// 默认样式 
UITableViewCellSelectionStyleDefault 
NS_ENUM_AVAILABLE_IOS(7_0) // 默认样式      3种样式下,selectedBackgroundView他的优先级高于selectionStyle   */ 
UIView *cellsBg = [[UIView alloc] init];  
cellsBg.backgroundColor = [UIColor redColor];  
cell.selectedBackgroundView = cellsBg;
/*      contentView = 0x7f96fb553470:    cell.textLabel.superview = 0x7f96fb553470   ==0x7f96fb553470   ==0x7f96fb553470   */ 
//cell上面的子控件不是直接添加在cell上,而是添加到contentView上
NSLog(@"contentView = %p: cell.textLabel.superview = %p==%p==%p", cell.contentView, cell.textLabel.superview, cell.imageView.superview, cell.detailTextLabel.superview);}
- 注意:cell上面的子控件要添加到cell.contentView上  常见代理/易错属性 - 前两者易错   选中某一行时候调用
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath{  NSLog(@"%s, row = %ld", __FUNCTION__, indexPath.row); }
//取消选中某一行时候调用
- (void)tableView:(UITableView *)tableView didDeselectRowAtIndexPath:(NSIndexPath *)indexPath{  NSLog(@"%s, row = %ld", __FUNCTION__, indexPath.row); }
//根据indexPath返回对应的高度
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath{  if (indexPath.row == 0) {     return 222;   }
return 88; }
- 分割线样式的枚举容易写错  #warning 在设置分割线样式为None的时候如果无效,先看你是否搞混了下列2个枚举 /* 
UITableViewCellSeparatorStyleNone UITableViewCellSelectionStyleNone */self.tableView.separatorStyle = UITableViewCellSeparatorStyleNone; 
cellWithTableView: cell获取封装 - 将获取cell的代码全部copy到cell内部,发现需要用到变量tableView,传入即可  - 传统封装 
+ (instancetype)cellWithTableView:(UITableView *)tableView{
1.确定重用标识
static NSString *ID = nil;
if (ID == nil) {     ID = [NSString stringWithFormat:@"%@ID", NSStringFromClass(self)];
} 
2.从缓存池中取
XMGCarCell *cell = [tableView dequeueReusableCellWithIdentifier:ID]; 
3.如果缓存池中没有,就自己创建
if (!cell) {
cell = [[XMGCarCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:ID];  }    return cell; }
- 注册封装  
+ (instancetype)cellWithTableView:(UITableView *)tableView{
//确定重用标示(规范的写法)
static NSString *ID = nil;
if (ID == nil) { 
ID = [NSString stringWithFormat:@"%@ID", NSStringFromClass(self)]; 
}     
//如果没有注册过,就注册1次
static BOOL isReged = NO;
if (isReged == NO) {
[tableView registerClass:self forCellReuseIdentifier:ID];     isReged = YES;
NSLog(@"%s, line = %d", __FUNCTION__,__LINE__);
}      
//1.缓存池    这里应该是XMGCarCell *cell,写成id 的原因是为了拖个代码块  
id cell = [tableView dequeueReusableCellWithIdentifier:ID]; 
return cell; 
}  
//数据覆盖封装 - 为cell添加模型属性
@property (nonatomic, strong) XMGCar *car; /**< carModel */
- 重写模型set方法完成内部赋值 
- (void)setCar:(XMGCar *)car{ 
_car = car;  
self.imageView.image = car.icon; 
self.textLabel.text = [car.name](http://car.name/); 
} 
//封装后创建cell的数据源方法只需要3步- 获取cell - 覆盖数据 - 返回cell
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
//3.1 获取cell
XMGCarCell *cell = [XMGCarCell cellWithTableView:tableView];
//3.2 数据覆盖    这样写的好处就是,将cell覆盖数据一行完成,具体覆盖了哪些数据,进入cell内部看  
 XMGGroup *g = self.groups[indexPath.section];
 XMGCar *car = g.cars[indexPath.row];
 cell.car = car;   
 //3.3.返回cell
 return cell; }
```

