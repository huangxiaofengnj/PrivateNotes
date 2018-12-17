# PrivateNotes
架构师进阶
## 1.集合的常用类
HashMap  HashSet LinkedList ArrayList LinkedBlockingQueue
ConcurrentHashMap CopyOnWriteArrayList ConcurrentLinkedDeque 
SynchronizedCollection 

1. hashmap跟hashtable的区别  
``https://www.cnblogs.com/wang-meng/p/5898837.html``
2. ArrayList: 内部采用数组存储元素，支持高效随机访问，支持动态调整大小  
    增删慢，查询快。
LinkedList: 内部采用链表来存储元素，支持快速插入/删除元素，但不支持高效地随机访问.  
    增删快，查询慢。
Vector: 可以看作线程安全版的ArrayList  


## 2.知识广度
1. http协议(定义、常见的请求头以及响应头<功能>、状态码)
2. hashCode生成策略  
``http://www.cnblogs.com/dolphin0520/p/3681042.html``  
很多地方都会利用到hash表来提高查找效率。在Java的Object类中有一个方法:
public native int hashCode();
hashCode方法的主要作用是为了配合基于散列的集合一起正常运行，这样的散列集合包括HashSet、HashMap以及HashTable。
考虑一种情况，当向集合中插入对象时，如何判别在集合中是否已经存在该对象了？（注意：集合中不允许重复的元素存在）
也许大多数人都会想到调用equals方法来逐个进行比较，这个方法确实可行。但是如果集合中已经存在一万条数据或者更多的数据，如果采用equals方法去逐一比较，效率必然是一个问题。此时hashCode方法的作用就体现出来了，当集合要添加新的对象时，先调用这个对象的hashCode方法，得到对应的hashcode值，实际上在HashMap的具体实现中会用一个table保存已经存进去的对象的hashcode值，如果table中没有该hashcode值，它就可以直接存进去，不用再进行任何比较了；如果存在该hashcode值， 就调用它的equals方法与新元素进行比较，相同的话就不存了，不相同就散列其它的地址，所以这里存在一个冲突解决的问题，这样一来实际调用equals方法的次数就大大降低了，说通俗一点：Java中的hashCode方法就是根据一定的规则将与对象相关的信息（比如对象的存储地址，对象的字段等）映射成一个数值，这个数值称作为散列值。

## 3.Mysql
存储过程、函数、事务、索引、触发器、视图、游标以及一些优化操作


## 4.Linux常用命令


##2.Lamda语法使用
  distinct(equal相等，则hashcode必须相等，反之不成立,最好equal不相等时hashcode也不等，否则会耗费计算时间)
##3.几种反射
1. CGLIB  
  - 面向类，final的方法和类无法动态代理
  - 业务需要定义实现``MethodInterceptor``的类，在``intercept()``中拦截原生类的方法调用
  - 原理：动态的生成一个有关被代理类的class文件，并转载运行之
  ``RpkServiceImpl$$EnhancerByCGLIB$$614bacd8 extends MyTarget implements net.sf.cglib.proxy.Factory``
  - Demo:
