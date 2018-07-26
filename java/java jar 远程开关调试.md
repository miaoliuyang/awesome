java -Xdebug -Xrunjdwp:transport=dt_socket,address=8888,server=y,suspend=n -jar xx.jar

-XDebug 启用调试

-Xrunjdwp 加载JDWP的JPDA参考执行实例。

transport  用于在调试程序和 VM 使用的进程之间通讯。

dt_socket 套接字传输。

server=y/n VM是否需要作为调试服务器执行。

address=2345调试服务器监听的端口号。

suspend=y/n 是否在调试客户端建立连接之后启动 VM 。


关于suspend多说一句，如果设置为y，它会阻塞程序运行，直到有客户端连接到对应的监听端口(这里是8888)，程序才真正开始执行。我们有时候会抱怨程序一闪而过，还没来得及在本地加载上代码程序就执行完了，这种情况就可以使用suspend参数。

还有一个细节是-jar参数不能写到-Xug参数前，像这样无法启用调试：
java -jar lib/Main.jar -Xdebug -Xrunjdwp:transport=dt_socket,address=8888,server=y,suspend=y

-Dserver.port  修改端口
