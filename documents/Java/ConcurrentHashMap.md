1. 并发同步实现的原理:

   当哈希表对应位置不存在接点值时, 采用 CAS 操作给结点赋值:

   ```java
   else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
   	// 这里采用 CAS 操作赋值
   	if (casTabAt(tab, i, null,new Node<K,V>(hash, key, value, null)))
   		break;                   // no lock when adding to empty bin
   }
   ```

   当结点有值时, 采用 `synchronized` 关键字将该结点同步:

   ```java
   synchronized (f) {
       if (tabAt(tab, i) == f) {
           if (fh >= 0) {
               binCount = 1;
               for (Node<K,V> e = f;; ++binCount) {
                   K ek;
                   if (e.hash == hash &&
                       ((ek = e.key) == key ||
                        (ek != null && key.equals(ek)))) {
                       oldVal = e.val;
                       if (!onlyIfAbsent)
                           e.val = value;
                       break;
                   }
                   Node<K,V> pred = e;
                   if ((e = e.next) == null) {
                       pred.next = new Node<K,V>(hash, key,
                                                 value, null);
                       break;
                   }
               }
           }
           else if (f instanceof TreeBin) {
               Node<K,V> p;
               binCount = 2;
               if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                              value)) != null) {
                   oldVal = p.val;
                   if (!onlyIfAbsent)
                       p.val = value;
               }
           }
       }
   }
   ```

   