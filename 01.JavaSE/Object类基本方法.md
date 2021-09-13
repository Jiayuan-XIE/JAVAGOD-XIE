# Object基本方法
## 1. equals()
作用：比较两个对象是否相等（引用的值，理解为对象的地址）

在用==对两个变量做比较的时候，我们比较的总是他们的值（对象的引用是地址值）。

而用equals()方法对对象做比较的时候（如果方法没有被重写），实际还是用==做的比较。
````java
public boolean equals(Object obj) {
     return (this == obj);
}
````

如果你想判断一个自定义类的两个不同的对象是否相等，就需要重写这个方法。
如：
````java
Person p1 = new Person("li",18);
Person p2 = new Person("li",18);
System.out.println(p1.equals(p2));
````
结果:false

重写equals()方法：
````java
 /**
         * @desc 覆盖equals方法
         */
        @Override
        public boolean equals(Object obj){
            if(obj == null){
                return false;
            }

            //如果是同一个对象返回true，反之返回false
            if(this == obj){
                return true;
            }

            //判断是否类型相同
            if(this.getClass() != obj.getClass()){
                return false;
            }

            Person person = (Person)obj;
            return name.equals(person.name) && age==person.age;
        }
````
再来测试
````java
Person p1 = new Person("li",18);
Person p2 = new Person("li",18);
System.out.println(p1.equals(p2));
````
结果:true

### ![image-20210705154031875](http://note.youdao.com/yws/public/resource/7d81e6a39024a96dd86efacf29f4ca80/xmlnote/WEBRESOURCE44c2dc5dcc804df0b1d0bf196473de7d/397)





## 2. hashCode()

作用：**获取哈希码**，这个码表示它在散列表中的位置。

也就是说：**hashCode() 在散列表中才有用，在其它情况下没用。**

而Java中，用到的散列表的主要由几个集合类，如HashMap、HashSet、HashTable。

因为我们都知道Set集合是不包含重复元素的，而判断待插入元素是否存在于集合之中，就需要使用`hashCode()`这个方法来协助判断了。

HashMap中的源代码，HashSet也是调用的这个方法。

````java
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
````



### equals()与hashCode()

为什么说实现equals()方法之前，需要先实现hashCode()方法？

上面说到，hashCode()在散列表中才有作用，在其他情况下没用。

所以，分两种情况：

1. 如果我们没有用到HashMap、HashSet、HashTable等集合类

   其实它们是没有任何关系的，这种情况下，equals()只会判断两个对象是否相等，而hashCode()并没有参与。

----

2. 我们用到了这些集合类

   **核心**：HashSet在判断两个对象是否相等时，会先判断他们的hash值，如果相等，再用equals()进行判断。

   因为两个相同的对象，他们的hash值必定相同，反之不然（不同的对象可能会产生**哈希碰撞**）。如果hash值相等了，再用equals判断是否是真的相等。

   如果你只重写equals()方法，而不重写hashCode()方法，则在判断的第一步就被卡住了(即equals认为的相等，而hash值不同)

   ----

   上述的源代码里可以看到，在判断待插入对象是否已存在于该集合中，用到了hashCode()这个函数协助判断。

   而假如我们重写了equals()方法：认为两个Person类的实例，只要他们的姓名和年龄相等，就认为这两个人是同一个人。

   

   那按理说，我们把这两个相等的对象（我们自定义的相等），放入集合中，应该只保存一个。

   其实不然，实际集合类保存了两个，因为这只是我们认为的相等（自以为是,自定义的equals）， 而集合类是先通过hashCode()方法返回的哈希值来判断这两个对象是否相等，如果相同，再用equals()方法进行判断（因为只用hash值来判断不全面，会出现两个不同的对象碰撞的情况，所以再用equals()再次判断，这样做的目的是为了减少equals的次数，从而大大提升执行速度）。

   

   这就出现了一个问题：

   集合类认为这两个对象是不相同的（因为Object类的hashCode()是按照对象的地址来哈希的，在hash值这里就被卡住了），这个方法如果没有被重写，那么这两个对象（我们认为相等的对象）就会同时出现在集合里（集合类并不知道我们认为他俩相同，因为集合类是通过hash值来判断的，而这个方法我们并没有重写）。

如何解决这个问题呢?

那就是在实现equals()方法之前，需要先实现hashCode()方法。      

让这两个方法保持**思想一致**。



代码如下：

````java
import java.util.*;
import java.lang.Comparable;


public class ConflictHashCodeTest2{

    public static void main(String[] args) {
        // 新建Person对象，
        Person p1 = new Person("eee", 100);
        Person p2 = new Person("eee", 100);
        Person p3 = new Person("aaa", 200);
        Person p4 = new Person("EEE", 100);

        // 新建HashSet对象
        HashSet set = new HashSet();
        set.add(p1);
        set.add(p2);
        set.add(p3);

        // 比较p1 和 p2， 并打印它们的hashCode()
        System.out.printf("p1.equals(p2) : %s; p1(%d) p2(%d)\n", p1.equals(p2), p1.hashCode(), p2.hashCode());
        // 比较p1 和 p4， 并打印它们的hashCode()
        System.out.printf("p1.equals(p4) : %s; p1(%d) p4(%d)\n", p1.equals(p4), p1.hashCode(), p4.hashCode());
        // 打印set
        System.out.printf("set:%s\n", set);
    }

    /**
     * @desc Person类。
     */
    private static class Person {
        int age;
        String name;

        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String toString() {
            return name + " - " +age;
        }

        /**
         * @desc重写hashCode
         */
        @Override
        public int hashCode(){
            int nameHash =  name.toUpperCase().hashCode();
            return nameHash ^ age;
        }

        /**
         * @desc 覆盖equals方法
         */
        @Override
        public boolean equals(Object obj){
            if(obj == null){
                return false;
            }

            //如果是同一个对象返回true，反之返回false
            if(this == obj){
                return true;
            }

            //判断是否类型相同
            if(this.getClass() != obj.getClass()){
                return false;
            }

            Person person = (Person)obj;
            return name.equals(person.name) && age==person.age;
        }
    }
}
````

此段代码来自[博客](https://www.cnblogs.com/skywang12345/p/3324958.html)

为什么先用hash值来判断？

答：**设计的层面**，因为两个相同的对象，他们的hash值必定相同，反之不然（不同的对象可能产生**哈希碰撞**）。如果hash值相等了，再用equals判断是否是真的相等。

hashCode返回的是个数字，一般情况下效率会高于equals方法。











