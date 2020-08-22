## Spring

### Spring中BeanFactory 和 ApplicationContext的作用和区别

- 作用

  1. BeanFactory 负责读取bean配置文件,管理bean的加载,实例化bean,维护bean之间的依赖关系,负责bean的生命周期

  2. ApplicationContext 除了提供上述BeanFactory所能提供的功能之外,还提供完整的框架支持

     1. 国际化支持

        - ```java
          package com.csa.taml.common.tools;
          
          import org.springframework.beans.factory.annotation.Autowired;
          import org.springframework.context.MessageSource;
          import org.springframework.context.i18n.LocaleContextHolder;
          import org.springframework.stereotype.Component;
          
          import javax.annotation.PostConstruct;
          import java.util.Locale;
          
          /**
           * 国际化资源工具类
           * @create 2017/11/13 16:37
           */
          @Component
          public class MessageUtils {
          
              @Autowired
              private MessageSource messageSource;
          
              private static MessageSource msgSource;
          
              /**
               * @PostConstruct修饰的方法会在服务器加载Servle的时候运行，并且只会被服务器执行一次。
               * PostConstruct在构造函数之后执行,init()方法之前执行
               */
              @PostConstruct
              public void init() {
                  msgSource = messageSource;
              }
          
              /**
               * 根据key值获得对应的中英文
               * @param key 资源文件的KEY值
               * @return 对应的中英文字符串
               */
              public static String getMessage(String key){
                  return getMessage(key, null);
              }
          
              /**
               * 根据key值获得对应的中英文
               * @param key 资源文件的KEY值
               * @param parameter 参数
               * @return 对应的中英文字符串
               */
              public static String getMessage(String key, Object[] parameter){
                  Locale locale = LocaleContextHolder.getLocale();
                  if(locale.getLanguage().equals("zh")){
                      locale = Locale.CHINA;
                  } else {
                      locale = Locale.US;
                  }
                  return msgSource.getMessage(key, parameter, locale);
              }
          
              /**
               * 根据key值获得对应的中英文
               * @param key 资源文件的KEY值
               * @param parameter 参数
               * @param def 为空时的默认字符
               * @return 对应的中英文字符串
               */
              public static String getMessage(String key, Object[] parameter, String def){
                  Locale locale = LocaleContextHolder.getLocale();
                  if(locale.getLanguage().equals("zh")){
                      locale = Locale.CHINA;
                  } else {
                      locale = Locale.US;
                  }
                  String msg = msgSource.getMessage(key, parameter, locale);
                  if(msg == null || msg.length() < 0){
                      msg = def;
                  }
                  return msg;
              }
          
              /**
               * 根据key值获得对应的中英文
               * @param key 资源文件的KEY值
               * @param parameter 参数
               * @param def 为空时的默认字符
               * @return 对应的中英文字符串
               */
              public static String getMessage(String key, Object[] parameter, String def, Locale locale){
                  if(locale == null){
                      locale = LocaleContextHolder.getLocale();
                  }
                  if(locale.getLanguage().equals("zh")){
                      locale = Locale.CHINA;
                  } else {
                      locale = Locale.US;
                  }
                  String msg = msgSource.getMessage(key, parameter, locale);
                  if(msg == null || msg.length() < 0){
                      msg = def;
                  }
                  return msg;
              }
          
              /**
               * 根据key值获得对应的英文
               * @param key 资源文件的KEY值
               * @param parameter 参数
               * @return 对应的英文字符串
               */
              public static String getMessageEnglish(String key, Object[] parameter){
                  return msgSource.getMessage(key, parameter, Locale.US);
              }
          
              /**
               * 根据key值获得对应的中文
               * @param key 资源文件的KEY值
               * @param parameter 参数
               * @return 对应的中文字符串
               */
              public static String getMessageChinese(String key, Object[] parameter){
                  return msgSource.getMessage(key, parameter, Locale.CHINA);
              }
          
              /**
               * 根据key值获得对应的英文
               * @param key 资源文件的KEY值
               * @return 对应的英文字符串
               */
              public static String getMessageEnglish(String key){
                  return getMessageEnglish(key, null);
              }
          
              /**
               * 根据key值获得对应的中文
               * @param key 资源文件的KEY值
               * @return 对应的中文字符串
               */
              public static String getMessageChinese(String key){
                  return getMessageChinese(key, null);
              }
          
              /**
               *判断当前当前环境的语言是否是中文
               */
              public static boolean isChinese(){
                  Locale locale = LocaleContextHolder.getLocale();
                  if(locale.getLanguage().equals("zh")){
                      return true;
                  }
                  return false;
              }
          
              /**
               * 根据key 值获取对应填充参数的字符串
               * @param key
               * @param params
               * @return
               */
              public static String getFormatMessaget(String key, Object... params) {
                  return String.format(getMessage(key), params);
              }
          
          
          }
          
          ```

     2. 资源访问:Resource rs = ctx.getResource("classPath:config.properties")

     3. 事件传递:通过实现ApplicationContextAware接口



