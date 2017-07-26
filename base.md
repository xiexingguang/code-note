# 基础常识
## 1 equals,==
equals比较的是内容，==比较的是内存地址。Object类的equals方法比较的是地址.所以子类如果要实现自己的相等逻辑就必须重写equlas。因为不重写默认是父类的equals方法比较的是地址。

为什么重写equals时要重写hashCode?

+ 1. 因为在Object类中，equals比较的是地址,hashCode返回得也是对象的地址这样逻辑和物理一致。后续的子类也最好保证逻辑地址和物理地址一致。
+ 2. hashCode相等那么对象肯定是同一个对象。
+ 3. 重写equals方法只是保证后续保证我们的业务类的相同,比如我们认为学生年纪和名称相同就是同一个学生。所以我们要学生类重写equals去表达这个概念。但是重写完equals我们最好保证逻辑上相等的对象最好物理地址也相等。所以我们最好重写hashCode。

	
代码理解

```java
 HashSet hashSet = new HashSet();
        Student student = new Student(12, "jasshine");
        Student student1 = new Student(12, "jasshine");

        hashSet.add(student);
        hashSet.add(student1);
       
       
 class Student {

    private int age;
    private String name;

    public Student(int age, String name) {
        this.age = age;
        this.name = name;
    }
}
        
```
以hashset为例,我们知道它是里面的对象不重复的.但我们打印结果发现set里面存储了2个student对象。但是student对象明显是2个相同的对象啊,年龄姓名都一样。

解释：因为hashset 首先是通过对象的hashcode去查找地址,找到后在通过对象的equals去比较。所以由于student对象的hashcode是继承父类的所以比较的是地址。那么这2个对象的hashcode肯定是不一样的。所以会put进去。我们新建的对象需要让他们相等,这个是我们业务上认为的相同,所以我们需要重写equals方法
如下。

```java
 @Override
    public boolean equals(Object o) {
        if (o instanceof Student) {
            Student s1 = (Student)o;
            if (s1.age == age && s1.name.equals(name)) {
                return true;
            }
        }
        return false;
    }
     @Override
    public int hashCode() {
        return age*12;
    }
```
如果不重写hashcode 发现hashset里面存储的还是2个对象。为什么？这很好理解啊，因为虽然equals上你认为相等了,但是hashset用的hashcode做为key去查找。对象的hashCode不等用的还是父类的hashCode呀
所以必须重写hashCode方法




