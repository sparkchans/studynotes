##### 基本脚本

- ###### 使用变量

    ```shell
    # 使用 set 查看全部环境变量
    # 使用环境变量
    echo UID: $UID
    echo HOME: $HOME
    # 用户定义变量, 中间不能出现任何空格
    var1=10
    # 将命令执行结果赋值给变量
    curDate=$(date)
    # 输出重定向
    # > 覆盖已有文件
    # >> 在已有文件上追加数据
    # 输入重定向
    # < 
    # << 内联输入重定向(inline input redirection)
    wc << EOF
    > test string 1
    > test string 2
    > test string 3
    >EOF
    ```

- ######  执行数学运算

    ```shell
    # 使用方括号，方括号前加 $ 是为了和 test 进行区分
    var1=$[1 + 5]
    ```

- ###### 退出脚本

    ```shell
    # Linux 提供变量 $? 保存上个已执行命令的退出状态码
    # exit 指定退出码
    var1=10
    exit 5
    echo $?
    ```

##### 结构化命令

- ###### if-then

    ```shell
    # if-then
    if pwd; then
    	echo "It worked"
    fi
    # if-then-else
    if pwd; then
    	echo "It worked"
    else 
    	ehco "It doesn't work"
    fi 
    # if-elif-then
    if grep $testuser /etc/passwd; then
    	echo "The user exists on this system"
    elif ls -d /home/$testuser; then
    	echo "The user has a directory"
    fi
    ```

- ###### test

    ```shell
    ################ 数值比较 ############
    # 左右方括号之前必须加上一个空格
    value1=10
    if [ $value1 -gt 5 ]; then
    	echo "The test value is greater than 5"
    elif [ $value1 -eq 5 ]; then
    	echo "The test value is equal to 5"
    elif [ $value1 -lt 5 ]; then
    	echo "The test value is less than 5"
    ################ 字符串比较 ############
    # 比较运算符左右需要加空格，>，< 符号需要转义
    # -n str1 检查 str1 的长度是否非 0
    # -z str1 检查 str1 的长度是否为 0
    testuser=root
    if [ $USER = $testuser ]; then
    	echo "Welcome $testuser"
    fi
    ################ 文件比较 ############
    # -d file 检查 file 是否存在并且是一个目录
    # -e file 检查 file 是否存在
    # -r file 检查 file 是否存在并且可读
    # -w file 检查 file 是否存在并且可写
    # -f file 检查 file 是否存在并且是一个文件
    # file1 -nt file2 检查 file1 是否比 file2 新
    # file1 -ot file2 检查 file1 是否比 file2 旧 
    ################ 复合条件 ############
    # [ condition1 ] && [ condition2 ]
    # [ condition1 ] || [ condition2 ]
    if [ -d $HOME ] && [ -w $HOME/testing ]; then
    	echo "The file exist and you can write it"
    else 
    	echo "I cannot write to the file"
    fi
    ################ 比较过程中使用高级数学表达式 ############
    # ** 幂运算
    # > 符号不需要转义
    val1=10
    if (( $val1 ** 2 > 90 )); then
    	(( val2 = $val1 ** 2 ))
    	echo "The square of $val1 is $val2"
    fi
    # 正则表达式匹配
    if [[ $USER == r* ]]; then
    	echo "Hello $USER"
    else 
    	echo "Sorry, I do not know you"
    fi
    ```

- ###### case

    ```shell
    case $USER in
    rich | barbara)
    	echo "Welcome, $USER";;
    testing)
    	echo "Special testing account";;
    *)
    	echo "Sorry, you are not allowed here"
    esac
    ```

    