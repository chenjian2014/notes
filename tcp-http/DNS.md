## DNS(Domain Name System)

### 作用

根据域名查出IP地址

### 工具

```
dig www.baidu.com
```

返回主要结果

```
;; QUESTION SECTION:
;www.google.com.			IN	A      -- 表示查询域名www.google.com的A记录，A是address的缩写

;; ANSWER SECTION:
www.google.com.		296	IN	A	216.58.199.100
```

```
dig +trace math.stackexchange.com    -- 显示DNS的整个分级查询过程
```

返回主要结果

```
; <<>> DiG 9.10.6 <<>> +trace math.stackexchange.com
;; global options: +cmd
.			3979	IN	NS	i.root-servers.net.
.			3979	IN	NS	e.root-servers.net.
;; Received 901 bytes from 172.20.66.131#53(172.20.66.131) in 11 ms

com.			172800	IN	NS	a.gtld-servers.net.
com.			172800	IN	NS	g.gtld-servers.net.
;; Received 1182 bytes from 192.203.230.10#53(e.root-servers.net) in 336 ms

stackexchange.com.	172800	IN	NS	ns-925.awsdns-51.net.
stackexchange.com.	172800	IN	NS	ns-cloud-d1.googledomains.com.
;; Received 823 bytes from 192.42.93.30#53(g.gtld-servers.net) in 64 ms

math.stackexchange.com.	1800	IN	A	151.101.1.69
math.stackexchange.com.	1800	IN	A	151.101.65.69
math.stackexchange.com.	1800	IN	A	151.101.129.69
math.stackexchange.com.	1800	IN	A	151.101.193.69
;; Received 115 bytes from 216.239.32.109#53(ns-cloud-d1.googledomains.com) in 45 ms
```

其中 

1. <font color=red>172.20.66.131</font> 是本机 dns服务器 ip, 其配置保存在 /etc/resolv.conf 文件中 (公网 dns 服务: 谷歌8.8.8.8 和 level 3 的 4.2.2.2, 可以用 dig @8.8.8.8 +trace math.stackexchange.com 看到)
2. <font color=red>.</font> 表示根域名, NS 记录表示 所有根域名服务器, 一共有 13 条记录, 这里限于篇幅省略了
3. <font color=red>com.</font> 表示顶级域名对应的服务器
4. <font color=red>math.stackexchange.com.</font> 有4条A 记录, 都可以访问网站

### 域名的层级

结构: 主机名.次级域名.顶级域名.根域名

- 根域名
- 顶级域名, 例如.com
- 次级域名, 例如 www.google.com 里面的 .google, 该级可以用户自己注册
- 主机名(三级域名), 例如www.google.com里面的 www, 可以用户任意分配

### 根域名服务器

总共有13组根域名服务器 

### NS 记录查询

dig命令可以单独查看每一级域名的NS记录

```shell
dig ns com
dig ns stackexchange.com

# +short参数可以显示简化的结果
dig +short ns com
dig +short ns stackexchange.com
```

### DNS的记录类型

- A: 地址记录（Address），返回域名指向的IP地址
- NS: 域名服务器记录（Name Server），返回保存下一级域名信息的服务器地址。该记录只能设置为域名，不能设置为IP地址
- CNAME: 规范名称记录（Canonical Name），返回另一个域名，即当前查询的域名是另一个域名的跳转
- AAAA: 返回域名指向的IPv6地址

### 从 ip 查域名

```bash
dig -x 192.30.252.153
```

### 其他命令

- host
- nslookup
- whois

### 查询过程

1. 本地 DNS 解析器缓存, 没有则下一步
2. 检查 hosts 文件, 没有则下一步
3. 查看本地 DNS 服务器, 配置在 /etc/resolv.conf 中, 没有则下一步
4. 本地 DNS 服务器如果没有设置转发模式, 则采用4, 否则 5
5. 本地 DNS发送请求到根域名服务器, 根域名服务器返回顶级域名服务器 IP, 本地 DNS 服务器继续请求顶级域名服务器, 得到次级域名服务器 Ip, 重复下去, 知道查到结果为止, 这被称为迭代查询
6. 本地 DNS将请求转发给上一级 DNS服务器, 上一级 DNS 没有设置转发, 则继续请求上一级 DNS 服务器, 直到拿到结果, 然后层层返回, 直到给到本地 DNS服务器, 这被称为递归查询



### 参考 

[http://www.ruanyifeng.com/blog/2016/06/dns.html](http://www.ruanyifeng.com/blog/2016/06/dns.html)

### 