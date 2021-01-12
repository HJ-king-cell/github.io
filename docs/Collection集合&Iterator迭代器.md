
# 第一章 BigInteger类

## 1.1 概述

java.math.BigInteger 类，不可变的任意精度的整数。如果运算中，数据的范围超过了long类型后，可以使用
BigInteger类实现，该类的计算整数是不限制长度的。



## 1.2 构造方法

| 方法                     | 说明                     |
| ------------------------ | ------------------------ |
| BigInteger(String value) | 可以把字符串整数变成对象 |

BigInteger(String value) 将 BigInteger 的十进制字符串表示形式转换为 BigInteger。超过long类型的范围，已经不能称为数字了，因此构造方法中采用字符串的形式来表示超大整数，将超大整数封装成BigInteger对象。



## 1.3 成员方法

BigInteger类提供了对很大的整数进行加、减、乘、除的方法，注意：都是与另一个BigInteger对象进行运算。

| 方法声明                   | 描述                                                         |
| -------------------------- | ------------------------------------------------------------ |
| add(BigInteger value)      | 返回其值为 (this + val) 的 BigInteger，超大整数加法运算      |
| subtract(BigInteger value) | 返回其值为 (this - val) 的 BigInteger，超大整数减法运算      |
| multiply(BigInteger value) | 返回其值为 (this * val) 的 BigInteger，超大整数乘法运算      |
| divide(BigInteger value)   | 返回其值为 (this / val) 的 BigInteger，超大整数除法运算，除不尽取整数部分 |

【示例】

~~~java
package com.itheima.sh.demo_03;

import java.math.BigInteger;

public class Test01 {
    public static void main(String[] args) {
        //基本类型的整数都有大小限制
        //int a = 10000000000;
        //long b = 10000000000L;

        //BigInteger表示一个无限大的整数
        BigInteger big1 = new BigInteger("23454375347534975347534");
        BigInteger big2 = new BigInteger("23454375347534343433343");
        //加法运算
        BigInteger add = big1.add(big2);
        System.out.println("求和:" + add);
        //减法运算
        BigInteger sub = big1.subtract(big2);
        System.out.println("求差:" + sub);
        //乘法运算
        BigInteger mul = big1.multiply(big2);
        System.out.println("乘积:" + mul);
        //除法运算(java中整数计算的结果还是整数)
        BigInteger div = big1.divide(big2);
        System.out.println("除法:" + div);
    }
}
~~~





# 第二章 BigDecimal类

- 概述：

  表示无限大的小数类型,使用基本类型做浮点数运算精度问题.对于浮点运算，不要使用基本类型，而使用BigDecimal类类型不会损失精度。

- 构造方法:

| 方法                   | 说明                                   |
| ---------------------- | -------------------------------------- |
| BigDecimal(double val) | 把小数数值变成BigDecimal对象           |
| BigDecimal(String val) | 把字符串值变成BigDecimal对象(建议使用) |

- 常用方法:


| 方法                                                         | 说明     |
| ------------------------------------------------------------ | -------- |
| BigDecimal add(BigDecimal value)                             | 加法运算 |
| BigDecimal subtract(BigDecimal value)                        | 减法运算 |
| BigDecimal multiply(BigDecimal value)                        | 乘法运算 |
| BigDecimal divide(BigDecimal value)                          | 除法运算 |
| BigDecimal divide(BigDecimal divisor, int scale, int roundingMode) | 除法运算 |

注意：对于divide方法来说，如果除不尽的话，就会出现java.lang.ArithmeticException异常。此时可以使用divide方法的另一个重载方法；

​	BigDecimal divide(BigDecimal divisor, int scale, int roundingMode): 	参数说明：divisor：除数对应的BigDecimal对象；scale:精确的位数；roundingMode取舍模式，表示舍入方式 RoundingMode.HALF_UP四舍五入

