# 2.x目标概览

1. 简化用户使用方式，提高框架稳定性和性能，且更加规范。
2. 尽量更多的使用注解，默认大于配置规则。
3. 全协程，核心化，易扩展，简洁易用。
4. 支持swoole 4+, php 7.1+
5. \> 90% 的单元测试覆盖率
6. 更丰富的 demo 和文档说明


## 历史包袱

1. reload热更新
2. defer延迟收包
3. redis前缀支持
4. 定时任务(分布式定时任务扩展)
5. 微服务支持

## 优化功能

1. 注解重构(优化扫描、解析、封装流程)
2. bean新增别名支持
3. 配置支持env/yaml
4. 异常处理优化
5. ORM重构，大体使用和laravel一样(不支持实体关联关系)
6. RPC支持协议扩展，默认支持json
7. config支持，pull/push/本地文件三种方式
8. sgo()函数，替代系统go函数

## 新增功能

1. swoft-cli 负责监控项目改动，负责重启项目
2. process-pool 进程池

## 开发整体规划

2.0整体开发分为三个阶段
1. 核心开发阶段，框架基础
2. 组件开发阶段，增加常用组件，丰富功能
3. 扩展开发阶段，提供各种各样的组件，完善生态系统


### 核心开发阶段

| 组件 | 名称 |
| --- | --- |
| core | 核心组件 |
| annotation | 注解组件 |
| bean | 容器对象组件 |
| aop | 切面组件 |
| event | 事件组件 |
| config | 配置组件 |
| console | 命令行组件 |
|http-message|抽象接口封装|
| tcp-server | TCP服务组件 |
| udp-server | UDP服务组件 |
| ws-server | Websocket服务组件 |
| http-server | HTTP服务组件 |
| rpc | RPC核心组件 |
| rpc-server | RPC服务端组件 |
| rpc-client | RPC客户端组件 |
| task | 任务组件 |
| i18n | 国际化组件 |
| database | 数据库组件 |
|guzzle|http客户端|
| cache | 缓存组件 |
| log | 日志组件 |

### 组件开发阶段
| 组件 | 名称 |
| --- | --- |
| connect-pool | 连接池 |
| process-pool| 进程池 |
|memory|内存管理组件|
|swoft-cli|进程监控更新组件|
|devtool|工具组件|
|view|视图|
|session|会话组件|
|auth|授权组件|
|rabbitmq|rabbitmq组件|
|kafka|kafka组件开发|
|ealsticsearch|搜索组件|
|pgsql|扩展支持|
|mongodb|扩展支持|
|debug|调试组件|
|swagger|API组件|


### 扩展开发阶段


### 组件详解

#### annotation

注解组件，主要负责注解扫描，注解扩展。

注解扫描规则

```php
/**
* 每个组件会有一个组件扫描加载类(根目录下)，现在有两种方式
* 1. 默认查找vendor/xx/xxx/下面所有依赖，查找是否存在AnnotationClassLoader,
* 存在开始读取扫描信息，扫描
* 2. 默认查找swoft/xxx 下所有依赖，流程和方式1一样，
* 用户自定义的组件通过读取配置的classLoader集合
*/
class XxxClassLoader extend AnnotationClassLoader
{
    /**
    * 返回扫描的目录和对应的命名空间(可以多个)和其它信息
    */
    public function classLoader() : array
    {
        // ...
    }
}
```

注解解析规则
```php
/**
* 定义任意一个注解
*/
class Bean{
    ......
}

/**
* 定义注解解析器并指定解析对于的注解，一个注解可以有多个解析器，解析器可以放在任意一个组件(能扫描的位置)
* 
* @AnnotationParser(Bean::class)
*/
class xxxParser{
    // .....
}

/**
* @AnnotationParser(Bean::class)
*/
class xxxParser2{
    // ......
}
```

#### bean

基于 PSR-11 规范，提供容器的管理和初始化，以及对象的注入。考虑是否支持手动创建bean?


