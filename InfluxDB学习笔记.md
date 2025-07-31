





# 概述

## 使用场景

InfluxDB 是一种时序数据库，时序数据库通常被用在监控场景，比如运维和 IOT（物联网）领域。这类数据库旨在存储时序数据并实时处理它们

比如。我们可以写一个程序将服务器上CPU的使用情况每隔10秒钟向InfluxDB中写 入一条数据。接着，我们写一个查询语句，查询过去 30秒 CPU的平均使用情况，然后让这个查询语句也每隔10秒钟执行一次。最终，我们配置一条报警规则，如果查询语句的执行结果>xxx，就立刻触发报警

上述就是一个指标监控的场景，在 IOT 领域中，也有大量的指标需要我们监控。比如， 机械设备的轴承震动频率，农田的湿度温度等等





## 为什么不用关系型数据库

### 写入性能

关系型数据库也是支持时间戳的，也能够基于时间戳进行查询。但是，从我们的使用场景出发，需要注意数据库的写入性能。通常，关系型数据库会采用B+树数据结构，在数据写入时，有可能会触发叶裂变，从而产生了对磁盘的随机读写，降低写入速度

当前市面上的时序数据库通常都是采用LSM Tree的变种，顺序写磁盘来增强数据的写入能力。通常时序数据库都会保证在单点每秒数十万的写入能力



### 数据价值

时序数据库一般用于指标监控场景。这个场景的数据有一个非常明显的 特点就是冷热差别明显。通常，指标监控只会使用近期一段时间的数据，比如我只查询某 个设备最近10分钟的记录，10分钟前的数据我就不再用了。那么这10分钟前的数据，对我们来说就是冷数据，应该被压缩放到磁盘里去来节省空间。而热数据因为经常要用，数据库就应该让它留在内存里，等待查询



### 数据只写不改

这是时序数据的一大特点。与之相应，时序数据库基本上是插入操作较多







## 版本特性比较

2020 年 InfluxDB推出了 2.0的正式版。2.x同 1.x相比，底层引擎原理相差不大，但会 涉及一些概念的转变（例如 db/rp换成了 org/bucket）。另外，对于 TICK生态来说，1.x需 要自己配置各个组件。2.x则是更加方便集成，有很棒的管理页面

另外，在查询语言方面，1.x是使用InfluxQL进行查询，它的风格近似SQL。2.x推出 了 FLUX 查询语言，可以使用函数与管道符，是一种更符合时序数据特性的更具表现力的 查询语言









# 部署InfluxDB

## Windows

### 第一步：打开连接

https://docs.influxdata.com/influxdb/v2/install/

![image-20250329004719252](img/InfluxDB学习笔记/image-20250329004719252.png)





### 第二步：下载





![image-20250329004742201](img/InfluxDB学习笔记/image-20250329004742201.png)







![image-20250329004803415](img/InfluxDB学习笔记/image-20250329004803415.png)







### 第三步：解压

解压后的文件内容如下：

![image-20250329005811938](img/InfluxDB学习笔记/image-20250329005811938.png)





### 第四步：运行

```sh
./influxd
```



```sh
PS D:\opensoft\influxdb> ls


    目录: D:\opensoft\influxdb


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
------         2024/12/3      1:53      114861056 influxd.exe
------         2024/12/3      1:53           1067 LICENSE
------         2024/12/3      1:53          11327 README.md


PS D:\opensoft\influxdb> .\influxd
```



```sh
PS D:\opensoft\influxdb> .\influxd
2025-03-28T16:59:33.320006Z     info    Welcome to InfluxDB     {"log_id": "0vZQxrI0000", "version": "v2.7.11", "commit": "fbf5d4ab5e", "build_date": "2024-12-02T17:48:13Z", "log_level": "info"}
2025-03-28T16:59:33.329328Z     info    Resources opened        {"log_id": "0vZQxrI0000", "service": "bolt", "path": "C:\\Users\\mao\\.influxdbv2\\influxd.bolt"}
2025-03-28T16:59:33.339934Z     info    Resources opened        {"log_id": "0vZQxrI0000", "service": "sqlite", "path": "C:\\Users\\mao\\.influxdbv2\\influxd.sqlite"}
2025-03-28T16:59:33.344225Z     info    Bringing up metadata migrations {"log_id": "0vZQxrI0000", "service": "KV migrations", "migration_count": 20}
2025-03-28T16:59:33.729370Z     info    Bringing up metadata migrations {"log_id": "0vZQxrI0000", "service": "SQL migrations", "migration_count": 8}
2025-03-28T16:59:33.786410Z     info    Using data dir  {"log_id": "0vZQxrI0000", "service": "storage-engine", "service": "store", "path": "C:\\Users\\mao\\.influxdbv2\\engine\\data"}
2025-03-28T16:59:33.786942Z     info    Compaction settings     {"log_id": "0vZQxrI0000", "service": "storage-engine", "service": "store", "max_concurrent_compactions": 16, "throughput_bytes_per_second": 50331648, "throughput_bytes_per_second_burst": 50331648}
2025-03-28T16:59:33.786942Z     info    Open store (start)      {"log_id": "0vZQxrI0000", "service": "storage-engine", "service": "store", "op_name": "tsdb_open", "op_event": "start"}
2025-03-28T16:59:33.786942Z     info    Open store (end)        {"log_id": "0vZQxrI0000", "service": "storage-engine", "service": "store", "op_name": "tsdb_open", "op_event": "end", "op_elapsed": "0.000ms"}
2025-03-28T16:59:33.786942Z     info    Starting retention policy enforcement service   {"log_id": "0vZQxrI0000", "service": "retention", "check_interval": "30m"}
2025-03-28T16:59:33.786942Z     info    Starting precreation service    {"log_id": "0vZQxrI0000", "service": "shard-precreation", "check_interval": "10m", "advance_period": "30m"}
2025-03-28T16:59:33.787979Z     info    Starting query controller       {"log_id": "0vZQxrI0000", "service": "storage-reads", "concurrency_quota": 1024, "initial_memory_bytes_quota_per_query": 9223372036854775807, "memory_bytes_quota_per_query": 9223372036854775807, "max_memory_bytes": 0, "queue_size": 1024}
2025-03-28T16:59:33.790974Z     info    Configuring InfluxQL statement executor (zeros indicate unlimited).     {"log_id": "0vZQxrI0000", "max_select_point": 0, "max_select_series": 0, "max_select_buckets": 0}
2025-03-28T16:59:33.804986Z     info    Starting        {"log_id": "0vZQxrI0000", "service": "telemetry", "interval": "8h"}
2025-03-28T16:59:33.805496Z     info    Listening       {"log_id": "0vZQxrI0000", "service": "tcp-listener", "transport": "http", "addr": ":8086", "port": 8086}

```





