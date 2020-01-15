1. Java 中的 int 型移位运算只支持最多 31 位的移位运算, 若需要超过 32 位运算需要拆成两部分进行

2. ```java
    int a = 1;
    a << 32 is equal to a << 0
    ```

    具体见 [Java Shift Operator and Auto Promotion](<https://stackoverflow.com/questions/30116352/java-shift-operator-and-auto-promotion>)

