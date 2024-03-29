# Java的三大特性：封装、继承、多态

继承：基于已有的类创建新的类，继承已存在的类就是复用（继承）这些类的方法，而且可以增加一些新的方法和字段，使新的类可以适应新的情况。

**继承的设计技巧：**

1.  将公共操作和字段放在超类中。

2.  不要使用受保护的字段。

3.  使用继承实现“is-a”关系。

4.  除非所有继承的方法都有意义，否则不要使用继承。

5.  在覆盖方法的时候，不要改变预期的行为。

6.  使用多态，而不要使用类型信息。

    1.  使用多态固有的动态分派机制执行正确的动作。

    2.  使用多态方法或者接口实现的代码比使用多个类型检测的代码更易于维护和扩展。

7.  不要滥用反射。

接口中的所有方法都自动是 public 方法。因此，在接口中声明方法时，不必提供关键字public。
