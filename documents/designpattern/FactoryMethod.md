1. 定义: Define an interface for creating an object,but let subclasses decide which class to instantiate.Factory Method lets a class defer instantiation to subclasses. (定义一个用于创建对象的接口, 让子类决定实例化哪一个类. 工厂方法使一个类的实例化延迟到其子类.)

2. 类图: 

    ![](E:\studynotes\studynotes\documents\designpattern\assets\FactoryMethod.png)

3. UML 类图分析: Product 接口定义产品的共性, 具体的产品应该实现该接口; Creator 接口声明了产品生成接口, 具体的产品工厂应该实现该接口.

4. 工厂方法模式的应用: java.util.Calendar#getInstance(), java.util.ResourceBundle#getBundle(), java.text.NumberFormat#getInstance()
, java.nio.charset.Charset#forName(), Integer#valueOf();
   
5. 工厂方法扩展: 定义为类的静态方法用于对象的产生, 简单工厂(Simple Factory); 为每一个产品类定义一个对应的工厂用于产品对象的生成; 限制对象的生成数量, 例如 JDBC 连接数据库会设置一个最大连接数量.

     