### 第五步：浏览器打开8086端口

![image-20250329010021348](img/InfluxDB学习笔记/image-20250329010021348.png)





http://127.0.0.1:8086/



![image-20250329010058711](img/InfluxDB学习笔记/image-20250329010058711.png)





### 第六步：配置用户和密码



![image-20250329010117227](img/InfluxDB学习笔记/image-20250329010117227.png)



![image-20250329010246689](img/InfluxDB学习笔记/image-20250329010246689.png)



得到token

```sh
cR13uybHvqWNvMe82LHJp95Xs8LhwRg3awJGzMlc4x2AJNm2EYLGAWK_s45o7vp1Z25oP9oPDiKWnb-1WayjwQ==
```







## linux

### apt方式安装

#### 第一步：设置镜像



```sh
wget -qO- https://repos.influxdata.com/influxdb.key | sudo apt-key add -
source /etc/lsb-release
echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
```



```sh
root@mao:/home/server# wget -qO- https://repos.influxdata.com/influxdb.key | sudo apt-key add -
Warning: apt-key is deprecated. Manage keyring files in trusted.gpg.d instead (see apt-key(8)).
OK
root@mao:/home/server# source /etc/lsb-release
root@mao:/home/server# echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
deb https://repos.influxdata.com/ubuntu noble stable
root@mao:/home/server# 
```





#### 第二步：更新



```sh
sudo apt-get update
```







#### 第三步：安装

```sh
sudo apt-get install influxdb2
```





```sh
root@mao:/home/server# sudo apt-get install influxdb2
正在读取软件包列表... 完成
正在分析软件包的依赖关系树... 完成
正在读取状态信息... 完成                 
下列软件包是自动安装的并且现在不需要了：
  libmalcontent-0-0 python3-netifaces
使用'sudo apt autoremove'来卸载它(它们)。
将会同时安装下列软件：
  influxdb2-cli
下列【新】软件包将被安装：
  influxdb2 influxdb2-cli
升级了 0 个软件包，新安装了 2 个软件包，要卸载 0 个软件包，有 148 个软件包未被升级。
需要下载 61.3 MB 的归档。
解压缩后会消耗 147 MB 的额外空间。
您希望继续执行吗？ [Y/n] y
获取:1 https://repos.influxdata.com/debian stable/main amd64 influxdb2 amd64 2.7.11-1 [49.6 MB]
获取:2 https://repos.influxdata.com/debian stable/main amd64 influxdb2-cli amd64 2.7.5-1 [11.7 MB]                                                                                  
已下载 61.3 MB，耗时 3分 57秒 (258 kB/s)                                                                                                                                            
正在选中未选择的软件包 influxdb2。
(正在读取数据库 ... 系统当前共安装有 345601 个文件和目录。)
准备解压 .../influxdb2_2.7.11-1_amd64.deb  ...
正在解压 influxdb2 (2.7.11-1) ...
正在选中未选择的软件包 influxdb2-cli。
准备解压 .../influxdb2-cli_2.7.5-1_amd64.deb  ...
正在解压 influxdb2-cli (2.7.5-1) ...
正在设置 influxdb2 (2.7.11-1) ...
Synchronizing state of influxdb.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable influxdb
正在设置 influxdb2-cli (2.7.5-1) ...
root@mao:/home/server# 

```



```sh
sudo systemctl start influxdb
```







#### 第四步：设置开机自启



```sh
sudo systemctl enable influxdb
```



```sh
root@mao:/home/server# sudo systemctl enable influxdb
Synchronizing state of influxdb.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable influxdb
root@mao:/home/server# sudo systemctl status influxdb
● influxdb.service - InfluxDB is an open-source, distributed, time series database
     Loaded: loaded (/usr/lib/systemd/system/influxdb.service; enabled; preset: enabled)
     Active: active (running) since Sun 2025-03-30 00:02:13 CST; 4s ago
       Docs: https://docs.influxdata.com/influxdb/
    Process: 99112 ExecStart=/usr/lib/influxdb/scripts/influxd-systemd-start.sh (code=exited, status=0/SUCCESS)
   Main PID: 99113 (influxd)
      Tasks: 38 (limit: 22686)
     Memory: 51.2M (peak: 80.1M)
        CPU: 2.004s
     CGroup: /system.slice/influxdb.service
             └─99113 /usr/bin/influxd

3月 30 00:02:12 mao influxd-systemd-start.sh[99113]: ts=2025-03-29T16:02:12.731888Z lvl=info msg="Starting query controller" log_id=0v_f4HyW000 service=storage-reads concurrency_qu>
3月 30 00:02:12 mao influxd-systemd-start.sh[99113]: ts=2025-03-29T16:02:12.743971Z lvl=info msg="Configuring InfluxQL statement executor (zeros indicate unlimited)." log_id=0v_f4H>
3月 30 00:02:12 mao influxd-systemd-start.sh[99113]: ts=2025-03-29T16:02:12.756105Z lvl=info msg=Starting log_id=0v_f4HyW000 service=telemetry interval=8h
3月 30 00:02:12 mao influxd-systemd-start.sh[99113]: ts=2025-03-29T16:02:12.756528Z lvl=info msg=Listening log_id=0v_f4HyW000 service=tcp-listener transport=http addr=:8086 port=80>
3月 30 00:02:12 mao influxd-systemd-start.sh[99147]: time="2025-03-30T00:02:12+08:00" level=warning msg="DBUS_SESSION_BUS_ADDRESS envvar looks to be not set, this can lead to runaw>
3月 30 00:02:12 mao influxd-systemd-start.sh[99147]: Command "print-config" is deprecated, use the influx-cli command server-config to display the configuration values from the run>
3月 30 00:02:13 mao influxd-systemd-start.sh[99185]: time="2025-03-30T00:02:13+08:00" level=warning msg="DBUS_SESSION_BUS_ADDRESS envvar looks to be not set, this can lead to runaw>
3月 30 00:02:13 mao influxd-systemd-start.sh[99185]: Command "print-config" is deprecated, use the influx-cli command server-config to display the configuration values from the run>
3月 30 00:02:13 mao influxd-systemd-start.sh[99112]: InfluxDB started
3月 30 00:02:13 mao systemd[1]: Started influxdb.service - InfluxDB is an open-source, distributed, time series database.

root@mao:/home/server# 
```







