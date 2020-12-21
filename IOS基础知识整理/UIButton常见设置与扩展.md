# UIButton

```objective-c
//不能直接给按钮的titleLabel/ imageView赋值
//因为这样赋值 按钮不知道 你需要是给它的哪个状态下的属性赋值   
[btn addTarget:self action:@selector(click:) forControlEvents:UIControlEventTouchUpInside];    // 当触发了UIControlEventTouchUpInside 事件之后 调用 控制器(self) 的click方法   
//任何继承自UIControl的类都可以使用 addTarget方法
[sender isKindOfClass:[UIButton class]]
//判断sender的类型是否匹配 
```

2.  ## 按钮的内部结构   图片 和 标题 间距 

    ​    

    ```objective-c
    self.titleEdgeInsets; //改变标题在按钮中的位置
    self.imageEdgeInsets;  //改变图片在按钮中的位置
    self.contentEdgeInsets //改变按钮中所有内容的位置设置内间距   图片 和 标题 位置布局     直接影响按钮内部的子控件     缺陷 不知道先调用的是哪个方法(不推荐使用)
      - (CGRect)titleRectForContentRect:(CGRect)contentRect    {      
      self.titleH = contentRect.size.height * 0.4;       return CGRectMake(0, 0, contentRect.size.width,self.titleH);
    } 
     - (CGRect)imageRectForContentRect:(CGRect)contentRect    {      
       return CGRectMake(0, contentRect.size.height * 0.4, contentRect.size.width, contentRect.size.height * 0.6);     }  
    // 一般情况下 都是使用layoutSubviews 布局子控件layoutSubviews 准确性🐔高 
    ```

    ​                         

## UIButton常见扩展

## RxOC扩展解读