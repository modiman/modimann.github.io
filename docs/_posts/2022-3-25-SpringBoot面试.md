# 概述

## 背景知识

### 什么是springboot ？

用来简化spring应用的初始搭建以及开发过程 使用特定的方式来进行配置（properties或yml文件）

创建独立的spring引用程序 main方法运行 

嵌入的Tomcat 无需部署war文件 

简化maven配置 

自动配置spring添加对应功能starter自动化配置 

> spring boot来简化spring应用开发，约定大于配置，去繁从简，just run就能创建一个独立的，产品级别的应用



### Springboot 有哪些优点？

- 减少开发，测试时间和努力。
- 使用JavaConfig有助于避免使用XML。
- 避免大量的Maven导入和各种版本冲突。
- 提供意见发展方法。
- 通过提供默认值快速开始开发。
- 没有单独的Web服务器需要。这意味着你不再需要启动Tomcat，Glassfish或其他任何东西。
- 需要更少的配置 因为没有web.xml文件。只需添加用@ Configuration注释的类，然后添加用@Bean注释的方法，Spring将自动加载对象并像以前一样对其进行管理。您甚至可以将@Autowired添加到bean方法中，以使Spring自动装入需要的依赖关系中。
- 基于环境的配置 使用这些属性，您可以将您正在使用的环境传递到应用程序：-Dspring.profiles.active = {enviornment}。在加载主应用程序属性文件后，Spring将在（application{environment} .properties）中加载后续的应用程序属性文件。

### 怎么理解 Spring Boot 中 “约定优于配置“

Spring Boot Starter、Spring Boot Jpa 都是“约定优于配置“的一种体现。都是通过“约定优于配置“的设计思路来设计的，Spring Boot Starter 在启动的过程中会根据约定的信息对资源进行初始化；Spring Boot Jpa 通过约定的方式来自动生成 Sql ，避免大量无效代码编写。



## 常用注解

* [Springboot全套注解解析《看不懂算我输》 ](https://www.nowcoder.com/discuss/1023533?type=2&channel=-1&source_id=discuss_terminal_discuss_hot_nctrack)

### 启动相关

启动类上面的注解是@SpringBootApplication，它也是 Spring Boot 的核心注解，主要组合包含了以下 3 个注解：

1. **@SpringBootConfiguration：** 组合了 @Configuration 注解，实现配置文件的功能。
2. **@EnableAutoConfiguration：** 打开自动配置的功能，也可以关闭某个自动配置的选项，如关闭数据源自动配置功能：如：当前类路径下有 Mybatis 这个 JAR 包，MybatisAutoConfiguration 注解就能根据相关参数来配置 Mybatis 的各个 Spring Bean。
3. **@SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })。**

#### @ComponentScan

这是 Spring 3.1 添加的一个注解，用来代替配置文件中的 component-scan 配置，开启组件扫描，即自动扫描包路径下的 @Component 注解进行注册 bean 实例到 context 中

### Bean相关

* @Autowired  @Autowired注解是按类型装配依赖对象，默认情况下它要求依赖对象必须存在，如果允许null值，可以设置它required属性为false(默认：true)。****

  * 注意事项： 
  *  在使用@Autowired时，首先在容器中查询对应类型的bean 
  *  如果查询结果刚好为一个，就将该bean装配给@Autowired指定的[数据]() 
  *  如果查询的结果不止一个，那么@Autowired会根据名称来查找。 
  *  如果查询的结果为空，那么会抛出异常。解决方法时，使用required=false

* **@Resource** 

     @Resource注解和@Autowired一样，也可以标注在字段或属性的setter方法上，  

     但它默认按名称装配。名称可以通过@Resource的name属性指定，  

     如果没有指定name属性，当注解标注在字段上，即默认取字段的名称作为bean名称寻找依赖对象，当注解标注在属性的setter方法上，即默认取属性名作为bean名称寻找依赖对象。

作者：码出宇宙
链接：https://www.nowcoder.com/discuss/1023533?type=2&channel=-1&source_id=discuss_terminal_discuss_hot_nctrack
来源：牛客网



#### @Component,@Repository,@Service,@Controller 

 我们一般使用 @Autowired 注解让 Spring 容器帮我们自动装配 bean。要想把类标识成可用于 @Autowired 注解自动装配的 bean 的类,可以采用以下注解实现： 