- 示例代码:

  ```java
  public class Demo02_BigDecimal {
      public static void main(String[] args) {
  
          //创建对象
          //基本类型做浮点数运算精度问题
          //小数在底层存储时无法存储一个精确的值
          //比如我们写一个0.09在底层可能存储的是一个无限接近0.09的数值
          System.out.println(10.0 / 3);       //3.3333333333333335
          System.out.println(0.09 + 0.01);   //0.09999999999999999
          //System.out.println(1.2+1.3);
  
          //表示一个无限大的小数并且没有计算错误
          BigDecimal b1 = new BigDecimal("0.09");
          BigDecimal b2 = new BigDecimal("0.01");
  
          //加法
          System.out.println(b1.add(b2));
          //减法
          System.out.println(b1.subtract(b2));
          //乘法
          System.out.println(b1.multiply(b2));
          //除法
          System.out.println(b1.divide(b2));
  
          //--------------------------------------------
          //BigDecimal是精确的数值
          BigDecimal b3 = new BigDecimal("10.0");
          BigDecimal b4 = new BigDecimal("3.0");
  
          //除法
          //BigDecimal divide(BigDecimal value)方法如果无法表示准确的商值（则抛出 ArithmeticException。 
          //System.out.println(b3.divide(b4));  //ArithmeticException
  
          //除法运算 保留2位小数 四舍五入
          System.out.println(b3.divide(b4,2, RoundingMode.HALF_UP));
      }
  }
  ```

**小结：Java中小数运算有可能会有精度问题，如果要解决这种精度问题，可以使用BigDecimal**		     



# 第三章  包装类

## 3.1 概述

Java提供了两个类型系统，基本类型与引用类型，使用基本类型在于效率，然而很多情况，会创建对象使用，因为对象可以做更多的功能，如果想要我们的基本类型像对象一样操作，就可以使用基本类型对应的包装类，如下：

| 基本类型 | 对应的包装类（位于java.lang包中） |
| -------- | --------------------------------- |
| byte     | Byte                              |
| short    | Short                             |
| int      | **Integer**                       |
| long     | Long                              |
| float    | Float                             |
| double   | Double                            |
| char     | **Character**                     |
| boolean  | Boolean                           |



## 3.2 Integer类

- Integer类概述

  包装一个对象中的原始类型 int 的值

- Integer类构造方法及静态方法

| 方法名                                  | 说明                                   |
| --------------------------------------- | -------------------------------------- |
| public Integer(int   value)             | 根据 int 值创建 Integer 对象(过时)     |
| public Integer(String s)                | 根据 String 值创建 Integer 对象(过时)  |
| public static Integer valueOf(int i)    | 返回表示指定的 int 值的 Integer   实例 |
| public static Integer valueOf(String s) | 返回保存指定String值的 Integer 对象    |

- 示例代码

```java
public class IntegerDemo {
    public static void main(String[] args) {
        //public Integer(int value)：根据 int 值创建 Integer 对象(过时)
        Integer i1 = new Integer(100);
        System.out.println(i1);

        //public Integer(String s)：根据 String 值创建 Integer 对象(过时)
        Integer i2 = new Integer("100");
		//Integer i2 = new Integer("abc"); //NumberFormatException
        System.out.println(i2);
        System.out.println("--------");

        //public static Integer valueOf(int i)：返回表示指定的 int 值的 Integer 实例
        Integer i3 = Integer.valueOf(100);
        System.out.println(i3);

        //public static Integer valueOf(String s)：返回保存指定String值的Integer对象 
        Integer i4 = Integer.valueOf("100");
        System.out.println(i4);
    }
}
```



## 3.3 装箱与拆箱

基本类型与对应的包装类对象之间，来回转换的过程称为”装箱“与”拆箱“：

- **装箱**：从基本类型转换为对应的包装类对象。

  用Integer与 int为例：（看懂代码即可）

  基本数值---->包装对象

  ```java
  Integer i = new Integer(4);//使用构造函数函数
  Integer iii = Integer.valueOf(4);//使用包装类中的valueOf方法
  ```

  

- **拆箱**：从包装类对象转换为对应的基本类型。

包装对象---->基本数值

```java
int num = i.intValue();
```



## 3.4 自动装箱与自动拆箱

由于我们经常要做基本类型与包装类之间的转换，从Java 5（JDK 1.5）开始，基本类型与包装类的装箱、拆箱动作可以自动完成。例如：

