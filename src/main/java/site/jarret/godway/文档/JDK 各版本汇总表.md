# JDK 各版本汇总表

| 版本               | 发布时间        | 支持截至日       | 最后一次更新                                                                                                                  |
|------------------|-------------|-------------|-------------------------------------------------------------------------------------------------------------------------|
| J2SE 1.4         | 2002 年 2 月  | 2013 年 2 月  | 2008 年 10 月                                                                                                             |
| J2SE 5.0         | 2004 年 9 月  | 2015 年 4 月  | 2009 年 11 月                                                                                                             |
| Java SE 6        | 2006 年 12 月 | 2018 年 12 月 | 2013 年 4 月                                                                                                              |
| Java SE 7        | 2011 年 7 月  | 2015 年 4 月  | 2022 年 7 月                                                                                                              |
| Java SE 8 (LTS)  | 2014 年 3 月  | 2030 年 12 月 | 2019 年 01 月 Oracle 停止更新(商用) 2030 年 12 月 Oracle 停止更新(非商用) 2026 年 05 月 AdoptOpenJDK 停止更新 2023 年 06 月 Amazon Corretto 停止更新 |
| Java SE 9        | 2017 年 9 月  | 不适用         | 2018 年 3 月 OpenJDK 停止更新                                                                                                 |
| Java SE 10       | 2018 年 3 月  | 不适用         | 2018 年 9 月 OpenJDK 停止更新                                                                                                 |
| Java SE 11 (LTS) | 2018 年 9 月  | 2026 年 9 月  | 2024 年 8 月 Amazon Corretto 停止更新 2022 年 9 月 AdoptOpenJDK 于 停止更新                                                          |
| Java SE 12       | 不适用         | 2019 年 3 月  | 2019 年 9 月OpenJDK 停止更新                                                                                                  |
| Java SE 13       | 不适用         | 2019 年 9 月  | 2020 年 3 月OpenJDK 停止更新                                                                                                  |
| Java SE 14       | 不适用         | 2020 年 3 月  | 2020 年 9 月OpenJDK 停止更新                                                                                                  |
| Java SE 14       | 2020 年 3 月  | 不适用         | 2020 年 9 月OpenJDK 停止更新                                                                                                  |
| Java SE 15       | 2020 年 9 月  | 不适用         | 2021 年 3月OpenJDK 停止更新                                                                                                   |
| Java SE 16       | 2021 年 3 月  | 不适用         | 2020 年 9 月OpenJDK 停止更新                                                                                                  |
| Java SE 17（LTS）  | 2021 年 9月   | 待定          | 待定                                                                                                                      |



# JDK 各版本特性

## JDK4

- 正则表达式
- 异常链
- NIO
- 日志类
- XML解析器
- XLST转换器

## JDK5

- 自动装箱
- 泛型
- 动态注解
- 枚举
- 可变长参数
- 遍历循环

## JDK6

- 提供动态语言支持
- 提供编译API和卫星HTTP服务器API
- 改进JVM的锁
- 同步垃圾回收
- 类加载

## JDK7

- 支持二进制数字 0b
- 多个异常捕获（多个异常通过使用 “|”操作符分隔）
- 安全的加减乘除
- Switch 支持 String字符串
- Java集合（Collections）增强
- 两个char之间的equals方法
- boolean类型的反转，空指针安全，参与位运算
- 数值可以加下划线用作分隔符
- 简化了可变参数方法的调用
- 调用泛型类的构造方法时，可以省去泛型参数，编译器会自动判断。
- 提供GI收集器
- 加强对非Java语言的调用支持（JSR-292,升级类加载架构)

## JDK8（LTS）

- Lambda表达式
- 函数式编程
- 接口可以添加默认方法和静态方法，也就是定义不需要实现类实现的方法
- 方法引用
- 重复注解，同一个注解可以使用多次
- 引入Optional来避免空指针
- 引入Streams相关的API
- 引入新的Date/Time相关的API
- 新增jdeps命令行，来分析类、目录、jar包的类依赖层级关系
- JVM使用MetaSpace代替了永久代（PermGen Space）

## JDK9

**重要：**

- 模块系统
- 交互式编程环境, REPL（JShell)
- HTTP 2 客户端 ,(jdk9 中引入httpClient api 代替原有的HttpURLConnection )
- 改进的 Javadoc
- 多版本兼容jar包
- 集合工厂方法
- 进程 API
- 轻量级的 JSON API
- String底层存储结构的变更（char数组变成byte字节数组）
- 下划线标识符命名的限制 _ 不允许了

**改进：**

- 改进，Stream API (在java8基础上，新增加4个方法)
- 改进， try-with-resources
- 改进，弃用注解 @Deprecated
- 改进，钻石操作符(Diamond Operator)
- 改进， Optional 类
- 改进，多分辨率图像 API
- 改进，CompletableFuture API
- 响应式流（Reactive Streams) API
- 改进, 私有接口方法（在接口中 jdk7 只能声明全名常量和抽象方法 jdk8 添加了静态方法和默认方法 jdk9添加了私有方法）

## JDK10

- 局部变量的类型推断
- GC改进和内存管理
- 线程本地握手
- 备用内存设备上的堆分配
- 其他Unicode语言 - 标记扩展
- 基于Java的实验性JIT编译器
- 开源根证书
- 根证书颁发认证（CA）
- 将JDK生态整合单个存储库
- 删除工具javah

## JDK11（LTS）

- 181 嵌套类可见性控制
- 309 动态文件常量
- 315 改进 Aarch64 Intrinsics
- 318 Epsilon–一个无操作的垃圾收集器
- 320 删除 Java EE 和 CORBA 模块
- 321 HttpClient
- 323 用于 Lambda 参数的局部变量语法
- 324 Curve25519 和 Curve448 算法的密钥协议
- 327 Unicode 10
- 328 Flight Recorder(飞行记录器)
- 329 haCha20 和 Poly1305 加密算法支持
- 330 Launch Single-File Source-Code Programs（启动单一文件的源代码程序）
- 331 低开销的 Heap Profiling
- 332 TLS 1.3支持
- 333 ZGC: A Scalable Low-Latency Garbage Collector（可伸缩低延迟垃圾收集器）
- 335 弃用 Nashorn JavaScript 引擎
- 336 弃用 Pack200 工具和 API

## JDK12

- 
- Switch Expressions
- Shenandoah GC

## JDK13

- switch优化更新
- 文本块升级
- 重新实现旧版套接字API
- 核心库/java.util中：I18N
- 取消使用未使用的内存

## JDK14

- switch优化变更为最终版
- 垃圾回收相关
- instanceof的模式匹配（预览版）
- 删除了安全库java.security.acl API
- 货币格式（优化）

## JDK15

- 隐藏类 hidden class
- 密封类 sealed class，通过sealed关键字修饰抽象类限定只允许指定的子类才可以实现或继承抽象类，避免抽象类被滥用

## JDK16

- ZGC性能优化
- instanceof模式匹配
- record的引入

## JDK17（LTS）

- 正式引入密封类sealed class，限制抽象类的实现
- 统一日志异步刷新，先将日志写入缓存，然后再异步刷新