# UIPickView和UIDatePicker(了解)

### 1.UIPickView什么时候用?

• 通常在注册模块,当用户需要选择一些东西的时候,比如说城市,往往弹出一个PickerView给他们选择。 -> 老虎机效果 

### 2.UIPickView常见用法,演示实例程序

1>独立的,没有任何关系 =>菜单系统 

2>相关联的,下一列和第一列有联系=>省会城市选择

3>图文并帽,=>国旗选择。

### 3.UIPickView

​        ![img](https://uploader.shimo.im/f/gCJdvsHR1H0jrKaH.png!thumbnail)      

### 4.UIDatePicker什么时候用? 

当用户选择日期的时候,一般弹出一个UIDatePicker给用户选择。 

### 5.UIDatePickerios6和ios7的区别

​        ![img](https://uploader.shimo.im/f/TI2eJwJRP2I3vTX3.png!thumbnail)      

### **注册界面**



#### 一.UITextFieldDelegate

```objective-c
/*
是否允许改变文本框的文字
 是否允许用户输入文字
 作用:拦截用户的输入
*/
- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string
{
    return NO; 
}
 
 //是否允许开始编辑
 //允许编辑文本框
- (BOOL)textFieldShouldBeginEditing:(UITextField *)textField
 
 //是否允许文本框结束编辑
- (BOOL)textFieldShouldEndEditing:(UITextField *)textField
```

#### 二.自定义键盘

键盘由文本框inputView属性决定

#### 三.KVC底层实现

 setValuesForKeysWithDictionary底层实现

 利用KVC字典转模型, [flag setValuesForKeysWithDictionary:dict];

 1.遍历字典中的所有key

[dict enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) { 

   2.给模型的属性赋值,利用KVC,把字典中的key当做模型的属性名使用,字典中的值传递给模型的属性.   [flag setValue:obj forKey:key];

name -> icon    KeyPath:模型中的属性名    属性的值

```objective-c
[flag setValue:dict[@"name"] forKey:@"name"]; 
[flag setValue:dict[@"icon"] forKey:@"icon"]; 
```

 setValue:forKey:底层实现

 给模型中的icon属性赋值

```objective-c
 [flag setValue:dict[@"icon"] forKey:@"icon"];
```

 1.首先去寻找模型中有木有setIcon:方法,直接调用setIcon:方法,[flag setIcon:dict[@"icon"]]

 2.接着寻找模型中有没有icon的属性名,如果有,就直接赋值icon = dict[@"icon"]

 3.接着寻找模型中有没有_icon的属性名,如果有,就直接赋值_icon = dict[@"icon"]

 4.找不到,直接报错,setValue:forUndefinedKey: 

### 四.UIDatePicker

 创建一个UIDatePicker

```objective-c
UIDatePicker *datePicker = [[UIDatePicker alloc] init];
```

 设置日期模型

```objective-c
datePicker.datePickerMode = UIDatePickerModeDate;
```

 设置地区,zh:中国

```objective-c
datePicker.locale = [NSLocale localeWithLocaleIdentifier:@"zh"];
```

 监听UIDatePicker的选中的日期

```objective-c
[datePicker addTarget:self action:@selector(dateChange:) forControlEvents:UIControlEventValueChanged]; 
```

### 代理方法:

```objective-c
#pragma mark - UIPickerViewDataSource
 
//返回有多少列
- (NSInteger)numberOfComponentsInPickerView:(UIPickerView *)pickerView
{
    
    return 3; 
}
 
//返回第component列有多少行
- (NSInteger)pickerView:(UIPickerView *)pickerView numberOfRowsInComponent:(NSInteger)component
{
    return 3; 
}

#pragma mark - UIPickerViewDelegate 
//返回每一列行高
- (CGFloat)pickerView:(UIPickerView *)pickerView rowHeightForComponent:(NSInteger)component;
 
//设置pickerView内容
//返回第component列第row行的标题
- (NSString *)pickerView:(UIPickerView *)pickerView titleForRow:(NSInteger)row forComponent:(NSInteger)component
{
    if (component == 1 && row == 1) { 
        return @"b"; 
    }
    return @"a"; 
}
 
//NSAttributedString:属性字符串,富文本:颜色,字体,阴影,空心,图文混排
- (NSAttributedString *)pickerView:(UIPickerView *)pickerView attributedTitleForRow:(NSInteger)row forComponent:(NSInteger)component
{
 
}
 
//返回每一行的控件
- (UIView *)pickerView:(UIPickerView *)pickerView viewForRow:(NSInteger)row forComponent:(NSInteger)component reusingView:(UIView *)view
{
    return [UIButton buttonWithType:UIButtonTypeContactAdd]; 
}
 
//选中某一行的时候调用
- (void)pickerView:(UIPickerView *)pickerView didSelectRow:(NSInteger)row inComponent:(NSInteger)component
{
    NSLog(@"%ld %ld",component,row);
}
```