```php
/**
* 定义一个bean对象
* 如果定义别名，同时会支持类名和别名注入该对象
* 
* @Bean("alias")
*/
class Xxx
{
    /**
    * 类型注入
    *
    * @var Object
    * @Inject()
    */
    private $object;
    
    /**
    * 别名注入
    *
    * @var Object
    * @Inject("alias")
    */
    private $object;
}
```

#### AOP

切面编程组件，采用AST抽象语法树,使用和1.x基本一致。

```php
/**
 * the test of aspcet
 *
 * @Aspect()
 *
 * @PointBean(
 *     include={AopBean::class},
 * )
 * @PointAnnotation(
 *     include={
 *      Cacheable::class,
 *      CachePut::class
 *      }
 *  )
 * @PointExecution(
 *     include={
 *      "Swoft\Testing\Aop\RegBean::reg.*",
 *     }
 * )
 */
class AllPointAspect
{
    /**
     * @Before()
     */
    public function before()
    {
        var_dump(' before1 ');
    }

    /**
     * @After()
     */
    public function after()
    {
        var_dump(' after1 ');
    }

    /**
     * @AfterReturning()
     */
    public function afterReturn(JoinPoint $joinPoint)
    {
        $result = $joinPoint->getReturn();
        return $result.' afterReturn1 ';
    }

    /**
     * @Around()
     * @param ProceedingJoinPoint $proceedingJoinPoint
     * @return mixed
     */
    public function around(ProceedingJoinPoint $proceedingJoinPoint)
    {
        $this->test .= ' around-before1 ';
        $result = $proceedingJoinPoint->proceed();
        $this->test .= ' around-after1 ';
        return $result.$this->test;
    }

    /**
     * @AfterThrowing()
     */
    public function afterThrowing()
    {
        echo "aop=1 afterThrowing !\n";
    }
}
```

#### event

基于 PSR-14 规范实现事件管理。考虑是否只支持注解事件？考虑是否先1.x一样提供统一操作的快捷方式（Swoft::xxx）?

事件规则
1. swoole 所有事件名称，`swoole.xxx.xx` 
2. swoft 框架所有事件名称 `swoft.xxx.xx`
3. 所有事件指定一个注解@Listener


```php
/**
* 定义事件监听器(监听swoole事件、swoft框架事件、用户自定义事件)
* 
* @Listener("xxx.xxx")
*/
class Xxxx implements EventHandlerInterface
{
    /**
     * @param EventInterface $event
     */
    public function handle(EventInterface $event)
    {
        // do something ....
    }
}

// 手动触发事件
Event::trigger('xxx.xxx', ...)
```

#### config

配置组件支持env/yaml两种方式，提供pull/push两种方式的扩展，默认加载本地配置文件。考虑是否同时支持两种？

```php
class Xxx
{
    /**
    * 注入配置文件内容，数组`.`关联关系
    * @Value("xxx.xx.xx")
    */
    private $configValue;
}
```

Bean对象配置还是用php文件方便简单。
1. 使用保留1.x`env()`函数，新增`config()`函数，Bean配置中使用读取配置
2. 使用`${beanName}` 格式，配置注入Bean对象，可以是系统或自定义的
3. 不再提供1.x `${xx.xx.xx}` 方式注入配置文件信息

```php
return [
    'httpRouter'       => [
        // 构造函数参数初始化
        [
            config('xx'), // 配置参数
            config('xxx.xx'),   // 配置参数
            '1',    // 普通参数
            '${lineFormatter}'  // 对象
        ]
        'ignoreLastSlash'  => false,
        // evn配置文件注入
        'tmpCacheNumber' => env('tmpCacheNumber'),  // 加载env配置
        // yaml配置文件注入       
        'matchAllCount'       =>config('xxx.xx.'),// 读取配置文件中的值
        '__option' =>[
            'alias'=> 'xxx',    // 别名
            'scope'=> 'xxx',    // 实例对象类型，单列......
        ]
    ],
    'applicationHandler' => [
        'class'     => \Swoft\Log\FileHandler::class,
        'logFile'   => '@runtime/logs/error.log',
        'formatter' => '${lineFormatter}',  // 配置注入bean
    ],
];
```
#### console

