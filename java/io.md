### IO/多路复用

|                        java                        |        select/poll         |           epell            |
| :------------------------------------------------: | :------------------------: | :------------------------: |
|             ServerSocketChannel.open()             |        fd4=socket()        |         fd4=socket         |
|     server.bind(new InetSocketAddress(port));      | bind(fd4)<br />listen(fd4) | bind(fd4)<br />listen(fd4) |
|                  Selector.open()                   |  create a select poll fd   |        epoll_create        |
| server.register(selector, SelectionKey.OP_ACCEPT); |      add accept event      |         epoll_ctl          |
|                 selector.select()                  |  select（fd4） poll(fd4)   |         epoll_wait         |