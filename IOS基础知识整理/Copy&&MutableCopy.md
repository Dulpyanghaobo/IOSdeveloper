# Copy & MutableCopy

> 使用copy或mutableCopy方法可以创建一个对象的副本

### copy

需要实现NSCoppying协议

这些创建的是不可变副本(如NSString、NSArray、NSDictionary)

### mutableCopy

需要先实现NSMutableCopying协议

创建的是可变副本(如NSMutableString、NSMutableArray、NSMutableDictionary)

> Copy的目的是建立副本，同时修改原始对象和复本不会互相干扰

### 深复制 & 浅复制 

深复制：内容拷贝，源对象和副本指向的是不同的两个对象,源对象引用计数器不变，副本计数器设置为1

浅复制：指针拷贝，源对象和副本指向的是同一个对象,对象的引用计数器+1，其实相当于做了一次retain操作

# 1.只有集合当中是系统元素时

### 不可变对象创建不可变副本(copy)才是浅复制，其他都是深复制

# 2.如果集合当中元素是自定义元素时

### [[NSMutableArray alloc] initWithArray:@[数组元素]] copyItems:YES]

```objective-c
//自定义对象的copyWithZone
- (id)copyWithZone:(NSZone *)zone
{
    Person *p = [[[self class] allocWithZone:zone] init]; 
    
    p.name = self.name; 
    p.age = self.age;
    
    return p;
}
```

