### java9特性

#### 模块化

在```src```下右击新建

![image-20210324130008456](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20210324130008456.png)

内容： 导包

```java
module java9Demo {
    //向外导包 导的是包
    exports com.saimo.bean;
    exports com.saimo.entity;
}
```



![image-20210324130147913](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20210324130147913.png)

内容：引如包  引的是模块

```java
module java9ModuleTest {
    requires java9Demo;
    requires java.logging;
}
```

这样一来，```public```就不再是```access```，必须要```requires```指明对其他依赖的依赖，进一步才是模块内的可访问性```public```

使用：

```java
public class ModuleTest {

    private final static Logger logger   =  Logger.getLogger("saimo");
    public static void main(String[] args) {
        Person person = new Person("xiaoMing",12);   //这些都是通过requires指明
        System.out.println(person.getName());
        User user = new User("小强",324);
        logger.info(user.getName());
        System.out.println(111);
    }
}

```

与maven的区别：

Maven 有两个主要的特征：依赖管理和构建管理。
	依赖管理即可以决定库中的版本并从仓库中下载下来。
	构建管理即管理开发过程中的任务，包括初始化、编译、测试、打包等。

Module是系统内置用于表述组件之间的关系，对于版本的管理还是处于最原始的状体，管理一种强制的依赖关系。



#### JShell

JShell是Java的REPL工具(Read-eval-print-loop)：交互式解析器，一种命令行工具。它允许你无需使用类或者方法包装来执行Java语句。

类似与python的交互式编程环境

进入Jshell，即在安装bin目录下进入即可

```java
F:\env\java9\bin>jshell.exe
|  欢迎使用 JShell -- 版本 9.0.4
|  要大致了解该版本, 请键入: /help intro

jshell> System.out.println(111)
111

jshell> int a = 10;
a ==> 10

jshell> System.out.println(a)
10

jshell>
```



#### 多版本兼容jar包



#### 接口的私有方法

默认方法中调用私有方法

```java
public interface Tinterface {
	//不对外提供  接口内部自己使用
    private void a(){
        System.out.println(1111);
    }

    default void b(){
        //在默认方法中调用私有方法
        a();
    }
}
```

#### 钻石操作符

```java
public static void main(String[] args) {
    //创建一个继承于HashSet的匿名子类 可重写一些方法
    Set<String> strings = new HashSet<>(){
        @Override
        public int size() {
            System.out.println("重写了size（）方法");
            return super.size();
        }
    };
    strings.size();
}
```



#### 只读集合

创建一个只读集合

```java
List<String> list = new ArrayList<>();

list.add("a");
list.add("b");
list.add("c");
//返回一个只读集合 jdk8
List<String> listRead = Collections.unmodifiableList(list);
//报错
listRead.add("d");

//使用jdk9
//返回一个只读集合
List<String> listRead = List.of("a", "b", "c");
//报错
listRead.add("d");
```

#### Stream Api

##### takeWhile

在进行Stream流的管道数据处理的时候，提供的Predicate条件返回false之后，将跳过剩余的数据元素直接返回

只会输出1，2，3，当遇到false后，会立刻返回

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4324, 645, 76, 878, 9978);
list.stream().takeWhile(x -> x < 90).forEach(System.out::println);
```

##### dropWhile

提供的Predicate条件在管道流中返回false之后，此元素后面的所有数据元素作为返回值返回 与takeWhile正好相反

输出3之后的数据

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4324, 645, 76, 878, 9978);
list.stream().dropWhile(x -> x < 90).forEach(System.out::println);
```

##### ofNullable

此方法返回一个包含单个元素的顺序Stream。如果提供的元素为null，则此方法返回空Stream

不会报错

```java
Stream<Object> objectStream = Stream.ofNullable(null);
```

##### Stream.iterate

起点，断言，操作

```java
Stream.iterate(0, x -> x < 10, x -> x + 1).forEach(System.out::println);
```

#### 全新http客户端

```java
public static void main(String[] args) throws URISyntaxException, IOException, InterruptedException {
    HttpClient httpClient = HttpClient.newHttpClient();
    HttpRequest req = HttpRequest.newBuilder(new URI("https://www.baidu.com")).GET().build();

    HttpResponse<String> response;
    response = httpClient.send(req, HttpResponse.BodyHandler.asString());
    System.out.println(response.statusCode());
    System.out.println(response.body());
}
```



### java11特性

#### 局部变量类型推断

```java
var a = "ming";
var b = new String("1");
```

推断后还是强类型，不可更改

#### 字符串处理增强

```java
//判空
boolean blank = " ".isBlank();
//去除首位空格
String strip = "dad ".strip();
//复制
String repeat = "da".repeat(2);
```

#### 集合增强

添加了创建不可变集合的api

从不可变集合中创建一个一个不可变对象，那么是指向同一个对象

```java
var list = List.of("Java", "Python", "C"); //不可变
var copy = List.copyOf(list);

System.out.println(list == copy);   // true
```

从一个可变集合创建，则重新创建一个不可变对象

```java
var list = new ArrayList<String>(); 
var copy = List.copyOf(list);

System.out.println(list == copy);   // false
```

#### Instream增强

transferTo： 直接将数据输出到outputStream

```java
var inputStream = new FileInputStream("javastack.txt");
try (var outputStream = new FileOutputStream("javastack2.txt")) {
    inputStream.transferTo(outputStream);
}
```