```java
Integer i = 4;//自动装箱。相当于Integer i = Integer.valueOf(4);
i = i + 5;//等号右边：将i对象转成基本数值(自动拆箱) i.intValue() + 5;
//加法运算完成后，再次装箱，把基本数值转成对象。
```



## 3.5 基本类型与字符串之间的转换

### 基本类型转换为String

- 转换方式
- 方式一：直接在数字后加一个空字符串
- 方式二：通过String类静态方法valueOf()
- 示例代码

```java
public class IntegerDemo {
    public static void main(String[] args) {
        //int --- String
        int number = 100;
        //方式1
        String s1 = number + "";
        System.out.println(s1);
        //方式2
        //public static String valueOf(int i)
        String s2 = String.valueOf(number);
        System.out.println(s2);
        System.out.println("--------");
    }
}
```

### String转换成基本类型 

除了Character类之外，其他所有包装类都具有parseXxx静态方法可以将字符串参数转换为对应的基本类型：

- `public static byte parseByte(String s)`：将字符串参数转换为对应的byte基本类型。
- `public static short parseShort(String s)`：将字符串参数转换为对应的short基本类型。
- **`public static int parseInt(String s)`：将字符串参数转换为对应的int基本类型。**
- **`public static long parseLong(String s)`：将字符串参数转换为对应的long基本类型。**
- `public static float parseFloat(String s)`：将字符串参数转换为对应的float基本类型。
- `public static double parseDouble(String s)`：将字符串参数转换为对应的double基本类型。
- `public static boolean parseBoolean(String s)`：将字符串参数转换为对应的boolean基本类型。

代码使用（仅以Integer类的静态方法parseXxx为例）如：

- 转换方式
  - 方式一：先将字符串数字转成Integer，再调用valueOf()方法
  - **方式二：通过Integer静态方法parseInt()进行转换**
- 示例代码

```java
public class IntegerDemo {
    public static void main(String[] args) {
        //String --- int
        String s = "100";
        //方式1：String --- Integer --- int
        //Integer i = Integer.valueOf(s);
        //public int intValue()
        //int x = i.intValue();
        int x = Integer.valueOf(s);
        System.out.println(x);
        //方式2
        //public static int parseInt(String s)
        int y = Integer.parseInt(s);
        System.out.println(y);
    }
}
```

> 注意:如果字符串参数的内容无法正确转换为对应的基本类型，则会抛出`java.lang.NumberFormatException`异常。

# 第四章 Collection集合

## 4.1 集合概述

在前面我们已经学习过并使用过集合ArrayList<E> ,那么集合到底是什么呢?

- **集合**：集合是java中提供的一种容器，可以用来存储多个数据。

集合和数组既然都是容器，它们有什么区别呢？

- 数组的长度是固定的。集合的长度是可变的。
- 数组中存储的是同一类型的元素，可以存储任意类型数据。集合存储的都是引用数据类型。如果想存储基本类型数据需要存储对应的包装类型。

~~~java
存储整数：int[]       存储字符串：String[]
存储整数：ArrayList<Integer>    存储字符串：ArrayList<String>
~~~



## 4.2  集合常用类的继承体系

Collection：单列集合类的根接口，用于存储一系列符合某种规则的元素，它有两个重要的子接口，分别是`java.util.List`和`java.util.Set`。其中，**`List`的特点是元素存取有序、元素可重复 ; `Set`的特点是元素存取无序，元素不可重复**。`List`接口的主要实现类有`java.util.ArrayList`和`java.util.LinkedList`，`Set`接口的主要实现类有`java.util.HashSet`和`java.util.LinkedHashSet`。

从上面的描述可以看出JDK中提供了丰富的集合类库，为了便于初学者进行系统地学习，接下来通过一张图来描述集合常用类的继承体系

![Collection集合体系图](picture/javase/迭代器/Collection集合体系图.jpg)

注意:这张图只是我们常用的集合有这些，不是说就只有这些集合。

