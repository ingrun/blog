---
title: Spring boot
---



## 新建项目

- 选中web项目
- 选中mybatis、jdbc、mysql

-   （redis,themeleaf）

## 建立数据库

- 因为数据库字段、索引对大小写是不敏感的，驼峰标识无意义；
  所以一般采用数据库字段下划线， 实体类驼峰的命名方式

## 项目引入yml文件
<!-- more -->

```yml

spring:
  datasource:
    username: root
    password: root
    url: jdbc:mysql://localhost:3306/vue-admin?useUnicode=true&characterEncoding=UTF-8
    driver-class-name: com.mysql.jdbc.Driver



  thymeleaf:
    servlet:
      content-type: text/html
    mode: LEGACYHTML5
    cache: false
    prefix: classpath:/templates/
    
    
mybatis-plus:
  mapper-locations: classpath*:/mapper/**.xml
  typeAliasesPackage: cn.allin.sdgreenfood.po
```



## 配置mapper扫描

```java
@MapperScan("cn.allin.huanshi.mapper")
@SpringBootApplication
public class HuanShiApplication {

    public static void main(String[] args) {
        SpringApplication.run(HuanShiApplication.class, args);
    }

}
```



## 配置mybatis-plus

```xml
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.34</version>
        </dependency>
<!--记得配置mysql 的版本 不然容易报错-->
	
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.1.2</version>
        </dependency>

```



## 配置shiro

```java
//MyShiroRealm.java

package cn.allin.huanshi.shiro;


import cn.allin.huanshi.domain.User;
import cn.allin.huanshi.mapper.UserMapper;
import cn.allin.huanshi.service.UserService;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;

//实现AuthorizingRealm接口用户用户认证
public class MyShiroRealm extends AuthorizingRealm {

    Logger logger = LoggerFactory.getLogger(getClass());

    @Autowired
    UserService userService;

    //角色权限
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        String name= (String) principalCollection.getPrimaryPrincipal();
        User user = null;
        try {
            user = userService.getUserInfo(name);
        } catch (Exception e) {
            logger.info("授权失败！");
        }

        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        simpleAuthorizationInfo.addRole(user.getRole());

        return simpleAuthorizationInfo;

    }

    //用户认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        //加这一步的目的是在Post请求的时候会先进认证，然后在到请求
        if (authenticationToken.getPrincipal() == null) {
            return null;
        }
        String userName = authenticationToken.getPrincipal().toString();

        User user = userService.getUserInfo(userName);
        if (user != null){
            return new SimpleAuthenticationInfo(user.getUsername(), user.getPassword(), getName());  //此处一定传用户名  否则会导致rememberMe 失效
        }
        return null;

    }
}
```

```java
//MyFormAuthenticationFilter.java

//ShiroConfig.java
```





