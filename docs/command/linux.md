# Linux命令

### 1.Python 简单命令

1. 启动一个简单服务

   ```
   python -m SimpleHTTPServer 18765
   ```
   
2. pm2启动一个服务

   ```
    pm2 start npm --name autopai-push -- run serve
   ```

### 2.查看CPU变高排查

1. 先执行top ，找到CPU占用比较高的进程

   ```
   top
   ```

2. jistack  进程ID  

   ```
   jstack 进程ID  >show.txt
   ```

3. 找到经常中cpu占用比较高的线程，线程Id转为16进制

   ```
   top  -p 进程ID - H  ## 查看进程中的线程情况
   ```

4. 通过线程ID查找线程情况

###  3.文件

```shell
lsof   #打开的文件
pcstat #缓存使用情况
```

### 4.句柄

##### 局部文件句柄限制

查看

```powershell
ulimit -n
ulimit -n 102400
```

修改

```powershell
sudo vim /etc/security/limits.conf

#   实际的限制
*   hard nofile 102400
#   警告的限制
*   soft nofile 102400 
```

##### 全局文件句柄限制

查看

```
cat /proc/sys/fs/file-max
```

临时修改

```powershell
echo 1000000 > /proc/sys/fs/file-max
```

修改

```powershell
suod vim /etc/sysctl.conf
fs.file-max=1000000
sudo sysctl -p
```

##### TCP释放时间，减小，调低time_wait状态端口等待时间

```powershell
# 调低端口释放后的等待时间，默认为60s，修改为15~30s
sysctl -w net.ipv4.tcp_fin_timeout=30
# 修改tcp/ip协议配置，
# 通过配置/proc/sys/net/ipv4/tcp_tw_resue, 默认为0，修改为1，释放TIME_WAIT端口给新连接使用
sysctl -w net.ipv4.tcp_timestamps=1
# 修改tcp/ip协议配置，快速回收socket资源，默认为0，修改为1
sysctl -w net.ipv4.tcp_tw_recycle=1
# 修改参数：
$ vi /etc/sysctl.conf
# -----意味着10000~65000端口可用
net.ipv4.ip_local_port_range = 10000     65000     
```

##### 安装iptalbes会使用nf_conntrack模块跟踪连接

跟踪的连接超过这个最大值，就会导致连接失败

```powershell
# 查看最大值
# wc -l /proc/net/nf_conntrack
# cat /proc/sys/net/nf_conntrack_max
1024000
# vi /etc/sysctl.conf
net.nf_conntrack_max = 2000500
# or
sysctl -w net.nf_conntrack_max=2000500
```

### 5.sudo 免密

```sh
# 进入root用户
# 文件添加写入的权限
chmod u+w /etc/sudoers
vim /etc/sudoers
root ALL=(ALL) ALL
test ALL=(ALL)  NOPASSWD:  ALL
# 文件的写入属性去掉
chmod u-w /etc/sudoers
```

### 6.查找大文件

```shell
# 查找文件
find / -type f -size +800M  -print0 | xargs -0 du -h

# 查看目录文件大小
du /* -sh

# 文件大小排序
du -h --max-depth=1 / |sort -hr
```

