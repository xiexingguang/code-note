#colletion集合介绍
	介绍一些不常见的操作,以及之前学习的时候遗漏的地方。
##1 linkedList
	链表实现,查找没有数组快,插入删除快
	
```java
   public class LinkedeListTest1 {
    public static void main(String[] args) {
        LinkedList linkedList = new LinkedList();
        linkedList.add("zst");
        linkedList.add("stone");
        linkedList.add("jasshine");
        linkedList.add("spener");

        Iterator iterator = linkedList.iterator();
        while (iterator.hasNext()) {
            // linkedList.add("xxg"); 加了这句会包并发修改异常
             System.out.println(iterator.next()); 

        }
        
        // 上面的迭代，在迭代的时候无法set ，add值，会报异常。而ListIterator则可以在
        // 迭代的时候修改增加数据
        ListIterator listIterator = linkedList.listIterator();
        while (listIterator.hasNext()) {
            listIterator.add("xxg");
            System.out.println(listIterator.next());
        }
    }

}
```
##2 hashset
	底层是基于hashmap的
	  无序，不重复。 因为hashset是hash结构，所以存进去的是以key做为hash值，然后处理该值得到组下标。
	所以不是按你放进去的顺序排序的。将实体-->hash-->index-->存储。在hashset中,比较是否是
	同一个对象，首先是比较你的hashcode值，如果hash值相同，才会去比较equlas方法。即比较对象的内容


```
package com.spencer.Algorithm.linelist;

/**
 * Created by apple on 16/8/2.
 */
public class HashSet {
    public static void main(String[] args) {
        java.util.HashSet hashSet = new java.util.HashSet();

        hashSet.add("xxg");
        hashSet.add("xxg");

        String x1 = new String("JASSHINE");
        String x2 = new String("JASSHINE");

        System.out.println(x1.hashCode());
        System.out.println(x2.hashCode());
        hashSet.add(x1);
        hashSet.add(x2);
        System.out.println(hashSet);

        Demon demon = new Demon();
        Demon demon1 = new Demon();
        System.out.println(demon.hashCode());
        System.out.println(demon1.hashCode());
        System.out.println( demon==demon1); //为false
        System.out.println(demon.equals(demon1));

        hashSet.add(demon);
        hashSet.add(demon1);
        System.out.println(hashSet);
    }
}

class Demon{
   
   //重写hashcode
    @Override
    public int hashCode() {
        return 10; 
    }
}

```
结果：
[com.spencer.Algorithm.linelist.Demon@3cd1a2f1, com.spencer.Algorithm.linelist.Demon@2f0e140b, xxg, JASSHINE]
###注意
1.hashcode 相同  并不代表 demon == demon1 会相等。因为 == 比较的不是hashcode 值。==比较的是对象的引用,即真正的对象存储的内存地址。

2.对应元素的添加，删除，修改都是依赖元素的hashcode，equals。 即比如删除一个元素，首先判断元素的hashcode是否能在hashset找到，找到以后再看equals是否一致，如果一致在删除
##3 TreeSet
	set 结构，无序，不重复，但内部元素可以排序，加入到里面的对象需要实现comparable接口
	
```java
	public class TreeSetTest1 {
    public static void main(String[] args) {
        TreeSet treeSet = new TreeSet();
        //treeSet.add("abc");
       // treeSet.add("abc");
      //  treeSet.add(new Object());
       // treeSet.add(new Object());
     //   treeSet.add("def");
       // treeSet.add("mgk");
        treeSet.add(new TestDemon(1));
        treeSet.add(new TestDemon(4));
        treeSet.add(new TestDemon(7));
        treeSet.add(new TestDemon(1));
        Iterator iterator = treeSet.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next()); //set 是无序的，所以打印的顺序跟添加的顺序不一样
        }

       // System.out.println(treeSet.);
        System.out.println(treeSet);
    }
}


class TestDemon implements  Comparable {
    private int age;
    public TestDemon(int age) {
        this.age = age;
    }
    @Override
    public int compareTo(Object o) {
        if (!(o instanceof TestDemon)) {
            throw new RuntimeException();
        }
        TestDemon testDemon = (TestDemon) o;
        if (testDemon.age > this.age) {
            return 1;
        } else if (testDemon.age == this.age) {
            return 0;
        }else{
            return -1;
        }
    }

    public String toString() {
        return "testDemon :" + this.age;
    }
}
```

