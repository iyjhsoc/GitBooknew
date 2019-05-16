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

**Figure 2.5.1**

![](/assets/2_5_1.PNG)

```
<bean id="accountService" class="com.foo.DefaultAccountService"/>

<!-- the following is equivalent, though redundant (singleton scope is the default) -->
<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>
```

### 2.5.2 原型Bean

使用场景：对所有状态的bean使用原型范围，对无状态bean使用单例范围。

效果：

**Figure2.5.2**

![](/assets/2_5_2.PNG)

![](/assets/2_5_2.PNG)

