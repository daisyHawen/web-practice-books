# 3.yahoo性能优化14条军规

---

1.ajax缓存：

* Post 返回200 每次都执行，不被缓存
* GET 返回304 同一地址不重复执行，可以被缓存

2.尽可能减少HTTP请求

3.使用CDN（内容分发网络）

4.添加Expire/Cahce-control头

5.启用Gzip压缩

6.CSS放在页面顶部

7.script放在页面底部

8.避免在css中使用Expression

9.将javascript和css都放在外部文件中（具体情况具体处理）

10.减少DNS查询

11.压缩Javascript和CSS

12.避免重定向

13.移除重复的标签

14.配置实体标签

