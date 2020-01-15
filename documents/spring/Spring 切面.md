1. **切面介绍:** 
   * 通知(advice): 切面的工作称为通知.
     * 前置通知(Before): 在目标方法被调用之前调用通知功能.
     * 后置通知(After): 在目标方法完成之后调用通知.
     * 返回通知(After-returning): 在目标方法成功执行之后调用通知.
     * 异常通知(After-throwing): 在目标方法抛出异常后调用通知.
     * 环绕通知(Around): 在被通知的方法调用之前和调用之后执行自定义的行为.
   * 连接点(join point): 在应用执行过程中能够插入切面的一个点.
   * 切点(pointcut): 定义会匹配通知所要织入的一个或多个连接点.
   * 切面(Aspect): 通知和切点共同定义了切面的全部内容-它是什么, 在何时和何处完成其功能.
   * 引入(Introduction): 引入允许我们向现有的类添加新方法或属性.
   * 织入(Weaving): 把切面应用到目标对象并创建新的代理对象的过程. 切面在指定的连接到被织入到目标对象中.
2. **通过切点来选择连接点:** 
    
    AspectJ 指示器 | 描述 
    :-------:|:--------:
    arg()   | 限制连接点匹配参数为指定类型的执行方法 
    @args() | 限制连接点匹配参数由指定注解标注的执行方法 
    execution() | 用于匹配是连接点的执行方法
    this() | 限制连接点匹配 AOP 代理的 bean 引用为指定类型的类
    target | 限制连接点匹配目标对象为指定类型的类
    @target | 限制连接点匹配特定的执行对象, 这些对象对应的类要由指定类型的注解
    within() | 限制连接点匹配指定的类型
    @within() | 限制连接点匹配指定注解所标注的类型
    @annotation | 限制匹配带有指定注解的连接点 
3. **使用注解创建切面:**  
   * 定义切面:
    ```Java
    public interface Performance {
        void perform();
    }

    @Aspect
    public class Audience {
        // @Before 定义前置通知
        @Before("execution(* study.Performance.perform(..))")
        public void silenceCellPhones() {
            System.out.println("Silencing cell phones");
        }
        
        // @Before 定义前置通知
        @Before("execution(* study.Performance.perform(..))")
        public void takeSeats() {
            System.out.println("Taking seats");
        }

        // @AfterReturning 定义返回通知
        @AfterReturning("execution(* study.Performance.perform(..))")
        public void applause() {
            Sytem.out.println("CLAP");
        }

        // @AfterThrowing 定义异常通知
        @AfterThrowing("execution(* study.Performance.perform(..))")
        public void demandRefund() {
            System.out.println("Demanding a refund");
        }
    }
    ```
    `@Aspect` 定义了一个切面, `execution(* study.Performance.perform(..))`定义了一个切点. 方法表示式以 **\*** 开始表示不关心方法返回值的类型; 然后指定了全限定的类名和方法名; 对于方法参数列表, 使用两个点号 (..) 表明选择任意的`perform()`方法, 无论方法的入参是什么. 
    * 创建环绕通知:
    ```Java
    @Aspect
    public class Audience {
        @Pointcut("execution(* study.Performance.perform(..))")
        public void performance() {}

        @Around("performance()")
        public void watchPerformance(ProceedingJoinPoint jp) {
            try {
                System.out.println("Silencing cell phones");
                System.out.println("Taking seats");
                jp.proceed();
                System.out.println("CLAP ClAP");
            } catch(Throwable e) {
                System.out.println("Demanding a refund");
            }
        }
    }
    ```
    环绕通知方法接收`ProceedingJoinPoint`作为参数. 当要将控制权交给被通知的方法时, 需要调用`ProceedingJoinPoint` 的 `proceed()`方法.
    * 处理通知中的参数: 
    ```Java
    public class CompactDisc {
        public void playTrack(int trackNumber) {
            // 省略其它代码
        }
    }

    @Aspect
    public class TrackCounter {
        private Map<Integer, Integer> trackCounts = new HashMap<>();

        @PointCut("execution(* study.CompactDisc.playTrack(int)) && args(trackNumber)")
        public void trackPlayed(int trackNumber) {}

        @Before("trackPlayed(trackNumber)")
        public void countTrack(int trackNumber) {
            int currentCount = getPlayCount(trackNumber);
            trackCounts.put(trackNumber, currentCount + 1);
        }
    }
    ```
    切点表达式中的 `args(trackNumber)`表明传递给`playTrack()`方法的 int 类型参数也会传递到通知中去. 参数的名称`trackNumber`也与切点方法签名中的参数相匹配.  

    这个参数会传递到通知方法中, 这个通知方法是通过`@Before`注解和命名切点`trackPlayed(trackNumber)`定义的. 切点定义中参数与切点方法中的参数名称是一样的, 这就完成了从命名切点到通知方法的参数转移.
   * 通过注解引入新功能:  
    ```Java
    /**
     * 增加的新功能接口
     **/
    public interface Encoreable {
        void performEncore();
    }
    /**
     * 增加的新功能实现
     */
    public class DefaultEncoreable implements Encoreable {
        @Override
        public void performEncore() {
            System.out.println("Encore");
        }
    }

    /**
     * 定义切面
     */
    @Aspect
    @Component
    public class EncoreableIntroducer {
        @DeclareParents(value = "study.Performance+", defaultImpl = "DefaultEncoreable.class")
        public static Encoreable encoreable;
    }
    ```
