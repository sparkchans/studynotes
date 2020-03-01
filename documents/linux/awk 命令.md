##### gawk 命令

搜索一个或多个文件中的匹配模式的记录，然后用 `action` 去处理这些记录。

- 命令格式：`gawk [options] [program] [file-list]`

- 参数选项

    - -F：指定分隔符

- 程序：`pattern {action}`

    - 模式(Pattern)
    - 动作(Action)

- 常用变量

    - $0：当前行
    - \$1-\$n：当前行的列
    - FILENAME：当前输入文件的名称
    - FS：输入文件列分隔符
    - NF：该行的列数
    - NR：该行的行号
    - OFS：输出列的分隔符
    - ORS：输出行的分隔符
    - RS：输入行的分隔符

- 简单使用

    ```shell
    # 打印文件内容
    gawk '{print}' cars
    # 打印匹配的文件内容
    gawk '/chevy/' cars
    # 打印行按分隔符分隔之后的列
    gawk '{print $3, $1}' cars
    # 打印匹配模式的行
    gawk '/chevy/ $3 $1' cars
    # 打印某列匹配模式的行
    gawk '$1 ~ /h/' cars
    # 打印匹配模式的行
    gawk '$5 >= 2000 && $5 <= 5000' cars
    # 选择范围
    gawk '/chevy/, /bmw/' cars
    # 使用 BEGIN pattern
    cat pr_header
    BEGIN {
    print "col1		col2	col3"
    print "---------------------"
    }
    {print}
    
    gawk -f pr_header cars
    # 打印每行字符数并排序, $0 代表当前行
    gawk '{print length, $0}' cars | sort -n
    # 打印行字符数超过 24 的行号, NR 代表行号
    gawk 'length > 24 {print NR}' cars
    # 打印匹配模式的行号，忽略大小写
    gawk '/world/i {print NR}' cars
    # 打印范围内的行
    gawk 'NR == 2, NR == 4' cars
    # 使用 END pattern
    gawk '{print} END {print NR, "cars for sale."}' cars
    # 替换输出
    #!/bin/gawk -f {
    	if ($1 ~ /ply/) $1 = "plymouth"
    	if ($1 ~ /chev/) $1 = "chevrolet"
    	print
    }
    # 设置输出分隔符
    gawk 'BEGIN {OFS = "\t"} {$1 = $1; print;}' cars
    gawk 'BEGIN {OFS = "\t"} {$1 = $1} {print;}' cars
    ```

##### 参考

[1] : [AWK 技巧（取倒列，过滤行，匹配，不匹配，内置变量等）](https://www.cnblogs.com/kevingrace/p/8481965.html)

[2] : [setting the output field separator in awk](https://stackoverflow.com/questions/20844666/setting-the-output-field-separator-in-awk)

