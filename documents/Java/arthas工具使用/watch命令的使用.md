### thread 命令

- thread id 显示线程的堆栈信息

  ![image-20210228165931712](https://i.loli.net/2021/02/28/RTtJe1fY9Z4cPrq.png)

- thread -n 打印最忙的线程

  

![image-20210228170053599](https://i.loli.net/2021/02/28/v5l81zRYq7rsaME.png)

- thread -b 找出当前阻塞其他线程的线程

  ![image-20210228170212626](https://i.loli.net/2021/02/28/rxeVN4E87msuLRa.png)

### jad

- 在线反编译 class 文件

  ![image-20210228170723996](https://i.loli.net/2021/02/28/csPO6hNT8QmdGXV.png)

### stack

- 查看方法调用链

  ![image-20210228170844390](https://i.loli.net/2021/02/28/Yo1smkEfUxnwT9W.png)

### watch 

- 观察方法的参数和返回值，和带 -f 参数效果相同

  ![image-20210228171641239](https://i.loli.net/2021/02/28/srLqdGRmwbhnoPx.png)

- 观察方法的入参

  ![image-20210228171718747](https://i.loli.net/2021/02/28/3d67awMvzrNX1Zs.png)

- 观察方法结束返回值

  ![image-20210228171836368](https://i.loli.net/2021/02/28/2NDOoZLWfaSRQzK.png)

- 观察方法抛出异常的返回值

  ![image-20210228171914019](https://i.loli.net/2021/02/28/mJKxc9arWp1iSP8.png)

- 观察方法结束的返回值，通过 -x 参数指定查看深度

  ![image-20210228172043331](https://i.loli.net/2021/02/28/Jd4sczUjEBH5QYO.png)

- 观察异常信息

  ![image-20210228172217155](https://i.loli.net/2021/02/28/P7M2jVONvfHso3Q.png)

- 按照入参进行过滤

  ![image-20210228172314715](https://i.loli.net/2021/02/28/qAy5MkOXUFWVBYw.png)

- 按照方法执行耗时进行过滤

  ![image-20210228172504245](https://i.loli.net/2021/02/28/QxGKoh5nuNTUv3i.png)

- 查看对象属性值

  ![image-20210228172814612](https://i.loli.net/2021/02/28/hS2QypiORTr1wMf.png)

### monitor

- 统计方法执行， -c 为统计周期

  ![image-20210228173844375](https://i.loli.net/2021/02/28/olYif2xJ5kjXSHD.png)

### trace 

- 输出方法调用的耗时

  ![image-20210228174342736](https://i.loli.net/2021/02/28/aP8ktFw4OGIUbKy.png)

- 输入方法耗时包含 JDK 方法

  ![image-20210228174454663](https://i.loli.net/2021/02/28/4SKmkLhsryTVezb.png)

- 根据耗时进行过滤

  ![image-20210228174621063](https://i.loli.net/2021/02/28/76UzJIVNDmjAfc5.png)