4. **在 XML 中定义切面:**  
    * 声明前置和后置通知:  
    ```XML
    <aop:config>
        <aop:aspect ref="audience">
            <aop:before
                pointcut="execution(* study.Performance.perform(..))"
                method="silenceCellPhones"
            />

            <aop:before
                pointcut="execution(* study.Performance.perform(..))"
                method="tackSeats"
            />

            <aop:after-returning
                pointcut="execution(* study.Performance.perform(..))"
                method="applause"
            />

            <aop:after-throwing
                pointcut="execution(* study.Performance.perform(..))"
                method="demandRefund"
            />
        </aop:aspect>
    </aop:config>

    <aop:config>
        <aop:aspect ref="audience">
            <aop:pointcut 
            id="performance"
            expression="execution(* study.Performance.perform(..))"
            />
            <aop:before
                pointcut-ref="performance"
                method="silenceCellPhones"
            />

            <aop:before
                pointcut-ref="performance"
                method="tackSeats"
            />

            <aop:after-returning
                pointcut-ref="performance"
                method="applause"
            />

            <aop:after-throwing
                pointcut-ref="performance"
                method="demandRefund"
            />
        </aop:aspect>
    </aop:config>
    ```
    * 声明环绕通知:  
    ```Java
    public class Audience {
        public void watchPerformance(ProceedingJoinPoint jp) {
            try {
                System.out.println("Silencing cell phones");
                System.out.println("Taking seats");
                jp.proceed();
                System.out.println("CLAP CLAP");
            } catch(Throwable e) {
                System.out.println("Demanding a refund");
            }
        }
    }

    <aop:config>
        <aop:aspect ref="audience">
            <aop:pointcut
                id="performance"
                expression="execution(* study.Performance.perform(..))"
            />
            <aop:around
                pointcut-ref="performance"
                method="watchPerformance"
            />
        </aop:aspect>
    </aop:config>
    ```
    * 为通知传递参数:  
    ```XML
    <aop:config>
        <aop:aspect ref="trackCounter">
            <aop:pointcut 
                id="trackPlayed"
                expression="execution(* study.CompactDisc.playTrack(int)) and args(trackNumber)"/>
            <aop:before
                pointcut-ref="trackPlayed"
                method="countTrack"
            />
        </aop:aspect>
    </aop:config>
    ```
    * 通过切面引入新的功能:  
    <aop:aspect>
        <aop:declare-parents
            types-matching="study.Performance+"
            implement-interface="study.Encoreable"
            default-impl="study.DefaultEncoreable"/>
    </aop:aspect> 