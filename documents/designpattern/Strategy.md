1. 定义: Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from the clients that use it. (定义一组算法, 将每个算法都封装起来, 并且使它们之间可以互换. 策略模式使得算法可以独立于客户端变化.)

2. 类图: 

    ![UML 类图](E:\studynotes\studynotes\documents\designpattern\assets\strategy.png)

3. UML 类图分析: Context 叫做上下文, 起承上启下的封装作用, 屏蔽客户端对策略算法的直接访问, 客户端只需要调用 Context#operation() 方法即可调用底层的策略方法. Strategy 为策略接口, 声明策略算法方法, 具体策略需要实现 Strategy 接口, 如 ConcretedStrategyA, ConcretedStrategyB.

4. 策略模式的应用: javax.servlet.http.HttpServlet#service() 方法, java.util.Comparator#compare() 方法, javax.servlet.Filter#doFilter() 方法

5. 策略模式的不足: 增加新的策略就需要增加新的类, 策略类需要对外暴露, 客户端需要知道具体策略的存在.

6. 策略模式的改进: 可以在 Context 类中使用简单工厂模式来完成对具体策略的创建, 这样客户端就不需要知道具体有哪些类型的策略了.

    

  [1] : [Java 库中的设计模式](https://stackoverflow.com/questions/1673841/examples-of-gof-design-patterns-in-javas-core-libraries)
  [2] ：[设计模式之禅](https://book.douban.com/subject/4260618/)
  [3] : [Head First 设计模式](https://book.douban.com/subject/2243615/)](<https://stackoverflow.com/questions/1673841/examples-of-gof-design-patterns-in-javas-core-libraries>)