命令行组件，命令行提供协程和非协程序两种模式。

```php
/**
 * Test command
 *
 * @Command(coroutine=true, name='test', enable=false)
 */
class TestCommand
{
    /**
     * Generate CLI command controller class
     * @Usage {fullCommand} CLASS_NAME SAVE_DIR [--option ...]
     * @Arguments
     *   name       The class name, don't need suffix and ext.(eg. <info>demo</info>)
     *   dir        The class file save dir(default: <info>@app/Commands</info>)
     * @Options
     *   -y, --yes BOOL             Whether to ask when writing a file. default is: <info>True</info>
     *   -o, --override BOOL        Force override exists file. default is: <info>False</info>
     *   -n, --namespace STRING     The class namespace. default is: <info>App\Commands</info>
     *   --suffix STRING            The class name suffix. default is: <info>Command</info>
     *   --tpl-file STRING          The template file name. default is: <info>command.stub</info>
     *   --tpl-dir STRING           The template file dir path.(default: devtool/res/templates)
     * @Example
     *   <info>{fullCommand} demo</info>     Gen DemoCommand class to `@app/Commands`
     *
     * @param Input  $input
     * @param Output $output
     *
     * @Mapping("test2")
     */
    public function test(Input $input, Output $output)
    {
        App::error('this is eror');
        App::trace('this is trace');
        Coroutine::create(function (){
            App::error('this is eror child');
            App::trace('this is trace child');
        });

        var_dump('test', $input, $output, Coroutine::id(),Coroutine::tid());
    }
}
```

#### websocket

websocket组件和之前差不多，不会有过多改变。新增一个@Ws注解，和@WebSocket功能一样。

```php
/**
 * Class EchoController
 * @package App\WebSocket
 * @WebSocket("/echo")
 */
class EchoController implements HandlerInterface
{
    /**
     * 在这里你可以验证握手的请求信息
     * - 必须返回含有两个元素的array
     *  - 第一个元素的值来决定是否进行握手
     *  - 第二个元素是response对象
     * - 可以在response设置一些自定义header,body等信息
     * @param Request $request
     * @param Response $response
     * @return array
     * [
     *  self::HANDSHAKE_OK,
     *  $response
     * ]
     */
    public function checkHandshake(Request $request, Response $response): array
    {
        return [self::HANDSHAKE_OK, $response];
    }

    /**
     * @param Server $server
     * @param Request $request
     * @param int $fd
     */
    public function onOpen(Server $server, Request $request, int $fd)
    {
        $server->push($fd, 'hello, welcome! :)');
    }

    /**
     * @param Server $server
     * @param Frame $frame
     */
    public function onMessage(Server $server, Frame $frame)
    {
        $server->push($frame->fd, 'I have received message: ' . $frame->data);
    }

    /**
     * on connection closed
     * @param Server $server
     * @param int $fd
     */
    public function onClose(Server $server, int $fd)
    {
        // you can do something. eg. record log, unbind user...
    }
}
```

#### http-server

提供http服务，整体和1.x差不多，不会有大改。

```php
/**
 * @Controller(prefix="/users")
 */
class RouteController
{
    /**
     * @RequestMapping("list")
     * @RequestMapping()
     * @RequestMapping(route="index")
     * @RequestMapping(route="index", method=RequestMethod::GET)
     * @RequestMapping(route="index",   method={RequestMethod::POST,RequestMethod::PUT})
     */
    public function index(): string
    {
    }
 }
```

#### rpc-server

提供RPC服务，默认支持jsonrpc协议，预留协议修改、服务注册、发现interface扩展。去掉历史包袱，defer操作。去掉版本号的支持，因为不常用，增加复杂度。

