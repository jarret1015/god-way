

<h1 align = "center">Spring Cloud Alibaba</h1>

当前最新稳定版本**2.2.7.RELEASE**，对应 Spring Cloud 版本 **Hoxton.SR12**，对应 Spring Boot 版本 **2.3.12.RELEASE**

[具体每个对应版本地址](https://github.com/alibaba/spring-cloud-alibaba/wiki/版本说明)

# 前言

Spring Cloud Alibaba 旨在为微服务开发提供一站式解决方案。该项目包括开发分布式应用程序和服务所需的组件，以便开发人员可以使用 Spring Cloud 编程模型轻松开发分布式应用程序。

使用Spring Cloud Alibaba，只需添加一些注解和配置，应用程序就可以使用阿里巴巴的分布式解决方案，并通过阿里巴巴中间件构建您自己的分布式系统。

[官方文档地址](https://github.com/alibaba/nacos/releases)

## 主要功能：

1. **服务限流降级**：基于<font color=red>Sentinel</font> ，对 Spring 体系内基本所有的客户端，网关进行了适配。支持WebServlet、WebFlux、OpenFeign、RestTemplate、Spring Cloud Gateway、Zuul、Dubbo 和 RocketMQ 接入限流降级功能。Sentinel应用比较简单，只需引入 starter，即可生效，可以在运行时通过控制台实时修改限流和降流规则，还支持对限流和降流Metrics的监控。
2. **服务注册和发现**：基于 <font color=red>Nacos</font> 提供 spring-cloud-alibaba-starter-nacos-discovery 实现了服务注册。依靠 @EnableDiscoveryClient 进行服务的注册，兼容 RestTemplate 和 OpenFeign 的客户端进行服务调用。
3. **分布式配置管理**：基于<font color=red>Nacos</font> 提供 spring-cloud-alibaba-starter-nacos-config 实现了配置管理功能，支持分布式系统的外部化配置，配置变化时自动刷新。
4. **Rpc Service**：扩展 Spring Cloud 客户端 RestTemplate 和 OpenFeign 以支持调用 <font color=red>Dubbo RPC</font> 服务。
5. **消息驱动能力**：基于 Spring Cloud Stream 为微服务应用构建消息驱动能力，提供 Binder 的新实现： <font color=red>Spring Cloud Stream RocketMQ </font> Binder，也新增了 Spring Cloud Bus 消息总线的新实现 Spring Cloud Bus RocketMQ。支持构建与共享消息系统连接的高度可扩展的事件驱动微服务。
6. **分布式事务**：基于 <font color=red>Seata </font>，解决微服务场景下面临的分布式事务问题。使用 @GlobalTransactional 注解，在微服务中传递事务上下文，可以对业务零侵入地解决分布式事务问题。
7. **阿里云对象存储**：基于<font color=red>Alibaba Cloud OSS</font>，海量、安全、低成本、高可靠的云存储服务。支持随时随地在任何应用程序中存储和访问任何类型的数据。
8. **阿里云任务调度**：基于<font color=red>Alibaba Cloud SchedulerX</font>，精准、高可靠、高可用的定时作业调度服务，响应时间秒级。
9. **阿里云短信服务**：基于<font color=red>Alibaba Cloud ACM</font>，覆盖全球的短信服务，阿里短信提供便捷、高效、智能的通信能力，帮助企业快速联系客户。

# 依赖管理

```java
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.2.7.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

# Nacos Config

## 快速开始

### 下载解压Nacos服务并启动

[下载地址](https://github.com/alibaba/nacos/releases)

**windows启动命令**  `startup.cmd -m standalone`

[Nacos地址](http://127.0.0.1:8848/nacos/)	默认账密都是 `nacos`

### 客户端使用方式

1. pom文件引入 Nacos Config Starter

   ```java
   <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    </dependency>
   ```

2. 添加bootstrap.properties 配置文件并配置 Nacos Config 元数据

   ```java
    spring.application.name=nacos-config-demo
    spring.cloud.nacos.config.server-addr=127.0.0.1:8848
   ```

   

3. 完成上述两步后，应用会从 Nacos Config 中获取相应的配置，并添加在 Spring Environment 的 PropertySources 中。这里我们使用 @Value 注解来将对应的配置注入到 ConfigController 的 name和 age 字段，并添加 @RefreshScope 打开动态刷新功能

```java
package site.jarret.nacosconfigdemo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/config")
@RefreshScope
public class ConfigController {

    @Value("${user.name}")
    private String name;

    @Value("${user.age}")
    private String age;

    /**
     * http://localhost:8080/config/get
     */
    @GetMapping("/get")
    public String get() {
        return "name :" + name + ", age: " + age;
    }
}
```

**DataId**	规则${`prefix`}-{`spring.profiles.active`}.${`file-extension`}

- `prefix` 默认为 `spring.application.name` 的值，也可以通过配置项 `spring.cloud.nacos.config.prefix`来配置。

- `spring.profiles.active` 即为当前环境对应的 profile，详情可以参考 [Spring Boot文档](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-profiles.html#boot-features-profiles)

  注意，当 active profile 为空时，对应的连接符 `-` 也将不存在，dataId 的拼接格式变成 `${prefix}`.`${file-extension}`

- `file-extension` 为配置内容的数据格式，可以通过配置项 `spring.cloud.nacos.config.file-extension`来配置。 目前只支持 `properties` 类型。

**group**	默认为 `DEFAULT_GROUP`，可以通过 `spring.cloud.nacos.config.group` 配置

**自动注入**

Nacos Config Starter 实现了 `org.springframework.cloud.bootstrap.config.PropertySourceLocator`接口，并将优先级设置成了最高。

在 Spring Cloud 应用启动阶段，会主动从 Nacos Server 端获取对应的数据，并将获取到的数据转换成 PropertySource 且注入到 Environment 的 PropertySources 属性中，所以使用 @Value 注解也能直接获取 Nacos Server 端配置的内容。

**动态刷新**

Nacos Config Starter 默认为所有获取数据成功的 Nacos 的配置项添加了监听功能，在监听到服务端配置发生变化时会实时触发 `org.springframework.cloud.context.refresh.ContextRefresher` 的 refresh 方法 。

如果需要对 Bean 进行动态刷新，请参照 Spring 和 Spring Cloud 规范。推荐给类添加 `@RefreshScope` 或 `@ConfigurationProperties` 注解

# Nacos Discovery

## Provider

1. 首先，修改 pom.xml 文件，引入 Nacos Discovery Starter。

   ```xml
   <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
   ```

2. 在应用的 /src/main/resources/application.properties 配置文件中配置 Nacos Server 地址

   ```properties
   spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
   ```

3. 使用 @EnableDiscoveryClient 注解开启服务注册与发现功能

   ```java
   @SpringBootApplication
   @EnableDiscoveryClient
   public class ProviderApplication {
   
   	public static void main(String[] args) {
   		SpringApplication.run(ProviderApplication.class, args);
   	}
   
   	@RestController
   	class EchoController {
   		@GetMapping(value = "/echo/{string}")
   		public String echo(@PathVariable String string) {
   				return string;
   		}
   	}
   }
   ```

   ### 应用启动

   1. 增加配置，在 nacos-discovery-provider-example 项目的 /src/main/resources/application.properties 中添加基本配置信息

      ```properties
      spring.application.name=service-provider
      server.port=18082
      ```

   1. 启动应用

## Consumer

#### 使用 RestTemplate 和 FeignClient

1. 添加 @LoadBlanced 注解，使得 RestTemplate 接入 Ribbon

   ```java
   @Bean
   @LoadBalanced
   public RestTemplate restTemplate() {
       return new RestTemplate();
   }
   ```

2. FeignClient 已经默认集成了 Ribbon ，此处演示如何配置一个 FeignClient。

   ```java
   @FeignClient(name = "service-provider")
   public interface EchoService {
       @GetMapping(value = "/echo/{str}")
       String echo(@PathVariable("str") String str);
   }
   ```

   使用 @FeignClient 注解将 EchoService 这个接口包装成一个 FeignClient，属性 name 对应服务名 service-provider。

   echo 方法上的 @RequestMapping 注解将 echo 方法与 URL "/echo/{str}" 相对应，@PathVariable 注解将 URL 路径中的 `{str}` 对应成 echo 方法的参数 str。

3. 完成以上配置后，将两者自动注入到 TestController 中。

   ```java
   @RestController
   public class TestController {
   
       @Autowired
       private RestTemplate restTemplate;
       @Autowired
       private EchoService echoService;
   
       @GetMapping(value = "/echo-rest/{str}")
       public String rest(@PathVariable String str) {
           return restTemplate.getForObject("http://service-provider/echo/" + str, String.class);
       }
       @GetMapping(value = "/echo-feign/{str}")
       public String feign(@PathVariable String str) {
           return echoService.echo(str);
       }
   }
   ```

4. 配置必要的配置，在 nacos-discovery-consumer-example 项目的 /src/main/resources/application.properties 中添加基本配置信息

   ```properties
   spring.application.name=service-consumer
   server.port=18083
   ```

5. 启动应用

# Sentinel

1. 引入 Sentinel starter。

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

1. 接入限流埋点

   - HTTP 埋点

     Sentinel starter 默认为所有的 HTTP 服务提供了限流埋点，如果只想对 HTTP 服务进行限流，那么只需要引入依赖，无需修改代码。

   - 自定义埋点

     如果需要对某个特定的方法进行限流或降级，可以通过 `@SentinelResource` 注解来完成限流的埋点，示例代码如下：

     ```java
     @SentinelResource(value = "resource", blockHandler = "handleException", blockHandlerClass = ExceptionUtil.class )
     public String hello() {
         return "Hello";
     }
     ```

     当然也可以通过原始的 `SphU.entry(xxx)` 方法进行埋点，可以参见 [Sentinel 文档]([https://github.com/alibaba/Sentinel/wiki/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8#%E5%AE%9A%E4%B9%89%E8%B5%84%E6%BA%90)。](https://github.com/alibaba/Sentinel/wiki/如何使用#定义资源)。)

2. 配置限流规则

   1. Sentinel 提供了两种配置限流规则的方式：代码配置 和 控制台配置。本示例使用的方式为通过控制台配置。

      1. 通过代码来实现限流规则的配置。一个简单的限流规则配置示例代码如下，更多限流规则配置详情请参考 [Sentinel 文档]([https://github.com/alibaba/Sentinel/wiki/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8#%E5%AE%9A%E4%B9%89%E8%A7%84%E5%88%99)。](https://github.com/alibaba/Sentinel/wiki/如何使用#定义规则)。)

      ```java
      List<FlowRule> rules = new ArrayList<FlowRule>();
      FlowRule rule = new FlowRule();
      rule.setResource(str);
      // set limit qps to 10
      rule.setCount(10);
      rule.setGrade(RuleConstant.FLOW_GRADE_QPS);
      rule.setLimitApp("default");
      rules.add(rule);
      FlowRuleManager.loadRules(rules);
      ```

      1. 通过控制台进行限流规则配置请参考文章后面的图文说明。

3. 降级

   熔断后降级异常处理

   ```java
   public final class ExceptionUtil {
   
      private ExceptionUtil() {
   
      }
   
      public static SentinelClientHttpResponse handleException(HttpRequest request,
            byte[] body, ClientHttpRequestExecution execution, BlockException ex) {
         System.out.println("Oops: " + ex.getClass().getCanonicalName());
         return new SentinelClientHttpResponse("custom block info");
      }
   
   }
   ```

   ### 启动 Sentinel 控制台

1. 首先需要获取 Sentinel 控制台，支持直接下载和源码构建两种方式。
   1. 直接下载：[下载 Sentinel 控制台](http://edas-public.oss-cn-hangzhou.aliyuncs.com/install_package/demo/sentinel-dashboard.jar)
   2. 源码构建：进入 Sentinel [Github 项目页面](https://github.com/alibaba/Sentinel)，将代码 git clone 到本地自行编译打包，[参考此文档](https://github.com/alibaba/Sentinel/tree/master/sentinel-dashboard)。
2. 启动控制台，执行 Java 命令 `java -jar sentinel-dashboard.jar`完成 Sentinel 控制台的启动。 控制台默认的监听端口为 8080。Sentinel 控制台使用 Spring Boot 编程模型开发，如果需要指定其他端口，请使用 Spring Boot 容器配置的标准方式，详情请参考 [Spring Boot 文档](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-customizing-embedded-containers)。

### 应用启动

1. 增加配置，在应用的 /src/main/resources/application.properties 中添加基本配置信息

   ```properties
   spring.application.name=sentinel-example
   server.port=18085
   spring.cloud.sentinel.transport.dashboard=localhost:8080
   ```

2. 启动应用

### 调用服务

# 问题

1. 各版本冲突，必须兼容版本

2. nacos默认集群方式启动，修改配置为单机启动 set MODE="standalone"

3. bootstrap文件不生效，Spring Boot 2.4以后，配置文件的加载方式进行了重构，从官网看，2个关键点

   1、加一个依赖 spring-cloud-starter-bootstrap

   2、加一个配置 spring.cloud.config.uri



