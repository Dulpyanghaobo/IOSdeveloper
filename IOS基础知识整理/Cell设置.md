# 自定义cell

 自定义cell的时候,子控件应该添加在initWithStyle方法中, 子控件应该添加在contentView上 

```objective-c
- (instancetype)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier
{
    if (self = [super initWithStyle:style reuseIdentifier:reuseIdentifier]) { 
        //FunLog
         //1.图片 
        UIImageView *icon_Ima = [[UIImageView alloc] init];
        [self.contentView addSubview:icon_Ima];
        self.icon_Ima = icon_Ima; 
        
         //2.名称 
        UILabel *title_Lab = [[UILabel alloc] init];
        [self.contentView addSubview:title_Lab];
        self.title_Lab = title_Lab; 
         //3.价格 
        ...
         //4.购买人数 
        ...
    }
    return self; 
}

```

\- 布局子控件:牺牲代码量保证可读性 

布局:注意要先调用[super layoutSubviews],然后再设置子控件的frame;

```objective-c
- (void)layoutSubviews
{
    [super layoutSubviews]; 
    CGFloat margin = 10;
    
     1.布局图片:上,左,上下间距相同,宽度80 
    CGFloat iconX = margin;
    CGFloat iconY = margin;
    CGFloat iconW = 80;
    CGFloat iconH = self.bounds.size.height - 2 * iconY; 
    self.icon_Ima.frame = CGRectMake(iconX, iconY, iconW, iconH); 
    
     2.布局title:顶部对齐icon,左边距离图片10,右边距离10,高度20 
    CGFloat titleX = ;
    CGFloat titleY = ;
    CGFloat titleW = ;
    CGFloat titleH = ;
    self.title_Lab.frame = CGRectMake(titleX, titleY, titleW, titleH); 
    
     3.价格: 底部和icon对齐,左边和title对齐,宽100,高15 
    CGFloat priceX = ;
    CGFloat priceW = ;
    CGFloat priceH = ;
    CGFloat priceY = ;
    self.price_Lab.frame = CGRectMake(priceX, priceY, priceW, priceH); 
    
     4.购买人数: 底部和价格对齐,左边距离价格10,右边10,高13, 
    CGFloat buyCountX = ;
    CGFloat buyCountW = ;
    CGFloat buyCountH = ;
    CGFloat buyCountY = ;
    self.buyCount_Lab.frame = CGRectMake(buyCountX, buyCountY, buyCountW, buyCountH); 
}
```

## 基本框架搭建

\- UITableViewController用法

- 设置ViewController继承自UITableViewController 
- \#warning 别把父类改成了UITableViewController 💣 
- @interface XMGTgController : UITableViewController 
- 删除storyboard中原有的控制器
- 拖进新控制器 
- 设置新控制器class:ViewController
- 设置新控制器is initial
- 实现2个数据源方法 

```objective-c
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
     //1.获取cell 
    XMGTgCell *cell = [XMGTgCell cellWithTableView:tableView];
     //2.覆盖数据 
    cell.tg = self.tgs[indexPath.row]; 
    // 3.返回cell 
    return cell; 
}
```

##  懒加载

```objc
- (NSArray *)tgs
{
    if (!_tgs) { 
        NSString *filePath = [[NSBundle mainBundle] pathForResource:@"tgs.plist" ofType:nil]; 
        NSArray *dicts = [NSArray arrayWithContentsOfFile:filePath];
        
        NSMutableArray *arrayM = [NSMutableArray arrayWithCapacity:dicts.count];
        for (NSDictionary *dict in dicts) { 
            XMGTg *obj = [XMGTg tgWithDict:dict];
            [arrayM addObject:obj];
        }
        
        _tgs = [arrayM copy];
    }
    return _tgs; 
}
```

### **自定义view****(cell)**

```objc
/**2.自定义view(cell)
- 创建一个继承UITableViewCell的类:XMGTgCell  
- 写好封装cell的代码(传统写法) **/
+ (instancetype)cellWithTableView:(UITableView *)tableView
{
    static NSString *identifier = @"TgCellID"; 
    XMGTgCell *cell = [tableView dequeueReusableCellWithIdentifier:identifier];
    if (!cell) { 
        cell = [[self alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:identifier]; 
    }
    
    return cell; 
}
 
//- 为cell增添模型属性tg,并且重写set方法,先给系统自带的赋值  
@class XMGTg; 
@interface XMGTgCell : UITableViewCell 
 
@property (nonatomic, strong) XMGTg *tg; /**< tg模型 */ 
+ (instancetype)cellWithTableView:(UITableView *)tableView;
 
@end
 
 //此处给子控件赋值
- (void)setTg:(XMGTg *)tg
{
    _tg = tg;
#warning noCode
}
 
 
//- 重写initWithFrame方法添加子控件 
 
 
// 传统通过代码自定义子控件,要将添加子控件的代码写在这个方法中
- (instancetype)initWithFrame:(CGRect)frame
 
//- 重写layoutSubviews方法布局 
//* 注意先调用super方法 
 
 
// 布局:注意要先调用[super layoutSubviews];
- (void)layoutSubviews
{
    [super layoutSubviews]; 
}
```