#### 第五步：访问

http://maosl.cn:8086/

![image-20250330000444430](img/InfluxDB学习笔记/image-20250330000444430.png)







![image-20250330000550860](img/InfluxDB学习笔记/image-20250330000550860.png)









#### 第六步：安装Chronograf

下载并导入GPG密钥

```sh
wget -q https://repos.influxdata.com/influxdata-archive_compat.key
echo '393e8779c89ac8d958f81f942f9ad7fb82a25e133faddaf92e15b16e6ac9ce4c influxdata-archive_compat.key' | sha256sum -c && cat influxdata-archive_compat.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/influxdata-archive_compat.gpg > /dev/null
```



添加仓库配置

```sh
# Ubuntu/Debian通用（自动识别版本）
echo 'deb [signed-by=/etc/apt/trusted.gpg.d/influxdata-archive_compat.gpg] https://repos.influxdata.com/debian stable main' | sudo tee /etc/apt/sources.list.d/influxdata.list
```



更新APT缓存

```sh
sudo apt-get update
```





安装Chronograf

```sh
sudo apt-get install chronograf
```



```sh
root@mao:/etc/influxdb# sudo apt-get install chronograf
正在读取软件包列表... 完成
正在分析软件包的依赖关系树... 完成
正在读取状态信息... 完成                 
下列软件包是自动安装的并且现在不需要了：
  libmalcontent-0-0 python3-netifaces
使用'sudo apt autoremove'来卸载它(它们)。
下列【新】软件包将被安装：
  chronograf
升级了 0 个软件包，新安装了 1 个软件包，要卸载 0 个软件包，有 149 个软件包未被升级。
需要下载 46.1 MB 的归档。
解压缩后会消耗 141 MB 的额外空间。
获取:1 https://repos.influxdata.com/debian stable/main amd64 chronograf amd64 1.10.6-1 [46.1 MB]
已下载 46.1 MB，耗时 3分 0秒 (256 kB/s)                                                                                                                                             
正在选中未选择的软件包 chronograf。
(正在读取数据库 ... 系统当前共安装有 345514 个文件和目录。)
准备解压 .../chronograf_1.10.6-1_amd64.deb  ...
正在解压 chronograf (1.10.6-1) ...
正在设置 chronograf (1.10.6-1) ...
Created symlink /etc/systemd/system/multi-user.target.wants/chronograf.service → /usr/lib/systemd/system/chronograf.service.
root@mao:/etc/influxdb# 

```









```sh
sudo systemctl start chronograf
sudo systemctl enable chronograf
```





http://maosl.cn:8888/



![image-20250329231303616](img/InfluxDB学习笔记/image-20250329231303616.png)





 	



![image-20250329232251417](img/InfluxDB学习笔记/image-20250329232251417.png)







![image-20250329232414260](img/InfluxDB学习笔记/image-20250329232414260.png)

















### 压缩版安装

#### 第一步：打开网站



![image-20250329221549793](img/InfluxDB学习笔记/image-20250329221549793.png)







选择linux

![image-20250329221612304](img/InfluxDB学习笔记/image-20250329221612304.png)



amd64:

 ```sh
 # Use curl to download the amd64 binary.
 curl --location -O \
 https://download.influxdata.com/influxdb/releases/influxdb2-2.7.11_linux_amd64.tar.gz
 ```



arm64:

```sh
# Use curl to download the arm64 binary.
curl --location -O \
https://download.influxdata.com/influxdb/releases/influxdb2-2.7.11_linux_arm64.tar.gz
```





#### 第二步：下载





![image-20250329222739919](img/InfluxDB学习笔记/image-20250329222739919.png)







```sh
root@mao:/home/server# mkdir influxDB
root@mao:/home/server# cd influxDB/
root@mao:/home/server/influxDB# ll
总计 8
drwxr-xr-x  2 root root 4096  3月 29 22:26 ./
drwxr-xr-x 41 root root 4096  3月 29 22:26 ../
root@mao:/home/server/influxDB# wget https://download.influxdata.com/influxdb/releases/influxdb2-2.7.11_linux_amd64.tar.gz
--2025-03-29 22:26:59--  https://download.influxdata.com/influxdb/releases/influxdb2-2.7.11_linux_amd64.tar.gz
正在解析主机 download.influxdata.com (download.influxdata.com)... 54.179.124.27, 52.76.90.69
正在连接 download.influxdata.com (download.influxdata.com)|54.179.124.27|:443... 已连接。
已发出 HTTP 请求，正在等待回应... 302 Found
位置：https://dl.influxdata.com/influxdb/releases/influxdb2-2.7.11_linux_amd64.tar.gz [跟随至新的 URL]
--2025-03-29 22:27:02--  https://dl.influxdata.com/influxdb/releases/influxdb2-2.7.11_linux_amd64.tar.gz
正在解析主机 dl.influxdata.com (dl.influxdata.com)... 18.244.214.54, 18.244.214.89, 18.244.214.90, ...
正在连接 dl.influxdata.com (dl.influxdata.com)|18.244.214.54|:443... 已连接。
已发出 HTTP 请求，正在等待回应... 200 OK
长度： 49614126 (47M) [application/x-tar]
正在保存至: ‘influxdb2-2.7.11_linux_amd64.tar.gz’

influxdb2-2.7.11_linux_amd64.tar.gz           100%[==============================================================================================>]  47.32M   293KB/s    用时 3m 9s 

2025-03-29 22:30:12 (256 KB/s) - 已保存 ‘influxdb2-2.7.11_linux_amd64.tar.gz’ [49614126/49614126])

root@mao:/home/server/influxDB# ll
总计 48464
drwxr-xr-x  2 root root     4096  3月 29 22:27 ./
drwxr-xr-x 41 root root     4096  3月 29 22:26 ../
-rw-r--r--  1 root root 49614126 12月  3 02:55 influxdb2-2.7.11_linux_amd64.tar.gz
root@mao:/home/server/influxDB# 

```







