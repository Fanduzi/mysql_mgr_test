# mysql_mgr_test
use script to create MySQL Group Replication environments fast and efficiently.

1. download MySQL installation packages. www.mysql.com and ensure IP is configured is /etc/hosts.
for example, /etc/hosts as below, 10.127.1.18 is current machine IP, and mysqltestdb is hostname,else MGR creation will have issues.
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.127.1.18  mysqltestdb

2. change parameters from file auto.cnf following your environment needs.
for example, change below parameter values, you need to put mysql running path to /usr/local/mysql and create directory /home/data
these can be changed following your needs.

base_dir=/usr/local/mysql
base_data_dir=/home/data

3. config file init.lst following your environments needs.
for example ,I need to crate 5 nodes in one machine, I should config init.lst as below.
```
24801 s1  24901 Y
24802 s2  24902 Y 
24803 s3  24903 Y 
24804 s4  24904 Y
24805 s5  24905 Y 
```
4. run script init.sh to start MGR environments creation.

if you need to create single Primary environments, you should put seconds onwards rows, the last values as N,for example.
```
24801 s1  24901 Y
24802 s2  24902 N 
24803 s3  24903 N 
24804 s4  24904 N
24805 s5  24905 N
```
if you need to create multi-Primary environments, you should put node role as Y, for example.
```
24801 s1  24901 Y
24802 s2  24902 Y 
24803 s3  24903 Y 
24804 s4  24904 Y
24805 s5  24905 Y 
```

5. use check.sh to check node status, you should use input parameter node_name which is included in init.lst
for example, we want to check node status ,node name is s2 which is from init.lst
I should use below command to check
sh check_node.sh s2

6. enjoy and feedback if you have more questions, mail to jeanrock@126.com




## 如果要实现快速部署MGR，下面是一些基本的步骤。
1. 首先需要下载MySQL软件，配置/etc/hosts文件，下载二进制包都不需要什么安装了，直接解压放入指定的目录即可，比如/usr/local/mysql。目前最新的版本是官方的5.7.19
假设 10.127.1.18是服务器的IP，那么在/etc/hosts里面就尤其需要注意，把它务必配置好。
比如下面的/etc/hosts的文件内容：
```
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.127.1.18  mysqltestdb
```
2. 有一个统一的配置文件 auto.cnf ,在这个配置文件里配置MySQL软件的路径，数据文件的路径即可。
这些没有固定的内容，都是根据你的需求和具体的配置来定。比如auto.cnf的内容如下：
```
export base_dir=/usr/local/mysql
export base_data_dir=/home/data
```
3. 配置节点列表，这是MGR部署关键的一个配置文件了。
每个节点的配置分为4部分：节点的端口，节点的别名，节点的内部端口，节点的角色。
节点的端口是数据库提供数据访问的端口，节点的别名，因为是在同一台服务器上模拟测试，所以需要标识不同节点的名字。
节点的内部端口，这是MGR在各个节点之间的通信端口，最后是节点的角色，如果为Y就是提供读写权限，负责，只有读权限。
如果是单主模式，最后的标识位第一个是Y,其他都为N
```
24801 s1  24901 Y
24802 s2  24902 N 
24803 s3  24903 N 
24804 s4  24904 N
24805 s5  24905 N
```
如果是多主模式，则节点的角色都要标记为Y
```
24801 s1  24901 Y
24802 s2  24902 Y 
24803 s3  24903 Y 
24804 s4  24904 Y
24805 s5  24905 Y 
```
4. 运行脚本init.sh 不需要输入任何的参数。
这是最耗时的步骤，也是最核心的脚本。

5. 使用`check_node.sh` 脚本可以检查各个节点的状态，，输入参数为节点别名，比如s1
   使用`start_node.sh` 脚本可以启动指定的节点,，输入参数为节点别名，比如s1
   使用`reset_node.sh` 脚本可以在节点需要重新加入集群的时候使用，输入参数为节点别名，比如s1
   使用`stop_node.sh`  脚本可以停止指定的节点，输入参数为节点别名，比如s1
   使用`conn_node.sh`  脚本可以连接到指定的节点，输入参数为节点别名，比如s1   
比如我要检查节点s2的状态，是否为oline,是否应用数据正常，可以使用check_node.sh来查看。
```
sh check_node.sh s2
```
