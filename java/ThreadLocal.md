## ThreadLocal源码解读

1. Thread ThreadLocalMap ThreadLocal 关系图

   ![threadLocal](../img\threadLocal.png)

2. ThreadLocal

   ThreadLocal每次初始后threadLocalHashCode增加HASH_INCREMENT

   ```java
   public class MyThreadLocal {	
   	private final int threadLocalHashCode = nextHashCode();
   
       private static AtomicInteger nextHashCode = new AtomicInteger();
   
       private static final int HASH_INCREMENT = 0x61c88647;
   
       private static int nextHashCode() {
           return nextHashCode.getAndAdd(HASH_INCREMENT);
       }
       
       public static void main(String[] args) {
           System.out.println(MyThreadLocal.nextHashCode());
           System.out.println(MyThreadLocal.nextHashCode());
           System.out.println(MyThreadLocal.nextHashCode());
           System.out.println(MyThreadLocal.nextHashCode());
       }
   }
   ```

   输出结果

   ```java
   0
   1640531527
   -1013904242
   626627285
   ```

   哈希算法

   ```
   public class MyThreadLocal {
   
       private final int threadLocalHashCode = nextHashCode();
   
       private static final int INITIAL_CAPACITY = 16;
   
       private static AtomicInteger nextHashCode = new AtomicInteger();
   
       private static final int HASH_INCREMENT = 0x61c88647;
   
       private static int nextHashCode() {
           return nextHashCode.getAndAdd(HASH_INCREMENT);
       }
   
       public static void main(String[] args) {
           System.out.println(MyThreadLocal.nextHashCode() & (INITIAL_CAPACITY - 1));
           System.out.println(MyThreadLocal.nextHashCode() & (INITIAL_CAPACITY - 1));
           System.out.println(MyThreadLocal.nextHashCode() & (INITIAL_CAPACITY - 1));
           System.out.println(MyThreadLocal.nextHashCode() & (INITIAL_CAPACITY - 1));
       }
   }
   ```

   输出结果

   ```
   0
   7
   14
   5
   ```

   方法

   ```java
    public T get() {
           Thread t = Thread.currentThread();
           // 获取当前线程ThreadLocalMap
           ThreadLocalMap map = getMap(t);
           if (map != null) {
               // 通过当前ThreadLocal的threadLocalHashCode哈希值获取Entry
               ThreadLocalMap.Entry e = map.getEntry(this);
               if (e != null) {
                   @SuppressWarnings("unchecked")
                   T result = (T)e.value;
                   return result;
               }
           }
           return setInitialValue();
    }
   
    public void set(T value) {
           Thread t = Thread.currentThread();
         	// 获取当前线程ThreadLocalMap
           ThreadLocalMap map = getMap(t);
           if (map != null)
             // 通过当前ThreadLocal的threadLocalHashCode哈希值设置Entry
               map.set(this, value);
           else
             // 当前线程没有ThreadLocalMap 场景ThreadLocalMap赋值给Tread
               createMap(t, value);
    }
   
    public void remove() {
            // 获取当前线程ThreadLocalMap
            ThreadLocalMap m = getMap(Thread.currentThread());
            if (m != null)
                // 删除当前线程的ThreadLocal的entry
                m.remove(this);
    } 
   ```

3. ThreadLocalMap

   成员变量

   ```java
   
           static class Entry extends WeakReference<ThreadLocal<?>> {
               /** The value associated with this ThreadLocal. */
               Object value;
   
               Entry(ThreadLocal<?> k, Object v) {
                   super(k);
                   value = v;
               }
           }
   
           /**
            * The initial capacity -- MUST be a power of two.
            */
           private static final int INITIAL_CAPACITY = 16;
   
           /**
            * The table, resized as necessary.
            * table.length MUST always be a power of two.
            */
           private Entry[] table;
           /**
            * The number of entries in the table.
            */
           private int size = 0;
   
           /**
            * The next size value at which to resize.
            */
           private int threshold; // Default to 0
   ```

   构造函数

   ```java
    ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
        		// 初始数组化大小
               table = new Entry[INITIAL_CAPACITY];
               // 获取散列值
               int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
               table[i] = new Entry(firstKey, firstValue);
               size = 1;
               // 设置阙值  len * 2 / 3;
               setThreshold(INITIAL_CAPACITY);
           }
   ```

    方法

   ```java 
    private Entry getEntry(ThreadLocal<?> key) {
     			// 获取散列值
               int i = key.threadLocalHashCode & (table.length - 1);
               Entry e = table[i];
               if (e != null && e.get() == key)
                   return e;
               else
                   return getEntryAfterMiss(key, i, e);
    }
   
   /**
    * 查找数组i之后的entry
    */
   private Entry getEntryAfterMiss(ThreadLocal<?> key, int i, Entry e) {
               Entry[] tab = table;
               int len = tab.length;
   
               while (e != null) {
                   ThreadLocal<?> k = e.get();
                   if (k == key)
                       return e;
                   if (k == null)
                       // entrty的key被回收了
                       expungeStaleEntry(i);
                   else
                       // 获取下一个数组的坐标
                       i = nextIndex(i, len);
                   e = tab[i];
               }
               return null;
   }
   
    private int expungeStaleEntry(int staleSlot) {
           ThreadLocal.ThreadLocalMap.Entry[] tab = table;
           int len = tab.length;
   
           // expunge entry at staleSlot
           // 清空该数组位置的entry
           tab[staleSlot].value = null;
           tab[staleSlot] = null;
           size--;
   
           // Rehash until we encounter null
           ThreadLocal.ThreadLocalMap.Entry e;
           int i;
           // 循环staleSlot之后的entry
           for (i = nextIndex(staleSlot, len); (e = tab[i]) != null; i = nextIndex(i, len)) {
               ThreadLocal<?> k = e.get();
               // 清理没有被应用的entry
               if (k == null) {
                   e.value = null;
                   tab[i] = null;
                   size--;
               } else {
                   // 重新计算当前位置ThreadLocal的散列值
                   int h = k.threadLocalHashCode & (len - 1);
                   // 计算后的散列值与当前散列值不相同
                   if (h != i) {
                       // 当前位置的数组设置null
                       tab[i] = null;
                       // Unlike Knuth 6.4 Algorithm R, we must scan until
                       // null because multiple entries could have been stale.
                       // 当前h位置==null设置entey
                       while (tab[h] != null) 
                           // h位置向后移动1位
                           h = nextIndex(h, len);
                       tab[h] = e;
                   }
               }
           }
           return i;
       }
   ```

   


​      

 