1. **配置 profile bean:**
    * 需求分析: 在开发和测试环境可能需要初始化不同的数据库源. Spring 使用 profile 来完成这个任务.
    * Java 中配置 Profile:
    ```Java
    @Configuration
    @Profile("dev")
    public class DevelopmentProfileConfig {
        @Bean(destroyMethod="shutdown")
        public DataSource dataSource() {
            return new EmbeddedDatabaseBuilde()
                .setType(EmbeddedDatabaseType.H2)
                .addScript("classpath:schema.sql")
                .addScript("classpath:test-data.sql")
                .build();
        }
    }

    @Configuration
    @Profile("prod")
    public class DevelopmentProfileConfig {
        @Bean(destroyMethod="shutdown")
        public DataSource dataSource() {
            JndiObjectFactoryBean jndiObjectFactoryBean = new JndiObjectFactoryBean();
            jndiObjectFactoryBean.setJndiName("jdbc/myDS");
            jndiObjectFactoryBean.setResourceRef(true);
            jndiObjectFactoryBean.setProxyInterface(javax.sql.DataSource.class);
            return (DataSource)jndiObjectFactoryBean.getObject();
        }
    }
    ```
    只有相应的 profile 激活的时候才会创建对应的 bean. 也可以将 @Profile 注解配置在方法上.
    ```Java
    @Configuration
    public class DevelopmentProfileConfig {
        @Bean(destroyMethod="shutdown")
        @Profile("prod")
        public DataSource jndiDataSource() {
            JndiObjectFactoryBean jndiObjectFactoryBean = new JndiObjectFactoryBean();
            jndiObjectFactoryBean.setJndiName("jdbc/myDS");
            jndiObjectFactoryBean.setResourceRef(true);
            jndiObjectFactoryBean.setProxyInterface(javax.sql.DataSource.class);
            return (DataSource)jndiObjectFactoryBean.getObject();
        }

        @Bean(destroyMethod="shutdown")
        @Profile("dev")
        public DataSource embeddedDataSource() {
            return new EmbeddedDatabaseBuilde()
                .setType(EmbeddedDatabaseType.H2)
                .addScript("classpath:schema.sql")
                .addScript("classpath:test-data.sql")
                .build();
        }
    }
    ```
    尽管每个 DataSource bean 都被声明在一个 profile 中, 并且只有当规定的 profile 激活时, 相应的 bean 才会被创建. 没有指定 profile 的 bean 始终都会被创建.
    * XML 中配置 Profile:
    ```XML
    <!--单独配置为一个文件-->
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:jdbc="http://www.springframework.org/schema/jdbc"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd"
        profile="dev">
        <jdbc:embedded-database id="dataSource">
            <jdbc:script location="classpath:schema.sql"/>
            <jdbc:script location="classpath:test-data.sql"/>
        </jdbc:embedded-database>
        
    </beans>

    <!--同时配置-->
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:jdbc="http://www.springframework.org/schema/jdbc"
        xmlns:jee="http://www.springframework.org/schema/jee"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee.xsd">
        <beans profile="dev">
            <jdbc:embedded-database id="dataSource">
                <jdbc:script location="classpath:schema.sql"/>
                <jdbc:script location="classpath:test-data.sql"/>
            </jdbc:embedded-database>
        </beans>
    
        <beans profile="prod">
            <jee:jndi-lookup id="dataSource"
                    jndi-name="jdbc/myDatabase"
                    resource-ref="true"
                    proxy-interface="javax.sql.DataSource"
            />
        </beans>
    </beans>

    ```
    * 激活 Profile:   
      Spring 在确定哪个 profile 处于激活状态时, 依赖两个独立的属性: `spring.profiles.active` 和 `spring.profiles.default`. `spring.profiles.active` 优先级高于 `spring.profiles.default`.

      属性设置方式: 
        * 作为 DispatcherServlet 的初始化参数.
        * 作为 Web 应用的上下文参数.
        * 作为 JNDI 条目.
        * 作为环境变量.
        * 作为 JVM 的系统属性.
        * 在集成测试类上, 使用 `@ActiveProfiles` 注解设置.

2. **条件化的 bean:**
   * 使用 `@Conditional` 根据条件初始化:
    ```Java
    @Bean
    @Conditional(MagicExistsCondition.class)
    public MagicBean magicBean() {
        return new MagicBean();
    }
    ```
    设置给 `@Conditional` 的类可以是任意实现了 `Condition` 接口的类. 例如:
    ```Java
    public class MagicExistsCondition implements Condition {
        
        @Override
        public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
            Environment env = context.getEnvironment();
            return env.containsProperty("magic");
        }
    }
    ```
    在 Spring 4 中, `@Profile` 注解基于 `@Conditional` 和 `Condition` 实现. 并提供了 `ProfileCondition` 作为 `Condition` 的实现.