#### 第三步：解压

```sh
 tar xvf influxdb2-2.7.11_linux_amd64.tar.gz
```



```sh
root@mao:/home/server/influxDB# ll
总计 48464
drwxr-xr-x  2 root root     4096  3月 29 22:27 ./
drwxr-xr-x 41 root root     4096  3月 29 22:26 ../
-rw-r--r--  1 root root 49614126 12月  3 02:55 influxdb2-2.7.11_linux_amd64.tar.gz
root@mao:/home/server/influxDB# 
root@mao:/home/server/influxDB# tar xvf influxdb2-2.7.11_linux_amd64.tar.gz 
influxdb2-2.7.11/
influxdb2-2.7.11/etc/
influxdb2-2.7.11/etc/logrotate.d/
influxdb2-2.7.11/etc/logrotate.d/influxdb
influxdb2-2.7.11/usr/
influxdb2-2.7.11/usr/lib/
influxdb2-2.7.11/usr/lib/influxdb/
influxdb2-2.7.11/usr/lib/influxdb/scripts/
influxdb2-2.7.11/usr/lib/influxdb/scripts/init.sh
influxdb2-2.7.11/usr/lib/influxdb/scripts/influxdb.service
influxdb2-2.7.11/usr/lib/influxdb/scripts/influxd-systemd-start.sh
influxdb2-2.7.11/usr/bin/
influxdb2-2.7.11/usr/bin/influxd
influxdb2-2.7.11/usr/share/
influxdb2-2.7.11/usr/share/influxdb/
influxdb2-2.7.11/usr/share/influxdb/README.md
influxdb2-2.7.11/usr/share/influxdb/influxdb2-upgrade.sh
influxdb2-2.7.11/usr/share/influxdb/LICENSE
root@mao:/home/server/influxDB# 
root@mao:/home/server/influxDB# ll
总计 48468
drwxr-xr-x  3 root root     4096  3月 29 22:49 ./
drwxr-xr-x 41 root root     4096  3月 29 22:26 ../
drwxrwxr-x  4 root root     4096 12月  3 01:51 influxdb2-2.7.11/
-rw-r--r--  1 root root 49614126 12月  3 02:55 influxdb2-2.7.11_linux_amd64.tar.gz
root@mao:/home/server/influxDB# cd influxdb2-2.7.11/
root@mao:/home/server/influxDB/influxdb2-2.7.11# ll
总计 16
drwxrwxr-x 4 root root 4096 12月  3 01:51 ./
drwxr-xr-x 3 root root 4096  3月 29 22:49 ../
drwxr-xr-x 3 root root 4096 12月  3 01:51 etc/
drwxr-xr-x 5 root root 4096 12月  3 01:51 usr/
root@mao:/home/server/influxDB/influxdb2-2.7.11# 

```







#### 第四步：运行





```sh
./usr/bin/influxd
```







![image-20250329233012430](img/InfluxDB学习笔记/image-20250329233012430.png)





```sh
root@mao:/home/server/influxDB/influxdb2-2.7.11# ./usr/bin/influxd
2025-03-29T15:29:10.557221Z     info    Welcome to InfluxDB     {"log_id": "0v_dBJc0000", "version": "v2.7.11", "commit": "fbf5d4ab5e", "build_date": "2024-12-02T17:48:15Z", "log_level": "info"}
2025-03-29T15:29:10.561937Z     info    Resources opened        {"log_id": "0v_dBJc0000", "service": "bolt", "path": "/root/.influxdbv2/influxd.bolt"}
2025-03-29T15:29:10.562113Z     info    Resources opened        {"log_id": "0v_dBJc0000", "service": "sqlite", "path": "/root/.influxdbv2/influxd.sqlite"}
2025-03-29T15:29:10.562936Z     info    Bringing up metadata migrations {"log_id": "0v_dBJc0000", "service": "KV migrations", "migration_count": 20}
2025-03-29T15:29:10.621172Z     info    Bringing up metadata migrations {"log_id": "0v_dBJc0000", "service": "SQL migrations", "migration_count": 8}
2025-03-29T15:29:10.695292Z     info    Using data dir  {"log_id": "0v_dBJc0000", "service": "storage-engine", "service": "store", "path": "/root/.influxdbv2/engine/data"}
2025-03-29T15:29:10.696028Z     info    Compaction settings     {"log_id": "0v_dBJc0000", "service": "storage-engine", "service": "store", "max_concurrent_compactions": 18, "throughput_bytes_per_second": 50331648, "throughput_bytes_per_second_burst": 50331648}
2025-03-29T15:29:10.696265Z     info    Open store (start)      {"log_id": "0v_dBJc0000", "service": "storage-engine", "service": "store", "op_name": "tsdb_open", "op_event": "start"}
2025-03-29T15:29:10.696793Z     info    Open store (end)        {"log_id": "0v_dBJc0000", "service": "storage-engine", "service": "store", "op_name": "tsdb_open", "op_event": "end", "op_elapsed": "0.531ms"}
2025-03-29T15:29:10.697118Z     info    Starting retention policy enforcement service   {"log_id": "0v_dBJc0000", "service": "retention", "check_interval": "30m"}
2025-03-29T15:29:10.697375Z     info    Starting precreation service    {"log_id": "0v_dBJc0000", "service": "shard-precreation", "check_interval": "10m", "advance_period": "30m"}
2025-03-29T15:29:10.699728Z     info    Starting query controller       {"log_id": "0v_dBJc0000", "service": "storage-reads", "concurrency_quota": 1024, "initial_memory_bytes_quota_per_query": 9223372036854775807, "memory_bytes_quota_per_query": 9223372036854775807, "max_memory_bytes": 0, "queue_size": 1024}
2025-03-29T15:29:10.711732Z     info    Configuring InfluxQL statement executor (zeros indicate unlimited).     {"log_id": "0v_dBJc0000", "max_select_point": 0, "max_select_series": 0, "max_select_buckets": 0}
2025-03-29T15:29:10.719833Z     info    Starting        {"log_id": "0v_dBJc0000", "service": "telemetry", "interval": "8h"}
2025-03-29T15:29:10.720023Z     info    Listening       {"log_id": "0v_dBJc0000", "service": "tcp-listener", "transport": "http", "addr": ":8086", "port": 8086}


```















