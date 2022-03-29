---
title: SpringBoot AOP

tags: 
- java
- spring aop
categories:
- java
---

AOP：面向切面编程，相对于OOP面向对象编程
Spring的AOP的存在目的是为了解耦。AOP可以让一组类共享相同的行为。在OOP中只能继承和实现接口，且类继承只能单继承，阻碍更多行为添加到一组类上，AOP弥补了OOP的不足。

还有就是为了清晰的逻辑，让业务逻辑关注业务本身，不用去关心其它的事情，比如事务。

Spring的AOP是通过JDK的动态代理和CGLIB实现的。

###### 注意：在完成了引入AOP依赖包后，不需要去做其他配置。AOP的默认配置属性中，spring.aop.auto属性默认是开启的，也就是说只要引入了AOP依赖后，默认已经增加了@EnableAspectJAutoProxy，不需要在程序主类中增加@EnableAspectJAutoProxy来启用。

pom.xml
```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.2.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
            <version>2.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.58</version>
        </dependency>
```

controller

```java
package com.example.springaopdemo.controller;

import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class AopController {

    @RequestMapping("/getInfo/{name}")
    public String getInfo(@PathVariable("name") String name){
        return "name: " + name;
    }

    @RequestMapping("/exception")
    public void exception(){
        throw new RuntimeException("this is a text exception");
    }

}


```
aspect

```java
package com.example.springaopdemo.aop;

import com.alibaba.fastjson.JSONObject;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class LogAspect {
    /**
     * 定义切入点，切入点为com.example.demo.aop.AopController中的所有函数
     *通过@Pointcut注解声明频繁使用的切点表达式
     */
    @Pointcut("execution(public * com.example.springaopdemo.controller.AopController.*(..)))")
    public void pointcut(){

    }

    /**
     * 环绕通知: 方法执行前后执行
     * @param point
     * @throws Throwable
     */
    //@Around("pointcut()")
    public void around(ProceedingJoinPoint point) throws Throwable {
        System.out.println("@Around：执行目标方法之前...");
        //访问目标方法的参数：
         Object[] args = point.getArgs();
//        if (args != null && args.length > 0 && args[0].getClass() == String.class) {
//            args[0] = "改变后的参数1";
//        }
        //用改变后的参数执行目标方法
        Object returnValue = point.proceed(args);
        System.out.println("@Around：执行目标方法之后...");
        System.out.println("@Around：被织入的目标对象为：" + point.getTarget());
        //return "原返回值：" + returnValue + "，这是返回结果的后缀";
    }

    /**
     * @description  在连接点执行之前执行的通知
     */
    @Before("pointcut()")
    public void printParam(JoinPoint joinPoint){
        //获取请求的方法
        Signature sig = joinPoint.getSignature();
        String method = joinPoint.getTarget().getClass().getName() + "." + sig.getName();
        //获取请求的参数
        Object[] args = joinPoint.getArgs();
        //fastjson转换
        String params = JSONObject.toJSONString(args);
        //打印请求参数
        System.out.println("@Before: "+method + ":" + params);
    }

    /**
     * @description  在连接点执行之后执行的通知（返回通知和异常通知的异常）
     */
    @After("pointcut()")
    public void doAfterGame(){
        System.out.println("@After: 方法执行后");
    }

    /**
     * @description  在连接点执行之后执行的通知（返回通知）
     */
    @AfterReturning(value = "pointcut()",returning = "returnValue")
    public void doAfterReturningGame(Object returnValue){
        System.out.println("@AfterReturning：returnValue is: "+returnValue);
    }

    /**
     * @description  在连接点执行之后执行的通知（异常通知）
     */
    @AfterThrowing(value = "pointcut()",throwing = "throwable")
    public void doAfterThrowingGame(Throwable throwable){
        System.out.println("@AfterThrowing："+throwable);
    }
}
```

