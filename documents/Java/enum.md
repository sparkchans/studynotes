## Java 中的枚举类型

- ### Java中通过关键字enum可以将一组具名的有限集合创建为一种信息的类型。

例如下面的代码定义了一个季节的枚举类型，它有四种取值。

```java
public enum Season {
    /**
     * 春天
     */
    SPRING,

    /**
     * 夏天
     */
    SUMMER,

    /**
     * 秋天
     */
    FALL,

    /**
     * 冬天
     */
    WINTER;
}
```

- ### 枚举类默认继承于java.lang.Enum类型。
    - ##### 枚举类型的**ordinal()**方法返回一个int值，这是每个enum实例在声明时的次序，从0开始。下面代码将输出0,1,2,3。

        ```java
        for (Season value : Season.values()) {
            System.out.println(value.ordinal());
        }
        ```

    - ##### 可以使用==来比较枚举类型

    - ##### Enum类中实现了equals()方法，hashCode()方法和compareTo()方法以及实现了Serializable接口

        ```java
        /**
         * equals()方法底层调用==实现
         */
        public final boolean equals(Object other) {
            return this==other;
        }
        
        public final int hashCode() {
            return super.hashCode();
        }
        
        /**
         * compareTo()方法底层返回的是ordinal值的差
         */
        public final int compareTo(E o) {
            Enum<?> other = (Enum<?>)o;
            Enum<E> self = this;
            if (self.getClass() != other.getClass() && // optimization
                self.getDeclaringClass() != other.getDeclaringClass())
                throw new ClassCastException();
            return self.ordinal - other.ordinal;
        }
        ```

        

    - ##### Enum的toString方法和name()方法默认返回都是Enum的name属性。但是可用通过重写toString()方法来返回自己想要的值。

        ```java
        public final String name() {
            return name;
        }
        
        public String toString() {
            return name;
        }
        ```

        

    

