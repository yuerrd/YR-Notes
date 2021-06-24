## Netty

1. ### ByteBuf

   Pooled  VS  Unpooled

   ```
   Pooled: 预先申请内存地址
   Unpooled: 每次使用时调用的调用系统api
   ```

   Unsafe  VS 非Unsafe

   ```
   Unsafe: 直接通过JDK Unsafe对象获取值
   非Unsafe: 数组和索引获取值
   ```

   Heap  VS  Direct

   ```
   Heap: 堆上获取内存
   Direct: 调用JDK api分配内存,不会被gc管理
   ```

2. 

