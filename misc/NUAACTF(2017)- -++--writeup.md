








# NUAACTF(2017)- -++--writeup


下载题目的压缩包：

![misc-3.1](https://github.com/rootwlen/ctf/blob/main/misc/img/misc-3.1.png)

”( ͡° ͜ʖ ͡°)fuck“编码的

```
“( ͡° ͜ʖ ͡°)fuck” 是一种模仿 Brainfuck 的编程语言，但将原始 Brainfuck 的 8 个符号变成了 **8 个不同的 `( ͡° ͜ʖ ͡°)` 变体表情符号。
```

![misc-3.2](https://github.com/rootwlen/ctf/blob/main/misc/img/misc-3.2.png)

参考网站：https://esolangs.org/wiki/(_%CD%A1%C2%B0_%CD%9C%CA%96_%CD%A1%C2%B0)fuck

![misc-3.3](https://github.com/rootwlen/ctf/blob/main/misc/img/misc-3.3.png)

直接按照对应的颜体字对照表解码出Brainfuck编码，不过这道题是2017年的，而在2018年的某一次修改中，.和-对应的Lenny Face被交换了以下，所以这里要按照旧表解码：

```
emoji_to_bf = {
    '( ͡° ͜ʖ ͡°)': b'+',
    '(> ͜ʖ<)': b'.',
    '(♥ ͜ʖ♥)': b'-',
    'ᕙ( ͡° ͜ʖ ͡°)ᕗ': b',',
    '(∩ ͡° ͜ʖ ͡°)⊃━☆ﾟ.*': b'<',
    'ᕦ( ͡°ヮ ͡°)ᕥ': b'>',
    'ᕦ( ͡° ͜ʖ ͡°)ᕥ': b'^',
    '( ͡°╭͜ʖ╮ ͡°)': b'v',
    'ಠ_ಠ': b'x',
    '( ͡°(': b'[',     
    ') ͡°)': b']'
}

# 编码为字节形式
replace_dict = {k.encode('utf-8'): v for k, v in emoji_to_bf.items()}

# 读取代码
with open("C:/++--.txt", 'rb') as f:
    code = f.read()

# 替换颜文字为 Brainfuck
for k, v in replace_dict.items():
    code = code.replace(k, v)

# 输出解密后的 Brainfuck 指令
print(code.decode())
```

解码之后：

![misc-3.4](https://github.com/rootwlen/ctf/blob/main/misc/img/misc-3.4.png)

```
++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>>>++++++++++.+++++++.--------------------..++.+++++++++++++++++.--------------.+++++++++++++++++++++.-------------------------.++++++++++++++++.<------------------.---.>----.--------.+++++++++++++++.------------------.++++++++.------------.+++++++++++++++++.<.>+++++.--.++++++++++.
```

转到Brainfuck解码：https://tool.bugku.com/brainfuck/

![misc-3.5](https://github.com/rootwlen/ctf/blob/main/misc/img/misc-3.5.png)
