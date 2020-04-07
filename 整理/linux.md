# linux

netstat:
    查看网络状态信息
    常用命令 netstat -an
    原理: 查询 /proc 目录下进程信息 以及 /proc/net 下的网络信息

ss:
    与 netstat 类似, Socket Statistics, 比 netstat 高效

ps:
    查看进程信息
    常用命令: ps -ef
    原理: 解析 /proc 目录

lsof:
    显示打开文件信息, list open file, 可以查看文件, 网络, 进程等信息
    常用命令: lsof -i :8080   查看网络端口占用
        lsof /aaa/bbb   查看文件打开信息

dig:
    查看 dns 信息
    常用命令: dig www.baidu.com

iptables:
    包过滤防火墙系统

