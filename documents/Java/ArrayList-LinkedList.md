1. 底层实现：

    - ArrayList:
        - ArrayList 底层采用动态扩展的数组进行实现(与 ArrayList 相对应的老的集合为 Vector).
        - CopyOnWriteList: ArrayList 的线程安全版本, 底层原理是复制了一个数组的副本, 在该副本上对数据进行插入或者删除操作, 然后将数组赋值给原来的值. 这样在进行删除或插入操作的同时不会影响其它线程的读, 缺点是占用原来的两本内存.
        - LinkedList 底层采用的是双向循环链表实现.

2. 方法时间复杂度:

    - ArrayList:

        - `get(int index)` : *O(1)* 
        - `add(E element)` : 在数组未满的情况下是*O(1)* ; 数组满的情况下是*O(n)* , 因为此时需要对数组进行扩容.
        - `add(int index, E element)` : *O(n)* (平均复杂度为 n/2), 因为需要移动数组的元素或对数组进行扩容.
        - `remove(int index)` : *O(n)* (平均复杂度为 n/2), 因为需要移动数组的元素.
        - `Iterator.remove()` : *O(n)* (平均复杂度为 n/2), 因为需要移动数组的元素.
        - `ListIterator.add(E element)` : *O(n)* (平均复杂度为 n/2), 因为需要移动数组的元素.

    - LinkedList:

        - `get(int index)` : *O(n)* (平均复杂度为 n/4).  因为内部采用双向链表实现, 内部对 `index` 进行了判断, 如果大于`size/2`则从尾部开始查找, 反之则从头部开始查找. 因此最坏的情况需要 n/2, 最好的情况 0, 所以平均复杂度为 n/4.

            ```java
            if (index < (size >> 1)) {
                Node<E> x = first;
                for (int i = 0; i < index; i++)
                    x = x.next;
                return x;
            } else {
                Node<E> x = last;
                for (int i = size - 1; i > index; i--)
                    x = x.prev;
                return x;
            }
            ```

        - `add(E element)`: *O(1)* 

        - `add(int index, E element)`: *O(n)* (平均复杂度为 n/4), 理由同上.

        - `remove(int index)`: *O(n)* (平均复杂度为 n/4), 理由同上`.

        - `Iterator.remove()`: *O(1)*

        - `ListIterator.add(E element)` *O(1)* 

3. 使用:

    * ArrayList: 

        * 遍历元素:

            ```java
            List<String> strs = new ArrayList<>();
            strs.add("test1");
            strs.add("test2");
            strs.add("test3");
            strs.add("test4");
            // for 循环方式
            for(int i = 0; i < strs.size(); i++) {
                System.out.println(strs.get(i));
            }
            // Iterator 方式
            Iterator<String> iterator = str.iterator();
            while (iterator.hasNext()) {
                System.out.println(iterator.next());
            }
            ```

        

        * 正确删除元素:

                List<String> strs = new ArrayList<>();
                strs.add("test1");
                strs.add("test2");
                strs.add("test3");
                strs.add("test4");
                Iterator<String> iterator = str.iterator();
                String temp;
                while (iterator.hasNext()) {
                    temp = iterator.next();
                    if (temp.equals("test2")) {
                        // 注意这里不能调用 strs.remove("test2") 进行删除, 这样会造成异常. 
                        // 即使没有异常产生, 这样删除的效率也不高, 因为又必须去遍历整个列表去找到元素的位置
                        iterator.remove();
                    }
                }

        * Iterator 删除元素原理: 

            在 `ArrayList`内部有一个`modCount`记录着内部元素`size`的变化次数, 任何引起元素`size`变化的操作都会更改这个值. 而在`Iterator`内部的方法执行前都会去检测这个值和自己当前保存的副本值是否一致, 如果不一致则会抛出异常. 在上述代码中, 如果在`while`循环中直接调用`strs.remove("test2")`方法删除元素就会导致`modeCount`值变化,  在下一次`Iterator#next()`方法调用时, 检测到这个值和自己保存的副本不一样, 就抛出异常了. 但是如果是调用`Iterator#remove()`方法, 本质上还是调用的`ArrayList.remove()`方法, 只不过`Iterator#remove()`方法在内部会更新它保存的`modeCount`副本, 这样就不会报错了. 在调用`Iterator#remove()`方法前一定要先调用`Iterator#next()`方法, 让它把游标移动到当前的元素.

            ```java
            public boolean hasNext() {
                return cursor != size;
            }
            
            @SuppressWarnings("unchecked")
            public E next() {
                // 这里去判断 modeCount 副本的值和 modeCount 是否相同
                checkForComodification();
                int i = cursor;
                if (i >= size)
                    throw new NoSuchElementException();
                Object[] elementData = ArrayList.this.elementData;
                if (i >= elementData.length)
                    throw new ConcurrentModificationException();
                cursor = i + 1;
                return (E) elementData[lastRet = i];
            }
            
            public void remove() {
                if (lastRet < 0)
                    throw new IllegalStateException();
                // 这里去判断 modeCount 副本的值和 modeCount 是否相同
                checkForComodification();
            
                try {
                    // 这里调用ArrayList#remove()方法删除元素
                    ArrayList.this.remove(lastRet);
                    cursor = lastRet;
                    lastRet = -1;
                    // 更新 modeCount 副本值
                    expectedModCount = modCount;
                } catch (IndexOutOfBoundsException ex) {
                    throw new ConcurrentModificationException();
                }
            }
            final void checkForComodification() {
                if (modCount != expectedModCount)
                    throw new ConcurrentModificationException();
            }
            ```

* LinkedList:

  * LinkedList

    * 遍历元素:

      ```java
       List<String> strs = new LinkedList<>();
       strs.add("test1");
       strs.add("test2");
       strs.add("test3");
       strs.add("test4");
       // Iterator 方式
       Iterator<String> iterator = str.iterator();
       while (iterator.hasNext()) {
       	System.out.println(iterator.next());
       }
       // 不要采用 for 循环方式
       for(int i = 0; i < strs.size(); i++) {
           // 因为调用 strs.get(i) 在内部又根据下标去循环查找元素, 相当于双重循环了
           System.out.println(strs.get(i));
       }
      ```

    - 正确删除元素:

      ```java
      List<String> strs = new LinkedList<>();
      strs.add("test1");
      strs.add("test2");
      strs.add("test3");
      strs.add("test4");
      Iterator<String> iterator = str.iterator();
      String temp;
      while (iterator.hasNext()) {
      	temp = iterator.next();
      	if (temp.equals("test2")) {
          	// 这里删除效率最高, 因为直接改变链表节点的指向即可
          	iterator.remove();
      	}
      }
      ```

4. 参考:

    [1] : [Arraylist 与 LinkedList 区别?](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/Java%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%E5%B8%B8%E8%A7%81%E9%9D%A2%E8%AF%95%E9%A2%98.md#arraylist-%E4%B8%8E-linkedlist-%E5%8C%BA%E5%88%AB)

    [2] : [When to use LinkedList over ArrayList in Java?](https://stackoverflow.com/questions/322715/when-to-use-linkedlist-over-arraylist-in-java)

    [3] : [Java集合必会14问](<https://zhuanlan.zhihu.com/p/40760616>)

    