集合本身是一个工具，它存放在java.util包中。在`Collection`接口定义着单列集合框架中最最共性的内容。



## 4.3 Collection 常用功能

Collection是所有单列集合的父接口，因此在Collection中定义了单列集合(List和Set)通用的一些方法，这些方法可用于操作所有的单列集合。方法如下：

- `public boolean add(E e)`：  把给定的对象添加到当前集合中 。
- `public void clear()` :清空集合中所有的元素。
- `public boolean remove(E e)`: 把给定的对象在当前集合中删除。
- `public boolean contains(Object obj)`: 判断当前集合中是否包含给定的对象。
- `public boolean isEmpty()`: 判断当前集合是否为空。
- `public int size()`: 返回集合中元素的个数。
- `public Object[] toArray()`: 把集合中的元素，存储到数组中

> tips: 有关Collection中的方法可不止上面这些，其他方法可以自行查看API学习。

```java
public class Demo02ListMethod {
    public static void main(String[] args) {
         //多态写法
        Collection<String> c = new ArrayList<>();

        //boolean add(E e)
        //添加方法(添加成功会返回true,添加失败会返回false.但是ArrayList只会成功不会失败所以返回永远是true,不接受这个返回值)
        c.add("石原里美");
        c.add("新垣结衣");
        c.add("石原里美");

        //void clear()
        //清空集合中的元素
        //c.clear();      //调用后集合被清空

        //boolean remove(Object e)
        //删除集合中的某个元素(返回的是true或false代表删除成功或失败,只会删除第一个匹配的元素)
        c.remove("柳岩");         //没有柳岩,删除没作用
        c.remove("石原里美");     //集合中有两个“石原里美”,会删除第一个

        //boolean contains(Object obj)
        //判断集合是否包含某个元素
        boolean b = c.contains("石原里美");
        System.out.println(b);     //集合中包含有“石原里美”这个元素所以结果是true


        //boolean isEmpty()
        //判断集合是否为空
        boolean b2 = c.isEmpty();
        System.out.println(b2);    //集合里面有元素,不为空  结果是false

        //int size()
        //获取集合的长度
        int size = c.size();
        System.out.println(size); //2

        //Object[] toArray()
        //把集合转成Object[]类型
        Object[] arr = c.toArray();
        //打印数组
        System.out.println(Arrays.toString(arr));

        //如果想要转成具体的类型,用到了一个高级写法,我现在可以给演示一下,不讲原理
        String[] strs = c.toArray(new String[c.size()]);
        System.out.println(Arrays.toString(strs));
    }
}
```



# 第五章 Iterator迭代器

## 5.1 Iterator接口

在程序开发中，经常需要遍历集合中的所有元素。针对这种需求，JDK专门提供了一个接口`java.util.Iterator`。

想要遍历Collection集合，那么就要获取该集合迭代器完成迭代操作，下面介绍一下获取迭代器的方法：

| 方法                       | 说明                                           |
| -------------------------- | ---------------------------------------------- |
| public Iterator iterator() | 获取集合对应的迭代器，用来遍历集合中的元素的。 |

下面介绍一下迭代的概念：

- **迭代**：即Collection集合元素的通用获取方式。在取元素之前先要判断集合中有没有元素，如果有，就把这个元素取出来，继续再判断，如果还有就再取出来。一直把集合中的所有元素全部取出。这种取出方式专业术语称为迭代。

  **注意：调用next()方法获取数据之前一定先调用hasNext()方法判断有没有有数据，否则会报异常。**

Iterator接口的常用方法如下：

| 方法              | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| E next()          | 获取集合中的元素                                             |
| boolean hasNext() | 判断集合中有没有下一个元素，如果仍有元素可以迭代，则返回 true。 |
| void remove()     | 删除当前元素                                                 |

接下来我们通过案例学习如何使用Iterator迭代集合中元素：

代码如下：

```java
public class Demo02 {
    public static void main(String[] args) {
        // 使用多态方式 创建对象
        Collection<String> coll = new ArrayList<String>();
        // 添加元素到集合
        coll.add("aaaa");
        coll.add("bbbb");
        coll.add("cccc");
        //根据当前集合对象生成迭代器对象
        Iterator<String> it = coll.iterator();
        //获取数据
        System.out.println(it.next());
        System.out.println(it.next());
        System.out.println(it.next());
        System.out.println(it.next());
    }
}
```