### Spring是如何解决循环依赖的

- Spring通过三级缓存解决了循环依赖,其中一级缓存为单例对象池==>singletonObjects,二级缓存为早期曝光对象earlySingletonObjects,三级缓存为早期曝光对象工厂===>singletonFactories
- 当A,B两个类发生循环依赖引用时,在A完成实例化后,就使用实例化后的对象去创建一个对象工厂,并添加到三级缓存中,如果A被AOP代理,那么通过这个工厂获取得到的就是A代理后的对象,如果A没有被AOP代理,难么这个工厂获取得到的就是A实例化的对象,当A进行属性注入时,会去创建B,同时B又去依赖A,所以创建B的同时又会去调用getBean(a)来获取需要的依赖,此时getBean(a)会从缓存中获取,第一步,先获取到三级缓存中的工厂;
- 第二步,调用对象工厂的getObject方法来获取得到对应的对象,得到对象后将其注入到B中.
- 紧接着B会走完他的生命周期流程,包括初始化,后置处理器等.当B创建完,再将B注入到A中,此时A再完成它的生命周期.最终,整个循环依赖结束

为什么使用三级缓存呢?二级缓存能解决循环依赖吗?

- 如果要使用二级缓存解决循环依赖,意味着所有Bean在实例后就要被AOP代理,这样句违背 Spring设计的原则,Spring在设计之初就是通过AnnotationAwareAspectJAutoProxyCreator这个后置处理器来在Bean生命周期的最后一步来完成AOP代理,而不是在实例化就立马进行AOP代理.

## SpringMVC

### 1. 什么是SpringMVC

   > SpringMVC是一个轻量级的Web框架,其将Model.View 和 Controller分离开来,将web层的职责进行解耦.

### 2. SpringMVC的执行流程

   ![SpringMVC执行流程图](http://47.114.45.144/springMvc.jpg)

   1. user发送请求到前端控制器DispatcherServlet;
   2. DispatcherServlet接受请求后,调用处理器映射器HandlerMapping,请求获取Handler;
   3. HandlerMapping根据url找到具体的Handler处理器,并返回给DispatcherServlet
   4. DispatcherServlet调用HandlerAdapter处理器适配器
   5. HandlerAdapter经过适配调用具体的处理器(Handler,也叫后端控制器)
   6. Handler执行完成并返回ModelAndView;
   7. HandlerAdapter将返回的ModelAndView返回给DispatcherServlet;
   8. DispatcherServlet调用ViewResolver视图解析器进行解析物理视图为逻辑视图
   9. ViewResolver将逻辑视图返回给DispatcherServlet
   10. DispatcherServlet 对View进行渲染视图
   11. DispatcherServlet最终响应回user

## SpringBoot

### 1. 什么是SpringBoot

   - > SpringBoot是Spring开源项目的一个子项目,是Spring组件一站式解决的方案,主要是简化Spring的配置,开发者更容易上手

### 2. SpringBoot的优点有哪些

   - 优点
     1. 开箱即用,简化配置
     2. 避免了maven导入依赖的冲突
     3. 提供了一些大型项目内嵌非服务性的功能:如内嵌Tomcat容器,安全管理,运行数据监控,运行状况检查和外部配置等

### 3. SpringBoot的核心注解

   - @SpringBootApplication==>也就是启动类上的注解,主要有以下几个注解复合而成
     - @SpringBootConfiguration:组合@Configuration注解,实现配置文件的功能
     - @EnableAutoConfiguration:打开自动配置功能,可以关闭自动配置数据源的配置
     - @Component:Spring组件扫描

## **Spring Boot、Spring MVC 和 Spring 有什么区别？**

## SpringCloud