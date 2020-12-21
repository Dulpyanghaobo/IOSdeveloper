# UIImageView

```objective-c
contentMode    //拉伸至填充整个UIImageView
UIViewContentModeScaleToFill,     //等比例拉伸 至自适应 
UIViewContentModeScaleAspectFit,     //等比例拉伸 至填充  
UIViewContentModeScaleAspectFill,      //不拉伸 不缩放 显示位置     
UIViewContentModeCenter,     
UIViewContentModeTop,     
UIViewContentModeBottom,     
UIViewContentModeLeft,     
UIViewContentModeRight,     
UIViewContentModeTopLeft,     
UIViewContentModeTopRight,     UIViewContentModeBottomLeft,     UIViewContentModeBottomRight, 
 UIImage *img = [UIImage imageNamed:iconName];
        //通过这种方式一定有缓存 
    //使用 imageWithContentsOfFile  没有缓存  用完就干掉 
    //用完了  必须替换图片 
```

## UIImageView常见扩展

>   图片拉伸的三种方式
>
>   ```objective-c
>   // 得到view的尺寸
>   CGSize viewSize = self.view.bounds.size;
>   // 初始化按钮
>   UIButton *button = [[UIButton alloc] init];
>   // 设置尺寸
>   button.bounds = CGRectMake(0, 0, 150, 50);
>   // 设置位置
>   button.center = CGPointMake(viewSize.width * 0.5f, viewSize.height * 0.5f);
>   // 加载图片
>   UIImage *image = [UIImage imageNamed:@"button"];
>   // 设置背景图片
>   [button setBackgroundImage:image forState:UIControlStateNormal];
>   // 添加按钮
>   [self.view addSubview:button];
>   - (UIImage *)resizableImageWithCapInsets:(UIEdgeInsets)capInsets resizingMode:(UIImageResizingMode)resizingMode
>   ```

-   UIImageResizingModeStretch：拉伸模式，通过拉伸UIEdgeInsets指定的矩形区域来填充图片
-   UIImageResizingModeTile：平铺模式，通过重复显示UIEdgeInsets指定的矩形区域来填充图片

```objective-c
CGFloat top = 25; // 顶端盖高度
CGFloat bottom = 25 ; // 底端盖高度
CGFloat left = 10; // 左端盖宽度
CGFloat right = 10; // 右端盖宽度
UIEdgeInsets insets = UIEdgeInsetsMake(top, left, bottom, right);
// 指定为拉伸模式，伸缩后重新赋值
image = [image resizableImageWithCapInsets:insets resizingMode:UIImageResizingModeStretch];
```



## SDWebImage解读