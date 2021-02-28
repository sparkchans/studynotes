## Linxu 防火墙

### Linux 防火墙原理

Linux 防火墙是一种典型的报过滤防火墙。它通过检测到达的数据包头中的信息，确定哪些数据包可以通过，哪一些应该被丢弃。防火墙行为的依据主要是数据包的目的地址、端口号和协议类型。

Linux 默认使用的表是 Filter 表，它默认包含了 3 个链，分别是 ：FORWARD，INPUT，OUTPUT。FORWARD  链中定义的规则作用于哪些需要转发到另外一个网络接口的数据包；INPUT  链中定义的规则作用于发送到本机的数据包；OUTPUT 链中定义的规则作用于从本机发送出去的数据包。

### Linux 防火墙管理命令 iptables

- 清空默认表中的信息

  ```shell
  iptables -F
  # 清空某一条特定链路的信息
  iptables -F INPUT
  ```

- 显示默认表中的规则信息

  ```shell
  iptables  -L
  ```

- 添加链路规则

  DROP 和 REJECT 区别在于：DROP  直接丢弃数据包且不响应；而 REJECT  丢弃数据后，还会给数据发送方发送一个响应。

  ```shell
  # 添加链路规则
  iptables -A chain-name -i interface -j target
  # 允许所有通过请求通过本地回环地址
  iptables -A INPUT -i lo -j INPUT
  # 增加对 22 端口的访问
  iptables -A INPUT -i eth0 -p tcp --dport 22 -j ACCEPT
  # 增加对 80 端口的访问
  iptables -A INPUT -i eth0 -p tcp --dport 80 -j ACCEPT
  # 给 INPUT 链增加一条日志记录，防火墙记录到的访问信息被保存在/var/log/messages 中
  iptables -A INPUT -i eth0 -j LOG
  ```

- 删除链路规则

  ```shell
  # 显示规则编号
  iptables -L --line-numbers
  # 删除指定编号的规则
  iptables -D number
  ```

  

  | 选项             | 含义                                         |
  | ---------------- | -------------------------------------------- |
  | -p proto         | 匹配网络协议：tcp，udp，icmp                 |
  | --icmp-type type | 匹配 ICMP 类型，和 -p icmp 配合使用。        |
  | -s source-ip     | 匹配来源主机的 IP                            |
  | -sport port      | 匹配来源主机的端口，和 -s source-ip 配合使用 |
  | -d dest-ip       | 匹配目标主机的 IP                            |
  | -dport port      | 匹配目标主机的端口，和 -d dest-ip 配合使用   |

  

