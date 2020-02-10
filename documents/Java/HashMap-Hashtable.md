1. 使用算法:

    - 计算大于等于某个整数的第一个为 2^<sup>n</sup> 的数:

        ```java
        static final int tableSizeFor(int cap) {
            int n = cap - 1;
            n |= n >>> 1;
            n |= n >>> 2;
            n |= n >>> 4;
            n |= n >>> 8;
            n |= n >>> 16;
            return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
        }
        ```

        - 分析:

            一个数为 2^<sup>n</sup>, 则该数的二进制表示形式某一位为 1, 其后面的位全部为 0. 若想找到大于等于某个整数的第一个为 2^<sup>n</sup> 的数, 若该数恰好是  2^<sup>n</sup>, 则所求的数即该数; 若该数不是  2^<sup>n</sup>, 则所求的数为该数的二进制形式所占位数加 1 所能表示的最小整数. 例如 30 的二进制表示为 `11110`, 占用 5 位, 则所求数为 `100000`. 求解这个数的过程为将该数的二进制表示形式的每一位都变成 1, 然后再加上 1. 上面的代码就是实现了该算法, 只不过上面的算法先减 1, 再加 1, 是为了包括该数恰好是 2^<sup>n</sup> 的情况.

    

    - 快速求余数: 

        ```java
        if ((tab = table) == null || (n = tab.length) == 0)
        	n = (tab = resize()).length;
        // 这里通过 (n - 1) & hash 计算 hash % n 的余数
        if ((p = tab[i = (n - 1) & hash]) == null)
        	tab[i] = newNode(hash, key, value, null);
        ```

        - 分析: 

            该算法只有除数为 2^<sup>n</sup> 才成立.  若除数为 2^<sup>n</sup>, 则被除数的最后 n 位就是最终的余数, 所以可以采用以上算法.

2. 区别:

    - 键和值: 

        `HashMap` 的键可以为 `null`, 并且只能有一个键为 `null`, 因为 `HashMap` 的把键为 `null` 的 `hashCode` 处理为 0, `HashMap` 可以有多个键的值为 `null`; `Hashtable` 的键不能为 `null`, 虽然内部没有进行判断, `Hashtable` 的值也不能为 `null`, 这个是内部进行了判断的.

        ```java
        // HashMap 内部的 hash 函数
        static final int hash(Object key) {
            int h;
            /** 
             * 若 key 为 null 则 hashCode 值为 0, 将哈希值右移 16 位与自己进行异或
             * 让高位也可以参与运算
             */
            return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
        }
        
        // Hashtable 内部
        // 这里判断了 value 不能为空
        if (value == null) {
            throw new NullPointerException();
        }
        Entry<?,?> tab[] = table;
        // 这里直接调用了 key 的 hashCode() 方法, 因此 key 不能为空
        int hash = key.hashCode();
        // hash 算法
        int index = (hash & 0x7FFFFFFF) % tab.length;
        ```

    - 线程安全: 

        `HashMap` 是线程不安全的, 需要使用 `Collections.synchronizedMap()`包装成线程安全的; `Hashtable` 是线程安全的, 它的每个方法都是使用 `synchronized` 关键字修饰的.

    - 扩容机制: 

        HashMap 在第一次保证初始化的容量为 2^<sup>n</sup>, 默认的容量为 16, 默认的负载因子为 0.75.  然后在扩容的时候将容量扩容为原来的两倍, 所以容量也总是保持为 2^<sup>n</sup>;  Hashtable 默认容量为 11, 默认的负载因子为 0.75. 然后在扩容的时候将容量扩容为原来的两倍加 1.

        ```Java
        // HashMap 的 resize() 方法
        final Node<K,V>[] resize() {
            Node<K,V>[] oldTab = table;
            int oldCap = (oldTab == null) ? 0 : oldTab.length;
            int oldThr = threshold;
            int newCap, newThr = 0;
            if (oldCap > 0) {
                if (oldCap >= MAXIMUM_CAPACITY) {
                    threshold = Integer.MAX_VALUE;
                    return oldTab;
                }
                // 这里扩容为原来的两倍
                else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                         oldCap >= DEFAULT_INITIAL_CAPACITY)
                    newThr = oldThr << 1; // double threshold
            }
            else if (oldThr > 0) // initial capacity was placed in threshold
                newCap = oldThr;
            else {               // zero initial threshold signifies using defaults
                newCap = DEFAULT_INITIAL_CAPACITY;
                newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
            }
            // 省略其它代码
        }
        
        // Hashtable 的 rehash 方法
        protected void rehash() {
            int oldCapacity = table.length;
            Entry<?,?>[] oldMap = table;
        
            // 扩容为原来的两倍加 1
            int newCapacity = (oldCapacity << 1) + 1;
            if (newCapacity - MAX_ARRAY_SIZE > 0) {
                if (oldCapacity == MAX_ARRAY_SIZE)
                    return;
                newCapacity = MAX_ARRAY_SIZE;
            }
        }
        ```

    - 底层实现:

        两者都是通过***除留余数法***来进行 hash 值的散列, 并通过***链地址法***解决冲突.  当 `HashMap` 的链表长度大于默认的阈值 8 时, 会将链表转换成红黑树, 以提高查找效率.

        ```java
        final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
            // 省略其它代码
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    // 链表长度大于默认的阈值, 则构建树形结构
                    if (binCount >= TREEIFY_THRESHOLD - 1) 
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
            // 省略其它代码
        }
        ```

