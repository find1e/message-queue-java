>写在前面: 
> 1. 在开始coding前请仔细阅读以下内容

这个是Java Demo, C++ Demo 请查看[链接](https://code.aliyun.com/middlewarerace2018/queue-race-2018-cpp)。
提交的时候，代码地址只需要写":"后面的内容，也即省去"git@code.aliyun.com"。
CPP的提交，请在镜像地址里写"CPP"，Java的无需填写。
用户名和密码可以不用填写。

```
-Xms1G -Xmx1G -XX:+UseConcMarkSweepGC -XX:+HeapDumpOnOutOfMemoryError -XX:+PrintGCDetails
```


## 1. 题目背景
Apache RocketMQ作为的一款分布式的消息中间件，历年双十一承载了万亿级的消息流转，为业务方提供高性能低延迟的稳定可靠的消息服务。随着业务的逐步发展和云上的输出，单机队列数量的逐步增加，给RocketMQ带来了新的挑战。复赛的题目要求设计一个单机百万队列以上的存储引擎，单机内存有限，需要充分利用数据结构与存储技术，最大化吞吐量。  

## 2. 题目描述

### 2.1 题目内容
实现一个进程内的队列引擎，单机可支持100万队列以上。


### 2.2 语言限定
JAVA和C++


注意：
Java和C++一起参与排名。
C++的Demo还在制作中（预计两天后可以出来），其核心逻辑与Java是一致的。选手们可以参考Java先行开始代码编写。


## 3.  程序目标

仔细阅读demo项目中的QueueStore，DefaultQueueStoreImpl，DemoTester三个类。

你的coding目标是重写DefaultQueueStoreImpl，并实现以下接口:  
abstract void put(String queueName, String message);  
abstract Collection<String> get(String queueName, long offset, long num);  

注：
评测时的数据存储路径为：/alidata1/race2018/data。
日志请直接打印在控制台标准输出，可以使用System.out.println，如果使用日志框架，请配置为ConsoleAppender。注意不要把日志输出到Error通道（也即不要使用System.err.println，如果使用日志框架，则不要使用log.error）。评测程序会把控制台标准输出的内容搜集出来，放置在OSS上面供用户排错，但是请不要密集打印日志，单次评测，最多不能超过100M。
日志下载路径为：http://race2018.oss-cn-beijing.aliyuncs.com/{teamcode}.logs.tgz，只保存最近一次任务的日志。


## 4.参赛方法说明
1. 在阿里天池找到"中间件性能挑战赛"，并报名参加
2. 在code.aliyun.com注册一个账号，并新建一个仓库名，并将大赛官方账号middlewarerace2018添加为项目成员，权限为reporter
3. fork或者拷贝本仓库的代码到自己的仓库，并实现自己的逻辑
4. 在天池提交成绩的入口，提交自己的仓库git地址，等待评测结果
5. 坐等每天10点排名更新


## 4. 测试环境描述
测试环境为4c8g的ECS，限定使用的最大JVM大小为4GB(-Xmx4g)。带一块300G左右大小的SSD磁盘。

SSD性能大致如下：
iops 1w 左右；块读写能力(一次读写4K以上) 在200MB/s 左右。

ulimit -a:

```
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 31404
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 6553560
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 10240
cpu time               (seconds, -t) unlimited
max user processes              (-u) 31404
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```
磁盘调度算法是 deadline
其它系统参数都是默认的。

## 5. 程序校验逻辑

校验程序分为三个阶段：
1.发送阶段
2.索引校验阶段
3.顺序消费阶段
请详细阅读DemoTester以理解评测程序的逻辑。

### 5.1. 程序校验规模说明
1.各个阶段线程数在20~30左右
2.发送阶段：消息大小在50字节左右，消息条数在20亿条左右，也即发送总数据在100G左右
3.索引校验阶段：会对所有队列的索引进行随机校验；平均每个队列会校验1~2次；
4.顺序消费阶段：挑选20%的队列进行全部读取和校验；
5.发送阶段最大耗时不能超过1800s；索引校验阶段和顺序消费阶段加在一起，最大耗时也不能超过1800s；超时会被判断为评测失败。

## 6. 排名规则

在结果校验100%正确的前提下，按照平均tps从高到低来排名


## 7. 第二/三方库规约

* 仅允许依赖JavaSE 8 包含的lib
* 可以参考别人的实现，拷贝少量的代码
* 我们会对排名靠前的代码进行review，如果发现大量拷贝别人的代码，将扣分

## 8.作弊说明

所有消息都应该进行按实际发送的信息进行存储，可以压缩，但不能伪造。
如果发现有作弊行为，比如通过hack评测程序，绕过了必须的评测逻辑，则程序无效，且取消参赛资格。


