1. dd: 用来转换或复制文件, 对设备进行备份

   - if: 输入文件
   - of: 输出文件
   - bs: 指定块大小
   - cbs: 指定转换缓冲区的大小
   - skip=n: 从输入文件开头跳过 blocks 个块后再复制
   - seek=n: 从输出文件开头跳过 blocks 个块后再复制
   - count=n: 仅拷贝 blocks 个块
   - conv=conversion: 用指定的参数转换文件
     - lcase: 把大写字符转换为小写字符
     - ucase: 把小写字符转换为大写字符
     - swap: 交换输入的每对字节 

2. 参考

   [1] : [Linux常用命令 —— dd命令和split命令](<https://www.yunforum.net/group-topic-id-1381.html>)

   [2] : [linux下dd命令详解](https://www.cnblogs.com/licheng/archive/2008/03/21/1116492.html)