## docker





```sh
docker run \
 --name influxdb2 \
 --publish 8086:8086 \
 --mount type=volume,source=influxdb2-data,target=/var/lib/influxdb2 \
 --mount type=volume,source=influxdb2-config,target=/etc/influxdb2 \
 --env DOCKER_INFLUXDB_INIT_MODE=setup \
 --env DOCKER_INFLUXDB_INIT_USERNAME=ADMIN_USERNAME \
 --env DOCKER_INFLUXDB_INIT_PASSWORD=ADMIN_PASSWORD \
 --env DOCKER_INFLUXDB_INIT_ORG=ORG_NAME \
 --env DOCKER_INFLUXDB_INIT_BUCKET=BUCKET_NAME \
 influxdb:2
```













# java连接influxDB

## 添加依赖

方式 1：使用官方 influxdb-client-java（推荐）
支持 InfluxDB 2.x 和 1.8+（兼容模式），基于 Reactive 编程，性能更好。

```xml
<dependency>
    <groupId>com.influxdb</groupId>
    <artifactId>influxdb-client-java</artifactId>
    <version>6.10.0</version> <!-- 检查最新版本 -->
</dependency>
```





```
implementation 'com.influxdb:influxdb-client-java:6.10.0'
```





方式 2：使用旧版 influxdb-java（仅限 InfluxDB 1.x）
适用于 InfluxDB 1.x，不推荐新项目使用。



```xml
<dependency>
    <groupId>org.influxdb</groupId>
    <artifactId>influxdb-java</artifactId>
    <version>2.23</version>
</dependency>
```





## 连接 InfluxDB

InfluxDB 2.x 连接方式

```java
package mao.influxdbdemo;

import com.influxdb.client.InfluxDBClient;
import com.influxdb.client.InfluxDBClientFactory;
import com.influxdb.client.WriteApi;
import com.influxdb.client.domain.WritePrecision;

import java.util.Random;

public class InfluxDB2Example
{
    public static int getIntRandom(int min, int max)
    {
        if (min > max)
        {
            min = max;
        }
        return min + (int) (Math.random() * (max - min + 1));
    }

    public static void main(String[] args)
    {
        // 配置连接参数
        String token = "";
        String org = "mao";
        String bucket = "mao";
        String url = "http://maosl.cn:29176";

        // 创建客户端
        InfluxDBClient client = InfluxDBClientFactory.create(url, token.toCharArray(), org, bucket);

        // 写入数据
        try (WriteApi writeApi = client.getWriteApi())
        {
            String data = "temperature,location=room value="+getIntRandom(10, 3000);
            writeApi.writeRecord(bucket, org, WritePrecision.NS, data);
        }

        // 查询数据
        String query = """
                    from(bucket: "mao")
                      |> range(start: -1h)
                      |> filter(fn: (r) => r._measurement == "temperature")
                """;

        client.getQueryApi().query(query).forEach(table ->
        {
            table.getRecords().forEach(record ->
            {
                System.out.println(record.getTime() + ": " + record.getValue());
            });
        });

        // 关闭客户端
        client.close();
    }
}

```





InfluxDB 1.x 连接方式

```java
import org.influxdb.InfluxDB;
import org.influxdb.InfluxDBFactory;
import org.influxdb.dto.Point;
import org.influxdb.dto.Query;

public class InfluxDB1Example {
    public static void main(String[] args) {
        // 配置连接参数
        String url = "http://localhost:8086";
        String username = "admin";
        String password = "admin";
        String database = "mydb";

        // 创建连接
        InfluxDB influxDB = InfluxDBFactory.connect(url, username, password);
        influxDB.setDatabase(database);

        // 写入数据（Line Protocol）
        influxDB.write(Point.measurement("temperature")
                .tag("location", "room")
                .addField("value", 25.3)
                .build());

        // 查询数据
        Query query = new Query("SELECT * FROM temperature WHERE time > now() - 1h", database);
        influxDB.query(query).getResults().forEach(result -> {
            result.getSeries().forEach(series -> {
                series.getValues().forEach(value -> {
                    System.out.println(value);
                });
            });
        });

        // 关闭连接
        influxDB.close();
    }
}
```





## **写入数据**

2.0

```java
try (WriteApi writeApi = client.getWriteApi()) {
    // 方式 1：直接写入 Line Protocol 字符串
    writeApi.writeRecord("temperature,location=room value=25.3");

    // 方式 2：使用 POJO（需注解）
    Temperature temp = new Temperature("room", 25.3);
    writeApi.writeMeasurement(WritePrecision.NS, temp);
}

// POJO 示例
import com.influxdb.annotations.Column;
import com.influxdb.annotations.Measurement;

@Measurement(name = "temperature")
public class Temperature {
    @Column(tag = true)
    String location;

    @Column
    Double value;

    // 构造方法、Getter/Setter...
}
```





InfluxDB 1.x

```java
Point point = Point.measurement("temperature")
        .tag("location", "room")
        .addField("value", 25.3)
        .build();

influxDB.write(point);
```





## 查询数据

InfluxDB 2.x（Flux 查询）

```java
String query = """
    from(bucket: "your-bucket")
      |> range(start: -1h)
      |> filter(fn: (r) => r._measurement == "temperature")
""";

List<FluxTable> tables = client.getQueryApi().query(query);
tables.forEach(table -> {
    table.getRecords().forEach(record -> {
        System.out.println(record.getTime() + ": " + record.getValue());
    });
});
```



InfluxDB 1.x（InfluxQL 查询）