1. [@Component]() ：通用的注解，可标注任意类为 Spring 组件。如果一个 Bean 不知道属于哪个层，可以使用[@Component]() 注解标注
2. @Repository : 对应持久层即 Dao 层，主要用于[数据]()库相关操作。 
3. @Service : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。 
4. @Controller : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回[数据]()给前端页面。

### @Scope

声明 Spring Bean 的作用域，使用方法:

```java
@Bean
@Scope("singleton")
public Person personSingleton() {
    return new Person();
} 
```

### @Bean 

   Spring的@Bean注解用于告诉方法，产生一个Bean对象，然后这个Bean对象交给Spring管理。  

   产生这个Bean对象的方法Spring只会调用一次，随后这个Spring将会将这个Bean对象放在自己的IOC容器中。

```java
@Configuration public class AppConfig {


    // 使用@Bean 注解表明myBean需要交给Spring进行管理
    // 未指定bean 的名称，默认采用的是 "方法名" + "首字母小写"的配置方式
    @Bean
    public MyBean myBean(){
        return new MyBean();
    }
}
```





### Spring Boot初始化环境变量流程?

**1、** 调用`prepareEnvironment`方法去设置环境变量 

**2、** 接下来有三个方法`getOrCreateEnvironment`，`configureEnvironment`，`environmentPrepared` 

**3、** `getOrCreateEnvironment`去初始化系统环境变量 

**4、** `configureEnvironment`去初始化命令行参数 

**5、** `environmentPrepared`当广播到来的时候调用`onApplicationEnvironmentPreparedEvent`方法去使用`postProcessEnvironment`方法`load yml`和`properties变量`



### Spring Boot扫描流程?

**1、** 调用run方法中的`refreshContext`方法 

**2、** 用AbstractApplicationContext中的`refresh`方法 

**3、** 委托给`invokeBeanFactoryPostProcessors`去处理调用链 

**4、** 其中一个方法`postProcessBeanDefinitionRegistry会`去调用`processConfigBeanDefinitions`解析`beandefinitions` 

**5、** 在`processConfigBeanDefinitions`中有一个`parse`方法，其中有`componentScanParser.parse`的方法，这个方法会扫描当前路径下所有`Component`组件







# 配置

### 配置加载顺序

使用 Spring Boot 会涉及到各种各样的配置，如开发、测试、线上就至少 3 套配置信息了。Spring Boot 可以轻松的帮助我们使用相同的代码就能使开发、测试、线上环境使用不同的配置。

**在 Spring Boot 里面，可以使用以下几种方式来加载配置。本章内容基于 Spring Boot 2.0 进行详解。**

1、properties文件；

2、YAML文件；

3、系统环境变量；

4、命令行参数；

等等……

### 不同环境配置

提供多套配置文件，如：

```xml
applcation.properties

application-dev.properties

application-test.properties

application-prod.properties
```

然后在applcation.properties文件中指定当前的环境spring.profiles.active=test,这时候读取的就是application-test.properties文件。



### 读取配置

Spring Boot 可以通过 

- @PropertySource
- @Value
- @Environment,
- @ConfigurationProperties 

来绑定变量

### 核心配置文件

Spring Boot 的核心配置文件是 application 和 bootstrap 配置文件。

application 配置文件这个容易理解，主要用于 Spring Boot 项目的自动化配置。

bootstrap 配置文件有以下几个应用场景。

- 使用 Spring Cloud Config 配置中心时，这时需要在 bootstrap 配置文件中添加连接到配置中心的配置属性来加载外部配置中心的配置信息；
- 一些固定的不能被覆盖的属性；
- 一些加密/解密的场景；



单纯做 Spring Boot 开发，可能不太容易遇到 bootstrap.properties 配置文件，但是在结合 Spring Cloud 时，这个配置就会经常遇到了，特别是在需要加载一些远程配置文件的时侯。

**spring boot 核心的两个配置文件：**

- **bootstrap (. yml 或者 . properties)：** boostrap 由父 ApplicationContext 加载的，比 applicaton 优先加载，配置在应用程序上下文的引导阶段生效。一般来说我们在 Spring Cloud Config 或者 Nacos 中会用到它。且 boostrap 里面的属性不能被覆盖；
- **application (. yml 或者 . properties)：** 由ApplicatonContext 加载，用于 spring boot 项目的自动化配置。



# 启动

**三个核心注解以及一个run方法**

## 启动过程分析

