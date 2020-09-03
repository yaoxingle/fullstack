> ###### 1#查看防火状态

systemctl status firewalld

service  iptables status

> ###### 2#暂时关闭防火墙

systemctl stop firewalld

service  iptables stop

> ###### 3#永久关闭防火墙

systemctl disable firewalld

chkconfig iptables off

> ###### 4#重启防火墙

systemctl enable firewalld

service iptables restart

> ###### 5#永久关闭后重启

//暂时还没有试过

chkconfig iptables on

> ###### 6#查看端口

lsof -i:端口号

> ###### 7#查看cpu信息

cat /proc/cpuinfo

cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c

> processor 逻辑处理器的id。
> physical id 物理封装的处理器的id。
> core id 每个核心的id。
> cpu cores 位于相同物理封装的处理器中的内核数量。
> siblings 位于相同物理封装的处理器中的逻辑处理器的数量。

> ###### 8#查看物理CPU个数

cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

> ###### 9#查看每个物理CPU中core的个数(即核数)


cat /proc/cpuinfo| grep "cpu cores"| uniq