```php
/**
 * Demo servcie
 *
 * @Service() // 实现了接口 DemoInterface
 */
class DemoService implements DemoInterface
{
    public function getUsers(array $ids):array
    {
        return [$ids];
    }

    public function getUser(string $id):array
    {
        return [$id];
    }

    /**
     * @param int    $type
     * @param int    $uid
     * @param string $name
     * @param float  $price
     * @param string $desc  default value
     * @return array
     */
    public function getUserByCond(int $type, int $uid, string $name, float $price, string $desc = "desc"):array
    {
        return [$type, $uid, $name, $price, $desc];
    }
}
```

#### rpc-client

整体和之前使用一样，去掉连接池、容器、降级等操作(预留扩展)。

```php
/**
 * rpc controller test
 *
 * @Controller(prefix="rpc")
 */
class RpcController
{

    /**
     * @Reference("user")
     *
     * @var DemoInterface
     */
    private $demoService;

    /**
     * @Reference(name="user")
     *
     * @var DemoInterface
     */
    private $demoServiceV2;

    /**
     * @RequestMapping(route="call")
     * @return array
     */
    public function call()
    {
        $version  = $this->demoService->getUser('11');

        return [
            'version'  => $version,
        ];
    }

}
```
#### task

任务和1.x基本一致，丢弃定时任务功能。任务里面默认开启协程操作，不再支持阻塞模式。

```
/**
 * @Task("demo")
 */
class DemoTask
{

    /**
     * Deliver coroutine task
     */
    public function coroutineJob(string $p1, string $p2): string
    {
        return sprintf('co-%s-%s', $p1, $p2);
    }

    /**
     * Deliver async task
     */
    public function asyncJob(string $p1, string $p2)
    {
        // Do anything you want.
        return sprintf('async-%s-%s', $p1, $p2);
    }

}

// 投递任务协程和异步任务
task_co('demo', 'coroutineJob', ['p1', 'p2']);
task_async('demo', 'asyncJob', ['p1', 'p2']);
```

#### i18n

国际化之前实现很简单，通过printf()，格式实现，考虑是否重新实现一套，还是使用现在这种方式？

```
[root@0dd3950e175b languages]# tree 
.
|-- en
|   |-- default.php
|   `-- msg.php
`-- zh
    |-- default.php
    `-- msg.php
    
// 配置语言
return [
    'body' => '这是一条消息 [%s] %d',
];

// 使用翻译
$data[] = translate('title', [], 'zh');
$data[] = translate('title', [], 'en');
$data[] = translate('msg.body', ['Swoft', 1], 'zh');
$data[] = translate('msg.body', ['Swoft', 2], 'en');
```

#### 数据库

数据库整体重构，AR和builder兼容laravel使用习惯，实体定义和1.x差不多。

1. 工具数据生成实体
2. 实体生成数据库表结构
3. 迁移脚本的支持，迁移脚本生成工具

```
/**
* 去掉@Talbe注解，新增表命名前缀支持
* 
* @Entity(table="xx_user", instance="default")
*/
class User extend Model
{
 /**
     * @Column 注解支持字段映射，类型验证，以及参数内容验证
     *
     * @Column(name="age", type=Types::INT)
     * @var int
     */
    private $age = 0;

    /**
     * 性别
     *
     * @Column(name="sex", type="int")
     * @var int
     */
    private $sex = 0;
}
```

迁移脚本

```
/**
* 相同版本的多个迁移脚本在一个事物里面执行和回滚。
* 
* @Migration(version='2.1.1')
*/
class AddXxxTable extend XxxxInterface
{
    /**
    * 只支持SQL，不支持其它操作方式，操作简单直接
    */
    public function up():bool
    {
        $sql = '';
        
        return $this->execute($sql);
    }
    
    public function down():bool
    {
        return true;
    }
}
```



