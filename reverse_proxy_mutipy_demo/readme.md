## 多层代理

reverse_proxy_level1=>reverse_proxy_level2=>real server

- reverse_proxy_level1.go 第一层代理
- reverse_proxy_level2.go 第二层代理
- real_server.go     真实服务


```text
➜  go-tool git:(master) ✗ curl --location --request GET 'http://127.0.0.1:2001'                                         
http://127.0.0.1:2004/
RemoteAddr=127.0.0.1:61145,X-Forwarded-For=127.0.0.1, 127.0.0.1,X-Real-Ip=127.0.0.1:61143
headers =map[Accept:[*/*] Accept-Encoding:[gzip] User-Agent:[curl/7.79.1] X-Forwarded-For:[127.0.0.1, 127.0.0.1] X-Real-Ip:[127.0.0.1:61143]]


X-Real-Ip: 请求真实remoteaddr 客户端ip
remoteaddr  第一层代理请求第二层代理的客户端地址
x-forwarded-for 有2个。 代表是用了2层代理
```



修改 X-Forwarded-For ，发现可以被串改
```text
➜  go-tool git:(master) ✗ curl -H 'X-Forwarded-For: 2.2.2.2' '127.0.0.1:2001/test'
http://127.0.0.1:2004/test
RemoteAddr=127.0.0.1:61168,X-Forwarded-For=2.2.2.2, 127.0.0.1, 127.0.0.1,X-Real-Ip=127.0.0.1:61166
headers =map[Accept:[*/*] Accept-Encoding:[gzip] User-Agent:[curl/7.79.1] X-Forwarded-For:[2.2.2.2, 127.0.0.1, 127.0.0.1] X-Real-Ip:[127.0.0.1:61166]]
```

修改 X-Real-Ip,  不能被串改
```text
  go-tool git:(master) ✗ curl -H 'X-Real-Ip: 2.2.2.2' '127.0.0.1:2001/test'
http://127.0.0.1:2004/test
RemoteAddr=127.0.0.1:61168,X-Forwarded-For=127.0.0.1, 127.0.0.1,X-Real-Ip=127.0.0.1:61171
headers =map[Accept:[*/*] Accept-Encoding:[gzip] User-Agent:[curl/7.79.1] X-Forwarded-For:[127.0.0.1, 127.0.0.1] X-Real-Ip:[127.0.0.1:61171]]
```