```java
Query query = new Query("SELECT * FROM temperature WHERE time > now() - 1h", "mydb");
QueryResult result = influxDB.query(query);
result.getResults().forEach(res -> {
    res.getSeries().forEach(series -> {
        series.getValues().forEach(row -> {
            System.out.println(row);
        });
    });
});
```





![image-20250730175344667](img/InfluxDB学习笔记/image-20250730175344667.png)





## 批量写入

```java
List<String> records = Arrays.asList(
    "temperature,location=room value=25.3",
    "humidity,location=room value=60.5"
);

try (WriteApi writeApi = client.getWriteApi()) {
    writeApi.writeRecords(records);
}
```









## 配置

```java
InfluxDBClient client = InfluxDBClientFactory.create(url, token.toCharArray(), org, bucket)
    .setWriteOptions(WriteOptions.builder()
        .batchSize(1000)      // 每 1000 条数据批量写入
        .flushInterval(1000)  // 每 1 秒刷新一次
        .retryInterval(5000) // 失败后 5 秒重试
        .build());
```



```java
InfluxDBClient client = InfluxDBClientFactory.create(url, token.toCharArray())
    .enableGzip();
```







## 注解方式

### 配置 InfluxDB 连接

通过 `application.yml` 配置连接参数：

```yml
influxdb:
  url: http://localhost:8086
  token: your-api-token
  org: your-org
  bucket: your-bucket
```





### 创建配置类 InfluxDBConfig

```java
package mao.influxdbdemo.config;

import com.influxdb.client.InfluxDBClient;
import com.influxdb.client.InfluxDBClientFactory;
import com.influxdb.client.QueryApi;
import com.influxdb.client.WriteApi;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * Project name(项目名称)：influxDB-demo
 * Package(包名): mao.influxdbdemo.config
 * Class(类名): InfluxDBConfig
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2025/7/30
 * Time(创建时间)： 17:13
 * Version(版本): 1.0
 * Description(描述)： 无
 */

@Configuration
public class InfluxDBConfig
{
    @Value("${influxdb.url}")
    private String url;

    @Value("${influxdb.token}")
    private String token;

    @Value("${influxdb.org}")
    private String org;

    @Value("${influxdb.bucket}")
    private String bucket;

    @Bean
    public InfluxDBClient influxDBClient()
    {
        return InfluxDBClientFactory.create(url, token.toCharArray(), org, bucket);
    }

    @Bean
    public WriteApi writeApi(InfluxDBClient influxDBClient)
    {
        return influxDBClient.getWriteApi();
    }

    @Bean
    public QueryApi queryApi(InfluxDBClient influxDBClient)
    {
        return influxDBClient.getQueryApi();
    }
}
```







### 定义 @InfluxDBMeasurement 注解

```java
package mao.influxdbdemo.enums;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface InfluxDBMeasurement
{
    /**
     * 对应 InfluxDB 中的 measurement 名称
     * @return
     */
    String name();
}
```





### 定义 @InfluxDBField 注解

```java
package mao.influxdbdemo.enums;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface InfluxDBField
{
    /**
     * 默认是 Field，true 表示 Tag
     *
     * @return
     */
    boolean isTag() default false;
}
```



### 定义实体类

温度

```java
package mao.influxdbdemo.entity;

import mao.influxdbdemo.enums.InfluxDBField;
import mao.influxdbdemo.enums.InfluxDBMeasurement;

import java.time.Instant;

/**
 * Project name(项目名称)：influxDB-demo
 * Package(包名): mao.influxdbdemo.entity
 * Class(类名): Temperature
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2025/7/30
 * Time(创建时间)： 17:16
 * Version(版本): 1.0
 * Description(描述)： 无
 */

@InfluxDBMeasurement(name = "temperature")
public class Temperature
{
    @InfluxDBField(isTag = true)
    private String location; // Tag

    @InfluxDBField
    private Double value;   // Field

    private Instant time;    // 自动映射到 _time

    public String getLocation()
    {
        return location;
    }

    public Temperature setLocation(String location)
    {
        this.location = location;
        return this;
    }

    public Double getValue()
    {
        return value;
    }

    public Temperature setValue(Double value)
    {
        this.value = value;
        return this;
    }

    public Instant getTime()
    {
        return time;
    }

    public Temperature setTime(Instant time)
    {
        this.time = time;
        return this;
    }
}
```





### 数据操作工具类

写入数据

```java
package mao.influxdbdemo.service;

import com.influxdb.client.WriteApi;
import com.influxdb.client.write.Point;
import mao.influxdbdemo.enums.InfluxDBField;
import mao.influxdbdemo.enums.InfluxDBMeasurement;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.lang.reflect.Field;

/**
 * Project name(项目名称)：influxDB-demo
 * Package(包名): mao.influxdbdemo.service
 * Class(类名): InfluxDBService
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2025/7/30
 * Time(创建时间)： 17:24
 * Version(版本): 1.0
 * Description(描述)： 无
 */

@Service
public class InfluxDBService
{
    @Autowired
    private WriteApi writeApi;

    public <T> void write(T entity)
    {
        InfluxDBMeasurement measurement = entity.getClass().getAnnotation(InfluxDBMeasurement.class);
        if (measurement == null)
        {
            throw new IllegalArgumentException("实体类缺InfluxDBMeasurement注解");
        }

        Point point = Point.measurement(measurement.name());
        for (Field field : entity.getClass().getDeclaredFields())
        {
            //字段
            field.setAccessible(true);
            InfluxDBField influxField = field.getAnnotation(InfluxDBField.class);
            if (influxField != null)
            {
                try
                {
                    Object value = field.get(entity);
                    if (value != null)
                    {
                        if (influxField.isTag())
                        {
                            point.addTag(field.getName(), value.toString());
                        }
                        else
                        {
                            point.addField("_" + field.getName(), value.toString());
                        }
                    }
                }
                catch (IllegalAccessException e)
                {
                    throw new RuntimeException(e);
                }
            }
        }

        writeApi.writePoint(point);
    }
}
```



写入：

