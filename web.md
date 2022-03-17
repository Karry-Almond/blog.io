# URL中需要编码的字符

简单总结：

>ASCII 表中没有对应的可显示字符，例如，汉字。 不安全字符，包括：# ”% <> [] {} | \ ^ ` 。 部分保留字符，即 & / : ; = ? @ 。

具体查看网址：[URL编码/解码详解_ccc369639963的博客-CSDN博客](https://blog.csdn.net/ccc369639963/article/details/123398268#:~:text=URL 之所以需要编码，是因为 URL 中的某些字符会引起歧义，比如 URL 查询参数中包含了"%26"或者"%"就会造成服务器解析错误；再比如，URL 的编码格式采用的是 ASCII,Unicode 格式，这表明 URL 中不允许包含任何非 ASCII 字符（比如中文），否则就会造成 URL 解析错误。)

可使用的编码解码网站：[URL解码 URL编码 在线URL解码/编码工具 (ip138.com)](https://tool.ip138.com/urlencode/)