常见启动代码

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        ConfigurableApplicationContext context =

                SpringApplication.run(DemoApplication.class, args);

        JdbcTemplate jdbcTemplate = context.getBean(JdbcTemplate.class);


    }
}
```

### 注解（@SpringBootApplication）



@SpringBootApplication注解由三个注解组合而成，分别是：

- @ComponentScan
- @EnableAutoConfiguration
- @SpringBootConfiguration

#### **@ComponentScan**

这个注解的作用是**告诉Spring扫描哪个包下面类，加载符合条件的组件**(比如贴有@Component和@Repository等的类)或者bean的定义。

所以有一个basePackages的属性，如果默认不写，则从声明@ComponentScan所在类的package进行扫描。

所以**启动类最好定义在Root package下**，因为一般我们在使用@SpringBootApplication时，都不指定basePackages的。

#### **@EnableAutoConfiguration**

这是一个复合注解，看起来很多注解，实际上关键在@Import注解，它会加载AutoConfigurationImportSelector类，然后就会触发这个类的selectImports()方法。根据返回的String数组(配置类的Class的名称)加载配置类。

```java
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware,
ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {
    //返回的String[]数组，是配置类Class的类名
    @Override
    public String[] selectImports(AnnotationMetadata annotationMetadata) {
        if (!isEnabled(annotationMetadata)) {
            return NO_IMPORTS;
        }
        AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(annotationMetadata);
        //返回配置类的类名
        return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
    }
}
```

我们一直点下去，就可以找到最后的幕后英雄，就是SpringFactoriesLoader类，通过loadSpringFactories()方法加载META-INF/[spring.factories](https://www.zhihu.com/search?q=spring.factories&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"295451397"})中的配置类。

这里使用了spring.factories文件的方式加载配置类，提供了很好的扩展性。

所以@EnableAutoConfiguration注解的作用其实就是开启自动配置，自动配置主要则依靠这种加载方式来实现。

#### **@SpringBootConfiguration**

* @SpringBootConfiguration继承自@Configuration，二者功能也一致，
* 标注当前类是配置类， 
* 将当前类内声明的一个或多个以@Bean注解标记的方法的实例纳入到[spring容器](https://www.zhihu.com/search?q=spring容器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"295451397"})中，并且实例名就是方法名。

这个注解用来取代`xml`形式的Bean注入

**示例**

```java
@Configuration
public class MyConfig {

    @Bean("user")
    public User getUser(){
    	System.out.println("User对象进行创建!");
        return new User("用户", 22, getDog());
    }

    @Bean("dog")
    public Dog getDog(){
    	System.out.println("Dog对象进行创建!");
        return new Dog("金毛", 3);
    }
}
```

**与之对应的xml**

```xml
<bean id="金毛" class = "com.XXX.Dog">
    
