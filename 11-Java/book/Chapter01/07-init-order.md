Java 类初始化顺序
===

对于静态变量、静态初始化块、变量、初始化块、构造器，它们的初始化顺序依次是（静态变量、静态初始化块）>（变量、初始化块）>构造器。我们也可以通过下面的测试代码来验证这一点：

```java
public class InitialOrderTest {
    // 静态变量
    public static String staticField = "Static Variable";
    // 变量
    public String field = "Member Variable";
    // 静态初始化块
    static {
        System.out.println(staticField);
        System.out.println("Static Code Block");
    }

    // 初始化块
    {
        System.out.println(field);
        System.out.println("Member Code Block");
    }

    // 构造器
    public InitialOrderTest() {
        System.out.println("Constructor");
    }

    public static void main(String[] args) {
        new InitialOrderTest();
    }
}
```

输出结果：

1. Static Variable 静态变量
2. Static Code Block 静态初始化块
3. Member Variable 成员变量
4. Member Code Block 初始化块
5. Constructor 构造器


### 继承类

```java
class Parent {
    // 静态变量
    public static String parentStaticField = "Parent Static Variable";
    // 变量
    public String parentField = "Parent Member Variable";

    protected int i = 9;
    protected int j = 0;

    // 静态初始化块
    static {
        System.out.println(parentStaticField);
        System.out.println("Parent Static Code Block");
    }

    // 初始化块
    {
        System.out.println(parentField);
        System.out.println("Parent Static Code Block");
    }

    // 构造器
    public Parent() {
        System.out.println("Parent Constructor");
        System.out.println("i = " + i + ", j = " + j);
        j = 20;
    }
}

public class SubClass extends Parent {
    // 静态变量
    public static String subStaticField = "SubClass Static Variable";
    // 变量
    public String subField = "SubClass Member Variable";

    // 静态初始化块
    static {
        System.out.println(subStaticField);
        System.out.println("SubClass Static Code Block");
    }

    // 初始化块
    {
        System.out.println(subField);
        System.out.println("SubClass Member Code Block");
    }

    // 构造器
    public SubClass() {
        System.out.println("SubClass Constructor");
        System.out.println("i = " + i + ", j = " + j);
    }

    // 程序入口
    public static void main(String[] args) {
        System.out.println("SubClass Main");
        new SubClass();
    }
}
```

运行一下上面的代码，结果马上呈现在我们的眼前：

1. Parent Static Variable 父类静态变量
2. Parent Static Code Block 父类静态变量初始化块
3. SubClass Static Variable 子类静态变量
4. SubClass Static Code Block 子类静态变量初始化块
5. SubClass Main 子类main方法
6. Parent Member Variable 父类成员变量
7. Parent Member Code Block 父类成员变量初始化块
8. Parent Constructor 父类构造器
9. i = 9, j = 0
10. SubClass Member Variable 子类成员变量
11. SubClass Member Code Block 子类成员变量初始化块
12. SubClass Constructor 子类构造器
13. i = 9, j = 20

可见，子类的静态变量和静态初始化块在父类的变量、初始化块和构造器初始化之前就已经完成。
静态变量、静态初始化块，变量、初始化块的初始化顺序取决于它们在类中出现的先后顺序。

##### 执行过程分析

1. 访问静态方法SubClass.main()，装载器会寻找已经编译的SubClass类的代码（即SubClass.class文件）。在装载的过程中，装载器注意到它有一个基类，于是它再装载基类。不管你创不创建基类对象，这个过程总会发生。如果基类还有基类，那么第二个基类也会被装载，依此类推。
2. 执行根基类的static初始化，然后是下一个派生类的static初始化，依此类推。这个顺序非常重要，因为派生类的“static初始化”有可能要依赖基类成员的正确初始化。
3. 当所有必要的类都已经装载结束，开始执行main()方法内的代码，new SubClass（）创建对象。
4. 类SubClass存在父类，则调用父类的构造函数，可使用super调用指定的构造方法。
基类的构造过程以及构造顺序与派生类相同。首先基类中各个变量按照字面顺序进行初始化，然后执行基类的构造方法的其余部分。
5. 对子类成员数据按照它们声明的顺序初始化，执行子类构造方法的其余部分。

### 内部类和内部静态类

```java
public class SubClass extends Parent {
    // 静态变量
    public static String subStaticField = "SubClass Static Variable";
    // 变量
    public String subField = "SubClass Member Variable";

    // 静态初始化块
    static {
        System.out.println(subStaticField);
        System.out.println("SubClass Static Code Block");
    }

    // 初始化块
    {
        System.out.println(subField);
        System.out.println("SubClass Member Code Block");
    }

    // 构造器
    public SubClass() {
        System.out.println("SubClass Constructor");
        System.out.println("i = " + i + ", j = " + j);
        new InnerClass();
        InnerStaticClass.print();
    }

    // 程序入口
    public static void main(String[] args) {
        System.out.println("SubClass Main");
        new SubClass();
    }

    public class InnerClass {
        public InnerClass() {
            System.out.println("InnerClass");
        }
    }

    public static class InnerStaticClass {
        // 静态变量
        public static String InnerStaticField = "Inner Static Class Variable";

        public static void print() {
            System.out.println("Inner Static Class Method");
        }

        static {
            System.out.println(InnerStaticField);
            System.out.println("Inner Static Class Code Block");
        }
    }
}


结果：

Parent Static Variable
Parent Static Code Block
SubClass Static Variable
SubClass Static Code Block
SubClass Main
Parent Member Variable
Parent Member Code Block
Parent Constructor
i = 9, j = 0
SubClass Member Variable
SubClass Member Code Block
SubClass Constructor
i = 9, j = 20
InnerClass
Inner Static Class Variable
Inner Static Class Code Block
Inner Static Class Method
```

可见，内部静态类只有在调用时才会初始化，包括其静态代码块。