```objc
//subViews添加子控件:
//- 参考storyboard/xib拖线,先写好weak属性 
//在这里模拟拖线,创建weak类型的子控件,并且在initWithStyle方法中创建添加到cell上
@property (nonatomic, weak) UIImageView *icon_Ima; /**< 图片 */ 
@property (nonatomic, weak) UILabel *title_Lab; /**< 名称 */
@property (nonatomic, weak) UILabel *price_Lab; /**< 价格 */ 
@property (nonatomic, weak) UILabel *buyCount_Lab; /**< 购买人数 */  
/*- 添加子控件 
* 自定义cell的时候,子控件应该添加在`initWithStyle`方法中 
* 子控件应该添加在`contentView`上 */

 /*自定义cell的时候,子控件应该添加在initWithStyle方法中, 子控件应该添加在contentView上 */
- (instancetype)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier
{
    if (self = [super initWithStyle:style reuseIdentifier:reuseIdentifier]) { 
        FunLog
        // 1.图片 
        UIImageView *icon_Ima = [[UIImageView alloc] init];
        [self.contentView addSubview:icon_Ima];
        self.icon_Ima = icon_Ima; 
        
         //2.名称 
        UILabel *title_Lab = [[UILabel alloc] init];
        [self.contentView addSubview:title_Lab];
        self.title_Lab = title_Lab; 
        // 3.价格 
        ...
     //    4.购买人数 
        ...
    }
    return self; 
}
 layoutSubViews布局子控件
//- 布局子控件:牺牲代码量保证可读性 
 //布局:注意要先调用[super layoutSubviews];
- (void)layoutSubviews
{
    [super layoutSubviews]; 
    CGFloat margin = 10;
    
    // 1.布局图片:上,左,上下间距相同,宽度80 
    CGFloat iconX = margin;
    CGFloat iconY = margin;
    CGFloat iconW = 80;
    CGFloat iconH = self.bounds.size.height - 2 * iconY; 
    self.icon_Ima.frame = CGRectMake(iconX, iconY, iconW, iconH); 
    
   //  2.布局title:顶部对齐icon,左边距离图片10,右边距离10,高度20 
    CGFloat titleX = iconX + iconW + margin;
    CGFloat titleY = iconY;
    CGFloat titleW = self.bounds.size.width - titleX - margin; // cell宽度 - 右边距离 - title左边距离titleX; 
    CGFloat titleH = 20;
    
    self.title_Lab.frame = CGRectMake(titleX, titleY, titleW, titleH); 
    
    // 3.价格: 底部和icon对齐,左边和title对齐,宽100,高15 
    CGFloat priceX = titleX;
    CGFloat priceW = 100;
    CGFloat priceH = 15;
    CGFloat priceY = iconY + iconH - priceH; // 底部对齐相当于priceY + priceH = iconY + iconH; 
    
    self.price_Lab.frame = CGRectMake(priceX, priceY, priceW, priceH); 
    
    // 4.购买人数: 底部和价格对齐,左边距离价格10,右边10,高13, 
    CGFloat buyCountX = priceX + priceW + margin; // priceX + priceW + 
    CGFloat buyCountW = self.bounds.size.width - 10 - buyCountX; // buyCountX + buyCountW = cellW - 10; 
    CGFloat buyCountH = 13;
    CGFloat buyCountY = priceY + priceH - buyCountH; // 底部对齐相当于 priceY + priceH = buyCountY + buyCountH; 
    
    self.buyCount_Lab.frame = CGRectMake(buyCountX, buyCountY, buyCountW, buyCountH); 
} 
 frameFun补充
// 在调用完layoutSubview,约束才会布局
// 当强制布局后,子控件的约束就变成frame了
NSLog(@"cellH = %.f", CGRectGetHeight(self.bounds));
NSLog(@"maxXoficon = %.f", CGRectGetMaxY(self.icon_Ima.frame));
```

### 等高cell--Mansonry

```objc
import "Masonry.h" 
```

\- 删除layoutSubViews代码

 - 在init方法中写mansonry代码添加约束 

### 等高cell:

等高cell-storyboard

- 步骤与xib基本相同 
- 不必用代码拿cell

```objc
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    if (indexPath.row != 0) { 
        
         1.获取cell 
        XMGTgCell *cell = [XMGTgCell cellWithTableView:tableView];
        
         2.覆盖数据 
        cell.tg = self.tgs[indexPath.row]; 
        
         3.返回cell 
        return cell; 
    }else
    {
         可以通过第0行的特殊cell来满足 / 代替 表头视图 
        return [tableView dequeueReusableCellWithIdentifier:@"testID"]; 
    }
}
```

### 静态单元格

 staticCells静态单元格 

 一般的死数据界面都可以用静态cell

- 例如:微信的发现 / 我两个界面,iPhone的 设置 界面 
- 样式都是group样式
- 调整组间距的方法有两种
  - 1.设置组头/尾 为@"         "这种空字符串
  - 2.添加没有cell的section

自定义静态cell需要注意: 

拖线需要先写代码,在通过代码拖 

也可以通过tag获取子控件操作 

[self viewWithTag:22];  

 customSeparator自定义分割线

 系统自带的分割线不能满足产品需求

-  设置cell不同的背景色 
* 自定义分割线 
- 删除原有的分割线 
- 1.设置tableView.separatorStyle为none
- 2.设置分割线颜色为clearColor(不建议cell中不要使用clearColor)
- 添加一个view,高度为1,背景色设置成分割线颜色,贴底 
-  设置分割线颜色为透明色
- warning 在cell中,尽量不要使用透明色 

self.tableView.separatorColor = [UIColor clearColor];

设置分割线样式为None
self.tableView.separatorStyle = UITableViewCellSeparatorStyleNone;
另一种自定义分割线的方法 
设置好子控件以后会自动调用这个方法

