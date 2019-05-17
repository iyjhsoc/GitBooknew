## 2.6 Bean 定义继承

使用基于XML的配置元数据时，可以使用该parent属性指定子bean定义，并将父bean指定为此属性的值。

父 Bean特点：

> 1. 不能单独实例化

```
<bean id="inheritedTestBean" abstract="true"
        class="org.springframework.beans.TestBean">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>

<bean id="inheritsWithDifferentClass"
        class="org.springframework.beans.DerivedTestBean"
        parent="inheritedTestBean" init-method="initialize">
    <property name="name" value="override"/>
    <!-- the age property value of 1 will be inherited from parent -->
</bean>
```

* 子 Bean 必须接受 父 Bean的所有属性，子Bean可以覆盖父 Bean的属性值

* 子bean定义从父级继承构造函数参数值、属性值和方法重写，并可以选择添加新值。您指定的任何初始化方法、销毁方法和/或静态工厂方法设置都将覆盖相应的父设置。

* 其余设置始终取自子定义：依赖、自动连线模式、依赖项检查、单例、作用域、Lazy Init。

前面的示例通过使用该abstract属性将父bean定义显式标记为abstract 。如果父定义未指定类，abstract则根据需要显式标记父bean定义，如下所示：

```
<bean id="inheritedTestBeanWithoutClass" abstract="true">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>

<bean id="inheritsWithClass" class="org.springframework.beans.DerivedTestBean"
        parent="inheritedTestBeanWithoutClass" init-method="initialize">
    <property name="name" value="override"/>
    <!-- age will inherit the value of 1 from the parent bean definition-->
</bean>
```



