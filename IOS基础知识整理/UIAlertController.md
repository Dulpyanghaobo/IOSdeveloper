```objective-c
- (void)testAlert {
    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"alert"
                                                    message:@"UIAlertView is deprecated. Use UIAlertController with a preferredStyle of UIAlertControllerStyleAlert instea"
                                                   delegate:self
                                          cancelButtonTitle:@"取消"
                                          otherButtonTitles:@"确定", nil]; 
    [alert show];
}
```

```objective-c
#pragma mark - UIAlertViewDelegate
- (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex
{
     根据buttonIndex判断是哪个按钮点击了 
}
```

-不必再设置代理

```objective-c
- (void)testAlertController {
   
     是用来替代UIAlertView/UIActionSheet 
     1.创建提示框 
    UIAlertController *alert = [UIAlertController alertControllerWithTitle:@"alertController"  标题 
     message:@"描述~"
     preferredStyle:UIAlertControllerStyleAlert]; alert/actionSheet 
 
     2.添加按钮和响应事件 
     2.1 创建 
    UIAlertAction *action0 = [UIAlertAction actionWithTitle:@"I know" 按钮文字 
    style:UIAlertActionStyleCancel  按钮样式 
    handler:^(UIAlertAction *action) {  按钮响应事件 
    NSLog(@"%s, line = %d", __FUNCTION__, __LINE__); 
                                                    }];
     2.2 添加到提示框上 
    [alert addAction:action0];
    
     3.展示提示框 
    [self presentViewController:alert animated:YES completion:nil]; 
}
```

### **keyBoardEditing(键盘处理):**

```objective-c
#pragma mark - 键盘处理 
 
- (void)viewDidLoad {
    [super viewDidLoad];
    self.tableView.rowHeight = 80; 
     #监听键盘的frame变化 
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(keyboardTransform:) name:UIKeyboardWillChangeFrameNotification object:nil]; 
}
 #通知响应的方法
- (void)keyboardTransform:(NSNotification *)note
{
    NSLog(@"%s, line = %d, %@", __FUNCTION__, __LINE__, note.userInfo); 
    /* 
     打印的note.userInfo 
     UIKeyboardAnimationCurveUserInfoKey = 7;
     UIKeyboardAnimationDurationUserInfoKey = "0.25";
     UIKeyboardBoundsUserInfoKey = "NSRect: {{0, 0}, {375, 216}}";
     UIKeyboardCenterBeginUserInfoKey = "NSPoint: {187.5, 775}";
     UIKeyboardCenterEndUserInfoKey = "NSPoint: {187.5, 559}";
     UIKeyboardFrameBeginUserInfoKey = "NSRect: {{0, 667}, {375, 216}}";
     UIKeyboardFrameEndUserInfoKey = "NSRect: {{0, 451}, {375, 216}}";
     */
     #拿到键盘弹出时间 
    CGFloat animaDur = [note.userInfo[UIKeyboardAnimationDurationUserInfoKey] doubleValue];
    # 拿到键盘将要变化的y值 
    CGFloat keyboardY = [note.userInfo[UIKeyboardFrameEndUserInfoKey] CGRectValue].origin.y;
     #计算出约束变化的值 
    CGFloat bottomMargin = CGRectGetHeight([UIScreen mainScreen].bounds) - keyboardY;
    
    self.bottomMar_Con.constant = bottomMargin; 
    # 动画布局完成操作 
    [UIView animateWithDuration:animaDur animations:^{
        [self.view layoutIfNeeded];
    }];
}
 #有添加就有移除
- (void)dealloc
{
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}
```

