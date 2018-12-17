## 1、load average值的含义:
假设我们的系统是单CPU单内核的，把它比喻成是一条单向马路，把CPU任务比作汽车。当车不多的时候，load <1；
当车占满整个马路的时候 load=1；
当马路都站满了，而且马路外还堆满了汽车的时候，load>1

假设我们服务器CPU是2核，那么将意味我们拥有2条马路，我们的Load = 2时，所有马路都跑满车辆。
#查看CPU core 
grep 'model name' /proc/cpuinfo | wc -l
#查看CPU相信信息
cat /proc/cpuinfo
## 2、QPS/TPS
QPS（TPS）：每秒钟request/事务 数量
并发数： 系统同时处理的request/事务数        
响应时间：  一般取平均响应时间
(很多人经常会把并发数和TPS理解混淆）
它们之间的关系：
QPS（TPS）= 并发数/平均响应时间
## 3.查看有哪些进程占用了CPU，按照占用比率降序排列
ps aux|head -1;ps aux|grep -v PID|sort -rn -k +3|head -10
其中第一句主要是为了获取标题（USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND）。
接下来的grep -v PID是将ps aux命令得到的标题去掉，即grep不包含PID这三个字母组合的行，再将其中结果使用sort排序。
sort -rn -k +3该命令中的-rn的r表示是结果倒序排列，n为以数值大小排序，而-k +3则是针对第3列的内容进行排序，再使用head命令获取默认前10行数据。(其中的|表示管道操作)

## 4.根据3可以推导出查看search进程占用的内存和CPU的命令如下：
ps aux|head -1;ps aux|grep -v PID|grep search

## 5、vim全局替换命令为：:%s/源字符串/目的字符串/g
  %s/org/dst/g
  
## 6.1 查看文件长度
   wc -l 1.log
6.2 查看内容所在的行数
cat 1.log|grep -n 'Exception'  
6.3 搜索12和26行之间的数据
sed -n '12,26p' 1.log
6.4 显示第100行数据
sed -n '10p' jmeter.log

## 7.linux机器内存不足时，会杀死进程，日志记录在：
/var/log/messages

## 8.source /etc/profile   使得修改后的系统环境变量立刻生效，系统环境变量对每个用户都是可用的
  source命令也称为“点命令”也就是一个点符号（.）
  一般JDK的一些常用命令都会配置在这个文件夹下，然后被添加到PATH下，如此在linux下就可以直接使用命令，如：java -version
  对于每个用户而言，在主目录(~)下的.bash_profile存放的是当前用户的环境变量，只对当前用户有效"
## 9.删除多个进程：
ps -aux|grep nginx|awk '{print $2}'|xargs kill -9"
"10.dig raf.nubia.cn
可以查看域名与IP绑定的路径"
