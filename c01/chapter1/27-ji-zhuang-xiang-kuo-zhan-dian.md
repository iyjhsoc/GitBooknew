### 4.7.1 使用BeanPostProcessor自定义bean

作用：如果要在Spring容器完成初始化配置和初始化bean之后实现某些自定义逻辑，测可以写入一个或者多个BeanPostProcessor方法

使用：通过order控制这些BeanPostProcessor执行的顺序

因为AOP自动代理是作为一个BeanPostProcessor自身实现的，所以BeanPostProcessors它们直接引用的bean 都没有资格进行自动代理

注册：BeanPostProcessor注册是通过 ApplicationContext自动检测。

          但也可以注册它们编程对一个ConfigurableBeanFactory使用 addBeanPostProcessor方法。

