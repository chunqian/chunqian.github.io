Java中共有三种变量，分别是类变量、成员变量和局部变量。他们分别存放在JVM的方法区、堆内存和栈内存中。
```java
    /**
     * @author Hollis
     */
    public class Variables {
    
        /**
         * 类变量
         */
        private static int a;
    
        /**
         * 成员变量
         */
        private int b;
    
        /**
         * 局部变量
         * @param c
         */
        public void test(int c){
            int d;
        }
    }
```    
上面定义的三个变量中，变量a就是类变量，变量b就是成员变量，而变量c和d是局部变量。

a作为类变量，他存放在方法区中；b作为成员变量，和对象一起存储在堆内存中（不考虑栈上分配的情况）；c和d作为方法的局部变量，保存在栈内存中。

之所以要在这一章节重点介绍下这三种变量类型，是因为很多人因为不知道这三种类型的区别，所以不知道他们分别存放在哪里，这就导致不知道那些变量需要考虑并发问题。

关于并发问题，目前本书《基本篇》还不涉及，会在下一本《并发篇》中重点介绍，这里先简单说明一下：

因为只有共享变量才会遇到并发问题，所以，变量a和b是共享变量，变量c和d是非共享变量。所以如果遇到多线程场景，对于变量a和b的操作是需要考虑线程安全的，而对于线程c和d的操作是不需要考虑线程安全的。