</bean>
```



#### **小结**

我们在这里画张图把@SpringBootApplication注解包含的三个注解分别解释一下。

![img](https://pic4.zhimg.com/v2-1479199baf2da312d04c817bf955e31b_b.jpg)



## Starters

starter是springboot自动配置的重要基础

starter出现之前，Spring想要引入一个模块需要

1. 到maven仓库去找需要引入的mybatis jar包，选取合适的版本。
2. 到maven仓库去找mybatis-spring整合的jar包，选取合适的版本。
3. 在spring的applicationContext.xml文件中配置dataSource和mybatis相关信息。

有了starter就只需要在pom文件中添加对应的依赖就可以完成项目的引入

### spring-boot-starter-parent 有什么用 ?

我们都知道，新创建一个 Spring Boot 项目，默认都是有 parent 的，这个 parent 就是 spring-boot-starter-parent ，spring-boot-starter-parent 主要有如下作用：

定义了 Java 编译版本为 1.8 。
使用 UTF-8 格式编码。
继承自 spring-boot-dependencies，这个里边定义了依赖的版本，也正是因为继承了这个依赖，所以我们在写依赖时才不需要写版本号。
执行打包操作的配置。
自动化的资源过滤。
自动化的插件配置。
针对 application.properties 和 application.yml 的资源过滤，包括通过 profile 定义的不同环境的配置文件，例如 application-dev.properties 和 application-dev.yml。



### SpringBoot常用的starter有哪些?

**1、** `spring-boot-starter-web` (嵌入tomcat和web开发需要servlet与jsp支持) 

**2、** `spring-boot-starter-data-jpa` (数据库支持) 

**3、** `spring-boot-starter-data-redis` (redis数据库支持) 

**4、** `spring-boot-starter-data-solr` (solr搜索应用框架支持) 

**5、** `mybatis-spring-boot-starter` (第三方的mybatis集成starter)



# 部署

### 热部署

这可以使用 DEV 工具来实现。

通过这种依赖关系，您可以节省任何更改，嵌入式tomcat 将重新启动。

Spring Boot 有一个开发工具（DevTools）模块，它有助于提高开发人员的生产力。

Java 开发人员面临的一个主要挑战是将文件更改自动部署到服务器并自动重启服务器。开发人员可以重新加载 Spring Boot 上的更改，而无需重新启动服务器。这将消除每次手动部署更改的需要。Spring Boot 在发布它的第一个版本时没有这个功能。

这是开发人员最需要的功能。DevTools 模块完全满足开发人员的需求。该模块将在生产环境中被禁用。它还提供 H2 数据库控制台以更好地测试应用程序。



### Spring Boot 打成的 jar 和普通的 jar 有什么区别 ?

Spring Boot 项目最终打包成的 jar 是可执行 jar ，这种 jar 可以直接通过 java -jar xxx.jar 命令来运行，这种 jar 不可以作为普通的 jar 被其他项目依赖，即使依赖了也无法使用其中的类。

Spring Boot 的 jar 无法被其他项目依赖，主要还是他和普通 jar 的结构不同。普通的 jar 包，解压后直接就是包名，包里就是我们的代码，而 Spring Boot 打包成的可执行 jar 解压后，在 \BOOT-INF\classes 目录下才是我们的代码，因此无法被直接引用。如果非要引用，可以在 pom.xml 文件中增加配置，将 Spring Boot 项目打包成两个 jar ，一个可执行，一个可引用。





### Spring Boot 中如何实现定时任务 ?

定时任务也是一个常见的需求，Spring Boot 中对于定时任务的支持主要还是来自 Spring 框架。

在 Spring Boot 中使用定时任务主要有两种不同的方式，一个就是使用 Spring 中的 @Scheduled 注解，另一个则是使用第三方框架 Quartz。

使用 Spring 中的 @Scheduled 的方式主要通过 @Scheduled 注解来实现。

使用 Quartz ，则按照 Quartz 的方式，定义 Job 和 Trigger 即可。







### 跨域问题 

跨域可以在前端通过 JSONP 来解决，但是 JSONP 只可以发送 GET 请求，无法发送其他类型的请求，在 RESTful 风格的应用中，就显得非常鸡肋，因此我们推荐在后端通过 （CORS，Cross-origin resource sharing） 来解决跨域问题。这种解决方案并非 Spring Boot 特有的，在传统的 SSM 框架中，就可以通过 CORS 来解决跨域问题，只不过之前我们是在 XML 文件中配置 CORS ，现在可以通过实现WebMvcConfigurer接口然后重写addCorsMappings方法解决跨域问题。

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("*")
                .allowCredentials(true)
                .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                .maxAge(3600);
    }

}

```

项目中前后端分离部署，所以需要解决跨域的问题。
我们使用cookie存放用户登录的信息，在spring拦截器进行权限控制，当权限不符合时，直接返回给用户固定的json结果。
当用户登录以后，正常使用；当用户退出登录状态时或者token过期时，由于拦截器和跨域的顺序有问题，出现了跨域的现象。
我们知道一个http请求，先走filter，到达servlet后才进行拦截器的处理，如果我们把cors放在filter里，就可以优先于权限拦截器执行。

```java
@Configuration
public class CorsConfig {

    @Bean
    public CorsFilter corsFilter() {
        CorsConfiguration corsConfiguration = new CorsConfiguration();
        corsConfiguration.addAllowedOrigin("*");
        corsConfiguration.addAllowedHeader("*");
        corsConfiguration.addAllowedMethod("*");
        corsConfiguration.setAllowCredentials(true);
        UrlBasedCorsConfigurationSource urlBasedCorsConfigurationSource = new UrlBasedCorsConfigurationSource();
        urlBasedCorsConfigurationSource.registerCorsConfiguration("/**", corsConfiguration);
        return new CorsFilter(urlBasedCorsConfigurationSource);
    }

}

```





###  CSRF 攻击

CSRF 代表跨站请求伪造。这是一种攻击，迫使最终用户在当前通过身份验证的Web 应用程序上执行不需要的操作。CSRF 攻击专门针对状态改变请求，而不是数据窃取，因为攻击者无法查看对伪造请求的响应。






