---
title: SpringBoot Jpa

tags: 
- java
- springboot
categories:
- java
---
SpringBoot中使用SpringDataJPA

JPA是什么?
JPA(Java Persistence API)是Sun官方提出的Java持久化规范. 为Java开发人员提供了一种对象/关联映射工具来管理Java应用中的关系数据. 它的出现是为了简化现有的持久化开发工作和整合ORM技术. 结束各个ORM框架各自为营的局面.

JPA仅仅是一套规范,不是一套产品, 也就是说Hibernate, TopLink等是实现了JPA规范的一套产品.

Spring Data JPA
Spring Data JPA是Spring基于ORM框架、JPA规范的基础上封装的一套JPA应用框架,是基于Hibernate之上构建的JPA使用解决方案,用极简的代码实现了对数据库的访问和操作,包括了增、删、改、查等在内的常用功能.

实践
引入pom
```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
```
entity

```java
package com.smile.springbootjpa.entity;

import lombok.Data;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
@Data
public class User {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false, unique = true)
    private String userName;

    @Column(nullable = false)
    private String passWord;

    @Column(nullable = false, unique = true)
    private String email;

    @Column(nullable = true, unique = true)
    private String nickName;

    @Column(nullable = false)
    private String regTime;

    public User(String userName, String passWord, String email, String nickName, String regTime) {
        this.userName = userName;
        this.passWord = passWord;
        this.email = email;
        this.nickName = nickName;
        this.regTime = regTime;
    }

    public User() {
    }

    // getter and setter
}
```
dao

```java

package com.smile.springbootjpa.dao;

import com.smile.springbootjpa.entity.User;
import org.springframework.data.jpa.repository.JpaRepository;


public interface UserRepository extends JpaRepository<User,Long> {

    User findByUserNameOrEmail(String userName, String email);

    User findByUserName(String userName);

}
```
Test
```java

package com.smile.springbootjpa.dao;

import com.smile.springbootjpa.entity.User;
import org.junit.Assert;
import org.junit.jupiter.api.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import javax.annotation.Resource;
import java.text.SimpleDateFormat;
import java.util.Date;

@RunWith(SpringRunner.class)
@SpringBootTest
public class UserRepositoryTest {

    @Resource
    private UserRepository userRepository;

    @Test
    public void test(){
        Date data = new Date();
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String formattedDate = dateFormat.format(data);

        userRepository.save(new User("aa","aa123456","aa@126.com","aa",formattedDate));
        userRepository.save(new User("bb","bb123456","bb@126.com","bb",formattedDate));
        userRepository.save(new User("cc","cc123456","cc@126.com","cc",formattedDate));

        Assert.assertEquals(3,userRepository.findAll().size());
        Assert.assertEquals("bb",userRepository.findByUserNameOrEmail("bb","bb@126.com").getNickName());
        userRepository.delete(userRepository.findByUserName("aa"));
    }
}

```

### springboot jpa 报错:

```
2020-01-20 14:40:01.449  INFO 5624 --- [           main] o.h.e.t.j.p.i.JtaPlatformInitiator       : HHH000490: Using JtaPlatform implementation: [org.hibernate.engine.transaction.jta.platform.internal.NoJtaPlatform]
2020-01-20 14:40:01.464  INFO 5624 --- [           main] j.LocalContainerEntityManagerFactoryBean : Initialized JPA EntityManagerFactory for persistence unit 'default'
2020-01-20 14:40:01.997  INFO 5624 --- [           main] c.s.s.SpringbootJpaApplication           : Started SpringbootJpaApplication in 6.056 seconds (JVM running for 7.417)
2020-01-20 14:40:02.002  INFO 5624 --- [extShutdownHook] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
2020-01-20 14:40:02.007  INFO 5624 --- [extShutdownHook] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2020-01-20 14:40:02.022  INFO 5624 --- [extShutdownHook] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.

Process finished with exit code 0

```

解决办法: 引入 spring-boot-starter-web

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```