
## SpringMVC异常全局捕获处理

1. 提出问题
在J2EE项目的开发中，不管是底层的数据库的操作过程，还是业务层的处理过程，还是控制层的处理过程，都不可避免会遇到各种不可知、不可测的异常需要处理。如果，每个过程都单独处理异常，系统的代码耦合度高，工作量大且不好统一维护管理。

2. 解决问题
能不能把异常处理从各个处理过程中解耦出来，这样既保证了相关处理过程功能的单一性，也实现了异常信息的统一处理和维护。

### SpringMVC 异常处理实现

SpringMVC 有如下几种实现方式

1. 使用@ExceptionHandler注解
2. 使用@ExceptionHandler + @ControllerAdvice
3. 实现HandlerExceptionResolver接口
4. 实例化SimpleMappingExceptionResolver类
5. 继承ResponseEntityExceptionHandler类

### 使用@ExceptionHandler注解

使用@ExceptionHandler注解，异常处理的方法必须与出现异常的方法在同一个Controller里面；注意，这种方式，不能全局控制异常，要在每个Controller类中写一遍，代码具有侵入性。

使用如下：

``` java
@Controller
@RequestMapping("/demo/")
public class DemoController {

    @RequestMapping(value = "test.json")
    @ResponseBody
    public ModelAndView test() {
        Result<User> ret = new Result<>();
        String a = null;
        a.toString();
        ModelAndView view = new ModelAndView();
        view.setViewName("/data");
        return view;
    }
    
    @ExceptionHandler
    public ModelAndView exception(Exception e) {
        ModelAndView view = new ModelAndView();
        view.setViewName("/data");
        return view;
    }
}
```

### 使用@ExceptionHandler + @ControllerAdvice

``` java
@ControllerAdvice
@ResponseBody
public class DemoExceptionHandle {

    private Logger log = LoggerFactory.getLogger(this.getClass());
    
    @ExceptionHandler
    public Result<User> exception(Exception e) {
        log.error("全局异常处理DemoExceptionHandle", e);
        Result<User> result = new Result<User>();
        result.setErrorDesc("错误的信息");
        return result;
    }
    
    @ExceptionHandler(RuntimeException.class)
    public ModelAndView exception2(Exception e) {
        ModelAndView view = new ModelAndView();
        view.setViewName("/data");
        log.error("全局异常处理GlobalExceptionHandle2", e);
        return view;
    }

}
```

### 实现HandlerExceptionResolver接口

``` java
public abstract class APIExceptionResolver implements HandlerExceptionResolver {
    
    private final Logger log = LoggerFactory.getLogger(this.getClass());
    
    @Override
    public ModelAndView resolveException(HttpServletRequest req,
            HttpServletResponse res, Object obj, Exception e) {
        log.error("请求相应异常", e);
        return new ModelAndView();
    }

}

```

### 实例化SimpleMappingExceptionResolver类

``` java
    SimpleMappingExceptionResolver er = new SimpleMappingExceptionResolver();
    // 为所有的异常定义默认的处理页面
    er.setDefaultErrorView("error");
    // 定义异常处理页面获取异常信息的变量名
    er.setExceptionAttribute("exception");
    // 将不同的异常映射到不同的页面上
    Properties mappings = new Properties();
    mappings.put("java.lang.Exception", "error");
    mappings.put("java.sql.SQLExcepton", "data");
    er.setExceptionMappings(mappings);
```