运行结果：

```java
aaaa
bbbb
cccc
Exception in thread "main" java.util.NoSuchElementException
	at java.base/java.util.ArrayList$Itr.next(ArrayList.java:896)
	at com.itheima.sh.demo_03.Demo02.main(Demo02.java:22)

```

分析异常的原因：

![迭代器迭代集合的异常](picture/javase/迭代器/迭代器迭代集合的异常.jpg)

说明：当迭代器对象指向集合时，可以获取集合中的元素，如果迭代器的光标移动集合的外边时，此时迭代器对象不再指向集合中的任何元素，会报NoSuchElementException没有这个元素异常。

**解决方案：在使用next()函数获取集合中元素前，使用hasNext()判断集合中是否还有元素。**

上述代码一条语句重复执行多次，我们可以考虑使用循环来控制执行的次数，

循环条件是 **迭代器对象.hasNext()** 为false时表示集合中没有元素可以获取了，循环条件迭代器对象.hasNext()  为true的时候说明还可以获取元素。

代码演示如下：

~~~java
package cn.itcast.sh.iterator;
import java.util.ArrayList;
import java.util.Collection;
import java.util.Iterator;
/*
 * 演示迭代器的使用
 */
public class IteratorDemo {
	public static void main(String[] args) {
		//创建集合对象
		Collection coll=new ArrayList();
		//向集合中添加数据
		coll.add("aaaa");
		coll.add("bbbb");
		coll.add("cccc");
		//根据当前集合获取迭代器对象
		Iterator it = coll.iterator();
		//取出数据
		/*System.out.println(it.next());//it.next()表示获取迭代器对象指向集合中的数据
		System.out.println(it.next());
		System.out.println(it.next());
		System.out.println(it.next());*/
		//使用while循环遍历集合
		while(it.hasNext())//it.hasNext()表示循环条件，如果为true，说明集合中还有元素可以获取，否则没有元素
		{
			//获取元素并输出
			System.out.println(it.next());
		}
	}
}
~~~

> tips: 
>
> 1.使用while循环迭代集合的快捷键：itit
>
> 2.在进行集合元素获取时，如果集合中已经没有元素了，还继续使用迭代器的next方法，将会抛出java.util.NoSuchElementException没有集合元素异常。



## 5.2 迭代器的实现原理

 我们在之前案例已经完成了Iterator遍历集合的整个过程。当遍历集合时，首先通过调用集合的iterator()方法获得迭代器对象，然后使用hashNext()方法判断集合中是否存在下一个元素，如果存在，则调用next()方法将元素取出，否则说明已到达了集合末尾，停止遍历元素。

​        Iterator迭代器对象在遍历集合时，内部采用指针的方式来跟踪集合中的元素。在调用Iterator的next方法之前，迭代器的索引位于第一个元素之前，不指向任何元素，当第一次调用迭代器的next方法后，迭代器的索引会向后移动一位，指向第一个元素并将该元素返回，当再次调用next方法时，迭代器的索引会指向第二个元素并将该元素返回，依此类推，直到hasNext方法返回false，表示到达了集合的末尾，终止对元素的遍历。

```
1)ctrl + N   搜索  ArrayList这个类
2)在ArrayList里面按 alt + 7 左边点击Itr
3)Itr是ArrayList里面的一个内部类。（这些东西不要求研究）
```

- 源码

