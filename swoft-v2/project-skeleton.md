# Swoft Skeleton

## Application Skeleton

> `app` 下的目录为了避免一些文件夹名称没有复数单词，导致命名不统一，所有的文件夹名称**统一使用单数**

```text
├── app/   ------ 应用代码目录
│   ├── Aspect/
│   ├── Console/  ------ 命令行代码目录
│   │   ├── Command/
│   ├── Http/     ------ HTTP 代码目录
│   │   ├── Controller/
│   │   └── Middleware/
│   ├── Helper/
│   │   └── Functions.php
│   ├── Listener/   ------ 事件监听器目录
│   ├── Model/   ------ 模型、逻辑等代码目录(这些层并不限定，根据需要使用)
│   │   ├── Dao/
│   │   ├── Data/
│   │   └── Logic/
│   ├── Rpc/
│   │   └── Middleware/
│   ├── WebSocket/  ------ WebSocket 代码目录
│   │   ├── Chat/
│   │   ├── Middleware/
│   │   └── ChatModule.php
│   ├── Application.php -------- 应用类文件继承自swoft核心
│   ├── AutoLoader.php  -------- 项目扫描等信息培训
│   └── bean.php
├── bin/
│   ├── bootstrap.php
│   └── swoft   ------ swoft 入口文件
├── config/     ------ 应用配置目录
│   ├── base.php
│   └── db.php
├── public/     ------ WEB可访问目录
├── resource/   ------ 应用相关资源目录
│   ├── languages/
│   └── views/
├── runtime/    ------ 临时文件目录(日志、上传文件、文件缓存等)
├── test/       ------ 单元测试代码目录
│   └── bootstrap.php
├── changelog.md
├── composer.json
├── composer.lock
├── phar.build.inc
└── phpunit.xml.dist
```

> render by `tree -L 2 -F --dirsfirst`
