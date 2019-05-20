### 4.7.1 使用BeanPostProcessor自定义bean

```
postProcessBeforeInitialization()
该方法在注入的Bean的init方法执行前执行

postProcessAfterInitialization()
该方法在注入的Bean的init方法执行后执行
```

作用：如果要在Spring容器完成初始化配置和初始化bean之后实现某些自定义逻辑，测可以写入一个或者多个BeanPostProcessor方法

使用：通过order控制这些BeanPostProcessor执行的顺序

因为AOP自动代理是作为一个BeanPostProcessor自身实现的，所以BeanPostProcessors它们直接引用的bean 都没有资格进行自动代理

注册：1. BeanPostProcessor注册是通过 ApplicationContext自动检测。

1. 但也可以注册它们编程对一个ConfigurableBeanFactory使用 addBeanPostProcessor方法。

第二个注册方法 适合在注册前的一些判断

第一种方法：不管有没有设置排序，以编程方式注册的内容始终在通过自动检测注册之前处理。

BeanPostProcessor的实现：

```
package scripting;

import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.beans.BeansException;

public class InstantiationTracingBeanPostProcessor implements BeanPostProcessor {

    // simply return the instantiated bean as-is
    public Object postProcessBeforeInitialization(Object bean,
            String beanName) throws BeansException {
        return bean; // we could potentially return any object reference here...
    }

    public Object postProcessAfterInitialization(Object bean,
            String beanName) throws BeansException {
        System.out.println("Bean " + beanName + " created : " + bean.toString());
        return bean;
    }

}
```

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:lang="http://www.springframework.org/schema/lang"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/lang
        http://www.springframework.org/schema/lang/spring-lang.xsd">

    <lang:groovy id="messenger"
            script-source="classpath:org/springframework/scripting/groovy/Messenger.groovy">
        <lang:property name="message" value="Fiona Apple Is Just So Dreamy."/>
    </lang:groovy>

    <!--
    when the above bean (messenger) is instantiated, this custom
    BeanPostProcessor implementation will output the fact to the system console
    -->
    <bean class="scripting.InstantiationTracingBeanPostProcessor"/>

</beans>
```

前面配置还定义了一个由Groovy脚本支持的bean。Spring的动态语言支持在28章中有详解

实现：

```
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.scripting.Messenger;

public final class Boot {

    public static void main(final String[] args) throws Exception {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("scripting/beans.xml");
        Messenger messenger = (Messenger) ctx.getBean("messenger");
        System.out.println(messenger);
    }

}
```

### 4.7.2使用BeanFactoryPostProcessor自定义配置元数据

BeanFactoryPostProcessor和BeanPostProcessor类似

