Process 子类的一个实例，该实例可用来控制进程并获得相关信息。Process 类提供了执行从进程输入、执行输出到进程、等待进程完成、检查进程的退出状态以及销毁（杀掉）进程的方法。 创建进程的方法可能无法针对某些本机平台上的特定进程很好地工作，比如，本机窗口进程，守护进程，Microsoft Windows 上的 Win16/DOS 进程，或者 shell 脚本。
创建的子进程没有自己的终端或控制台。

它的所有标准 io（即 stdin、stdout 和 stderr）操作都将通过三个流 (getOutputStream()、getInputStream() 和 getErrorStream()) 重定向到父进程。

子线程的输入是主线程提供的，这个数据对主线程来说就是输出，即jvm线程的OutputStream数据是子线程的stdin;主线程的InputStream是子线程的stdout

