**CAReplicatorLayer:复制图层**

顾名思义，复制图层就是用来复制的。它会将自己的子图层进行复制,连同子layer上的动画会一起复制。

属性说明：

```
instanceCount ：复制份数。（会把原始的子图层复制多少份，包括原来的一份） 
instanceTransform：形变。每一份相对上一份的形变量
instanceDelay ：每一份相对上一份的延迟的时间。 
```

修改CAReplicatorLayer的颜色通道，可以改变CAReplicatorLayer的显示的样式

```objective-c
 @property float instanceRedOffset; 
@property float instanceGreenOffset; 
@property float instanceBlueOffset; 
@property float instanceAlphaOffset; 
```

用这个图层，可以做颜色的渐变属性说明：

colors ：保存所有渐变的颜色，里面是CGColorRef，记得用id，让编译器以为数组里面的是OC对象。 

locations ：保存所有渐变的位置【0-1】 

startPoint：开始渐变的点【0-1】

endPoint：结束渐变的点【0-1】

#### 2.CAShapeLayer：形状图层

根据形状，绘制内容的图层

属性说明：

```objective-c
path：描述形状的路径。默认会把路径封闭，然后填充。
 
fillColor :填充的颜色
 
strokeColor：描边的颜色（如果想仅仅只描边的话，就把fillColor设为clearcolor就好）
 
strokeStart：开始描边的比例【0-1】
 
strokeEnd：结束描边的比例【0-1】
```

#### 3.CAGradientLayer：渐变图层

```objective-c
 // Do any additional setup after loading the view, typically from a nib. 
    //复制层 
    CAReplicatorLayer *repL = [CAReplicatorLayer layer]; 
    repL.frame = self.contentV.bounds; 
  //  复制5份出来. 
    repL.instanceCount = 6; 
   // 每一个形变,都是相对于它上一个复制出来的子层开始的. 
    repL.instanceTransform = CATransform3DMakeTranslation(40, 0, 0); 
  //  动画延时执行. 
    repL.instanceDelay = 0.5; 
 
    //要设置复制层的颜色,原始层的颜色要设为白色. 
    repL.instanceColor = [UIColor whiteColor].CGColor; 
    [self.contentV.layer addSublayer:repL];
 
    
  //  添加音量震动条 
    CALayer *layer = [CALayer layer]; 
    layer.frame = CGRectMake(0, self.contentV.bounds.size.height - 150, 30,       150); 
    layer.backgroundColor = [UIColor redColor].CGColor; 
    
    layer.position = CGPointMake(0, self.contentV.bounds.size.height); 
    layer.anchorPoint = CGPointMake(0, 1); 
 
    [repL addSublayer:layer]; 
    
  //  添加动画.(缩放,只缩放y方向). 
  //  创建动画对象 
    CABasicAnimation *anim = [CABasicAnimation animation]; 
   // 设置属性值. 
    anim.keyPath = @"transform.scale.y"; 
    anim.toValue = @0; 
    anim.repeatCount = MAXFLOAT; 
    anim.duration = 0.5; 
    anim.autoreverses = YES; 
    [layer addAnimation:anim forKey:nil]; 
```

​        ![img](https://uploader.shimo.im/f/fg1tpg2kCBQtL8ym.png!thumbnail)      