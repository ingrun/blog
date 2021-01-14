---
title: SpringBoot整合Swagger3生成接口文档
date: 2021-01-14
---



前后端分离的项目，接口文档的存在十分重要。与手动编写接口文档不同，swagger是一个自动生成接口文档的工具，在需求不断变更的环境下，手动编写文档的效率实在太低。与swagger2相比新版的swagger3配置更少，使用更加方便。



## 一、pom文件中引入Swagger3依赖

```xml
<dependency>
     <groupId>io.springfox</groupId>
      <artifactId>springfox-boot-starter</artifactId>
      <version>3.0.0</version>
</dependency>
```



## 二、Application上面加入@EnableOpenApi注解

```java
@EnableOpenApi
@SpringBootApplication
@MapperScan(basePackages = {"cn.ruiyeclub.dao"})
public class Swagger3Application {
    public static void main(String[] args) {
        SpringApplication.run(Swagger3Application.class, args);
    }
}	
```



## 三、Swagger3Config的配置

```java
@Configuration
public class Swagger3Config {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.OAS_30)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Swagger3接口文档")
                .description("更多请咨询服务开发者")
                .version("1.0")
                .build();
    }
}
```



## 四、Swagger注解的使用说明

```java
@Api：用在请求的类上，表示对类的说明
    tags="说明该类的作用，可以在UI界面上看到的注解"
    value="该参数没什么意义，在UI界面上也看到，所以不需要配置"

@ApiOperation：用在请求的方法上，说明方法的用途、作用
    value="说明方法的用途、作用"
    notes="方法的备注说明"

@ApiImplicitParams：用在请求的方法上，表示一组参数说明
    @ApiImplicitParam：用在@ApiImplicitParams注解中，指定一个请求参数的各个方面
        name：参数名
        value：参数的汉字说明、解释
        required：参数是否必须传
        paramType：参数放在哪个地方
            · header --> 请求参数的获取：@RequestHeader
            · query --> 请求参数的获取：@RequestParam
            · path（用于restful接口）--> 请求参数的获取：@PathVariable
            · div（不常用）
            · form（不常用）    
        dataType：参数类型，默认String，其它值dataType="Integer"       
        defaultValue：参数的默认值

@ApiResponses：用在请求的方法上，表示一组响应
    @ApiResponse：用在@ApiResponses中，一般用于表达一个错误的响应信息
        code：数字，例如400
        message：信息，例如"请求参数没填好"
        response：抛出异常的类

@ApiModel：用于响应类上，表示一个返回响应数据的信息
            （这种一般用在post创建的时候，使用@RequestBody这样的场景，
            请求参数无法使用@ApiImplicitParam注解进行描述的时候）
    @ApiModelProperty：用在属性上，描述响应类的属性
```



Controller层的配置：

```java


@Api( tags = "活动管理" )
@RestController
@RequestMapping("/activity")
public class ActivityController {

    @Resource
    private ActivityService activityService;

    @ApiOperation("获取活动信息")
    @GetMapping("/get/{id}")
    public Object get(@PathVariable String id){
        return ResponseUtils.success(activityService.get(id));
    }

    @ApiOperation("删除活动信息")
    @PostMapping("/del/{id}")
    public Object del(@PathVariable String id){
        return ResponseUtils.success(activityService.del(id));
    }

    @ApiOperation("添加活动信息")
    @PostMapping("/add")
    public Object add(@RequestBody Activity activity){
        return ResponseUtils.success(activityService.add(activity));
    }

    @ApiOperation("修改活动信息")
    @PostMapping("/upd")
    public Object upd(@RequestBody Activity activity){
        return ResponseUtils.success(activityService.upd(activity));
    }
}
```



## 五、访问地址

- Swagger的访问路径由port/swagger-ui.html 改成了 port/swagger-ui/ 或 `ip:port/swagger-ui/index.html`







[https://zhuanlan.zhihu.com/p/161947638](https://zhuanlan.zhihu.com/p/161947638)

