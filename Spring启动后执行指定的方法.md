
Spring启动后执行指定的方法

前面的话

我在系统启动后就需要开启某个服务，在Spring容器将所有的Bean都初始化完成后，就会执行某个方法。

方法如下：

1. XML文件中配置

'<bean id="demoService" class="com.huangd.xms.service.impl.DemoServiceImpl" init-method="start"></bean>'

Java代码

``` java
public class DemoServiceImpl implements DemoService {

    @Override
    public void start() {
        // 初始化数据
        // 开启需要的服务
        // 从预先数据库中加载数据
    }

}
```

2. 注解方式

``` java
@Service("demoService")
public class DemoServiceImpl implements DemoService {

    @PostContruct
    @Override
    public void start() {
        // 初始化数据
        // 开启需要的服务
        // 从预先数据库中加载数据
    }

}
```

3. 实现initiailzingBean接口

``` java
package com.huangd.xms.service.impl;

import org.springframework.beans.factory.InitializingBean;
import org.springframework.stereotype.Service;

@Service
public class DemoInitializingBean implements InitializingBean {

    @Override
    public void afterPropertiesSet() throws Exception {
        // 初始化数据
        // 开启需要的服务
        // 从预先数据库中加载数据
    }

}
```

4. 监听器 前置处理器 后置处理 