
String.split()
=========

###参数为正则表达式
```java
public String[] split(String regex)
```
参数regex是一个正规则表达式(regular-expression)，不是简单的字符串(String)。因此，对特殊字符的使用需要注意。

```java
// "|"，"+"，"*"，"\\"等特殊号直接分隔字符串将抛出java.util.regex.PatternSyntaxException异常,
// 需用"\\|"，"\\+","\\*"，"\\\\"等转义。
// "\"在Java字符串中不能单独出现，除了\b, \t, \n, \f, \r, \", \', \\
// 例：String[] array = "aaa\\bbb\\bccc".split("\\\\")。

// 错误方式
public static void main(String[] args) {
    String str = "aa|bb|cc";
    String[] strArray = str.split("|"); // 错误，结果见下文输出
    // String[] strArray = str.split("."); // 错误，赋值后的strArray为一个空数组[]
    // String[] strArray = str.split("\\."); // 错误，strArray相当于str未变
    // String[] strArray = str.split("d"); // 错误，strArray还是相当于str
    for (String s : strArray) {
        System.out.println(s);
    }
}
// 输出结果同str.split("")：
a
a
|
b
b
|
c
c

// 上述结果显然不符期望，可见对于特殊字符分隔需要注意，防止意外。
// 正确方式：
public static void main(String[] args) {
    String str = "aa|bb|cc";
    String[] strArray = str.split("|");
    for (String s : strArray) {
        System.out.println(s);
    }
}
// 输出结果：
aa
bb
cc
```
###连接符“|”
若在一个字符串中有多个分隔符，可以用“|”作为连字符：
```java
// 把三个都分隔出来，可以用String.split("and|or");
public static void main(String[] args) {
    String str = "youandmeorher";
    String[] strArray = str.split("and|or");
    for (String s : strArray) {
        System.out.println(s);
    }
}
// 输出结果
you
me
her

// 若字符串含有多个空格及tab键，可用split("\\s+"),按空白部分进行拆分，不管是几个空格，也不管是空格还是tab键，如下：
public static void main(String[] args) {
    String str = "you   and   		  me   or her";
    String[] strArray = str.split("\\s+");
    for (String s : strArray) {
        System.out.println(s);
    }
}

// 例子中的空白部分既有空格，也有tab键，输出结果：
you
and
me
or
her
```