```java
public class ArrayList<E>{
	//Itr属于ArrayList集合的成员内部类，实现了迭代器Iterator接口
	private class Itr implements Iterator<E> {
        //定义游标，默认值是0
		int cursor;       // index of next element to return
		int lastRet = -1; // index of last element returned; -1 if no such
        /*
        	1.第一个 modCount 集合结构变动次数，如：一开始add调用了4次，那么这个变量就是4
        	2.第二个 expectedModCount表示期望的更改次数 在调用iterator()方法时，初始化值等于modCount ，初始化时也是4，这里 int expectedModCount = modCount;
        */
		int expectedModCount = modCount;
		//判断集合是否有数据的方法，如果有数据返回true，没有返回false
        //size表示集合长度，假设添加3个数据，那么size的值就是3
		public boolean hasNext() {
            /*
            	判断游标变量cursor是否等于集合长度size,假设向集合中存储三个数据那么size等于3
            	1.第一次cursor的默认值是0 size是3 --》cursor != size --》0!=3-->返回true
            */
		    return cursor != size;
		}

		@SuppressWarnings("unchecked")
		public E next() {
            //主要作用是判断it迭代器数据是否和list集合一致 我们下面会讲解
		    checkForComodification();
            //每次调用一次next方法都会将游标cursor的值赋值给变量i
		    int i = cursor;
            //判断i是否大于等于集合长度size,如果大于等于则报NoSuchElementException没有这个元素异常
		    if (i >= size)
			throw new NoSuchElementException();
            //获取ArrayList集合的数组
		    Object[] elementData = ArrayList.this.elementData;
            //判断i的值是否大于等于数组长度，如果为true则报并发修改异常
		    if (i >= elementData.length)
			throw new ConcurrentModificationException();
            /*
            	将i的值加1赋值给游标cursor.就是将游标向下移动一个索引，可以理解指针向下移动一次
            */
		    cursor = i + 1;
            /*
            	elementData就是集合的底层数组，获取索引i位置的元素返回给调用者
            */
		    return (E) elementData[lastRet = i];
		}
	  }
}
```

- 讲解图

![迭代器原理](picture/javase/迭代器/迭代器原理.png)


## 5.3迭代器的问题：并发修改异常

- 异常：

  ```java
  ConcurrentModificationException(并发修改异常)
  ```

- 产生原因：

  - 在迭代器遍历的同时如果使用集合对象修改**集合长度**(增或删）就会出现并发修改异常。

- 代码演示：

  ```java
  package com.itheima.sh.demo_04;
  
  import java.util.ArrayList;
  import java.util.Collection;
  import java.util.Iterator;
  
  public class Test01 {
      public static void main(String[] args) {
          Collection<String> list = new ArrayList<>();
          list.add("aaa");
          list.add("ddd");
          list.add("bbb");
          list.add("ccc");
          Iterator<String> it = list.iterator();
          while(it.hasNext()){
              String s = it.next();
              if("ddd".equals(s)){
                  //使用集合调用方法删除
                  list.remove(s);
              }
          }
          System.out.println(list);
      }
  }
  
  ```

- 源码

~~~java
public class ArrayList<E>{
    private class Itr implements Iterator<E> {
            int cursor;       // index of next element to return
            int lastRet = -1; // index of last element returned; -1 if no such
         /*
        	1.第一个 modCount 集合结构变动次数，如：一开始add调用了4次，那么这个变量就是4
        	2.第二个 expectedModCount表示期望的更改次数 在调用iterator()方法时，初始化值等于	     modCount ，初始化时也是4，这里 int expectedModCount = modCount;
        */
            int expectedModCount = modCount;

            public boolean hasNext() {
                return cursor != size;
            }

            @SuppressWarnings("unchecked")
            public E next() {
                checkForComodification();
                int i = cursor;
                if (i >= size)
                    throw new NoSuchElementException();
                Object[] elementData = ArrayList.this.elementData;
                if (i >= elementData.length)
                    throw new ConcurrentModificationException();
                cursor = i + 1;
                return (E) elementData[lastRet = i];
            }
         }
}
~~~



~~~java
1.为什么会报并发修改异常?
         1）在Itr内部类中的成员位置有这样一行代码,
                     private class Itr implements Iterator<E> {

                            int expectedModCount = modCount;
                    }
                    由于是成员变量，所以在执行list.iterator()创建It对象时就已经有值了。
                        上述两个变量含义：
                        第一个 modCount 集合结构变动次数，如：一开始add调用了4次，那么这个变量就是4，
                        第二个 expectedModCount 在调用iterator()方法时，初始化值等于modCount ，初始化时也是4，这里 int expectedModCount = modCount;

        2)it.next(): 看源代码可以发现每次在next()调用后，都会先调用checkForComodification()这个方法；
             public E next() {
                // checkForComodification()： 主要作用是判断it迭代器数据是否和list集合一致，
                checkForComodification();
             }
        
       3）在checkForComodification()方法体中有如下代码：
             final void checkForComodification() {
				// 这个方法判断当 modCount ！= expectedModCount 时,
                //抛出异常ConcurrentModificationException：
                if (modCount != expectedModCount)
                    throw new ConcurrentModificationException();
            }

		4)
            //如果我们调用的是集合list的remove方法，那么modCount 就会+1 而expectedModCount 不变，这就会造成 modCount ！= expectedModCount；
                     public boolean remove(Object o) {
                        if (o == null) {
                           -------------
                        } else {
                            -------------删除方法
                                    fastRemove(index);
                            ----------------
                        }
                        return false;
                    }
                    fastRemove(index);方法：
                         private void fastRemove(int index) {
                            //这里加1了
                            modCount++;
                            。。。。。。。。。。。。。
                        }
       
