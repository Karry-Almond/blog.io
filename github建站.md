# 

# 使用jekyll自定义博客

虽然有github官网的说明文档，但是实际操作起来还是有些生硬，故做以下博客。

# 卸载与重装nodejs

因为本机的nodejs太老了，使用之后发现老是报一些包的错误，故卸载nodejs并重装。没有这个情况的同学可以不管。

具体查看网址：https://blog.csdn.net/strawberry_x/article/details/113648770

# 更换git账户后，用git推送仍然是原始账号

github desktop都已经是更新后的账户了，但是使用windows下的cmd进行hexo d时仍然显示是原始账户。

解决方法：

控制面板--》用户账户--》凭据管理器=》windows凭据

选择保存有原始账户的凭据并删除。

具体查看网址：https://blog.csdn.net/qq_40615403/article/details/89325364

更换账户之后会要求输入账户密码，但是在2021-8之后，github不支持了“账户密码”的输入，而是仅支持“token”。

token的获取见网址：https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token

在弹窗要有输入的password处输入获得的token。