``
public class CglibTest {
	public static void main(String[] args) {  
		List<String> classNames = PackageScanUtil.scanPackageb		("cn.vivo.reflect.proxy.impl");
		for (String name : classNames) {
			try {
				Class<?> cls = Class.forName(name);
				Enhancer enhancer = new Enhancer();
				enhancer.setSuperclass(cls);
				enhancer.setCallback(new CglibSoftMethodInterceptor());
				SoftService proxy = (SoftService)enhancer.create();
				proxy.getSoftDetail("cn.nuia.neostore");
				//Over proxy class name:cn.vivo.proxy.impl.RpkServiceImpl$$EnhancerByCGLIB$$614bacd8
				System.out.println("Over proxy class name:" + proxy.getClass().getName());
			} catch (Exception e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
	}
}
`` 

2. Jdk Proxy
  - 面向接口，只能反射实现了接口的类
  - 业务需要定义实现``InvocationHandler``的类，在``invoke()``中拦截原生类的方法调用
  - 原理：动态生成了一个``Proxy``的子类，并实现了业务接口，而JAVA中是**单继承，从而决定了JDK Proxy只能反射接口，而不是反射类**
  ``com.sun.proxy.$Proxy0 extends java.lang.reflect.Proxy implements TargetInterface``
  - 添加如下代码可以生成中间的代理.class文件，在磁盘中可以看到：
  ``System.setProperty("sun.misc.ProxyGenerator.saveGeneratedFiles", "true")``

3. Spring的代理
  - Spring中的代理对象其实是JDK Proxy和CGLIB Proxy 的结合
  - 业务要实现``org.aopalliance.intercept.MethodInterceptor`` 的``invoke()``接口，通过``ProxyFactory.setInterfaces()``接口判断走CGLIB还是JDK Proxy的动态代理

4. ASM JavaAssist
  - 在JVM运行期间，遵循JAVA编译系统组织.class文件的结构和格式，生成二进制文件，然后加载这些二进制文件转化为对应的类，这样就完成了动态创建一个类的能力，也就是1-2-3中提到的动态代理。
  - 常用的开源框架有ASM JavaAssist

5. 参考资料
  - ``https://blog.csdn.net/sunnycoco05/article/details/78845878``
  - ``https://www.cnblogs.com/flyingeagle/articles/7102282.html``
 
##4.线程的状态机、并发、线程池、NIO/BIO/AIO
1. 守护进程  
`` thread.setDaemon(true)``  
任何一个守护线程都是整个JVM中所有非守护线程的保姆,只要当前JVM实例中尚存在任何一个非守护线程没有结束，守护线程就全部工作；只有当最后一个非守护线程结束时，守护线程随着JVM一同结束工作。

##5.Spring
1. 原理图  
   ![Spring基本概念原理图](https://images2015.cnblogs.com/blog/799093/201607/799093-20160724232929888-1552179747.png)
2. Spring aop/Aspectj  
  - 通过**AspectJProxyFactory**类的addAspect(ControllerAspect)方法反射
<code>
 proxyFactory.addAspect(ControllerAspect.class);   
 SearchController proxy = (SearchController)pf.getProxy();  
 proxy.getSearchList(form,form2);
<code>

    @Aspect//通过该注解将该类标识为一个切面    
    public class ControllerAspect {  
    @Around("execution(* cn.vivo.controller..*.*(..))")  
    public void around(ProceedingJoinPoint joinPoint) throws Throwable {   

        String className = joinPoint.getTarget().getClass().getSimpleName();  
    	String methodName = joinPoint.getSignature().getName();  
    	Signature signature = joinPoint.getSignature();  
    	Object[] args = joinPoint.getArgs();  
    	for (Object object : args) {  
    		BaseForm form = (BaseForm)object;  
			System.out.println("around:"+form.toString());  
		}  
    	  
        joinPoint.proceed();  

        System.out.println(className+" Around method:"+methodName+","+signature.getDeclaringTypeName());
            }  
        }

</code>

3. Spring的生命周期

4. Spring使用ThreadLocal解决线程安全问题  
   在同步机制中，使用锁机制保证多线程访问变量的安全性，相对而言需要考虑到锁的对象，锁的释放等问题，比较复杂。  
   ThreadLocal以**空间换时间**，为解决多线程并发的问题，为每一个线程提供一个独立的副  本，隔离了多个线程对数据的访问冲突，提供了线程安全的共享对象，将不安全的数据封装进了  ThreadLocal中  
   ``https://blog.csdn.net/zengdeqing2012/article/details/77098994``

3. 参考资料

``https://www.cnblogs.com/wang-meng/p/5701982.html ``

##6.Spring MVC的原理，如何实现一个自定义的Spring MVC
1. 原理图
  ![Spring MVC原理图](https://images2015.cnblogs.com/blog/799093/201607/799093-20160724233025107-1243112232.jpg)

2. 参考资料  
   ``https://www.cnblogs.com/wang-meng/p/5701987.html``
##6.Tomcat原理
##JVM原理
##7.线上问题处理
  测试脚本变成压测脚本了，定位思路
##9.Redis的原理

##10.Ngix+openRes的优化
 - Lua
 - 反向代理
 - 
##11.mybatis

//#{} 只是替换？，相当于PreparedStatement使用占位符去替换参数，可以防止sql注入。

//${} 是进行字符串拼接，相当于sql语句中的Statement，使用字符串去拼接sql；$可以是sql中的任一部分传入到Statement中，不能防止sql注入。

使用${} 去取出参数值信息，需要使用${value}
// #{} 只是表示占位，与参数的名字无关，如果只有一个参数，会自动对应。

##hibernate的实现原理与3种反射结合起来
1. 核心思想要了解；开发流程<加载文件的过程>；查询方式；
2. 如何优化；sql与hql的区别；update与 saveOrUpdate的区别；get和load的区别；
3. Java对象三种状态的特征以及转换、核心API、一级缓存和二级缓存、延迟加载


##12.ThreadLocal

##14.设计模式
## **15.解决原子性、内存可见性、有序性**
  transient：不需要序列号
  volidate  
  Lock  
  AtomicInteger  
  synchronized   
### 分布式锁
https://blog.csdn.net/xlgen157387/article/details/79036337
分布式的CAP理论告诉我们“任何一个分布式系统都无法同时满足**一致性（Consistency）**、**可用性（Availability）**和**分区容错性（Partition tolerance）**，最多只能同时满足两项

#### 1.数据库实现
<code>
DROP TABLE IF EXISTS `method_lock`;  
CREATE TABLE `method_lock` (  
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',  
  `method_name` varchar(64) NOT NULL COMMENT '锁定的方法名,唯一索引 ',  
  `desc` varchar(255) NOT NULL COMMENT '记录当前线程信息与机器信息',  
  `update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE   CURRENT_TIMESTAMP,  
  PRIMARY KEY (`id`),  
  UNIQUE KEY `uidx_method_name` (`method_name`) USING BTREE  
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8 COMMENT='锁定中的方法';  
</code>

由于method_name的唯一性约束，插入一条固定数据，则数据库会确保只会有一条数据成功入库，一旦代码检测到入库成功，代表当前ECS实例上的机器获取锁成功

```
INSERT INTO method_lock (method_name, desc) VALUES ('methodName', '测试的methodName');
```

其他ECS实例循环等待，如等待3秒，再次插入，看是否可以成功
获取锁的ECS实例业务处理完毕后，如当ES搜索返回空时，查询数据库成功，则删除此数据，如此其他实例可以继续获得锁

```
delete from method_lock where method_name ='methodName';
```

需要考虑锁失效机制，如果数据库崩溃，所有的EC实例都无法获得锁，因此，需要有一个定时器定时清除该数据

#### 2.Redis实现
获取锁

```
 SET resource_name my_random_value NX PX 30000
```

利用Redis执行Lua脚本的原子性，解锁

<code>
if redis.call("get",KEYS[1]) == ARGV[1] then  
    return redis.call("del",KEYS[1])    
else
    return 0
end
</code>



##16.数据库
###  红黑树  二叉树 B+书
###  数据库索引原理与性能优化
 Innodb
group by,having

   Bloom过滤器--softId的int特性自定义过滤器--兼容已外发客户端版本pageSize服务器统一控制为10
## 算法
### LurCache

##17.Volley的原理

## 18. 数据结构与算法
1. 链表与数组。
2. 队列和栈，出栈与入栈。
3. 链表的删除、插入、反向。
4. 字符串操作。
5. Hash表的hash函数，冲突解决方法有哪些。
6. 各种排序：冒泡、选择、插入、希尔、归并、快排、堆排、桶排、基数的原理、平均时间复杂度、最坏时间复杂度、空间复杂度、是否稳定。
7. 快排的partition函数与归并的Merge函数。
8. 对冒泡与快排的改进。
9. 二分查找，与变种二分查找。
10. 二叉树、B+树、AVL树、红黑树、哈夫曼树。
11. 二叉树的前中后续遍历：递归与非递归写法，层序遍历算法。
12. 图的BFS与DFS算法，最小生成树prim算法与最短路径Dijkstra算法。
13. KMP算法。
14. 排列组合问题。
15. 动态规划、贪心算法、分治算法。（一般不会问到）
16. 大数据处理：类似10亿条数据找出最大的1000个数.........等等

## 18.常见面试题
<Code>
http://www.cnblogs.com/wang-meng/p/5701982.html  
https://www.cnblogs.com/wang-meng/p/5701946.html  

</code>

## GitHub账号
0241110108@163.com