~~~



- 解决办法：

  - 使用集合增加或删除都会出现并发修改异常。
  - 在Iterator迭代器中，有删除方法可以避免异常。但是他没有增加的方法。

  ```java
  public class Test01 {
      public static void main(String[] args) {
          Collection<String> list = new ArrayList<>();
          list.add("aaa");
          list.add("ddd");
          list.add("bbb");
          list.add("ccc");
          Iterator<String> it = list.iterator();
          while(it.hasNext()){
              String s = it.next();
              if("ddd".equals(s)){
                  //使用集合调用方法删除
  //                list.remove(s);
                  //使用迭代器的删除方法
                  it.remove();
            }
          }
          System.out.println(list);
      }
  }
  ```

  - 为什么使用迭代器Iterator中的删除方法就不会报并发修改异常了

    ~~~java
     5）如果我们调用迭代器的remove方法，expectedModCount 会重新赋值，
                迭代器中的remove方法：
                 public void remove() {
                       。。。。。。。。。
                            expectedModCount = modCount;
                       。。。。。。。。。。。。。。。。。
                    }
    ~~~

    

  - 在迭代器中有特殊的子类可以做元素的增加，这个类是ArrayList里面的内部类，叫ListItr.

    因为以后也不用，所以现在不讲了，但是简单扩展一下告诉你有这个东西，以后万一有兴趣大家自己学一下。



## 5.4 增强for

增强for循环(也称for each循环)是**JDK1.5**以后出来的一个高级for循环，专门用来遍历数组和集合的。它的内部原理其实是个Iterator迭代器，所以在遍历的过程中，不能对集合中的元素进行增删操作。

格式：

```java
for(元素的数据类型  变量 : Collection集合or数组){ 
  	//写操作代码
}
```

它用于遍历Collection和数组。通常只进行遍历元素，不要在遍历的过程中对集合元素进行增删操作。

代码演示

```java
public class NBForDemo1 {
    public static void main(String[] args) {
		int[] arr = {3,5,6,87};
       	//使用增强for遍历数组
		for(int a : arr){//a代表数组中的每个元素
			System.out.println(a);
		}
    	
    	Collection<String> coll = new ArrayList<String>();
    	coll.add("小河神");
    	coll.add("老河神");
    	coll.add("神婆");
    	
    	for(String s :coll){
    		System.out.println(s);
    	}
	}
}
```



> tips: 
>
> 1.快捷键：
> 	1)数组/集合.for 
>      2)iter
>
> 2.增强for循环必须有被遍历的目标，目标只能是Collection或者是数组；
>
> 3.增强for（迭代器）仅仅作为遍历操作出现，不能对集合进行增删元素操作，否则抛出ConcurrentModificationException并发修改异常 
>
> 4.如果需要使用索引,也不能使用增强for



> 小结：Collection是所有单列集合的根接口，如果要对单列集合进行遍历，通用的遍历方式是迭代器遍历或增强for遍历。



