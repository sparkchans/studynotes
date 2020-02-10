1. **被动引用不会触发类初始化的实例:**  
   * 子类引用父类的静态字段, 不会导致子类初始化.
    ```Java
    public class SuperClass {
        public static int value = 123;
        
        static {
            System.out.println("SuperClass Initial");
        }
    }
   
    public class SubClass {
        static {
            System.out.println("SubClass Initial");
        }
    }
   
    public class NotInitialization {
        public static void main(String[] args) {
            // 输出为 SuperClass Initial
            System.out.println(SuperClass.vale);
        }
    }
    ```
    对于静态字段, 只有直接定义这个字段的类才会被初始化, 因此通过子类来引用父类中定义的静态字段, 只会触发父类的初始化而不会触发子类的初始化.
    * 通过数组来引用类, 不会触发类的初始化
    ```Java
    public class NotInitialization {
        public static void main(String[] args) {
            // 不会输出任何值
            SuperClass[] sca = new SuperClass[10];
        }
    }
   
    ```
    * 直接引用常量:
    ```Java
    public class ConstClass {
        public static final String HELLOWORLD = "hello world";
        static {
            System.out.println("ConstClass Initial");
        }
    }
   
    public class NotInitialization {
        public static void main(String[] args) {
            // 不会输出任何值
            System.out.println(ConstClass.HELLOWORLD);
        }
    }
    // 经过编译器处理, 上面的代码等效为
    public class NotInitialization {
        public static void main(String[] args) {
            // 不会输出任何值
            System.out.println("hello world");
        }
    }
    ```
    * 访问类的`class`属性
    ```Java
    public class NotInitialization {
        public static void main(String[] args) {
            // 不会输出任何值
            Class<?> clazz = SuperClass.class;
        }
    }
    ```
2. **参考:**  
  [1] : [深入理解Java虚拟机（第2版）](https://book.douban.com/subject/24722612/)   