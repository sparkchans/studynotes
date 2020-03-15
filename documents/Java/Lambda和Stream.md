## Lambda 创建

```java
List<String> names = new ArrayList<String> {
    {
        add("name1");
        add("name2");
    }
};
// 匿名内部类
Collections.sort(names, new Comparator<String>() {
   @Override
    public int compare(String o1, String o2) {
        return o1.compareTo(o2);
    }
});
/**
 * lambda 表达式，这里 lambda 表达式就是匿名内部类的重写方法的实现体
 * 只不过这里进行了简化，简化标准如下
 * 1.当参数个数只有一个时，可以省略小括号
 * 2.当执行体只包含一条语句时，可以省略大括号，return 和语句结尾分号
 */
Collections.sort(names, (o1, o2) -> o1.compareTo(o2));

```

## 方法引用

```java
/**
 * 方法引用有三种类别
 * 1. objectName::instanceMethod
 * 2. className::staticMethod
 * 3. className::instanceMethod
 */
List<String> names = new ArrayList<String> {
    {
        add("name1");
        add("name2");
    }
};
// 这里相当于 name -> "validName".indexOf(name)
names.stream.forEach("validName"::indexOf);
// 这里相当于 name -> System.out.println(name)
names.stream.forEach(System.out::println);
// 这里相当于 name -> name.length()
names.stream.forEach(String::length);
```



## 参考

[1] : [Java8初体验（一）lambda表达式语法](<http://ifeve.com/lambda/>)

[2] : [Java 8 中的 Streams API 详解](<https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/index.html>)

[3] : [Java8初体验（二）Stream语法详解](<http://ifeve.com/stream/>)

[4] : [《JAVA8开发指南》使用流式操作](<http://ifeve.com/java8-adopting-streams/>)