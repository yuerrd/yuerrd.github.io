# Supervisor

### 名词解释

supervisor：要安装的软件的名称。
supervisord：装好supervisor软件后，supervisord用于启动supervisor服务。
supervisorctl：用于管理supervisor配置文件中program。

### 安装

```powershell

yum install epel-release
yum install -y supervisor
# 开机自启动
systemctl enable supervisord
# 启动supervisord服务
systemctl start supervisord 
# 查看supervisord服务状态
systemctl status supervisord 
 # 查看是否存在supervisord进程
ps -ef|grep supervisord
```

### 常用命令

```powershell
supervisorctl status 查看进程运行状态
supervisorctl start 进程名 启动进程
supervisorctl stop 进程名 关闭进程
supervisorctl restart 进程名 重启进程
supervisorctl update 重新载入配置文件
supervisorctl shutdown 关闭supervisord
supervisorctl clear 进程名 清空进程日志
supervisorctl 进入到交互模式下。使用help查看所有命令。
start stop restart + all 表示启动，关闭，重启所有进程。
```

### 配置

 配置文件路径  /etc/supervisord.cof

 子进程配置文件路径   /etc/supervisord.d

- 子进程配置文件描述
    
    ```powershell
    #项目名
    [program:blog]
    #脚本目录
    directory=/opt/bin
    #脚本执行命令
    command=/usr/bin/python /opt/bin/test.py
    
    #supervisor启动的时候是否随着同时启动，默认True
    autostart=true
    #当程序exit的时候，这个program不会自动重启,默认unexpected，设置子进程挂掉后自动重启的情况，有三个选项，false,unexpected和true。如果为false的时候，无论什么情况下，都不会被重新启动，如果为unexpected，只有当进程的退出码不在下面的exitcodes里面定义的
    autorestart=false
    #这个选项是子进程启动多少秒之后，此时状态如果是running，则我们认为启动成功了。默认值为1
    startsecs=30
    
    #脚本运行的用户身份 
    user = root
    
    #日志输出 
    stderr_logfile=/tmp/blog_stderr.log 
    stdout_logfile=/tmp/blog_stdout.log 
    #把stderr重定向到stdout，默认 false
    redirect_stderr = true
    #stdout日志文件大小，默认 50MB
    stdout_logfile_maxbytes = 20MB
    #stdout日志文件备份数
    stdout_logfile_backups = 20
    ```