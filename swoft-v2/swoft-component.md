# Components Design

## Component Skeleton

```text
├── src/
│   ├── Annotation/  -------- 组件注解类定义
│   ├── Concern/
│   ├── Contract/
│   ├── Exception/
│   ├── Helper/
│   ├── Listener/
│   ├── AutoLoader.php  -------- 组件扫描等信息培训
├── test/   ------ 单元测试代码目录
│   ├── case/
│   └── bootstrap.php
├── LICENSE
├── README.md
├── composer.json
└── phpunit.xml
```

## 组件注解收集

- 通过 composer 的 `ClassLoader` 对象得到所有的psr4 加载注册信息
- 找到每个psr4命名空间所对应的目录，查看是否有 swoft 需要的 `AutoLoader.php`
  - 允许在启动application时，设置跳过扫描一些确定的命名空间以加快速度。 默认跳过扫描 `'Psr\\', 'PHPUnit\\', 'Symfony\\'` 几个公共的命名空间
- 加载各个组件的 `AutoLoader.php` 文件。通过它的配置**有目的**的扫描指定的路径，_避免像 1.0 一样扫描了很多无效的目录_
- `AutoLoader` 必须实现 `LoaderInterface`, 同时可以选择实现其他几个有用的interface
  - 实现 `LoaderInterface` 可以定义要扫描的目录
  - 实现 `DefinitionInterface` 可以定义一个数组来配置一些组件内的bean
  - 实现 `ComponentInterface` 除了同时支持上面的配置外，还可以自定义**是否启用**当前组件以及添加一些组件描述
- 开始解析每个组件的 `AutoLoader`，收集注解信息，bean配置等
  - 没有启用的组件，将会跳过解析它的 `AutoLoader::class` 扫描配置
  - 同样允许在启动application时，设置 **禁用** 指定的 `AutoLoader::class` 以达到禁止扫描这个组件的目的

## AutoLoader 示例

`AutoLoader.php` example: 


```php
<?php declare(strict_types=1);

namespace Swoft\WebSocket\Server;

use Swoft\Helper\ComposerJSON;
use Swoft\Server\Swoole\SwooleEvent;
use Swoft\SwoftComponent;
use Swoft\WebSocket\Server\Router\Router;
use Swoft\WebSocket\Server\Swoole\CloseListener;
use Swoft\WebSocket\Server\Swoole\HandShakeListener;
use Swoft\WebSocket\Server\Swoole\MessageListener;

/**
 * Class AutoLoader
 *
 * @since 2.0
 * @package Swoft\WebSocket\Server
 */
class AutoLoader extends SwoftComponent
{
    /**
     * @return bool
     */
    public function enable(): bool
    {
        return false;
    }

    /**
     * Get namespace and dir
     *
     * @return array
     * [
     *     namespace => dir path
     * ]
     */
    public function getPrefixDirs(): array
    {
        return [__NAMESPACE__ => __DIR__];
    }

    /**
     * Metadata information for the component.
     *
     * @return array
     * @see ComponentInterface::getMetadata()
     */
    public function metadata(): array
    {
        $jsonFile = \dirname(__DIR__) . '/composer.json';

        return ComposerJSON::open($jsonFile)->getMetadata();
    }

    /**
     * @return array
     * @throws \ReflectionException
     * @throws \Swoft\Bean\Exception\ContainerException
     */
    public function coreBean(): array
    {
        return [
            'wsServer'     => [
                'on' => [
                    // http
                    // SwooleEvent::REQUEST   => \bean(RequestListener::class),
                    // websocket
                    SwooleEvent::HANDSHAKE => \bean(HandShakeListener::class),
                    SwooleEvent::MESSAGE   => \bean(MessageListener::class),
                    SwooleEvent::CLOSE     => \bean(CloseListener::class),
                ]
            ],
            'wsRouter'     => [
                'class' => Router::class,
            ],
            'wsDispatcher' => [
                'class' => Dispatcher::class,
            ],
        ];
    }
}
```
