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

  