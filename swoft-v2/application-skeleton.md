# Application Skeleton

## Application Skeleton

> `app` 下的目录为了避免一些文件夹名称没有复数单词，导致命名不统一，所有的文件夹名称**统一使用单数**

```text
app/    ------ 应用代码目录
  |- Boot/ ------ 初始化引导目录
  |- Command/ ------ 命令行代码目录
  |- Controller/ ------ HTTP控制器代码目录
  |- Helper/
  |- Listener/
  |- Middleware/
  |- Model/   ------ 模型/逻辑层目录(这些层并不限定，根据需要使用)
    |- Dao/   ------ Dao目录
    |- Data/   ------ Data目录
    |- Entity/   ------ 表模型目录
    |- Form(Request)/   ------ 表单(请求)验证目录
    |- Logic/   ------ 逻辑目录
  |- WebSocket/ ------ WebSocket控制器代码目录
  |- Swoft.php
bin/  
  |- swoft  ------ swoft 入口文件
config/  ------ 应用配置目录
public/  ------ WEB可访问目录
resource/   ------ 应用相关资源目录
  |- languages
  |- views
runtime/  ------ 临时文件目录(日志、上传文件、文件缓存等)
test/     ------ 单元测试代码目录
composer.json
phar.build.inc
phpunit.xml.dist
```

## Component Skeleton

