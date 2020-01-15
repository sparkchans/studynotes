1. **Spring 装配 bean 的机制:**   
   * 在 XML 中进行显示配置.
   * 在 Java 中进行显示配置.
   * 隐式的 bean 发现机制和自动装配.
2. **自动化装配 bean:** 
   * 自动装配实现机制:
     * 组件扫描(component scanning): Spring 会自动发现应用上下文中所创建的 bean.
     * 自动装配(autowiring): Spring 自动满足 bean 之间的依赖.
   * 创建可被发现的 bean:   
     * 使用`@Component`标识 bean:   
        被 `@Component` 注解修饰的 bean 可以被 Spring 视作一个 bean. `CDPlayer` 被 `@Component` 修饰, 因此 Spring 可以识别并会创建该类的示例, 并放到 Spring 应用的容器中. `@Component` 就好像一个标志一样, 有了这个标志就能被 Spring 所认识.
        ```Java 
        @Component
        public class CDPlayer {
            public void play() {
                System.out.println("play");
            }
        }
        ```
      * 使用 `@ComponentScan` 开启扫描:   
        使用了 `@Component` 标识了 Spring 所能够认识的 bean 后, 还需要使用 `@CompenentScan` 注解告诉 Spring 可以按照这个方式去识别 bean. `@Component` 默认会扫描与配置类相同的包及子包. 当然也可以为 `@Component` 指定扫描路径. `@Component` 可以为 bean 指定名称, 若不指定名称, 则默认的名称为 bean 对应类的类名第一个字母小写.
        ```Java
        @Configuration
        @ComponentScan
        public class CDPlayerConfig {

        }
        ```
        也可以使用 XML 方式来启用组件扫描:    
        ```XML
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xmlns:context="http://www.springframework.org/schema/context"
              xsi:schemaLocation="http://www.springframework.org/schema/beans 
              http://www.springframework.org/schema/beans/spring-beans.xsd 
              http://www.springframework.org/schema/context 
              https://www.springframework.org/schema/context/spring-context.xsd">
              <context:component-scan base-package="study"/>
        </beans>

        ```
    * 使用 `@Autowired` 实现自动装配:   
      将一个 `CompactDisc` 类装配到 `CDPlayer` 类中. `@Autowired` 注解可以用在属性, 构造器, 方法上.
      ```Java
      @Component
      public class CompactDisc {
          public void play() {
            System.out.println("Play");
          }
      }

      @Component
      public class CDPlayer {
          private CompactDisk cd;

          @Autowired
          public CDPlayer(CompactDisc cd) {
              this.cd = cd;
          }

          public void play() {
              this.cd.play();
          }
      }
      ```
3. **通过 Java 代码装配 bean:**  
    Java 代码装配 bean 的情况适用于你向将第三方库中的组件装配到你的应用中, 但是你没法在它的类上添加 `@Component` 和 `@Autowired` 注解.
    * 使用 `@Bean` 注解声明 bean, 默认情况下 bean 的名称和带有 @Bean 注解的方法名是一样的, 也可以通过 name 属性指定名称.
      ```Java
      @Configuration
      @ComponentScan
      public class CDPlayerConfig {
          
          // 不指定名称, 默认名称为 compactDisc
          @Bean
          public CompactDisc compactDisc() {
              return new CompactDisc();
          }

          // 指定名称, 名称为 myCompactDisc
          @Bean(name="myCompactDisc")
          public CDPlayer compactDisc() {
              return new CompactDisc();
          }
      }
      ```
      * 借助 JavaConfig 实现注入:   
         前面是借助 `@Autowired` 注解来实现 bean 装配的. 
        ```Java 
        @Configuration
        @ComponentScan
        public class CDPlayerConfig {
            
            @Bean
            public CompactDisc compactDisc() {
                return new CompactDisc();
            }

            // 这里调用 compactDisc() 方法来装配 bean
            @Bean
            public CDPlayer cDPlayer() {
                return new CDPlayer(compactDisc());
            }

            // 也可以采用下面这种方式, 这种方式不用要求将 CompactDisc 和 CDPlayer 声明在同一个配置类中
            @Bean
            public CDPlayer cDPlayer(CompactDisc compactDisc) {
                return new CDPlayer(compactDisc);
            }
        }
        ```
