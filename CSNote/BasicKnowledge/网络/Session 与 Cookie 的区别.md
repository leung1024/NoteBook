# Session 与 Cookie 的区别

## 什么是Cookie

由于HTTP协议无状态，所以为了保存用户的临时状态信息，需要在浏览器中存储cookie数据。服务端可以通过cookie中的某些数据来确定和跟踪用户。

#### 应用场景：

- 用户身份识别
- 用户的历史记录

## 什么是Session

简单定义为浏览器与服务器的交互会话，Session的数据保存形式有内存、数据库、文件等。Session依赖于Session ID来确定用户的身份，因此服务器还需要通过Cookie或者URL的方式将Session ID告诉用户。服务器通过Session ID能够识别用户的身份和获取对应的状态信息，从而避免将一些关键的数据保存在用户可访问的Cookie中。

## reference

- [Cookie 与 Session 的区别](https://juejin.im/entry/5766c29d6be3ff006a31b84e)
- [彻底弄清楚session是什么？](https://blog.csdn.net/think2me/article/details/38726429)

