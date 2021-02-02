# Java 知识点整理


记录java中常见的知识点

<!--more-->


### 接口
java接口不能被实例化，但是可以被实现，要实现接口，必须实现接口中多有的方法，否则就要声明为抽象类。

### 多线程的实现
java内置了多线程的支持，一个进程包含了多个线程，每条线程并行执行不同的任务。  
1.实现Runnable接口，实现run()方法，并且new一个Thread对象，执行start()方法。run()方法中是此线程执行的方法体。  
2.继承Tread类，重写run()方法，实例化Tread对象后，执行start()方法，run()方法中是此线程执行的方法体。  
3.wait()，notify()不是Thread的方法，而是Object基础类的方法，因为每个对象都有锁，这两个方法是操作锁的方法，  
wait()方法的使用必须放在synchronized(obj)代码中，而且要放在while循环里面而不是if条件里面

### 面向对象的特征
1.封装：将属性和方法封装在一个类中，属性一般用private，用public修饰的get/set方法实现取值和赋值，这样将细节隐藏，只提供接口给外界操作。  
2.继承：面向对象的显著特征，JAVA是单继承，在父类中定义的属性和方法适用于其本身以及每一层子类的全部对象。减小代码的冗余度，增加复用性，子类只需要定义特殊的属性和方法即可。  
3.多态：【重载】通过修改参数的数量、修改参数的数据类型实现方法的重载，方法的重载增加了程序的可读性。【重写】子类继承父类时，名字相同，参数相同的方法为重写的方法。【不能覆盖静态方法】因为静态方法是绑定的类，静态方法属于类区域，实例方法属于堆区域

### final finally finalize的区别
final可以声明属性、方法和类，分别表示：属性不可变、方法不可覆盖、类不可继承  
finally是异常处理语句中的一部分，表示总是执行  
finalize是Object类的方法，是垃圾收集器将对象从内存中清楚之前对此对象调用的，做清理工作。

### JVM
java代码编译由java源码编译器完成  
java字节码的执行由JVM执行引擎完成  
过程包含三个机制：  
java源码编译机制  
类加载机制  
类执行机制  
JVM包括：  
栈：运行时方法  
堆：创建的对象  
本地方法栈：native方法存放  
方法区：加载的类、final变量、静态变量、属性等  

### JNI
java native Interface 实现了java和本地代码的交互，Java可以通过JNI访问操作系统底层

### Native
JDK中有一部分方法是用native修饰的，是用来访问本地方法的，这些本地方法不是java实现，但是java可以直接拿来用  
```java
public native static void Hello();
```

### 分布式系统架构思路
水平扩展  
负载均衡  
垂直拆分  

### Stream
jdk 1.8中增加了Lambda表达式，让行为表达为数据变得更容易，使得开发具有更强大的表达能力。  
Lambda表达式还引进了双冒号的引用，引用的是方法，不是一个属性
```java
list.forEach(n -> System.out.println(n));
list.forEach(System.out::println);
```
Stream有助于，为各种数据来源，可能的并行批量操作，建立简明的，声明性的表达式。  
让开发者更加注重要"做什么"，具体怎么做，交给库的开发者。

### static <T>
<T>泛型，在使用泛型前，进入集合的元素可以是任何类型，但当从集合中取出，所有类型都是Object类型的，需要进行向下的强制类型转换。

### Freemarker
是一个模板引擎，用java编写，用来生成web html文件  
由Template HTML文件和Java Objects文件，通过Freemarker生成最终的HTML文件  
可以通过mail发送，可以保存到本地文件，可以通过Web返回给浏览器  
适用于MVC架构，可以脱离servlet，代替jsp作为视图层，实现前后端工作的分离互不干扰  
jsp文件在编译以后会占用PermGen空间，而Freemarker不会。

### JBoss

### String和StringBuilder\StringBuffer
String用+运算符连接字符串和StringBuffer用append()方法连接的方式，如果是直接连接没有区别，编译之后都是用StringBuilder的方法，但是如果在循环结构中，就不同了，如果用String的+，循环体中会重复创建StringBuilder对象，而垃圾回收不及时的时候，会占用大量内存。

### hibernate的merge()方法
新new一个对象，  
如果该对象设置了ID，则这个对象就当作游离态处理：  
当ID在数据库中不能找到时，用update的话肯定会报异常，然而用merge的话，就会insert。  
当ID在数据库中能找到的时候，update与merge的执行效果都是更新数据，发出update语句；  
如果没有设置ID的话，则这个对象就当作瞬态处理：  
用update的话，由于没有ID，所以会报异常，merge此时则会保存数据，根据ID生产策略生成一条数据；

