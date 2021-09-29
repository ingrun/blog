---
title: maven 指定单个JAR 的 url
date: 2021-06-09
---



 在不影响原来的配置的情况下，可以通过修改pom.xml文件，添加如下节点来修改URL

```xml
  <repositories>
  	<repository>
  		<id>repository.hibernate</id>
  		<name>Hibernate jar</name>
  		<url>http://mirrors.ibiblio.org/maven2</url>
  	</repository>
  </repositories>
```

 这样就可以更换URL 了

