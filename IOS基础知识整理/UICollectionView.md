# UICollectionView的介绍

iOS6引入的控件(视图),可以实现多列布局,布局更加灵活,用法类似于UITableView.

组成部分Cells(单元格)

Supplementary Views（补充的view，相当于TableView的页眉和页脚），

Decoration Views（装饰View，用于装饰整个CollectionView的）。

​        ![img](https://uploader.shimo.im/f/l2K91B1QeMhXrmf0.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/pN2XwnTUgfdGHECb.png!thumbnail)      

### UICollectionView和UICollectionViewLayout的关系

UICollectionViewLayout和UICollectionView的关系，就像人的大脑和人的行为的关系一样。

大脑控制人的行为。UICollectionViewLayout负责管理（决定）每一个cell的大小，位置信息等。UICollectionView只是负责显示每一个cell。

### UICollectionViewFlowLayout的使用

UICollectionViewLayout的子类.

线性布局,或者称为流水式布局.

### Flow Layout常用的属性:

```objective-c
Item size
Line spacing
Inter cell spacing
Scrolling direction
Header and footer size
Section Inset
```

​        ![img](https://uploader.shimo.im/f/04RmFopV0Z0GcB2P.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/aK9h3y7FhUNn72Qs.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/rVrQfXslGgLb3H8M.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/t7czyQvVRhDKOERK.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/6pSAWqpAthPywEOV.png!thumbnail)      

### 常用方法（类比UITableView的DataSource方法）

```objective-c
//返回cell个数
- (NSInteger) collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section 
 
//返回cell
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath
 
//注册重用cell
- (void)registerClass:(nullable Class)cellClass forCellWithReuseIdentifier:(NSString *)identifier;
- (void)registerNib:(nullable UINib *)nib forCellWithReuseIdentifier:(NSString *)identifier
```

