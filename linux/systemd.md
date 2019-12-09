# systemd

### systemctl

用来管理守护进程

配置文件如下

```shell
[Unit]
Description=myproject deamon

[Service]
Environment=MYPROJECT_ENV=test
User=root
Group=root
WorkingDirectory=/myproject
ExecStart=/usr/local/bin/gunicorn -c deploy/gunicorn.py myproject:app
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID
PrivateTmp=true
Restart=always
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

具体含义如下

```
[unit]
Description: 简短描述
Documentation: 文档地址
Requires: 当前 Unit 依赖的其他 Unit，如果它们没有运行，当前 Unit 会启动失败
Wants: 与当前 Unit 配合的其他 Unit，如果它们没有运行，当前 Unit 不会启动失败
BindsTo: 与Requires类似，它指定的 Unit 如果退出，会导致当前 Unit 停止运行
Before: 如果该字段指定的 Unit 也要启动，那么必须在当前 Unit 之后启动
After: 如果该字段指定的 Unit 也要启动，那么必须在当前 Unit 之前启动
Conflicts: 这里指定的 Unit 不能与当前 Unit 同时运行
Condition...: 当前 Unit 运行必须满足的条件，否则不会运行
Assert...: 当前 Unit 运行必须满足的条件，否则会报启动失败
[Service]
Type: 定义启动时的进程行为。它有以下几种值。
Type=simple: 默认值，执行ExecStart指定的命令，启动主进程
Type=forking: 以 fork 方式从父进程创建子进程，创建后父进程会立即退出
Type=oneshot: 一次性进程，Systemd 会等当前服务退出，再继续往下执行
Type=dbus: 当前服务通过D-Bus启动
Type=notify: 当前服务启动完毕，会通知Systemd，再继续往下执行
Type=idle: 若有其他任务执行完毕，当前服务才会运行
ExecStart: 启动当前服务的命令
ExecStartPre: 启动当前服务之前执行的命令
ExecStartPost: 启动当前服务之后执行的命令
ExecReload: 重启当前服务时执行的命令
ExecStop: 停止当前服务时执行的命令
ExecStopPost: 停止当其服务之后执行的命令
RestartSec: 自动重启当前服务间隔的秒数
Restart: 定义何种情况 Systemd 会自动重启当前服务，可能的值包括always（总是重启）、on-success、on-failure、on-abnormal、on-abort、on-watchdog
TimeoutSec: 定义 Systemd 停止当前服务之前等待的秒数
Environment: 指定环境变量
[Install]
WantedBy: 它的值是一个或多个 Target，当前 Unit 激活时（enable）符号链接会放入/etc/systemd/system目录下面以 Target 名 + .wants后缀构成的子目录中
RequiredBy: 它的值是一个或多个 Target，当前 Unit 激活时，符号链接会放入/etc/systemd/system目录下面以Target 名 + .required后缀构成的子目录中
Alias: 当前 Unit 可用于启动的别名
Also: 当前 Unit 激活（enable）时，会被同时激活的其他 Unit
```

启动和关闭守护进行

```shell
# 把文件复制到 /etc/systemd/system/下后

# 重载所有修改过的配置文件
systemctl daemon-reload

# 重新加载一个服务的配置文件
systemctl reload myproject.service

# 启动服务
systemctl start myproject.service

# 停止服务
systemctl stop myproject.service

# 重启服务
systemctl restart myproject.service

# 杀死一个服务的所有子进程
systemctl kill myproject.service

# 查看服务的状态，可以看到一些错误
systemctl status myproject.service
```

查看日志

```shell
# 查看指定服务最新日志
$ sudo journalctl -f -u myproject

# 查看所有日志（默认情况下 ，只保存本次启动的日志）
$ sudo journalctl

# 查看内核日志（不显示应用日志）
$ sudo journalctl -k

# 查看系统本次启动的日志
$ sudo journalctl -b
$ sudo journalctl -b -0

# 查看上一次启动的日志（需更改设置）
$ sudo journalctl -b -1

# 查看指定时间的日志
$ sudo journalctl --since="2012-10-30 18:17:16"
$ sudo journalctl --since "20 min ago"
$ sudo journalctl --since yesterday
$ sudo journalctl --since "2015-01-10" --until "2015-01-11 03:00"
$ sudo journalctl --since 09:00 --until "1 hour ago"

# 显示尾部的最新10行日志
$ sudo journalctl -n

# 显示尾部指定行数的日志
$ sudo journalctl -n 20

# 实时滚动显示最新日志
$ sudo journalctl -f

# 查看指定服务的日志
$ sudo journalctl /usr/lib/systemd/systemd

# 查看指定进程的日志
$ sudo journalctl _PID=1

# 查看某个路径的脚本的日志
$ sudo journalctl /usr/bin/bash

# 查看指定用户的日志
$ sudo journalctl _UID=33 --since today

# 查看某个 Unit 的日志
$ sudo journalctl -u nginx.service
$ sudo journalctl -u nginx.service --since today

# 实时滚动显示某个 Unit 的最新日志
$ sudo journalctl -u nginx.service -f

# 合并显示多个 Unit 的日志
$ journalctl -u nginx.service -u php-fpm.service --since today

# 查看指定优先级（及其以上级别）的日志，共有8级
# 0: emerg
# 1: alert
# 2: crit
# 3: err
# 4: warning
# 5: notice
# 6: info
# 7: debug
$ sudo journalctl -p err -b

# 日志默认分页输出，--no-pager 改为正常的标准输出
$ sudo journalctl --no-pager

# 以 JSON 格式（单行）输出
$ sudo journalctl -b -u nginx.service -o json

# 以 JSON 格式（多行）输出，可读性更好
$ sudo journalctl -b -u nginx.serviceqq
 -o json-pretty

# 显示日志占据的硬盘空间
$ sudo journalctl --disk-usage

# 指定日志文件占据的最大空间
$ sudo journalctl --vacuum-size=1G

# 指定日志文件保存多久
$ sudo journalctl --vacuum-time=1years
```

