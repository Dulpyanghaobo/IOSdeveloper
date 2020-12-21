# UILabel

## 1.常见设置

-   text 设置label的文字

-   textcolor 设置label的文字颜色   

-   textAlignment 对齐方式   

-   numberOfLines 换行个数 0 "自动换行"   

-   linesBreakMode 缩进方式 ...abc abc... a..bc   

-   font 字体大小和样式 (系统默认样式, 斜体样式,粗体样式)      font 系统默认样式  斜体  粗体 系统样式    

    ```objective-c
    label.font = [UIFont systemFontOfSize:19.0f];//粗体
    label.font = [UIFont boldSystemFontOfSize:24.0f];//斜体
    label.font = [UIFont italicSystemFontOfSize:24.0f];
    ```

    ```objective-c
    @property(nonatomic,copy)  NSString      *text; //显示的文字 
    @property(nonatomic,retain) UIFont       *font; //字体
    @property(nonatomic,retain) UIColor      *textColor; //文字颜色 
    @property(nonatomic)    NSTextAlignment  textAlignment; //对齐模式（比如左对齐、居中对齐、右对齐）  
    UIFont//代表字体，常见创建方法有以下几个：
      + (UIFont *)systemFontOfSize:(CGFloat)fontSize; //系统默认字体
    + (UIFont *)boldSystemFontOfSize:(CGFloat)fontSize;// 粗体 
    + (UIFont *)italicSystemFontOfSize:(CGFloat)fontSize; //斜体 
    ```

    

## 2.常见扩展

## 3.YYLabel解读