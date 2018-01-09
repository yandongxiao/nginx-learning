# lua-nginx-module

lua-nginx-module将Lua语言集成到了Nginx的世界，使得开发者可以使用Lua语言来编写代码。之所以选择Lua语言，也是因为它的性能与C相近。

> lua-nginx-module 针对的是（HTTP 0.9/1.0/1.1/2.0, WebSockets）等协议

每个请求都会在Lua的一个协程内进行处理，lua-nginx-module提供了一套**同步但非阻塞的网络接口**。内部原理：1. lua-nginx-module接口执行异步请求，然后yield释放资源，同时该协程会被放入Nginx的时间模型中；2. 当Nginx接收到后端响应，触发协程继续执行。


1. Lua coroutine是否可以进行阻塞式的操作？

	不可以，因为Lua不会主动释放资源。但lua-nginx-module的接口保证是100%非阻塞的。

2. 如何设置一个nginx worker配备多少Lua coroutine?

	一个nginx worker配备了一个Lua解释器。在一个Lua解释器内可以创建任意多个协程。

3. lua-nginx-module是的协程并发性问题？

	Lua是非抢占式的多协程工作模式，协程需要自己主动释放资源，其它协程才能够被执行。

	> 如果Lua模块调用了Nginx的IO接口，这些接口的实现方式中存在主动yield资源的操作。此时，Nginx就会去处理别的请求。

4. 如何进行server级别的变量共享？

	- 使用 ngx.shared.DICT；
	- 使用redis，mysql等方式存储变量，使用openresty的其它模块，读写存储。例如，lua-resty-redis。

5. 不是所有的lua模块都可以与nginx-lua-module共用，作者提供了lua-resty-*系列的模块，如何编写module?

6. 什么是WebSockets？

7. Cosockets?