-(void)awakeFromNib
{
   另一种快速设置cell分割线的土方法 
  self.layer.borderColor = [UIColor colorWithRed:0.5 green:0.5 blue:0.5 alpha:0.3].CGColor; 
  self.layer.borderWidth = 0.5; 
}

通过代码自定义分割线与添加子控件一样

在initWithStyle方法中添加 

在layoutSubView是方法中布局

### 设置不等高Cell

#### 不等高cell纯代码:

```objective-c

/**不等高cell 
- 先设置高度rowH = 250 
- 复习frame设置等高cell **/
// 1.头像:宽高都是30,距离top.left都是10
CGFloat iconX = ;
CGFloat iconY = ;
CGFloat iconW = ;
CGFloat iconH = ;
self.icon_Ima.frame = CGRectMake(iconX, iconY, iconW, iconH);
 
// 2.昵称:left距离icon_Ima为10,顶部和icon_Ima对齐,宽高根据文字大小决定
CGFloat nameX = ;
CGFloat nameY = ;
CGFloat nameW = ;
CGFloat nameH = ;
self.name_Lab.frame = CGRectMake(nameX, nameY, nameW, nameH);
 
 //3.vip:宽高14,距离昵称10,垂直方向与昵称中线对齐
CGFloat vipX = ;
CGFloat vipY = ;
CGFloat vipW = ;
CGFloat vipH = ;
self.vip_Ima.frame = CGRectMake(vipX, vipY, vipW, vipH);
 
 //4.正文:左边与头像对齐,右边距离与左边距离相同,顶部距离头像间距10,高度根据文字多少决定
CGFloat textX = ;
CGFloat textY = ;
CGFloat textW = ;
CGFloat textH = ;
self.text_Lab.frame = CGRectMake(textX, textY, textW, textH);
 
// 5.配图:宽高100,左边与正文对齐,顶部距离正文距离为10
CGFloat pictureX = ;
CGFloat pictureY = ;
CGFloat pictureW = ;
CGFloat pictureH = ;
self.picture_Ima.frame = CGRectMake(pictureX, pictureY, pictureW, pictureH);
 
/**BetterCellH 
优化cellH的计算
reason: 代理方法heightForRowAtIndexPath以及cell布局方法layoutSubviews存在大量重复性代码,必须抽取出来 
解决办法: remove其中较"晚"调用方法中的计算,将heightForRowAtIndexPath中的计算通过模型传递给之后用到的地方:cell布局方法layoutSubviews*/
 
//- 1.模型中新添加frames属性
@property (nonatomic, assign) CGRect icon_Ima_frame; /**< 头像frame */ 
@property (nonatomic, assign) CGRect name_Lab_frame; /**< 昵称frame */ 
@property (nonatomic, assign) CGRect vip_Ima_frame; /**< vipframe */ 
@property (nonatomic, assign) CGRect text_Lab_frame; /**< 正文frame */ 
@property (nonatomic, assign) CGRect picture_Ima_frame; /**< 配图frame */ 
 
/**- 2.heightForRowAtIndexPath代理方法中计算所有子控件frame
 根据indexPath返回cell的高度**/
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    NSLog(@"%s, line = %d, row = %ld", __FUNCTION__, __LINE__, indexPath.row); 
    XMGStatus *status = self.statuses[indexPath.row]; 
    
     //在这里先计算好cell的高度,然后返回 
    
    CGFloat margin = 10;
    
   //  1.头像:宽高都是30,距离top.left都是10 
    CGFloat iconX = margin;
    CGFloat iconY = margin;
    CGFloat iconW = 30;
    CGFloat iconH = iconW;
    CGRect icon_Ima_frame = CGRectMake(iconX, iconY, iconW, iconH);
    status.icon_Ima_frame = icon_Ima_frame;
    
   //  2.昵称:left距离icon_Ima为10,顶部和icon_Ima对齐,宽高根据文字大小决定 
    CGFloat nameX = CGRectGetMaxX(icon_Ima_frame) + margin;
    CGFloat nameY = iconY;
     //根据字体大小计算单行文字的size 
    CGSize nameSize = [status.name sizeWithAttributes:@{NSFontAttributeName: NameFont}];
    CGFloat nameW = nameSize.width;
    CGFloat nameH = nameSize.height;
    CGRect name_Lab_frame = CGRectMake(nameX, nameY, nameW, nameH);
    status.name_Lab_frame = name_Lab_frame;
    
     //3.vip:宽高14,距离昵称10,垂直方向与昵称中线对齐 
    CGFloat vipX = CGRectGetMaxX(name_Lab_frame) + margin;
    CGFloat vipW = 14;
    CGFloat vipH = 14;
    CGFloat vipY = CGRectGetMidY(name_Lab_frame) - vipH * 0.5;         
 
CGRectGetMidY(self.name_Lab.frame) = vipY + vipH * 0.5
    CGRect vip_Ima_frame = CGRectMake(vipX, vipY, vipW, vipH);
    status.vip_Ima_frame = vip_Ima_frame;
    
    //4.正文:左边与头像对齐,右边距离与左边距离相同,顶部距离头像间距10,高度根据文字多少决定 
    CGFloat textX = iconX;
    CGFloat textY = margin + CGRectGetMaxY(icon_Ima_frame);
    CGFloat textW = self.tableView.bounds.size.width - 2 * textX; 
 
    CGSize maxSize = CGSizeMake(textW, CGFLOAT_MAX);
    CGSize textSize = [status.text boundingRectWithSize:maxSize      
 
    options:NSStringDrawingUsesLineFragmentOrigin attributes:@{NSFontAttributeName:                   
    TextFont} context:nil].size; 
 
    CGFloat textH = textSize.height;
    CGRect text_Lab_frame = CGRectMake(textX, textY, textW, textH);
    status.text_Lab_frame = text_Lab_frame;
    
     //5.配图:宽高100,左边与正文对齐,顶部距离正文距离为10 
    CGRect picture_Ima_frame = CGRectZero;
    if (status.picture) { 
        CGFloat pictureW = 100;
        CGFloat pictureH = 100;
        CGFloat pictureX = textX;
        CGFloat pictureY = margin + CGRectGetMaxY(text_Lab_frame);
        picture_Ima_frame = CGRectMake(pictureX, pictureY, pictureW, pictureH);
    }
    status.picture_Ima_frame = picture_Ima_frame;
    
    CGFloat CellH = (status.picture)? CGRectGetMaxY(picture_Ima_frame) + margin: CGRectGetMaxY(text_Lab_frame) + margin;
    
    return CellH; 
}

//- 3.由模型传入cell,在 layoutSubviews 中完成布局 
// 3.在layoutSubviews方法中布局
- (void)layoutSubviews
{
    [super layoutSubviews]; 
    
    self.icon_Ima.frame = self.status.icon_Ima_frame; 
    self.name_Lab.frame = self.status.name_Lab_frame; 
    self.vip_Ima.frame = self.status.vip_Ima_frame; 
    self.text_Lab.frame = self.status.text_Lab_frame; 
    self.picture_Ima.frame = self.status.picture_Ima_frame; 
}

/**总结:
- 重复计算问题解决 
- 代理方法调用频繁,实际问题仍然严峻 

 根据问题继续优化
- 思考每个cell对应一个模型 
- 在模型中新增属性cellH@property (nonatomic, assign) CGFloat cellH; /**< cell的高度 */ 
//- 重写get方法,懒加载中计算

//懒加载
- (CGFloat)cellH
{
    if (!_cellH) { 
        // 在这里先计算好cell的高度,然后返回呢? 
        NSLog(@"%s, line = %d", __FUNCTION__, __LINE__); 
        ...
        _cellH = (self.picture)? CGRectGetMaxY(picture_Ima_frame) + margin: CGRectGetMaxY(text_Lab_frame) + margin;
    }
    return _cellH; 
}
 
//- 此时控制器中代理方法知道的很少 

- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    XMGStatus *status = self.statuses[indexPath.row]; 
    return status.cellH; 
}
//additional(了解)
//- cell中的布局可以在模型的set方法中写
 
-// 新建XMGStatusFrame类 
//* Frame相关属性在声明文件中是只读的:
@property (nonatomic, strong) XMGStatus *status; /**< 微博模型 */ 
@property (nonatomic, assign, readonly) CGRect icon_Ima_frame; /**< 头像frame */ 
@property (nonatomic, assign, readonly) CGRect name_Lab_frame; /**< 昵称frame */ 
@property (nonatomic, assign, readonly) CGRect vip_Ima_frame; /**< vipframe */ 
@property (nonatomic, assign, readonly) CGRect text_Lab_frame; /**< 正文frame */ 
@property (nonatomic, assign, readonly) CGRect picture_Ima_frame; /**< 配图frame */ 
@property (nonatomic, assign, readonly) CGFloat cellH; /**< cell的高度 */ 

//- 在status模型的set方法中实现只读属性的计算 
#import "XMGStatusFrame.h" 
@interface XMGStatusFrame () 
@property (nonatomic, assign) CGRect icon_Ima_frame; /**< 头像frame */ 
@property (nonatomic, assign) CGRect name_Lab_frame; /**< 昵称frame */ 
@property (nonatomic, assign) CGRect vip_Ima_frame; /**< vipframe */ 
@property (nonatomic, assign) CGRect text_Lab_frame; /**< 正文frame */ 
@property (nonatomic, assign) CGRect picture_Ima_frame; /**< 配图frame */ 
@property (nonatomic, assign) CGFloat cellH; /**< cell的高度 */  
@end
 
@implementation XMGStatusFrame 
 
- (void)setStatus:(XMGStatus *)status
{
    _status = status;
    
    CGFloat margin = 10;
    
  //   1.头像:宽高都是30,距离top.left都是10 
    CGFloat iconX = margin;
    CGFloat iconY = margin;
    CGFloat iconW = 30;
    CGFloat iconH = iconW;
    CGRect icon_Ima_frame = CGRectMake(iconX, iconY, iconW, iconH);
    self.icon_Ima_frame = icon_Ima_frame; 
    
//     2.昵称:left距离icon_Ima为10,顶部和icon_Ima对齐,宽高根据文字大小决定 
    CGFloat nameX = CGRectGetMaxX(icon_Ima_frame) + margin;
    CGFloat nameY = iconY;
  //   根据字体大小计算单行文字的size 
    CGSize nameSize = [status.name sizeWithAttributes:@{NSFontAttributeName: NameFont}];
    CGFloat nameW = nameSize.width;
    CGFloat nameH = nameSize.height;
    CGRect name_Lab_frame = CGRectMake(nameX, nameY, nameW, nameH);
    self.name_Lab_frame = name_Lab_frame; 
    
//     3.vip:宽高14,距离昵称10,垂直方向与昵称中线对齐 
    CGFloat vipX = CGRectGetMaxX(name_Lab_frame) + margin;
    CGFloat vipW = 14;
    CGFloat vipH = 14;
    CGFloat vipY = CGRectGetMidY(name_Lab_frame) - vipH * 0.5; // 
  CGRectGetMidY(self.name_Lab.frame) = vipY + vipH * 0.5 
    CGRect vip_Ima_frame = CGRectMake(vipX, vipY, vipW, vipH);
    self.vip_Ima_frame = vip_Ima_frame; 
    
 //    4.正文:左边与头像对齐,右边距离与左边距离相同,顶部距离头像间距10,高度根据文字多少决定 
    CGFloat textX = iconX;
    CGFloat textY = margin + CGRectGetMaxY(icon_Ima_frame);
    CGFloat textW = [UIScreen mainScreen].bounds.size.width - 2 * textX;
    
    
    CGSize maxSize = CGSizeMake(textW, CGFLOAT_MAX);
    CGSize textSize = [status.text boundingRectWithSize:maxSize options:NSStringDrawingUsesLineFragmentOrigin attributes:@{NSFontAttributeName: TextFont} context:nil].size;
    CGFloat textH = textSize.height;
    CGRect text_Lab_frame = CGRectMake(textX, textY, textW, textH);
    self.text_Lab_frame = text_Lab_frame; 
    
//     5.配图:宽高100,左边与正文对齐,顶部距离正文距离为10 
    CGRect picture_Ima_frame = CGRectZero;
    if (status.picture) { 
        CGFloat pictureW = 100;
        CGFloat pictureH = 100;
        CGFloat pictureX = textX;
        CGFloat pictureY = margin + CGRectGetMaxY(text_Lab_frame);
        picture_Ima_frame = CGRectMake(pictureX, pictureY, pictureW, pictureH);
    }
    self.picture_Ima_frame = picture_Ima_frame; 
    
    self.cellH = (status.picture)? CGRectGetMaxY(picture_Ima_frame) + margin: CGRectGetMaxY(text_Lab_frame) + margin; 
}

//- 删除XMGStatus中的多余属性,完善模型属性set方法 
- (void)setIcon:(UIImage *)icon
{
    if ([icon isKindOfClass:[NSString class]]) { 
        _icon = [UIImage imageNamed:(NSString *)icon];
    }else
    {
        _icon = icon;
    }
}
 
- (void)setVip:(id)vip
{
    if ([vip isKindOfClass:[NSNumber class]]) { 
        _vip = [vip boolValue];
    }else
    {
        _vip = vip;
    }
}
 
- (void)setPicture:(UIImage *)picture
{
    if ([picture isKindOfClass:[NSString class]]) { 
        _picture = (picture)? [UIImage imageNamed:(NSString *)picture]: nil; 
    }else
    {
        _picture = picture;
    }
}

//- 修改控制器中的模型数组为 statusFrames 

// 懒加载
- (NSArray *)statusFrames
{
    if (!_statusFrames) { 
        NSString *filePath = [[NSBundle mainBundle] pathForResource:@"statuses.plist" ofType:nil]; 
        NSArray *dicts = [NSArray arrayWithContentsOfFile:filePath];
        
        NSMutableArray *arrayM = [NSMutableArray arrayWithCapacity:dicts.count];
        for (NSDictionary *dict in dicts) { 
            XMGStatus *obj = [XMGStatus statusWithDict:dict];
             转换代码如下 
            XMGStatusFrame *statusFrame = [[XMGStatusFrame alloc] init];
            statusFrame.status = obj;
            [arrayM addObject:statusFrame];
        }
        _statusFrames = [arrayM copy];
    }
    return _statusFrames; 
}
 
 
//- 修改cell中的模型属性为statusFrame 
 
 
// 2.在模型的set方法中为子控件赋值数据
- (void)setStatusFrame:(XMGStatusFrame *)statusFrame
{
    _statusFrame = statusFrame;
    XMGStatus *status = statusFrame.status;
    
    // 1.头像 
    self.icon_Ima.image = status.icon; 
    // 2.昵称 
    self.name_Lab.text = status.name; 
    self.name_Lab.textColor = status.isVip ? [UIColor orangeColor]: [UIColor blackColor]; 
    
   //  3.vip 
    self.vip_Ima.hidden = !status.isVip; 
    
   //  4.正文 
    self.text_Lab.text = status.text; 
  //   5.配图 
    if (status.picture) { 
        self.picture_Ima.hidden = NO; 
        self.picture_Ima.image = status.picture; 
    }else
    {
        self.picture_Ima.hidden = YES; 
        self.picture_Ima.image = nil; 
    }
    
  //   可以将frame在模型的方法中提前设置好,这样不用重写layoutSubviews方法了 
    self.icon_Ima.frame = statusFrame.icon_Ima_frame; 
    self.name_Lab.frame = statusFrame.name_Lab_frame; 
    self.vip_Ima.frame = statusFrame.vip_Ima_frame; 
    self.text_Lab.frame = statusFrame.text_Lab_frame; 
    self.picture_Ima.frame = statusFrame.picture_Ima_frame; 
}
 
//- 完善数据源代理方法中的赋值 
#pragma mark - UITableViewDataSource
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    return self.statusFrames.count; 
}
// 在cell显示到屏幕上之前,cell的高度就已经被确定
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
//     拿到cell 
    XMGStatusCell *cell = [XMGStatusCell cellWithTableView:tableView];
  //   覆盖数据 
    cell.statusFrame = self.statusFrames[indexPath.row]; 
    return cell; 
}
 
pragma mark - UITableViewDelegate
/**warning 当tableView加载的时候,在cell显示到屏幕上之前(cell创建之前),tableView会先拿到所有的cell的高度,来确定contentSize用来估算右边索引条的高度,所以在创建cell的时候,一定是要知道cell高度的 
 根据indexPath返回cell的高度**/
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    XMGStatusFrame *statusFrame = self.statusFrames[indexPath.row]; 
    return statusFrame.cellH; 
}

//不等高cell-Autolayout-iOS8+
//了解估算高度
 
//- 多添加一个最后一个子控件距离contentView底部的约束 
//- 在viewDidLoad方法中 

// 先设置估算高度,tableView刚开始显示的滚动条就可以根据估算高度得到contentSize
self.tableView.estimatedRowHeight = 250;
// 取消storyboard中行高的设置
self.tableView.rowHeight = UITableViewAutomaticDimension;
 
//- 在有配图的情况下,拖线设置新添加约束的值即可 

 //微博正文距离底部的约束
@property (weak, nonatomic) IBOutlet NSLayoutConstraint *textToBottom; 
// 5.配图
if (status.picture) { 
    self.picture_Ima.hidden = NO; 
    self.picture_Ima.image = status.picture; 
    self.textToBottom.constant = 120; 
}else
{
    self.picture_Ima.hidden = YES; 
    self.picture_Ima.image = nil; 
    self.textToBottom.constant = 10; 
}

/** 不等高cell-Autolayout-iOS6,7
 首先恢复高度250的等高cell
- 去掉最后底部的约束以及代码 
- 去掉self-sizing的两行代码 

 先设置估算高度,tableView刚开始显示的滚动条就可以根据估算高度得到contentSize**/
    self.tableView.estimatedRowHeight = 250;
   // 行高是由约束自动决定是多少 
    self.tableView.rowHeight = UITableViewAutomaticDimension;

/**分析方法调用顺序
 
- 再次启用代理方法 heightForRowAtIndexPath 
* 创建一个临时的 tempCell 
* 强制布局 
* 解决依赖于屏幕宽度的 text_Lab **/
 
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
   //  要在此处得到显示在屏幕上的cell的高度 
    // 拿到cell 
    XMGStatusCell *tempCell = [XMGStatusCell cellWithTableView:tableView];
   //  覆盖数据 
    tempCell.status = self.statuses[indexPath.row]; 
    
     //需要在布局前,对我们多行cell显示的最大宽度设置限制 
    [tempCell.text_Lab setPreferredMaxLayoutWidth:tableView.bounds.size.width - 20];
    
//warning 由于我们创建的tempCell不是显示到屏幕上的,所以父view没有宽度,导致原先的约束失效,我们的Autolayout没有布局成frame 
    // 强制布局 
    [tempCell layoutIfNeeded];
    
        [tempCell.text_Lab setPreferredMaxLayoutWidth:tableView.bounds.size.width - 20]; 
   
        [tempCell layoutIfNeeded]; 
    
    CGFloat cellH = (tempCell.status.picture) ? CGRectGetMaxY(tempCell.picture_Ima.frame) :CGRectGetMaxY(tempCell.text_Lab.frame);
    cellH += 10;
    
    return cellH; 
}

//- 分析总结: 
//* cell.text_Lab.宽度约束 = cell.contentView的宽度 - 20 
/* 由于我们创建的tempCell不显示到屏幕上的,所以cell.contentView宽度不确定,导致原先的约束无效,我们的Autolayout没有布局成frame 
* 解决办法(2种): 
* 1.设置text_Lab的最大宽度*/
 
[tempCell.text_Lab setPreferredMaxLayoutWidth:tableView.bounds.size.width - 20];
 
//* 2.为text_Lab的宽度约束拖一条线手动设置
 
// 手动设置了有效的,不依赖于父view的约束
self.textW.constant = [UIScreen mainScreen].bounds.size.width - 20;
 
 
//- 性能优化 
//* heightForRowAtIndexPath`调用频繁
//* tempCell 只需要创建一次 
//* 封装cellH 的计算 
//* 设置估算高度 self.tableView.estimatedRowHeight = 250; 