### 线程安全
线程安全是多线程访问同一代码，不会产生不确定的结果。  
编写线程安全的代码需要依靠线程同步。  
线程安全会涉及到synchronized，同一段代码只能有一个线程来操作。  
Collections工具类中提供了synchronizedSet、synchronizedSortedSet、synchronizedList、synchronizedMap、synchronizedSortedMap方法，返回对应的同步对象，解决多线程并发访问集合时的线程安全问题。

### Struts和Spring框架整合
StrutsAction管理委托给Spring框架，使用代理Action

### POST和GET的区别
1.GET是从服务器上获取资源，POST是向服务器提交资源  
2.GET传递参数是以name=value的形式，拼接在URL后面，用?开始拼接，后面用&连接各个参数，会显示在URL中，不安全；POST是将表单中的信息，放在请求头或者消息体中，安全而且可以大量传输。

### 向上转型
之前一直没有理解向上转型，今天碰到了Queue和LinkedList的时候，发现之前对这个概念的理解有出入。LinkedList实现了Queue接口，当一个对象是Queue的引用但是指向LinkedList的时候，只可以调用LinkedList类中实现的Queue的方法，LinkedList中自己的方法，该对象无法调用。

### 接口回调
在了解向上转型的时候，发现了接口调用这个概念，继续用Queue和LinkedList做解释。如果一个对象用Queue引用，指向了LinkedList类，那么该对象可以调用被LinkedList实现的Queue的方法。

### import
之前没有注意过import引用，今天发现同一个pakage下的类调用其他类不用import，尝试了一下调用其他pakage中的类，发现报了编译错误，需要用import引用一下。

### Queue\LinkedList
Queue继承Collection接口，LinkedList实现了它。支持FIFO，尾部添加，头部删除。  
添加:offer()，添加失败返回false  
删除:poll()，删除失败返回null  
获取:peek()，获取失败返回null  

### Vector\Stack
Vector继承Collection接口，Stack继承了Vector，支持LIFO，栈顶添加，栈顶删除。  
push()，添加元素  
pop( )，移除栈顶对象，返回该对象  
peek( )，返回栈顶对象  

### 判断文件是否存在
```java
File f = new File(path);	//path是全路径
boolean flag = f.exists();
```

### 得到文件列表
```java
File[] files = file.listFiles();
// 可以根据名字中的子字符串去过滤文件并对其进行操作
for(File f : files){
    // 获取到文件名
	if(f.getName().indexOf(biz) != -1){
		f.delete();
	}
}
```

### 获取绝对路径
```java
request.getSession().getServletContext().getRealPath(虚拟path)
// 但是这个方法被遗弃了，因为打包之后war包中没有目录结构，现改为
this.getClass().getClassLoader().getResource("/").getPath(); 
// 此处为classes目录, 往上一层可以用
getResource("/../")，
// 直接指向文件名getResource("/../xxx.xxx")
```

### 获取REQUEST信息
```java
// JSP：
http://localhost:8080/dmsd-itoo-exam-log-web/course/index.jsp
// 工程名：/dmsd-itoo-exam-log-web
request.getContextPath()：// 得到工程名：/dmsd-itoo-exam-log-web；
request.getServletPath()：// 返回当前页面所在目录下全名称：/course/index.jsp；
request.getRequestURL()：// 返回IE地址栏地址：http://localhost:8080/dmsd-itoo-exam-log-web/course/index.jsp；
request.getRequestURI() ：// 返回包含工程名的当前页面全路径：/dmsd-itoo-exam-log-web/course/index.jsp。
```

### 获取文件的后缀
```java
String subfix = path.substring(path.lastIndexOf("."), path.length());
```

### 重定向
```java
response.sendRedirect();
```

### 布尔型转字符串
```java
String s = String.valueOf( flag );
```

### String.getBytes()
根据指定的decode编码返回某字符串在该编码下的byte数组表示
```java
byte[] b_gbk = "中".getBytes("GBK"); 
byte[] b_utf8 = "中".getBytes("UTF-8"); 
byte[] b_iso88591 = "中".getBytes("ISO8859-1"); 
// b_gbk的长度为2，b_utf8的长度为3，b_iso88591的长度为1。 
byte[] b = filename.getBytes("GBK");
filename = new String(b, "8859_1");
```

### Runtime类
Runtime类封装了运行时的环境。每个 Java 应用程序都有一个 Runtime 类实例，使应用程序能够与其运行的环境相连接。  
一般不能实例化一个Runtime对象，应用程序也不能创建自己的 Runtime 类实例，但可以通过 getRuntime 方法获取当前Runtime运行时对象的引用。  
一旦得到了一个当前的Runtime对象的引用，就可以调用Runtime对象的方法去控制Java虚拟机的状态和行为.  
当Applet和其他不被信任的代码调用任何Runtime方法时，常常会引起SecurityException异常。  

