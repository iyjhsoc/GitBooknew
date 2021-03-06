## 2.5 Bean范围

**Bean线程范围**

| Scope | Description |
| :--- | :--- |
| singleton | （默认）将每个Spring IoC容器的单个bean定义范围限定为单个对象实例。 |
| prototype | 将单个bean定义范围限定为任意数量的对象实例。 |
| request | 将单个bean定义范围限定为单个HTTP请求的生命周期; 也就是说，每个HTTP请求都有自己的bean实例，它是在单个bean定义的后面创建的。仅在具有Web感知功能的Spring环境中有效ApplicationContext。 |
| session | 将单个bean定义范围限定为HTTP的生命周期Session。仅在具有Web感知功能的Spring环境中有效ApplicationContext。 |
| global session | 将单个bean定义范围限定为全局HTTP的生命周期Session。通常仅在portlet上下文中使用时有效。仅在具有Web感知功能的Spring环境中有效ApplicationContext。 |

### 2.5.1 单例范围

使用场景：对无状态bean使用单例范围

效果：当您定义bean并将其作为单一作用域时，Spring IoC容器只创建该bean定义的对象的一个实例。此单个实例存储在此类单例bean的缓存中，并且该命名Bean的所有后续请求和引用都将返回缓存对象。

范围：它将在每个容器中实例化一次

**Figure 2.5.1**

![](/assets/2_5_1.PNG)

```
<bean id="accountService" class="com.foo.DefaultAccountService"/>

<!-- the following is equivalent, though redundant (singleton scope is the default) -->
<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>
```

### 2.5.2 原型Bean

使用场景：对所有状态的bean使用原型范围，对无状态bean使用单例范围。

效果：数据访问对象（DAO）通常不配置为原型，因为典型的DAO不保持任何会话状态; 这个作者更容易重用单例图的核心。

性能：Spring不管理原型bean的完整生命周期：容器实例化，配置和组装原型对象，并将其交给客户端，而不再记录该原型实例。

**Figure2.5.2**

![](/images/2_5_2.PNG)

### 2.5.3具有原型bean依赖关系的单例bean

### 2.5.4 Web范围的Bean:request, session, and global session范围

只有使用Web感知的Spring Application 才可以感知到上面三个范围

访问方式：

```
   Spring MVC 中访问：处理请求的SpringDispatcherServvlet和DispatcherPortlet处理

  servlet2.4+Web容器：

   对于servlet 3.0+，这可以通过WebApplicationInitializer接口以编程方式完成。或者，对于较旧的容器，将以下声明添加到Web应用程序web.xml文件中：
```

```XML
<web-app>
    ...
    <listener>
        <listener-class>
            org.springframework.web.context.request.RequestContextListener
        </listener-class>
    </listener>
    ...
</web-app>
```

```
servlet2.3
```

用提供的javax.servlet.Filter 实现

```
<web-app>
    ...
    <filter>
        <filter-name>requestContextFilter</filter-name>
        <filter-class>org.springframework.web.filter.RequestContextFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>requestContextFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    ...
</web-app>
```

#### Request Scope

范围：请求开始到请求结束

```
<bean id="loginAction" class="com.foo.LoginAction" scope="request"/>
```

#### session Scope

范围：Spring容器UserPreferences通过在userPreferences单个HTTP的生存期内使用bean定义来创建bean 的新实例Session。

```
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session"/>
```

#### Global session Scope

范围：global session 范围是类似于标准HTTP`Session`范围，并且只适用于基于portlet的web应用程序的上下文。

```
<bean id="userPreferences" class="com.foo.UserPreferences" scope="globalSession"/>
```

#### 依赖关系Scoped beans

request Scope 作用域注入另一个,Bean测必须注入AOP代理来替换作用域bean

##### 代理类的选择

###### 创建代理类：将子&lt;aop:scoped-proxy/&gt;元素插入到作用域bean定义中。

```
                   创建的代理类是基于CGLIB的类代理
```

场景：1. bean类的定义范围的在request,session,global session和自定义范围水平要求&lt;aop:scoped-proxy/&gt;元素。

```
        2.当将一个生命周期短的注入一个生命周期长的bean时
```

```
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session">
    <aop:scoped-proxy/>
</bean>
<bean id="userManager" class="com.foo.UserManager">
    <property name="userPreferences" ref="userPreferences"/>
</bean>
```

4.5.5 自定义范围

从基础范围返回对象：会话范围实现返回会话范围的bean。如果它不存在，则该方法在将其绑定到会话以供将来参考之后返回该bean的新实例

```
Object get(String name, ObjectFactory objectFactory)
```

从基础范围中删除对象：会话范围实现从基础会话中删除会话范围的bean。

```
Object remove(String name)
```

注册范围应在销毁时或在范围中指定的对象被销毁时应执行的回调：有关销毁回调的更多信息，请参阅Javadoc或Spring作用域实现。

```
void registerDestructionCallback(String name, Runnable destructionCallback)
```

获取基础范围的对话标识符：每个范围的标识符都不同。对于会话范围的实现，该标识符可以是会话标识符。

```
String getConversationId()
```