XMGStatusCell *tempCell;
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    // 要在此处得到显示在屏幕上的cell的高度 
    // 拿到cell 
    if (!tempCell) { 
        tempCell = [XMGStatusCell cellWithTableView:tableView];
        NSLog(@"%p, %s, line = %d", tempCell, __FUNCTION__, __LINE__); 
    }
   //  覆盖数据 
    tempCell.status = self.statuses[indexPath.row]; 
    
    return tempCell.cellH; 
}
//- 估算高度的作用:让 heightForRowAtIndexPath 懒加载 
//- 代理方法 

/*tableView用估算高度快速计算估算值来节省加载时间,如果这些方法实现,那我们显示在屏幕上的cell才会调用cellForRow
 Use the estimatedHeight methods to quickly calcuate guessed values which will allow for fast load times of the table.
 If these methods are implemented, the above -tableView:heightForXXX calls will be deferred until views are ready to be displayed, so more expensive logic can be placed there.**/
- (CGFloat)tableView:(UITableView *)tableView estimatedHeightForFooterInSection:(NSInteger)section NS_AVAILABLE_IOS(7_0);

```



### 自定义删除cell

#### tableView.reloadData 刷新tableview当中的数据

\- 对tableView进行操作的时候分两步

1.操作数据(增删改)

2.刷新表格

\- 注意bug:拷贝控件会一同拷贝响应事件 

​        ![img](https://uploader.shimo.im/f/nNqICQ3QSqMoGXiK.png!thumbnail)      

```objc
#pragma mark - 数据刷新操作 
- (IBAction)remove {
     //获得所有被打钩的模型 
     //点击了删除按钮 
    NSMutableArray *deletedWineArray = [NSMutableArray array]; 
    for (XMGWine *wine in self.wineArray) { 
        if (wine.isChecked) { 
            [deletedWineArray addObject:wine]; 
        }
    }
    
     //删除所有被打钩的模型 
    [self.wineArray removeObjectsInArray:deletedWineArray];
 
     //刷新表格 
    [self.tableView reloadData];
}
```

```objc
#pragma mark - <代理方法>
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath 
{
    // 修改模型 
    XMGWine *wine = self.wineArray[indexPath.row]; 
    wine.checked = !wine.isChecked; 
    
     //刷新 
    [tableView reloadData]; 
}
 