### 嵌入式服务器

思考一下在你的虚拟机上部署应用程序需要些什么。

**1、**安装 Java

**2、**安装 Web 或者是应用程序的服务器（Tomat/Wbesphere/Weblogic 等等）

**3、**部署应用程序 war 包

如果我们想简化这些步骤，应该如何做呢？

让我们来思考如何使服务器成为应用程序的一部分？

你只需要一个安装了 Java 的虚拟机，就可以直接在上面部署应用程序了，

这个想法是嵌入式服务器的起源。

当我们创建一个可以部署的应用程序的时候，我们将会把服务器（例如，tomcat）嵌入到可部署的服务器中。

例如，对于一个 Spring Boot 应用程序来说，你可以生成一个包含 Embedded Tomcat 的应用程序 jar。你就可以想运行正常 Java 应用程序一样来运行 web 应用程序了。

嵌入式服务器就是我们的可执行单元包含服务器的二进制文件（例如，tomcat.jar）。



### 当 Spring Boot 应用程序作为 Java 应用程序运行时，后台会发生什么？

如果你使用 Eclipse IDE，Eclipse maven 插件确保依赖项或者类文件的改变一经添加，就会被编译并在目标文件中准备好！在这之后，就和其它的 Java 应用程序一样了。

当你启动 java 应用程序的时候，spring boot 自动配置文件就会魔法般的启用了。

当 Spring Boot 应用程序检测到你正在开发一个 web 应用程序的时候，它就会启动 tomcat。



### RequestMapping 和 GetMapping 的不同之处在哪里？

RequestMapping 具有类属性的，可以进行 GET,POST,PUT 或者其它的注释中具有的请求方法。GetMapping 是 GET 请求方法中的一个特例。它只是 ResquestMapping 的一个延伸，目的是为了提高清晰度。

# 源码阅读

启动流程

一、SpringBoot启动的时候，会构造一个SpringApplication的实例，构造SpringApplication的时候会进行初始化的工作，初始化的时候会做以下几件事：

1、把参数sources设置到SpringApplication属性中，这个sources可以是任何类型的参数.
2、判断是否是web程序，并设置到webEnvironment的boolean属性中.
3、创建并初始化ApplicationInitializer，设置到initializers属性中 。
4、创建并初始化ApplicationListener，设置到listeners属性中 。
5、初始化主类mainApplicatioClass。


二、SpringApplication构造完成之后调用run方法，启动SpringApplication，run方法执行的时候会做以下几件事：

1、构造一个StopWatch计时器，用来记录SpringBoot的启动时间 。
2、初始化监听器，获取SpringApplicationRunListeners并启动监听，用于监听run方法的执行。
3、创建并初始化ApplicationArguments,获取run方法传递的args参数。
4、创建并初始化ConfigurableEnvironment（环境配置）。封装main方法的参数，初始化参数，写入到 Environment中，发布 ApplicationEnvironmentPreparedEvent（环境事件），做一些绑定后返回Environment。
5、打印banner和版本。
6、构造Spring容器(ApplicationContext)上下文。先填充Environment环境和设置的参数，如果application有设置beanNameGenerator（bean）、resourceLoader（加载器）就将其注入到上下文中。调用初始化的切面，发布ApplicationContextInitializedEvent（上下文初始化）事件。
7、SpringApplicationRunListeners发布finish事件。
8、StopWatch计时器停止计时，日志打印总共启动的时间。
9、发布SpringBoot程序已启动事件(started())
10、调用ApplicationRunner和CommandLineRunner
11、最后发布就绪事件ApplicationReadyEvent，标志着SpringBoot可以处理就收的请求了(running())

springboot启动类

```java
@SpringBootApplication
@MapperScan("com.example.seckilldemo")
@EnableSwagger2
public class SeckillDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(SeckillDemoApplication.class, args);
    }

}
```

**重要概念**：

* @SpringBootApplication注解
* SpringApplication.run方法

## SpringApplication.run

首先可以看出run是一个SpringApplication类下的一个静态方法

点开SpringApplication，找到run方法

```java
  public static ConfigurableApplicationContext run(Class<?> primarySource, String... args) {
        return run(new Class[]{primarySource}, args);
    }
```

看到这是一个返回值类型为`ConfigurableApplicationContext`的静态的方法，同时调用了另一个重载方法`run`

继续定位

