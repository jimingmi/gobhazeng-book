# 快速开始

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@SpringBootApplication
public class SpringBootDemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringBootDemoApplication.class, args);
	}

	@RequestMapping(value = "/")
	public String index(){
		return "Hello World";
	}

}
```

 - `@RestController`相当于`@Controller`与`@ResponseBody`，数据格式以JSON返回。
 - `@SpringBootApplication`为SpringBoot核心注解，开启自动配置。

# 关于缓存支持

https://www.jianshu.com/p/64f684bd0ce9