```java
package mao.influxdbdemo.service;

import com.influxdb.client.QueryApi;
import com.influxdb.client.WriteApi;
import com.influxdb.client.write.Point;
import com.influxdb.query.FluxRecord;
import com.influxdb.query.FluxTable;
import mao.influxdbdemo.enums.InfluxDBField;
import mao.influxdbdemo.enums.InfluxDBMeasurement;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.lang.reflect.Field;
import java.util.ArrayList;
import java.util.List;

/**
 * Project name(项目名称)：influxDB-demo
 * Package(包名): mao.influxdbdemo.service
 * Class(类名): InfluxDBService
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2025/7/30
 * Time(创建时间)： 17:24
 * Version(版本): 1.0
 * Description(描述)： 无
 */

@Service
public class InfluxDBService
{
    @Autowired
    private WriteApi writeApi;

    @Autowired
    private QueryApi queryApi;

    public <T> void write(T entity)
    {
        InfluxDBMeasurement measurement = entity.getClass().getAnnotation(InfluxDBMeasurement.class);
        if (measurement == null)
        {
            throw new IllegalArgumentException("实体类缺InfluxDBMeasurement注解");
        }

        Point point = Point.measurement(measurement.name());
        for (Field field : entity.getClass().getDeclaredFields())
        {
            //字段
            field.setAccessible(true);
            InfluxDBField influxField = field.getAnnotation(InfluxDBField.class);
            if (influxField != null)
            {
                try
                {
                    Object value = field.get(entity);
                    if (value != null)
                    {
                        if (influxField.isTag())
                        {
                            point.addTag(field.getName(), value.toString());
                        }
                        else
                        {
                            point.addField("_" + field.getName(), value.toString());
                        }
                    }
                }
                catch (IllegalAccessException e)
                {
                    throw new RuntimeException(e);
                }
            }
        }

        writeApi.writePoint(point);
    }


    public <T> List<T> query(String fluxQuery, Class<T> entityClass)
    {
        List<T> result = new ArrayList<>();
        List<FluxTable> tables = queryApi.query(fluxQuery);

        for (FluxTable table : tables)
        {
            for (FluxRecord record : table.getRecords())
            {
                try
                {
                    T entity = entityClass.getDeclaredConstructor().newInstance();
                    for (Field field : entityClass.getDeclaredFields())
                    {
                        field.setAccessible(true);
                        InfluxDBField influxField = field.getAnnotation(InfluxDBField.class);
                        if (influxField != null)
                        {
                            // 处理 Tag 和 Field 的映射
                            Object value;
                            if (influxField.isTag())
                            {
                                value = record.getValueByKey(field.getName()); // Tag 直接按字段名匹配
                            }
                            else
                            {
                                //value = record.getValue(); // Field 值存储在 _value
                                value = record.getValueByKey("_" + field.getName());
                            }
                            if (value != null)
                            {
                                // 处理数据类型转换
                                if (field.getType().equals(Integer.class))
                                {
                                    value = Integer.parseInt(value.toString());
                                }
                                else if (field.getType().equals(Double.class))
                                {
                                    value = Double.parseDouble(value.toString());
                                }
                                else if (field.getType().equals(Long.class))
                                {
                                    value = Long.parseLong(value.toString());
                                }
                                else if (field.getType().equals(Boolean.class))
                                {
                                    value = Boolean.parseBoolean(value.toString());
                                }
                                else if (field.getType().equals(String.class))
                                {
                                    value = value.toString();
                                }
                                field.set(entity, value);
                            }
                        }
                        else if ("time".equals(field.getName()))
                        {
                            // 特殊处理时间字段
                            field.set(entity, record.getTime());
                        }
                    }
                    result.add(entity);
                }
                catch (Exception e)
                {
                    throw new RuntimeException("Failed to map FluxRecord to entity", e);
                }
            }
        }
        return result;
    }
}

```





TemperatureService接口

```java
package mao.influxdbdemo.service;

import mao.influxdbdemo.entity.Temperature;

import java.util.List;

/**
 * Project name(项目名称)：influxDB-demo
 * Package(包名): mao.influxdbdemo.service
 * Interface(接口名): TemperatureService
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2025/7/30
 * Time(创建时间)： 17:35
 * Version(版本): 1.0
 * Description(描述)： 无
 */

public interface TemperatureService
{
    /**
     * 保存数据
     *
     * @param temperature 温度
     */
    void save(Temperature temperature);

    /**
     * 查询最近一小时的数据
     *
     * @return {@link List }<{@link Temperature }>
     */
    List<Temperature> queryLastOneHour();
}
```



实现类：

```java
package mao.influxdbdemo.service.impl;

import mao.influxdbdemo.entity.Temperature;
import mao.influxdbdemo.service.InfluxDBService;
import mao.influxdbdemo.service.TemperatureService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * Project name(项目名称)：influxDB-demo
 * Package(包名): mao.influxdbdemo.service.impl
 * Class(类名): TemperatureServiceImpl
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2025/7/30
 * Time(创建时间)： 17:35
 * Version(版本): 1.0
 * Description(描述)： 无
 */

@Service
public class TemperatureServiceImpl implements TemperatureService
{

    @Autowired
    private InfluxDBService influxDBService;

    @Override
    public void save(Temperature temperature)
    {
        influxDBService.write(temperature);
    }

    @Override
    public List<Temperature> queryLastOneHour()
    {
        String flux = """
        from(bucket: "mao")
          |> range(start: -1h)
          |> filter(fn: (r) => r._measurement == "temperature")
    """;
        return influxDBService.query(flux, Temperature.class);
    }
}

```





单元测试：

```java
package mao.influxdbdemo.service.impl;

import mao.influxdbdemo.entity.Temperature;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.time.Instant;
import java.util.List;

import static org.junit.jupiter.api.Assertions.*;

/**
 * Project name(项目名称)：influxDB-demo
 * Package(包名): mao.influxdbdemo.service.impl
 * Class(测试类名): TemperatureServiceImplTest
 * Author(作者）: mao
 * Author QQ：1296193245
 * GitHub：https://github.com/maomao124/
 * Date(创建日期)： 2025/7/30
 * Time(创建时间)： 17:41
 * Version(版本): 1.0
 * Description(描述)： 测试类
 */

@SpringBootTest
class TemperatureServiceImplTest
{
    @Autowired
    private TemperatureServiceImpl temperatureService;

    /**
     * 得到随机数
     *
     * @param min 最小值
     * @param max 最大值
     * @return double
     */
    public static double getDoubleRandom(double min, double max)
    {
        if (min > max)
        {
            min = max;
        }
        return min + (Math.random() * (max - min));
    }

    @Test
    void save()
    {
        temperatureService.save(new Temperature().setLocation("room").setValue(getDoubleRandom(0, 100))
                .setTime(Instant.now()));
    }

    @Test
    void queryLastOneHour()
    {
        List<Temperature> temperatures = temperatureService.queryLastOneHour();
        temperatures.forEach(System.out::println);
    }
}
```





