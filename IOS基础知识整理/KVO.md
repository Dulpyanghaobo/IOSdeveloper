# KVO

>   由于ObjC主要基于Smalltalk进行设计，因此它有很多类似于Ruby、Python的动态特性，例如动态类型、动态加载、动态绑定等。今天我们着重介绍ObjC中的键值编码（KVC）、键值监听（KVO）特性：

## **键值编码KVC**

>   我们知道在C#中可以通过反射读写一个对象的属性，有时候这种方式特别方便，因为你可以利用字符串的方式去动态控制一个对象。其实由于oc的语言特性，你根本不必进行任何操作就可以进行属性的动态读写，这种方式就是Key Value Coding（简称KVC）。

KVC的操作方法由NSKeyValueCoding协议提供，而NSObject就实现了这个协议，也就是说ObjC中几乎所有的对象都支持KVC操作，常用的KVC操作方法如下：

动态设置：**setValue:属性值 forKey:属性名**（用于简单路径）、**setValue:属性值 forKeyPath:属性路径**（用于复合路径，例如Person有一个Account类型的属性，那么person.account就是一个复合属性）

动态读取：**valueForKey:属性名**、**valueForKeyPath:属性名**（用于复合路径）下面通过一个例子来理解KVC

```objective-c
 Account.h

#import <Foundation/Foundation.h>

 @interface Account : NSObject

 #pragma mark - 属性

 #pragma mark 余额 

@property (nonatomic,assign) float balance;

@end

 Account.m

#import "Account.h"

 @implementation

 Account

 @end

 Person.h

#import <Foundation/Foundation.h>

 @class Account; 

@interface Person : NSObject{ 

@private  int _age;_

 }

#pragma mark - 属性

#pragma mark 姓名

@property (nonatomic,copy) NSString *name;

#pragma mark 账户

@property (nonatomic,retain) Account *account; 

#pragma mark - 公共方法

#pragma mark 显示人员信息

-(void)showMessage;

@end

Person.m

#import "Person.h"

@implementation Person

#pragma mark - 公共方法
#pragma mark 显示人员信息
-(void)showMessage{ 
NSLog(@"name=%@,age=%d",_name,_age);}
@end

main.m

#import <Foundation/Foundation.h>

#import "Person.h"

#import "Account.h"

int main(int argc, const char * argv[]) {

 @autoreleasepool {        

 Person *person1=[[Person alloc]init]; 

 [person1 setValue:@"Kenshin" forKey:@"name"]; 

 [person1 setValue:@28 forKey:@"age"];

 //注意即使一个私有变量仍然可以访问 

[person1 showMessage]; 

//结果：name=Kenshin,age=28 

NSLog(@"person1's name is :%@,age is :%@",person1.name,[person1 valueForKey:@"age"]);

//结果：person1's name is :Kenshin,age is :28 

Account *account1=[[Account alloc]init];

person1.account=account1;

//注意这一步一定要先给account属性赋值，否则下面按路径赋值无法成功，因为account为nil，当然这一步骤也可以写成:[person1 setValue:account1 forKeyPath:@"account"];

[person1 setValue:@100000000.0 forKeyPath:@"account.balance"];
NSLog(@"person1's balance is :%.2f",[[person1 valueForKeyPath:@"account.balance"] floatValue]);

//结果：person1's balance is :100000000.00 
     } 

 return 0;

 }
```

>   KVC使用起来比较简单，但是它如何查找一个属性进行读取呢？具体查找规则（假设现在要利用KVC对a进行读取）：如果是动态设置属性，则优先考虑调用setA方法，如果没有该方法则优先考虑搜索成员变量_a,如果仍然不存在则搜索成员变量a，如果最后仍然没搜索到则会调用这个类的setValue:forUndefinedKey：方法(注意搜索过程中不管这些方法、成员变量是私有的还是公共的都能正确设置)；如果是动态读取属性，则优先考虑调用a方法（属性a的getter方法），如果没有搜索到则会优先搜索成员变量_a，如果仍然不存在则搜索成员变量a，如果最后仍然没搜索到则会调用这个类的valueforUndefinedKey:方法(注意搜索过程中不管这些方法、成员变量是私有的还是公共的都能正确读取)；