```java
public static ConfigurableApplicationContext run(Class<?>[] primarySources, String[] args) {
        return (new SpringApplication(primarySources)).run(args);
    }
```

发现返回值没有变，形参从`Class<?> primarySource`变成了数组`Class<?>[] primarySources`,同时又调用了`run`的第三个重载方法。



**需要注意的是，这次在返回时new了一个新的SpringApplication对象，然后调用了run方法**

继续往下定位之前，先看一下这一步都干了什么，定位到`SpringApplication`的含参构造方法

**SpringApplication中的成员参数决定了SpringBoot的配置属性，启动使用默认值。想要修改可以在main方法中自己实例化一个SpringApplication对象并通过set方法修改这些成员变量的值**

```java
public SpringApplication(Class<?>... primarySources) {
        this((ResourceLoader)null, primarySources);
    }

    public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
        this.sources = new LinkedHashSet();
        this.bannerMode = Mode.CONSOLE;
        this.logStartupInfo = true;
        this.addCommandLineProperties = true;
        this.addConversionService = true;
        this.headless = true;
        this.registerShutdownHook = true;
        this.additionalProfiles = Collections.emptySet();
        this.isCustomEnvironment = false;
        this.lazyInitialization = false;
        this.applicationContextFactory = ApplicationContextFactory.DEFAULT;
        this.applicationStartup = ApplicationStartup.DEFAULT;
        this.resourceLoader = resourceLoader;
        Assert.notNull(primarySources, "PrimarySources must not be null");
        //用一个linkedhashset存放主类的class对象
        this.primarySources = new LinkedHashSet(Arrays.asList(primarySources));
        
        this.webApplicationType = WebApplicationType.deduceFromClasspath();
        this.bootstrapRegistryInitializers = new ArrayList(this.getSpringFactoriesInstances(BootstrapRegistryInitializer.class));
        this.setInitializers(this.getSpringFactoriesInstances(ApplicationContextInitializer.class));
        this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class));
        this.mainApplicationClass = this.deduceMainApplicationClass();
    }
```



再次定位，这次找到了业务逻辑所在的`run`方法，逐句分析

```java
/**
	 * 运行 Spring 应用
	 * 创建并刷新一个新的 ApplicationContext
	 * 参数是来自main方法的args
	 * 返回一个运行中的 ApplicationContext
	 */

public ConfigurableApplicationContext run(String... args) {
        //记录运行开始时间
    	long startTime = System.nanoTime();
    	//获取到所有之前的 bootstrapRegistryInitializers 
        //挨个执行 initialize() 来完成对引导启动器上下文环境设置
        DefaultBootstrapContext bootstrapContext = this.createBootstrapContext();
    
    
    
        ConfigurableApplicationContext context = null;//返回值初始化
    	//设置是否运行于无显示设备、驱动的服务器端环境中
        this.configureHeadlessProperty();
    
        SpringApplicationRunListeners listeners = this.getRunListeners(args);
        listeners.starting(bootstrapContext, this.mainApplicationClass);

        try {
            ApplicationArguments applicationArguments = new DefaultApplicationArguments(args);
            ConfigurableEnvironment environment = this.prepareEnvironment(listeners, bootstrapContext, applicationArguments);
            this.configureIgnoreBeanInfo(environment);
            Banner printedBanner = this.printBanner(environment);
            context = this.createApplicationContext();
            context.setApplicationStartup(this.applicationStartup);
            this.prepareContext(bootstrapContext, context, environment, listeners, applicationArguments, printedBanner);
            this.refreshContext(context);
            this.afterRefresh(context, applicationArguments);
            
            //打印程序启动时间
            Duration timeTakenToStartup = Duration.ofNanos(System.nanoTime() - startTime);
            if (this.logStartupInfo) {
                (new StartupInfoLogger(this.mainApplicationClass)).logStarted(this.getApplicationLog(), timeTakenToStartup);
            }

            listeners.started(context, timeTakenToStartup);
            this.callRunners(context, applicationArguments);
        } catch (Throwable var12) {
            this.handleRunFailure(context, var12, listeners);
            throw new IllegalStateException(var12);
        }

        try {
            Duration timeTakenToReady = Duration.ofNanos(System.nanoTime() - startTime);
            listeners.ready(context, timeTakenToReady);
            return context;
        } catch (Throwable var11) {
            this.handleRunFailure(context, var11, (SpringApplicationRunListeners)null);
            throw new IllegalStateException(var11);
        }
    }

```