![image-20250730182853691](img/InfluxDB学习笔记/image-20250730182853691.png)









# Flux 查询

## 概述

Flux 是 InfluxDB 2.0 及更高版本中使用的 **函数式数据脚本语言**，专为时序数据处理设计。与 SQL 不同，Flux 采用管道式（Pipe-forward）语法，强调数据流的转换和操作。



## **核心特点**

- **时序数据优化**：直接处理时间序列数据（如降采样、插值、窗口计算）。
- **函数式编程**：通过链式调用函数（`|>` 管道符）逐步转换数据。
- **多数据源支持**：可查询 InfluxDB、CSV、PostgreSQL、Prometheus 等。
- **可扩展性**：支持自定义函数和插件。





## 语法

Flux 查询通常由以下部分组成：

```sh
from(bucket: "example-bucket")       // 数据源
  |> range(start: -1h)               // 时间范围
  |> filter(fn: (r) => r._field == "temperature")  // 过滤
  |> mean()                          // 聚合计算
  |> yield(name: "result")           // 输出结果
```





| 操作符       | 说明                         | 示例                               |       |           |
| :----------- | :--------------------------- | :--------------------------------- | ----- | --------- |
| `            | >`                           | 管道符（数据流向下传递）           | `data | > mean()` |
| `fn: (r) =>` | Lambda 函数（过滤/转换数据） | `filter(fn: (r) => r._value > 25)` |       |           |
| `_time`      | 时间戳字段                   | `r._time`                          |       |           |
| `_value`     | 数值字段                     | `r._value`                         |       |           |





## 核心函数



### **数据源与时间范围**

```flux
from(bucket: "telegraf")                     // 指定存储桶
  |> range(start: -1h, stop: now())          // 查询最近1小时数据
  // 或绝对时间
  |> range(start: 2023-01-01T00:00:00Z, stop: 2023-01-02T00:00:00Z)
```



### **数据过滤**

```flux
|> filter(fn: (r) => r._measurement == "cpu")                // 按表名过滤
|> filter(fn: (r) => r._field == "usage_user")               // 按字段名过滤
|> filter(fn: (r) => r.host == "server01" and r._value > 80) // 多条件
```



### **聚合与计算**

```flux
|> mean()                                    // 计算平均值
|> sum()                                     // 求和
|> max()                                     // 最大值
|> min()                                     // 最小值
|> stddev()                                  // 标准差
|> cumulativeSum()                           // 累计和
|> derivative(unit: 1s)                      // 计算变化率（导数）
```



### **时间窗口与降采样**

```flux
|> aggregateWindow(
    every: 1m,                              // 每分钟聚合一次
    fn: mean,                               // 聚合函数
    createEmpty: false                      // 忽略空窗口
  )
```



### **数据重塑**

```flux
|> pivot(rowKey: ["_time"], columnKey: ["_field"], valueColumn: "_value")  // 行转列
|> map(fn: (r) => ({ r with _value: r._value * 100.0 }))                  // 修改字段值
```



### **多数据源联合查询**

```flux
import "sql"
import "csv"

data1 = from(bucket: "bucket1") |> range(start: -1h)
data2 = sql.from(
  driverName: "postgres",
  dataSourceName: "postgresql://user:pass@localhost:5432/db",
  query: "SELECT * FROM sensors"
)

join(tables: {data1: data1, data2: data2}, on: ["sensor_id"])
```







## 完整示例

### **示例 1：查询 CPU 使用率并计算 5 分钟均值**

```flux
from(bucket: "telegraf")
  |> range(start: -1h)
  |> filter(fn: (r) => r._measurement == "cpu" and r._field == "usage_user")
  |> aggregateWindow(every: 5m, fn: mean)
  |> yield(name: "cpu_mean")
```

### **示例 2：检测异常值（超过阈值）**

```flux
from(bucket: "sensors")
  |> range(start: -24h)
  |> filter(fn: (r) => r._measurement == "temperature")
  |> map(fn: (r) => ({ r with is_alert: if r._value > 30 then "ALERT" else "OK" }))
  |> filter(fn: (r) => r.is_alert == "ALERT")
```

### **示例 3：多表关联（CPU + 内存）**

```flux
cpu = from(bucket: "telegraf")
  |> range(start: -1h)
  |> filter(fn: (r) => r._measurement == "cpu")

mem = from(bucket: "telegraf")
  |> range(start: -1h)
  |> filter(fn: (r) => r._measurement == "mem")

join(tables: {cpu: cpu, mem: mem}, on: ["_time", "host"])
```

---

## 

## 调试与优化

### **调试技巧**

- 使用 `|> debug()` 打印中间结果：

  ```flux
  data |> filter(fn: (r) => r._value > 0) |> debug()
  ```

- 在 InfluxDB UI 的 **Script Editor** 中实时验证查询。



### **性能优化**

- **缩小时间范围**：优先使用 `range(start: -1h)` 而非全量查询。
- **尽早过滤**：先 `filter()` 再聚合，减少计算量。
- **使用索引字段**：对 `_measurement`、`_field`、`tag` 过滤效率更高。





## 对比 Flux 与 SQL

| 特性         | Flux                         | SQL (InfluxQL)         |
| ------------ | ---------------------------- | ---------------------- |
| **语法风格** | 函数式管道                   | 声明式（SELECT/WHERE） |
| **时序处理** | 原生支持窗口、插值、降采样   | 有限支持（需手动实现） |
| **多数据源** | 支持（InfluxDB、CSV、SQL等） | 仅限 InfluxDB          |
| **学习曲线** | 较高（需适应管道操作）       | 较低（类似传统 SQL）   |



















# 示例







