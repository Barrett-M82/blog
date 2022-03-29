---
title: springboot抛弃tomcat
tags:
- springboot
---

**直接修改启动类**

```
package com.smile.client;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import java.util.concurrent.CountDownLatch;

@SpringBootApplication
public class ClientApplication {

	public static void main(String[] args) throws InterruptedException {
        SpringApplication application = new SpringApplication( ClientApplication.class );

        // 如果是web环境，默认创建AnnotationConfigEmbeddedWebApplicationContext，因此要指定applicationContextClass属性
        application.setApplicationContextClass( AnnotationConfigApplicationContext.class );
        application.run( args );

        // 如果不想让spring容器退出，可以使用以下代码
        CountDownLatch latch = new CountDownLatch( 1 );
        latch.await();
	}

}

```