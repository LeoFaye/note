# https

## 作用

保证web传输过程中的安全。

## 对称加密

```
f1(key, data) = x;
f2(key, x) = data;
```
客户端传输数据data，使用算法f1和密钥key对数据加密为x，再传输；
服务端使用算法f2和相同的密钥key对x进行解密获取数据data。

> key只有一个

服务端不可能为每一个客户端定制一个key，所以只有一个key。

> 安全？

理论上黑客只能拿到密文，但是黑客也可能曾经是一个客户，它知道key，这样的话就危险了，因为key只有一个，黑客就可以根据这个key去获取其他人传输的信息。

## 非对称加密

```
f(pk, data) = x;
f(sk, x) = data;

f(sk, data) = y;
f(px, y) = data;
```
使用公钥和数据加密，可以通过私钥解密；
相反，使用私钥和数据加密，可以通过公钥解密。

> 客户端向服务端发数据过程

1.客户端发送请求向服务端索要公钥。（服务端有公钥和私钥）  
2.服务端把公钥发给客户端。  
3.客户端使用公钥加密数据data，发送密文 x 到服务端。  
4.服务端使用私钥解密 x，获得数据data。

> 服务端向客户端发数据过程

1.服务端使用私钥加密数据data，发送密文 y 到客户端。（使用私钥加密数据是因为客户端只有公钥）  
2.客户端使用公钥解密 y，获得数据data。

> 安全？

在服务端向客户端发数据时（第2步），黑客也可以使用公钥对数据进行解密，因为公钥人人都可以索要。

## 对称加密和非对称加密分析

对称加密的缺点是key只有一个，如果客户端都有不一样的key，就安全了。

非对称加密中，只有服务端向客户端发数据是不安全的。

## 对称加密+非对称加密
![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/net/https_k_pk_sk.png)

客户端随机生成字符串key，并把key使用非对称加密传输到服务端，接着就可以使用对称加密传输了。

> 安全？(有中间人问题)
![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/net/https_key_pk_sk_hk.png)

## 对称加密+非对称加密+CA
![image](https://leofaye-ghb.obs.cn-east-3.myhuaweicloud.com/note/net/https_key_pk_sk_ca.png)

CA 是权威机构，服务端通过付费方式得到license，client索要公钥时候获得license，然后用本地cpk解密获得pk，就算黑客拦截返回客户端假的license，
在使用本地cpk解密时候会报错，所以安全。