3. **处理自动装配歧义:**
    如果某个接口存在多个实现类的时候, Spring 在装配 bean 的时候就不知道去装配哪个. 为了消除歧义可采用如下方式:
    * 标识首选的 bean:
    ```Java
    //　Java 方式
    @Component
    @Primary
    public class IceCream implements Dessert {

    }

    // XML 方式
    <bean id="iceCream" class="study.IceCream" primary="true"/>
    ```
    * 限定自动装配的 bean:
    ```Java
    @Autowired
    @Qualifier("iceCream")
    public void setDessert(Dessert dessert) {
        this.dessert = dessert;
    }
    ```
    `@Qualifier` 注解所引用的 bean 要有 String 类型的限定符, 所有的 bean 都会有一个默认的限定符, 它和 bean 的 ID 相同. 使用限定符的好处是它不必和类名耦合在一起. 在装配时既可以通过 bean 的 ID 装配也可以通过定义的限定符装配.
    ```Java
    @Component("iceCream")
    @Qualifier("cold")
    public class IceCream implements Dessert {

    }

    // 这里通过 cold 限定符装配
    @Autowired
    @Qualifier("cold")
    public void setDessert(Dessert dessert) {
        this.dessert = dessert;
    }

    // 这里通过 iceCream 限定符装配
    @Autowired
    @Qualifier("iceCream")
    public void setDessert(Dessert dessert) {
        this.dessert = dessert;
    }
    ```
4. **bean 的作用域:**
    Spring bean 默认的作用域为单例, Spring 定义了多种 bean 的作用域: 
   * 单例(Singleton): 在整个应用中, 只创建 bean 的一个实例.
   * 原型(Prototype): 每次注入或者通过 Spring 上下文获取的时候, 都会创建一个新的 bean 实例.
   * 会话(Session): 在 Web 应用中, 为每个会话创建一个 bean 实例.
   * 请求(Request): 在 Web 应用中, 为每个请求创建一个 bean 实例.
 * 使用会话和请求作用域:
   对于商城的应用场景来说, 购物车的 bean　为会话作用域是合适的.
   * Java 配置:
       ```Java
       @Component
       @Scope(value=WebApplicationContext.SCOPE_SESSION,
       proxyMode=ScopedProxyMode.INTERFACES)
       public ShoppingCart cart() {}
       ```
       `proxyMode` 解决了将会话或请求作用域的 bean 注入到单例 bean 中所遇到的问题.
       ```Java
       @Component
       public class StoreService {
           @Autowired
           public void setShoppingCart(ShoppingCart shoppingCart) {
               this.shoppingCart = shoppingCart;
           }
       }
       ```
       因为 StoreService 是一个单例的 bean, 会在 Spring 应用上下文加载的时候创建. 当它创建的时候, Spring 会试图将 ShoppingCart bean 注入到 setShoppingCart() 方法中. 但是 ShoppingCart bean 是会话作用域的, 此时并不存在. 直到某个用户进入系统, 创建了会话之后, 才会出现 ShoppingCart 实例.   

       Spring 会注入一个到 ShoppingCart bean 的代理, 这个代理会暴露与 ShoppingCart 相同的方法. 当 StoreService 调用 ShoppingCart 的方法时, 代理会对其进行懒解析并将调用委托给会话作用域内真正的 ShoppingCart bean 实例.
   * XML 配置:
       ```XML
       <bean id="cart" class="study.ShoppingCart"
       scope="session">
           <aop:scoped-proxy/>
       </bean>
       ```
5.**运行时注入:**  
  * 注入外部的值:
    * Java 方式: 
        ```Java
        @Configuration
        @PropertySource("classpath:/study/app.properties")
        public class ExpressiveConfig {
            @Autowired
            private Environment env;

            @Bean
            public BlankDisc disc() {
                return new BlankDisc(env.getProperty("disc.title"), env.getPropery("disc.artist"));
            }
        }

        // 使用注解方式和占位符方式
        public BlankDisc(@Value("${disc.title}") String title, @Value("${disc.artist}") String artist) {
            this.title = title;
            this.artist = artist;
        }
        ```
        使用注解和占位符方式需要配置一个 `PropertySourcesPlaceHolderConfigure` bean.
        ```Java
        @Bean
        public static PropertySourcesPlaceHolderConfigure placeHoderConfigure() {
            return new PropertySourcesPlaceHolderConfigure();
        }
        ```
    * XML 方式:
        ```XML
        <context:property-placeholder/>

        <bean id="blankDisc" class="study.BlankDisc"
        c:_title="${disc.title}"
        c:_artist="${disc.artist}"/>
        ```
    * Spring 表达式语言方式:
        ```Java
        public BlankDisc(@Value("#{systemProperties['disc.title']}") String title, @Value("#{systemProperties['disc.artist']}") String artist) {
            this.title = title;
            this.artist = artist;
        }

        <bean id="blankDisc" class="study.BlankDisc"
        c:_title="#{systemProperties['disc.title']}"
        c:_artist="#{systemProperties['disc.artist']}"/>
        ```