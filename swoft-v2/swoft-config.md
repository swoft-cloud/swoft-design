# swoft 配置

- 配置数据来源 - system ENV, config 目录，remote url
- system ENV - 系统的环境变量数据，php 运行时会拷贝一份系统的作为初始数据

## `.env 文件`

- 跟应用配置无关，只是提供了一种可以快捷的配置一份环境数据，会载入补充到system ENV数据里
  - 它的作用跟 `export Var=val` 或者 通过 `docker ENV` 设置环境变量一样
- 不应当滥用 `.env`, 应当只是配置一些 **关键性的信息**
- env 数据不会主动参与应用配置。都需要使用者手动的取拿取 `env('KEY')`

## bean config

`/config/bean.php` 特别提供了一个文件用于提供数组方式来配置一个 bean

```php
return [
    'mybean' => [
        'class' => MyClass::class,
        // first element. key is 0
        [
            // construct args
        ],
        // properties: key => value
        'prop1' => 'val1',
        'prop2' => 'val2',
        // 本次增强: 可以对这个 bean 做一些设置
        '__options' => [
            'alias' => ['alias-name'],
            'scope' => Scope::SINGLETON, // Scope::PROTOTYPE
        ],
    ]
]
```

## app config

- 本次增强支持多种格式，可以是 `yml`, `json`, `php`
- `/config` 目录里才是真正的应用配置数据(除了 `/config/bean.php`)
  - 默认文件 `app.{EXT}`，其他文件都会按文件名为key合并到默认文件数据中去
- 从远程URL(`http://abc.com/config.json`)加载 ？？这个待定 

## `config` bean

关于 `config` 这个bean对象(可以在 `/config/bean.php` 配置它):

```php
'config' => [
    // 指定使用格式，这会在 config 目录下加载指定后缀的配置文件
    'format' => 'yaml', // json php ...
    // 'readonly' => false, // true
    'priority' => 'file', // file: 先加载 'files' 的配置 dir: 先加载 config 目录的数据
    'files' => [ // 允许加载指定文件 or URL
        '/path/to/config.json',
        'http://abc.com/config.json', // 待定
    ],
],
```

### config example

- use php:

```php
[
    'db' => [
        // 主动使用ENV数据，允许通过环境变量覆盖默认值
        'uri' => env('DB_URI', '127.0.0.1:3306/test?user=root&password=123456'),
        'minActive' => 5, // 像这种的,如果没有需要不推荐写到 `.env`
        'maxActive' => 15,
    ],
];
```

- use yaml:

```yaml
db:
    # 主动使用ENV数据，允许通过环境变量覆盖默认值
    uri: ${env.DB_URI | 127.0.0.1:3306/test?user=root&password=123456}
    minActive: 5
    maxActive: 15
```

