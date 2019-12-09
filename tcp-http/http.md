# http


### 请求格式

```
[请求方法][空格][URL][空格][协议版本][回车符\r][换行符\n]   --请求行
[头部字段名][:][值][\r\n]
            ......                         --请求头
[头部字段名][:][值][\r\n]
[\r\n]
[请求体]
```

示例
```shell
# --trace output.txt 可获得更详细结果
curl -v -H 'Content-Type:application/json' -H 'Accept:application/json' --user name:password --cookie "name=test" -X POST -d '{"appid":"test","data":123}' http://example.com/test
```

请求如下
```
POST /test HTTP/1.1
Host: example.com
Authorization: Basic bmFtZTpwYXNzd29yZA==
User-Agent: curl/7.54.0
Cookie: name=test
Content-Type:application/json
Accept:application/json
Content-Length: 43

{"appid":"test","data":123}
```

### 响应格式

```
[协议版本][空格][状态码][空格][状态文本][回车符\r][换行符\n]   --状态行
[头部字段名][:][值][\r\n]
            ......                         --请求头
[头部字段名][:][值][\r\n]
[\r\n]
[body]
```

示例
```
HTTP/1.1 200 OK
Server: nginx/1.12.2
Date: Thu, 21 Nov 2019 11:54:04 GMT
Content-Type: text/plain; charset=utf-8
Transfer-Encoding: chunked
Connection: keep-alive

body
```

