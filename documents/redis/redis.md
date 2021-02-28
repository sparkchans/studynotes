## Redis常用命令

### Redis 登录

```shell
# 显示版本
redis-cli -v
# 启动 redis
redis-server
```



### 全局命令

- #### keys *

    keys 命令显示所有的键。会在遍历所有的键，时间复杂度为O(n)。

- #### dbsize

    dbsize 显示键的总数。它直接获取Redis内置的键总数变量，时间复杂度为O(1)。

- #### exists key

    exists 检查键是否存在。返回1则存在，返回0则不存在。
    
- ##### del key

    删除键

- ##### expire key

    设置键过期时间

- ##### ttl key

    检查键的过期时间

- ##### type key

    检查键的类型

- ##### object encoding key

    检查键的底层数据结构类型

### 字符串

- #### set

    ```shell
    set key value nx nxseconds px pxmillseconds nx ex
    ```

    - ##### nx 秒级过期时间

    - ##### px 秒级过期时间

    - ##### nx 键不存在设置成功

    - ##### ex 键存在设置成功，用于更新

    - ##### setnx  命令同 nx，可用于分布式锁的实现

    - ##### setpx 命令通 px

- #### get 

    获取键对应的值

    ```shell
    get key
    ```

- #### mset

    ```shell
    mset key1 value1 key2 value2 
    ```

    批量设置值

- #### mget 

    ```shell
    mget key1 key2
    ```

    批量获取值

- #### incr

    incr 命令值不是整数，返回错误；值是整数，返回自增结果；键不存在，返回结果为1。

- #### 内部编码

    - ##### int 8 字节长整数

    - ##### embstr 小于等于39字节的字符串

    - ##### raw 大于39字节的字符串

    - ##### 使用 *object encoding* 命令查看编码

### 哈希

- #### 对应命令

    ```shell
    hset key field value
    
    hget key field value
    
    hdel key field value
    
    hexist key field 
    
    hkeys key
    
    hvals key
    
    hgetall key
    ```

- #### 内部编码

    - ##### 压缩列表

        当哈希元素小于默认的512个时，并且所有值小于64字节时，使用ziplist作为内部实现。

    - ##### 哈希表

### 列表

- #### 对应命令

    ```shell
    # 从左边添加
    lpush
    # 从右边添加
    rpush
    # 插入
    linsert key before|after pivot value
    
    # 查找，索引下标从左到右分别是0，N-1；从右到左分别是-1到-N
    lrange key start end
    
    # 获取指定下标
    lindex key index
    
    # 获取长度
    llen key
    
    # 从左侧删除
    lpop key
    # 从右侧删除
    rpop key
    ```

    ![image-20200809110428514](C:/Users/15003/AppData/Roaming/Typora/typora-user-images/image-20200809110428514.png)

### 集合

- #### 相关命令

    ```shell
    # 添加元素
    sadd key element
    # 删除元素
    srem key element
    # 计算元素个数
    scard key
    # 判断元素是否在集合中
    sismember key element
    # 随机从集合返回指定个数元素
    srandmember key count
    # 从集合随机弹出一个元素
    spop key
    # 获取所有元素
    smembers key
    # 求集合交集
    sinter key key
    # 求集合并集
    sunion key key
    # 求集合差集
    sdiff key key
    # 将集合结果保存
    sinterstore destination key 
    sunionstore destination key
    sdiffstore destination key
    ```

- #### 内部编码

    - #### intset

        当集合中的元素都是整数且元素小于默认的512时

    - #### hashtable

### 有序集合

- #### 相关命令

    ```shell
    # 添加成员
    zadd key score member score member
    # 计算成员个数
    zcard key
    # 计算成员分数
    zscore key member
    # 计算成员排名, 排名从0开始
    zrank key member
    # 删除成员
    zrem key member
    # 增加成员分数
    zincrby key increment member
    # 返回指定排名范围内的成员
    zrane key start end [withscores]
    ```

- #### 内部实现

    - #### ziplist

        当元素格式小于默认的128并且元素的值小于64字节时采用。

    - #### skiplist