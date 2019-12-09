## url 

### 格式

合法 url格式

```
<protocol>://<user>:<password>@<host>:<port>/<path>;<params>?<query>#<frag>
```

其中

protocol: 协议，常用的协议是http

user, password: 用户名密码

hostname: 主机地址，可以是域名，也可以是IP地址

port: 端口 http协议默认端口是：80端口，如果不写默认就是:80端口

path: 路径 网络资源在服务器中的指定路径

parameter: 用于指定特殊参数的可选项 (这个不知道怎么用, 没见过)

query: 查询字符串

fragment: 用于指定网络资源中的片断 (这个不知道怎么用, 没见过)

### 编码

一般来说，URL只能使用英文字母、阿拉伯数字和某些特定的标点符号，不能使用其他文字和符号.

如果包含非法字符, 就会进行 url 编码.

示例

输入 https://www.baidu.com/s?wd=url编码 会变成 https://www.baidu.com/s?wd=url%E7%BC%96%E7%A0%81