4. **通过 XML 来装配 Bean:**  
   * 通过构造器装配:
     * 引用装配:
        ```XML
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xmlns:context="http://www.springframework.org/schema/context"
              xsi:schemaLocation="http://www.springframework.org/schema/beans 
              http://www.springframework.org/schema/beans/spring-beans.xsd 
              http://www.springframework.org/schema/context 
              https://www.springframework.org/schema/context/spring-context.xsd">
              <bean id="compactDisc" class="study.CompactDisc"/>
              <bean id="cDPlayer" class="study.CDPlayer">
                  <construct-arg ref="compactDisc"/>
              </bean>
        </beans>
        ```
      * 赋值装配: 
        ```XML
        @Component
        public class CompactDisc {
            private String title;
            private String artist;
            // 省略其它代码
        }
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xmlns:context="http://www.springframework.org/schema/context"
              xsi:schemaLocation="http://www.springframework.org/schema/beans 
              http://www.springframework.org/schema/beans/spring-beans.xsd 
              http://www.springframework.org/schema/context 
              https://www.springframework.org/schema/context/spring-context.xsd">
              <bean id="compactDisc" class="study.CompactDisc">
                  <construct-arg name="title" value="testTitle"/>
                  <construct-arg name="artist" value="testArtist"/>
              </bean>
        </beans>
        ```
      * 装配集合:  
        * 装配 List:
          ```XML
          @Component
          public class CompactDisc {
              // 省略其它代码
              private List<String> tracks;
          }

          <?xml version="1.0" encoding="UTF-8"?>
          <beans xmlns="http://www.springframework.org/schema/beans"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                xmlns:context="http://www.springframework.org/schema/context"
                xsi:schemaLocation="http://www.springframework.org/schema/beans 
                http://www.springframework.org/schema/beans/spring-beans.xsd 
                http://www.springframework.org/schema/context 
                https://www.springframework.org/schema/context/spring-context.xsd">
                <bean id="compactDisc" class="study.CompactDisc">
                    <construct-arg name="title" value="testTitle"/>
                    <construct-arg name="artist" value="testArtist"/>
                    <construct-arg name="tracks">
                        <list>
                            <value>testTrack1</value>
                            <value>testTrack2</value>
                            <value>testTrack3</value>
                            <value>testTrack4</value>
                        </list>
                    </construc-arg>
                </bean>
          </beans>


          ```
        * 装配 Set:
          ```XML
          <?xml version="1.0" encoding="UTF-8"?>
          <beans xmlns="http://www.springframework.org/schema/beans"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                xmlns:context="http://www.springframework.org/schema/context"
                xsi:schemaLocation="http://www.springframework.org/schema/beans 
                http://www.springframework.org/schema/beans/spring-beans.xsd 
                http://www.springframework.org/schema/context 
                https://www.springframework.org/schema/context/spring-context.xsd">
                <bean id="compactDisc" class="study.CompactDisc">
                    <construct-arg name="title" value="testTitle"/>
                    <construct-arg name="artist" value="testArtist"/>
                    <construct-arg name="tracks">
                        <set>
                            <value>testTrack1</value>
                            <value>testTrack2</value>
                            <value>testTrack3</value>
                            <value>testTrack4</value>
                        </set>
                    </construc-arg>
                </bean>
          </beans>
          ```
      * 设置属性: 设置属性时必须要有对应的 `Setter` 方法.
        ```XML
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xmlns:context="http://www.springframework.org/schema/context"
              xsi:schemaLocation="http://www.springframework.org/schema/beans 
              http://www.springframework.org/schema/beans/spring-beans.xsd 
              http://www.springframework.org/schema/context 
              https://www.springframework.org/schema/context/spring-context.xsd">
              <bean id="compactDisc" class="study.CompactDisc">
                  <property name="title" value="testTitle"/>
                  <property name="artist" value="testArtist"/>
                  <property name="tracks">
                      <list>
                          <value>testTrack1</value>
                          <value>testTrack2</value>
                          <value>testTrack3</value>
                          <value>testTrack4</value>
                      </list>
                  </property>
              </bean>
        </beans>
        ```
5. **导入和混合配置:**
   * 在 JavaConfig 导入其它 JavaConfig 配置:
    ```Java
    @Configuration
    public class DiscConfig {
        @Bean
        public CompactDisc compactDisc() {
            return new CompactDisc();
        }
    }

    @Configuration
    @Import(DiscConfig.class)
    public class CDPlayerConfig {
        @Bean
        public CDPlayer(CompactDisc compactDisc) {
            return new CDPlayer(compactDisc);
        }
    }
    // 或者创建一个更高级别的 JavaConfig
    @Configuration
    @Import({CompactDiscConfig.class, CDPlayerConfig.class}) 
    public class SoundSystemConfig {

    }
    ```

   * 在 JavaConfig 中引用 XML 配置:   
   假设现在 `CompactDisc` 是配置在 `disc-config.xml` 中:
      ```Java
      @Configuration
      @Import(CDPlayerConfig.class)
      @ImportResource("classpath:disc-config.xml") 
      public class SoundSystemConfig {

      }
      ```
    * 在 XML 配置中引入 JavaConfig:   
      ```XML
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:context="http://www.springframework.org/schema/context"
            xsi:schemaLocation="http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd 
            http://www.springframework.org/schema/context 
            https://www.springframework.org/schema/context/spring-context.xsd">
            <bean class="study.CDPlayerConfig"/>
            <import resource="disc-config.xml"/>
      </beans>
      ```