##### sed 命令

对来自标准输入或文件流的输入进行过滤，输出符合过滤条件的行。

- 命令格式：`sed [-n] program [file-list]`

- 程序(program)

    - 地址(address)：指定从读取的开始行和结束行

    - 指令(instruction)：对文本进行操作
        - a(append)
        - c(change)
        - d(delete)
        - i(insert)
        - p(print)
        - q(quit)
    - 参数列表(argument-list)

- 基本使用

    ```shell
    # 打印匹配模式的行
    sed -n '/hello/ p' lines
    # 打印指定范围内的行
    sed -n '3, 6 p' lines
    # 打印前 5 行
    sed '5 q' lines
    ```

    