3. 特点:

    - 判断对象相等的标准:

        两者都是当且仅当 `hashCode()` 方法返回值和 `equals()` 方法返回值都相等时才会认为两个对象是同一个对象. 

    - Set 视图:

        两者都可以通过 `entrySet()` 方法返回一个内部数据的 `Set`, 这和 `Arrays#asList()` 方法返回一个数组的 `List` 视图类似.

4. 相关代码实现:

    - HashMap

      ```java
      /**********************************HashMap*********************************/
      // LinkedHashMap 的回调
      void afterNodeAccess(Node<K,V> p) { }
      void afterNodeInsertion(boolean evict) { }
      void afterNodeRemoval(Node<K,V> p) { }
      
      // 存放元素
      final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                         boolean evict) {
          Node<K,V>[] tab; Node<K,V> p; int n, i;
          if ((tab = table) == null || (n = tab.length) == 0)
              n = (tab = resize()).length;
          // 对应位置没有元素则直接存放
          if ((p = tab[i = (n - 1) & hash]) == null)
              // 新建结点
              tab[i] = newNode(hash, key, value, null);
          else {
              Node<K,V> e; K k;
              if (p.hash == hash &&
                  // 对应位置存在一个元素, 且元素的键的值等于当前元素
                  ((k = p.key) == key || (key != null && key.equals(k))))
                  e = p;
              else if (p instanceof TreeNode)
                  // 如果是结点类型为树的结点类型, 则将调用树的存放方法
                  e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
              else {
                  for (int binCount = 0; ; ++binCount) {
                      if ((e = p.next) == null) {
                          p.next = newNode(hash, key, value, null);
                          if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                              treeifyBin(tab, hash);
                          break;
                      }
                      if (e.hash == hash &&
                          ((k = e.key) == key || (key != null && key.equals(k))))
                          break;
                      p = e;
                  }
              }
              if (e != null) { // existing mapping for key
                  V oldValue = e.value;
                  if (!onlyIfAbsent || oldValue == null)
                      e.value = value;
                  // 元素访问之后回调
                  afterNodeAccess(e);
                  return oldValue;
              }
          }
          ++modCount;
          if (++size > threshold)
              resize();
          // 元素插入之后回调
          afterNodeInsertion(evict);
          return null;
      }
      // 新建结点
      Node<K,V> newNode(int hash, K key, V value, Node<K,V> next) {
          return new Node<>(hash, key, value, next);
      }
      /******************************LinkedHashMap**********************************/
      ```

5. 自己实现 Object 对象作为键:

    - 重写 `hashCode()` :  通过该方法返回哈希值
    - 重写 `equals()`： 需要遵守自反性、对称性、传递性、一致性以及对于任何非null的引用值x，x.equals(null)必须返回false的这几个特性，**目的是为了保证key在哈希表中的唯一性**

    