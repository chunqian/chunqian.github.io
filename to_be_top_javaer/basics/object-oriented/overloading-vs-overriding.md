重载（Overloading）和重写（Overriding）是Java中两个比较重要的概念。但是对于新手来说也比较容易混淆，本文就举两个实际的例子，来说明下到底是什么是重写和重载。

## 定义

首先我们分别来看一下重载和重写的定义：

重载：指的是在同一个类中，多个函数或者方法有同样的名称，但是参数列表不相同的情形，这样的同名不同参数的函数或者方法之间，互相称之为重载函数或者方法。

重写：指的是在Java的子类与父类中有两个名称、参数列表都相同的方法的情况。由于他们具有相同的方法签名，所以子类中的新方法将覆盖父类中原有的方法。

## 重载的例子

```java
class Dog{
    public void bark(){
        System.out.println("woof ");
    }

    //overloading method
    public void bark(int num){
        for(int i=0; i<num; i++)
            System.out.println("woof ");
    }
}
```
    

上面的代码中，定义了两个bark方法，一个是没有参数的bark方法，另外一个是包含一个int类型参数的bark方法。我们就可以说这两个方法是重载方法，因为他们的方法名相同，参数列表不同。

在编译期，编译期可以根据方法签名（方法名和参数情况）情况确定具体哪个bark方法被调用。

方法重载的条件需要具备以下条件和要求：

1、被重载的方法必须改变参数列表；
2、被重载的方法可以改变返回类型；
3、被重载的方法可以改变访问修饰符；
4、被重载的方法可以声明新的或更广的检查异常；
5、方法能够在同一个类中或者在一个子类中被重载。

## 重写的例子

下面是一个重写的例子，看完代码之后不妨猜测一下输出结果：

```java
class Dog{
    public void bark(){
        System.out.println("woof ");
    }
}
class Hound extends Dog{
    public void sniff(){
        System.out.println("sniff ");
    }

    public void bark(){
        System.out.println("bowl");
    }
}

public class OverridingTest{
    public static void main(String [] args){
        Dog dog = new Hound();
        dog.bark();
    }
}
```
    

输出结果：

    bowl
    

上面的例子中，我们分别在父类、子类中都定义了bark方法，并且他们都是无参方法，所以我们就说这种情况就是方法重写。即子类Hound重写了父类Gog中的bark方法。

在测试的main方法中，`dog`对象被定义为`Dog`类型。

在编译期，编译器会检查Dog类中是否有可访问的`bark()`方法，只要其中包含`bark（）`方法，那么就可以编译通过。

在运行期，`Hound`对象被`new`出来，并赋值给`dog`变量，这时，JVM是明确的知道`dog`变量指向的其实是`Hound`对象的引用。所以，当`dog`调用`bark()`方法的时候，就会调用`Hound`类中定义的`bark（）`方法。这就是所谓的动态多态性。

方法重写的条件需要具备以下条件和要求：

1、参数列表必须完全与被重写方法的相同；
2、返回类型必须完全与被重写方法的返回类型相同；
3、访问级别的限制性一定不能比被重写方法的强；
4、访问级别的限制性可以比被重写方法的弱；
5、重写方法一定不能抛出新的检查异常或比被重写的方法声明的检查异常更广泛的检查异常
6、重写的方法能够抛出更少或更有限的异常（也就是说，被重写的方法声明了异常，但重写的方法可以什么也不声明）
7、不能重写被标示为final的方法；
8、如果不能继承一个方法，则不能重写这个方法。