## **键值监听KVO**

>   我们知道在WPF、Silverlight中都有一种双向绑定机制，如果数据模型修改了之后会立即反映到UI视图上，类似的还有如今比较流行的基于 MVVM设计模式的前端框架，例如Knockout.js。其实在ObjC中原生就支持这种机制，它叫做Key Value Observing（简称KVO）。KVO其实是一种观察者模式，利用它可以很容易实现视图组件和数据模型的分离，当数据模型的属性值改变之后作为监听器 的视图组件就会被激发，激发时就会回调监听器自身。在ObjC中要实现KVO则必须实现NSKeyValueObServing协议，不过幸运的是 NSObject已经实现了该协议，因此几乎所有的ObjC对象都可以使用KVO。

在ObjC中使用KVO操作常用的方法如下：

注册指定Key路径的监听器：**addObserver: forKeyPath: options: context:**

删除指定Key路径的监听器：**removeObserver: forKeyPath**、**removeObserver: forKeyPath: context:**

回调监听：**observeValueForKeyPath: ofObject: change: context:**

KVO的使用步骤也比较简单：

-   通过addObserver: forKeyPath: options: context:为被监听对象（它通常是数据模型）注册监听器
-   重写监听器的observeValueForKeyPath: ofObject: change: context:方法

```objective-c
 Account.h 
 KVCAndKVO  
 @interface Account : NSObject
 #pragma mark - 属性
 #pragma mark 余额 
 @property (nonatomic,assign) float balance;
 @end
 Account.m
 #import "Account.h"
 @implementation Account
 @end
 Person.h
 #import <Foundation/Foundation.h>
 @class Account;
 @interface Person : NSObject{
 @private  int _age;
 }
 #pragma mark - 属性
 #pragma mark 姓名
 @property (nonatomic,copy) NSString *name;
 #pragma mark 账户
 @property (nonatomic,retain) Account *account;
 #pragma mark - 公共方法
 #pragma mark 显示人员信息
 -(void)showMessage;
 @end
 Person.m
 Person.m
 KVCAndKVO
 #import "Person.h"
 #import "Account.h"
 @implementation Person
 #pragma mark - 公共方法
 #pragma mark 显示人员信息
 -(void)showMessage{   		NSLog(@"name=%@,age=%d",_name,_age);
 }
 #pragma mark 设置人员账户
 -(void)setAccount:(Account *)account{
 _account=account;
 添加对Account的监听
 [self.account addObserver:self forKeyPath:@"balance" options:NSKeyValueObservingOptionNew context:nil]; } #pragma mark - 覆盖方法
 #pragma mark 重写observeValueForKeyPath方法，当账户余额变化后此处获得通知
 -(void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context{
 if([keyPath isEqualToString:@"balance"]){//这里只处理balance属性
NSLog(@"keyPath=%@,object=%@,newValue=%.2f,context=%@",keyPath,object,[[change objectForKey:@"new"] floatValue],context);   }
}
#pragma mark 重写销毁方法
-(void)dealloc{
[self.account removeObserver:self forKeyPath:@"balance"]; 移除监听
[super dealloc];
注意启用了ARC，此处不需要调用
}
@end
main.m
#import <Foundation/Foundation.h>
#import "Person.h"
#import "Account.h"
int main(int argc, const char * argv[]) {   @autoreleasepool {
Person *person1=[[Person alloc]init];    person1.name=@"Kenshin";
Account *account1=[[Account alloc]init];    account1.balance=100000000.0;
person1.account=account1;        account1.balance=200000000.0;
注意执行到这一步会触发监听器回调函数observeValueForKeyPath: ofObject: change: context: 结果：keyPath=balance,object=<Account: 0x100103aa0>,newValue=200000000.00,context=(null)          
}
return 0; 
}
```

>   在上面的代码中我们在给人员分配账户时给账户的balance属性添加了监听，并且在监听回调方法中输出了监听到的信息，同时在对象销毁时移除监听，这就构成了一个典型的KVO应用。