### exec(String command) 
```java
Process p = r.exec(command);  
// 在单独的进程中执行指定的字符串命令。
// ecec()方法返回一个Process对象，可以使用这个对象控制Java程序与新运行的进程进行交互。
```

### 判断中文
通过Character.UnicodeBlock.of(String word)，返回一个Character.UnicodeBlock对象，看是否在中文对应的集合中

### JOSN返回数据案例
```java
// 定义map
Map map = new HashMap();
// 定义list
List imgList = new ArrayList();
// 遍历数据
for(int i=0;i<data.size();i++){
    StationImage stationImage = (StationImage)data.get(i);
    // 将当前对象放入map中
    Map imgMap = new HashMap();
    imgMap.put("image_name", stationImage.getImage_name());
    imgMap.put("image_des", stationImage.getImage_des());
    imgMap.put("image_url", stationImage.getImage_url());
    // 将当前Map放入list
	imgList.add(imgMap);
}

// 将list放入map
map.put("imgInfo", imgList);
JSONObject jb = JSONObject.fromObject(map, jsonConfig);
String result = jb.toString();
PrintWriter out = getOut(response);
out.print(result);
```

### shutdownHook
```java
Runtime.getRuntime().addShutdownHook(shutdownHook);
```
在jvm中增加一个关闭的钩子，在JVM关闭的之前，会先执行所有通过addShutdownHook添加的钩子，可以进行内存清理和对象销毁。

### Assert
junit中的Assert是断言，可以判断isNull isEmpty isTrue  isFalse等。
eclipse中可以开启断言  
assert [boolean 表达式]   如果为true 程序继续执行  如果为false  则抛出AssertionException异常  并终止执行  

### String...
参数长度可变  从0到n  当大于一时相当于String[]

### Timer和TimerTask
Timer是一个jdk提供的定时器工具，可以在主线程之外单独执行指定任务  
TimerTask是一个被Timer执行的任务  
timer.schedule (task, time)   在指定时间执行一次  
timer.schedule (task, firstTime，period)   在首次时间执行一次，后面每隔period时间执行一次  

### 加密方式
对称加密 + key

### linux安装jdk
yum search java|grep jdk

### WebSocket连接频率控制
使用List<Long> ，用时间戳标记每次连接的时间，在channelInactive里面控制是否重新连接。  
检查List超时的时间戳，超时的删除。

### HashMap
返回Key的value，如果没有返回默认值
getOrDefault(Key, defaultValue)

### JSON解析
解析json时碰到了问题，当json格式为{'key':['aaa','bbb']}这种时，使用net.sf.json中的JSONObject解析不出来，使用fastjosn可以解析  
但是解析出来的object中如果有的key的value为null时，containsKey返回值是true，使用时又是null，这样会报空指针异常，改用getOrDefault方法  

### com.google.protobuf.Timestamp 和 java.util.Date相互转换
```java
Timestamps.fromMillis(date.getTime());
new Date(timestamp.getSeconds() * 1000);
```

### split()正则表达式
```java
String time = "2016-10-03T12:00:00.000Z";
String[] timeArr = time.split("T|\\.");
```

### matches正则表达式
```java
String content = "Canceled: Canceled via API.\nSubmitted via API";
String pattern = ".*Canceled.*via.*API[\\s\\S]*Submitted.*";
System.out.println(Pattern.matches(pattern, content));
// 注意\n用.匹配不到，需要用[\\s\\S]
```

### javah 生成头文件
javah [<package><class>]  
javah com.nd.job.Animal  
生成的头文件中函数的命名规则：Java_包名_类名_方法名，由于要用到包名，所以要在包括全包名的目录下执行javah命令。

---

### 加密通讯

### URI拼接参数
https://www.jianshu.com/p/3d1d4becbc31

### fastJson反序列化
https://my.oschina.net/u/566591/blog/664617  
https://www.cnblogs.com/liqipeng/p/9148545.html  

---

### 字符串&JSON处理
当需要json格式的字符串时，可以先用json对象处理数据，最后用json转成string，避免了拼接和转义的处理
```java
String[] argss = new String[]{"swap/order:BTC-USD-SWAP","swap/order:EOS-USD-SWAP"};
JSONObject obj = new JSONObject();
obj.put("op", "subscribe");
obj.put("args", argss);
System.out.println(JSON.toJSONString(obj));
```

### list的remove应该使用iterator, 不能使用foreach删除

### hashmap rehash 的过程

### hashmap 和 currenthashmap 的区别, 以及为什么

### treeMap 的实现

### quickSort & binarySearch & 红黑树 & 

