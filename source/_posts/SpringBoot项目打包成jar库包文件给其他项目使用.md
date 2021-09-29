

### SpringBoot项目打包成jar库包文件给其他项目使用的方法



# 1. 介绍 

## 1.1 介绍

福哥打算做一个tfjava项目，把通用的公共库整合到一起，打包成jar库包给其他项目使用。现在把操作过程记录下来，分享给大家~~

# 2. 打包

打包可以通过IDE工具直接生成，也可以通过Maven命令生成，下面福哥将两种方法都介绍给大家

## 2.1 打包插件

在pom.xml里将默认的spring-boot-maven-plugin插件改成maven-compiler-plugin插件



```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
</plugin>
```



或直接修改配置

```xml
<plugin>            
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <classifier>exec</classifier>     
    </configuration>
</plugin>
```

