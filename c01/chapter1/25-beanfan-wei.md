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

场景：1. bean类的定义范围的在request,session,global session和自定义范围水平要求&lt;aop:scoped-proxy/&gt;元素。

           2. 当将一个生命周期短的注入一个生命周期长的bean时

```
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session">
    <aop:scoped-proxy/>
</bean>
<bean id="userManager" class="com.foo.UserManager">
    <property name="userPreferences" ref="userPreferences"/>
</bean>
```



