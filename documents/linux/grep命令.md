- ### 正则表达式指定

  ```shell
  # 这里会搜索 "hello*" 这个字符串, * 不被解释为通配符
  grep -F "hello*" test.txt
  ```

  

- ### 匹配指定

  ```shell
  # 通过 -e 参数指定多个表达式, 搜索含有 hello 或 nihao 字符串的
  grep -e hello -e nihao test.txt
  # 通过管道连接搜索含有 hello 和 nihao 字符串的
  grep hello test.txt | grep nihao
  # 通过 -i 参数忽略大小写
  grep -i hello test.txt
  # 通过 -v 参数指定选择不匹配的
  grep -v hello test.txt
  ```

- ### 输出控制

  ```shell
  # -c 参数指定输出为匹配的行数
  grep -c hello test.txt
  # -l 指定输出第一个匹配的文件名
  grep -l hello test.txt
  # -m 指定匹配多少个后停止输出
  grep -m 3 hello test.txt
  ```

- ####  输出前后匹配的行

    ```shell
    grep -B 10 -A 10 "test" /var/test.log
    ```

- #### 正则表达式时查询

    ```shell
    grep "test*" /var/test.log
    ```

- #### 固定字符串查询

    ```shell
    # 这里不会将*当作通配符
    grep -F "test*" /var/test.log
    ```

- #### 匹配最多行数后不再输出

    ```shell
    grep -m 10 "test*" /var/test.log
    ```

- #### 输出匹配的行号和文件名

    ```shell
    grep -n -H "test*" /var/test.log
    ```

- #### 输出匹配的文件名

    ```shell
    # 输出匹配的文件名
    grep -l "test*" /var/test.log
    
    # 输出非匹配的文件名
    grep -L "test*" /var/test.log
    ```

    