使用自定义Scope实现，您不仅限于范围的编程注册。您还可以Scope使用以下CustomScopeConfigurer类以声明方式进行注册 ：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean class="org.springframework.beans.factory.config.CustomScopeConfigurer">
        <property name="scopes">
            <map>
                <entry key="thread">
                    <bean class="org.springframework.context.support.SimpleThreadScope"/>
                </entry>
            </map>
        </property>
    </bean>

    <bean id="bar" class="x.y.Bar" scope="thread">
        <property name="name" value="Rick"/>
        <aop:scoped-proxy/>
    </bean>

    <bean id="foo" class="x.y.Foo">
        <property name="bar" ref="bar"/>
    </bean>

</beans>
```

### 2.5.6 自定义bean的性质

#### 2.5.6.1 生命周期回调

方法：

1、 JSR-250注解@PostConstruct和@PreDestroy

2、在内部，Spring Framework使用BeanPostProcessor实现来处理它可以找到的任何回调接口并调用适当的方法。或自己实现方法。

3、除了初始化和销毁​​回调之外，Spring管理的对象还可以实现Lifecycle接口，以便这些对象可以参与由容器自身生命周期驱动的启动和关闭过程。

4、不会产生不必要的耦合 使用init-method和destroy-method对象定义元数据。

##### 第四种方法的实现：

初始化回调：

```
       该org.springframework.beans.factory.InitializingBean接口允许后对bean的所有必要属性容器设置一个bean来执行初始化的工作。的InitializingBean接口规定了一个方法：
```

```
void afterPropertiesSet() throws Exception;
```

使用 init-method实现：

```
<bean id="exampleInitBean" class="examples.ExampleBean" init-method="init"/>
```

```
public class ExampleBean {

    public void init() {
        // do some initialization work
    }

}
```

#### 销毁回调

实现org.springframework.beans.factory.DisposableBean接口允许bean在包含它的容器被销毁时获得回调。的 DisposableBean接口规定了一个方法：

```
void destroy() throws Exception;
```

DisposableBean接口实现：

```
<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup"/>
```

```
public class ExampleBean {

    public void cleanup() {
        // do some destruction work (like releasing pooled connections)
    }

}
```

使用destroy-method实现：

```
<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup"/>
```

```
public class ExampleBean {

    public void cleanup() {
        // do some destruction work (like releasing pooled connections)
    }

}
```

#### 默认初始化和销毁​​方法

```
public class DefaultBlogService implements BlogService {

    private BlogDao blogDao;

    public void setBlogDao(BlogDao blogDao) {
        this.blogDao = blogDao;
    }

    // this is (unsurprisingly) the initialization callback method
    public void init() {
        if (this.blogDao == null) {
            throw new IllegalStateException("The [blogDao] property must be set.");
        }
    }

}
```

```
<beans default-init-method="init">

    <bean id="blogService" class="com.foo.DefaultBlogService">
        <property name="blogDao" ref="blogDao" />
    </bean>

</beans>
```

#### 启动和关闭回调

该Lifecycle接口为具有自己的生命周期要求的任何对象定义了基本方法：

```
public interface Lifecycle {

    void start();

    void stop();

    boolean isRunning();

}
```

当ApplicationContext本身启动和停止时，它会将这些调用级联到该上下文中定义的所有生命周期实现。它通过委托给 LifecycleProcessor：

```
public interface LifecycleProcessor extends Lifecycle {

    void onRefresh();

    void onClose();

}
```

场景：两个对象之间存在依赖关系

解决的问题： 两个对象的启动和停止顺序是很重要的

启动时，具有最低相位的对象首先开始，停止时，遵循相反的顺序。因此，实现SmartLifecycle和getPhase（）方法返回的对象Integer.MIN\_VALUE将是第一个开始和最后一个停止的对象。

```
public interface Phased {

    int getPhase();

}
```

```
public interface SmartLifecycle extends Lifecycle, Phased {

    boolean isAutoStartup();

    void stop(Runnable callback);

}
```

```
<bean id="lifecycleProcessor" class="org.springframework.context.support.DefaultLifecycleProcessor">
    <!-- timeout value in milliseconds -->
    <property name="timeoutPerShutdownPhase" value="10000"/>
</bean>
```

#### 在非Web应用程序中正常关闭Spring IoC容器

原理：在富客户端桌面环境中; 您在JVM上注册了一个关闭钩子。

实现：要注册关闭挂钩，请调用registerShutdownHook\(\)在AbstractApplicationContext类上声明的方法：

```
import org.springframework.context.support.AbstractApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public final class Boot {

    public static void main(final String[] args) throws Exception {

        AbstractApplicationContext ctx = new ClassPathXmlApplicationContext(
                new String []{"beans.xml"});

        // add a shutdown hook for the above context...
        ctx.registerShutdownHook();

        // app runs here...

        // main method exits, hook is called prior to the app shutting down...

    }
}
```

#### 2.5.6.2 ApplicationContextAware和BeanNameAware

当ApplicationContext创建实现org.springframework.context.ApplicationContextAware接口的类时，将为该类提供对该 接口的引用ApplicationContext。

作用：增强ApplicationContext的功能

缺点：不符合IOC规范和造成代码与Spring耦合

```
public interface ApplicationContextAware {

    void setApplicationContext(ApplicationContext applicationContext) throws BeansException;

}
```

#### 2.5.6.3 其他Aware接口

![](/assets/Aware interfaces.PNG)