```

```objc
//局部刷新方法(包括增加/删除/更改):
- (IBAction)remove {
     //删除模型数据 
    [self.wineArray removeObjectAtIndex:0];
    [self.wineArray removeObjectAtIndex:0];
    
    // 刷新 
    NSArray *indexPaths = @[ 
                            [NSIndexPath indexPathForRow:0 inSection:0], 
                            [NSIndexPath indexPathForRow:1 inSection:0] 
                            ];
    [self.tableView deleteRowsAtIndexPaths:indexPaths withRowAnimation:UITableViewRowAnimationMiddle]; 
}
```

```objc
//左滑删除方法:(要使用代理)
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath 
{
     //删除模型 
    [self.wineArray removeObjectAtIndex:indexPath.row];
    
    // 刷新 
    [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationLeft]; 
}
 
/**
 *  修改Delete按钮文字为“删除” 
 */
- (NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath 
{
    return @"删除"; 
//    return [NSString stringWithFormat:@"删除-%zd", indexPath.row];
}
//左滑删除出现两个按钮的方法:(要使用代理)
```

​        ![img](https://uploader.shimo.im/f/lOC1RxNLVWOZDvGU.png!thumbnail)      

**按钮**

```objective-c
- (IBAction)remove {
     //删除模型数据 
    [self.wineArray removeObjectAtIndex:0];
    [self.wineArray removeObjectAtIndex:0];
    
   //  刷新 
    NSArray *indexPaths = @[ 
                            [NSIndexPath indexPathForRow:0 inSection:0], 
                            [NSIndexPath indexPathForRow:1 inSection:0] 
                            ];
    [self.tableView deleteRowsAtIndexPaths:indexPaths withRowAnimation:UITableViewRowAnimationMiddle]; 
}
 
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath 
{
    
}
/**
 *  左滑cell时出现什么按钮 
 */
- (NSArray *)tableView:(UITableView *)tableView editActionsForRowAtIndexPath:(NSIndexPath *)indexPath 
{
    UITableViewRowAction *action0 = [UITableViewRowAction rowActionWithStyle:UITableViewRowActionStyleNormal title:@"关注" handler:^(UITableViewRowAction *action, NSIndexPath *indexPath) { 
        NSLog(@"点击了关注"); 
        
       //  收回左滑出现的按钮(退出编辑模式) 
        tableView.editing = NO; 
    }];
    
    UITableViewRowAction *action1 = [UITableViewRowAction rowActionWithStyle:UITableViewRowActionStyleDefault title:@"删除" handler:^(UITableViewRowAction *action, NSIndexPath *indexPath) { 
//要删除的哪一行
        [self.wineArray removeObjectAtIndex:indexPath.row];
        [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationAutomatic]; 
    }];
```

​        ![img](https://uploader.shimo.im/f/JHygSO9d1r3KJV8C.png!thumbnail)      

```objective-c
#pragma mark - 数据刷新操作 
- (IBAction)remove {
    [self.tableView setEditing:!self.tableView.isEditing animated:YES];
}
#pragma mark - <代理方法>
/**
 *  只要实现了这个方法，左滑出现Delete按钮的功能就有了 
 *  点击了“左滑出现的Delete按钮”会调用这个方法 
 */
 //点击了删除按钮 调用这个办法 然后删除模型 刷新
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath 
{
    NSLog(@"shabi"); 
    // 删除模型 
    [self.wineArray removeObjectAtIndex:indexPath.row];
    
     //刷新 
    [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationLeft]; 
}
 
/**
 *  修改Delete按钮文字为“删除” 
 */
 //先调用这个办法
- (NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath 
{
    NSLog(@"删除"); 
    return @"删除"; 
}
```

**批量删除:**

​        ![img](https://uploader.shimo.im/f/aYI6Bhh8fA8MYMtZ.png!thumbnail)      

```objective-c
#pragma mark - 数据刷新操作 
- (IBAction)multipleRemove {
    [self.tableView setEditing:!self.tableView.isEditing animated:YES];
    
    self.removeButton.hidden = !self.tableView.isEditing; 
}
 
- (IBAction)remove {
     self.tableView.indexPathsForSelectedRows = [0, 1] 
    // 获得需要删除的模型数据 
    NSMutableArray *deletedWineArray = [NSMutableArray array]; 
    for (NSIndexPath *indexPath in self.tableView.indexPathsForSelectedRows) { 
        [deletedWineArray addObject:self.wineArray[indexPath.row]]; 
    }
    
    // 删除模型数据 
    [self.wineArray removeObjectsInArray:deletedWineArray];
    
   //  刷新 
    [self.tableView deleteRowsAtIndexPaths:self.tableView.indexPathsForSelectedRows withRowAnimation:UITableViewRowAnimationLeft];
}
 
 
#pragma mark - <代理方法>
/**
 *  只要实现了这个方法，左滑出现Delete按钮的功能就有了 
 *  点击了“左滑出现的Delete按钮”会调用这个方法 
 */
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath 
{
    NSLog(@"点击了代理方法"); 
    // 删除模型 
    [self.wineArray removeObjectAtIndex:indexPath.row];
    
    // 刷新 
    [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationLeft]; 
}
 
/**
 *  修改Delete按钮文字为“删除” 
 */
- (NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath 
{
    return @"删除"; 
}

//- 刷新 
// 带动画的刷新
 //reloadRowsAtIndexPaths:要刷新的数组索引
// withRowAnimation:动画类型
[self.tableView reloadRowsAtIndexPaths:@[[NSIndexPath indexPathForRow:0 inSection:0]] withRowAnimation:UITableViewRowAnimationRight];

//- 删除 
//带动画的删除
[self.tableView deleteRowsAtIndexPaths:@[[NSIndexPath indexPathForRow:0 inSection:0],
                                         [NSIndexPath indexPathForRow:1 inSection:0]]
                      withRowAnimation:UITableViewRowAnimationTop];

//- 添加  
//带动画的添加
[self.tableView insertRowsAtIndexPaths:@[[NSIndexPath indexPathForRow:0 inSection:0]]
                    withRowAnimation:UITableViewRowAnimationBottom];
//左划删除
 
//- 实现数据源方法 
#warning 修改delete为删除:1.将模拟器语言改成简体中文  2.为项目添加简体中文 
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath
{
    NSLog(@"%s, line = %d, editingStyle= %ld, row = %zd", __FUNCTION__, __LINE__, editingStyle, indexPath.row); 
    if (editingStyle == UITableViewCellEditingStyleDelete) { 
        // 1.处理数据 
        [self.wines removeObjectAtIndex:indexPath.row];
         ///2.刷新表格 
        [tableView deleteRowsAtIndexPaths:@[indexPath]
                         withRowAnimation:UITableViewRowAnimationTop];
    }
}
 
/*- 修改按钮文字为汉字 
* 1.手机/模拟器设为简体中文
* 2.项目中添加中文支持
![](images/006.png)
 
- 代理方法自定义按钮文字 
 该方法return的是左划时候按钮上的具体内容*/
- (NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath
{
    NSLog(@"%s, line = %d", __FUNCTION__, __LINE__); 
    return [NSString stringWithFormat:@"删--%zd", indexPath.row]; 
}
//左划出现多个按钮iOS8 
//- 实现代理方法 
- (NSArray *)tableView:(UITableView *)tableView editActionsForRowAtIndexPath:(NSIndexPath *)indexPath
{
} 
//- 数组内元素`UITableViewRowAction` 
typedef NS_ENUM(NSInteger, UITableViewRowActionStyle) {
    UITableViewRowActionStyleDefault = 0,
    UITableViewRowActionStyleDestructive = UITableViewRowActionStyleDefault,
    UITableViewRowActionStyleNormal
} NS_ENUM_AVAILABLE_IOS(8_0);
 
NS_CLASS_AVAILABLE_IOS(8_0) @interface UITableViewRowAction : NSObject <NSCopying> 
 
+ (instancetype)rowActionWithStyle:(UITableViewRowActionStyle)style title:(NSString *)title handler:(void (^)(UITableViewRowAction *action, NSIndexPath *indexPath))handler; 
 
@property (nonatomic, readonly) UITableViewRowActionStyle style; 
@property (nonatomic, copy) NSString *title; 
@property (nonatomic, copy) UIColor *backgroundColor; // default background color is dependent on style 
@property (nonatomic, copy) UIVisualEffect* backgroundEffect; 
@end
 
//- 该方法实现后不会再调用commitEditingStyle的实现 
 
//- 总结补充: 
//* cell.contentView左划后,tableView.editing = YES 
 
//左划出现多个按钮实际开发
 
//比较实用的两个第三方
//- SWTableViewCell <https://github.com/CEWendel/SWTableViewCell>
//- MGSwipeTableCell <https://github.com/MortimerGoro/MGSwipeTableCell>

//batchProcessing
//- 允许编辑模式下多选 
self.tableView.allowsMultipleSelectionDuringEditing = YES;` 
 
//- 删除所有选中行  
// 删除数据
- (IBAction)deleteData {
    // 1.处理数据 
   //  这个方法能拿到所有的选中cell的indexPath 
    NSArray *array = [self.tableView indexPathsForSelectedRows];
    
    NSMutableArray *arrayM = [NSMutableArray arrayWithCapacity:array.count];
    for (NSIndexPath *indexPath in array) { 
        [arrayM addObject:self.wines[indexPath.row]];
    }
   //  删除wines中的某些元素 
    [self.wines removeObjectsInArray:arrayM];
    // 2.刷新表格 
    [self.tableView reloadData]; 
    [self.tableView deleteRowsAtIndexPaths:array
                          withRowAnimation:UITableViewRowAnimationTop];
}
```

