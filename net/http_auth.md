# http auth

![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/net/http_auth.png)

## session 方式
session 方式就是第一次请求把信息放到session里（结构为K-V键值对），然后返回sessionID，下一次请求把sessionID带到cookie中。  

如果是分布式的应用，就需要使用一台服务器记录session，因为在A机器上生成的session，在B机器上不是同一个。  

解决方案

1. nginx 配置负载均衡方式为基于客户端 ip 哈希的方式。
2. session 存到共享的服务器上，比如redis。

## http header authorization 方式

### base64 方式
base64(用户名：密码)进行编码，放到header中，服务端获取，进行解码，用冒号分隔得到用户名和密码，正规网站https，抓包抓不下来，是安全的。  

缺点就是每次都要查询数据库进行认证。

### bearer token 方式
token是怎么来的？  
第一次发送用户名和密码到服务端，服务端验证成功后，使用对称加密算法，比如ASE(用户信息，key), key是服务端很长的一个文本，然后得到一个密文，发给客户端，客户端下一次请求就携带这个token，然后服务端使用AES的解密算法。  

比如JWT (json web token) jwt.io 有很多库。