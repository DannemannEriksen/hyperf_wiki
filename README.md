# hyperf_wiki
Hyperf 是基于 Swoole 4.5+ 实现的高性能、高灵活性的 PHP 协程框架，内置协程服务器及大量常用的组件，性能较传统基于 PHP-FPM 的框架有质的提升，提供超高性能的同时，也保持着极其灵活的可扩展性，标准组件均基于 PSR 标准 实现，基于强大的依赖注入设计，保证了绝大部分组件或类都是 可替换 与 可复用 的。

框架组件库除了常见的协程版的 MySQL 客户端、Redis 客户端，还为您准备了协程版的 Eloquent ORM、WebSocket 服务端及客户端、JSON RPC 服务端及客户端、GRPC 服务端及客户端、Zipkin/Jaeger (OpenTracing) 客户端、Guzzle HTTP 客户端、Elasticsearch 客户端、Consul 客户端、ETCD 客户端、AMQP 组件、Apollo 配置中心、阿里云 ACM 应用配置管理、ETCD 配置中心、基于令牌桶算法的限流器、通用连接池、熔断器、Swagger 文档生成、Swoole Tracker、视图引擎、Snowflake 全局 ID 生成器 等组件，省去了自己实现对应协程版本的麻烦。

Hyperf 还提供了 基于 PSR-11 的依赖注入容器、注解、AOP 面向切面编程、基于 PSR-15 的中间件、自定义进程、基于 PSR-14 的事件管理器、Redis/RabbitMQ 消息队列、自动模型缓存、基于 PSR-16 的缓存、Crontab 秒级定时任务、国际化、Validation 表单验证器 等非常便捷的功能，满足丰富的技术场景和业务场景，开箱即用。


安装
服务器要求
Hyperf 对系统环境有一些要求，仅可运行于 Linux 和 Mac 环境下，但由于 Docker 虚拟化技术的发展，在 Windows 下也可以通过 Docker for Windows 来作为运行环境，通常来说 Mac 环境下，我们更推荐本地环境部署，以避免 Docker 共享磁盘缓慢导致 Hyperf 启动速度慢的问题。

hyperf\hyperf-docker 项目内已经为您准备好了各种版本的 Dockerfile ，或直接基于已经构建好的 hyperf\hyperf 镜像来运行。

当您不想采用 Docker 来作为运行的环境基础时，您需要确保您的运行环境达到了以下的要求：

PHP >= 7.3
Swoole PHP 扩展 >= 4.5，并关闭了 Short Name
OpenSSL PHP 扩展
JSON PHP 扩展
PDO PHP 扩展 （如需要使用到 MySQL 客户端）
Redis PHP 扩展 （如需要使用到 Redis 客户端）
Protobuf PHP 扩展 （如需要使用到 gRPC 服务端或客户端）
安装 Hyperf
Hyperf 使用 Composer 来管理项目的依赖，在使用 Hyperf 之前，请确保你的运行环境已经安装好了 Composer。

通过 Composer 创建项目
hyperf/hyperf-skeleton 项目是我们已经为您准备好的一个骨架项目，内置了一些常用的组件及相关配置的文件及结构，是一个可以快速用于业务开发的 Web 项目基础，在安装时，您可根据您自身的需求，对组件依赖进行选择。
执行下面的命令可以于当前所在位置创建一个 hyperf-skeleton 项目

composer create-project hyperf/hyperf-skeleton
Copy to clipboardErrorCopied
Docker 下开发
假设您的本机环境并不能达到 Hyperf 的环境要求，或对于环境配置不是那么熟悉，那么您可以通过以下方法来运行及开发 Hyperf 项目：

启动镜像
可以根据实际情况，映射到宿主机对应的目录，以下以 /workspace/skeleton 为例

如果 docker 启动时开启了 selinux-enabled 选项，容器内访问宿主机资源就会受限，所以启动容器时可以增加 --privileged -u root 选项

docker run --name hyperf \
-v /workspace/skeleton:/data/project \
-p 9501:9501 -it \
--privileged -u root \
--entrypoint /bin/sh \
hyperf/hyperf:7.4-alpine-v3.11-swoole
Copy to clipboardErrorCopied
将 Composer 镜像设置为阿里云镜像，加速国内下载速度
视情况而定

composer config -g repo.packagist composer https://mirrors.aliyun.com/composer
Copy to clipboardErrorCopied
创建项目
cd /data/project
composer create-project hyperf/hyperf-skeleton
Copy to clipboardErrorCopied
启动项目
cd hyperf-skeleton
php bin/hyperf.php start
Copy to clipboardErrorCopied
接下来，就可以在宿主机 /workspace/skeleton/hyperf-skeleton 中看到您安装好的代码了。 由于 Hyperf 是持久化的 CLI 框架，当您修改完您的代码后，通过 CTRL + C 终止当前启动的进程实例，并重新执行 php bin/hyperf.php start 启动命令即可。

存在兼容性问题的扩展
由于 Hyperf 基于 Swoole 协程实现，而 Swoole 4 带来的协程功能是 PHP 前所未有的，所以与不少扩展都仍存在兼容性的问题。
以下扩展（包括但不限于）都会造成一定的兼容性问题，不能与之共用或共存：

xhprof
xdebug
blackfire
trace
uopz

快速入门
为了让您更快的了解 Hyperf 的使用，本章节将以 创建一个 HTTP Server 为例，通过对路由、控制器的定义实现一个简单的 Web 服务，但 Hyperf 不止于此，完善的服务治理、gRPC 服务、注解、AOP 等功能将由具体的章节阐述。

定义访问路由
Hyperf 使用 nikic/fast-route 作为默认的路由组件并提供服务，您可以很方便的在 config/routes.php 中定义您的路由。
不仅如此，框架还提供了极其强大和方便灵活的 注解路由 功能，关于路由的详情文档请查阅 路由 章节

通过配置文件定义路由
路由的文件位于 hyperf-skeleton 项目的 config/routes.php ，下面是一些常用的用法示例。

<?php
use Hyperf\HttpServer\Router\Router;

// 此处代码示例为每个示例都提供了三种不同的绑定定义方式，实际配置时仅可采用一种且仅定义一次相同的路由

// 设置一个 GET 请求的路由，绑定访问地址 '/get' 到 App\Controller\IndexController 的 get 方法
Router::get('/get', 'App\Controller\IndexController::get');
Router::get('/get', 'App\Controller\IndexController@get');
Router::get('/get', [\App\Controller\IndexController::class, 'get']);

// 设置一个 POST 请求的路由，绑定访问地址 '/post' 到 App\Controller\IndexController 的 post 方法
Router::post('/post', 'App\Controller\IndexController::post');
Router::post('/post', 'App\Controller\IndexController@post');
Router::post('/post', [\App\Controller\IndexController::class, 'post']);

// 设置一个允许 GET、POST 和 HEAD 请求的路由，绑定访问地址 '/multi' 到 App\Controller\IndexController 的 multi 方法
Router::addRoute(['GET', 'POST', 'HEAD'], '/multi', 'App\Controller\IndexController::multi');
Router::addRoute(['GET', 'POST', 'HEAD'], '/multi', 'App\Controller\IndexController@multi');
Router::addRoute(['GET', 'POST', 'HEAD'], '/multi', [\App\Controller\IndexController::class, 'multi']);
Copy to clipboardErrorCopied
通过注解来定义路由
Hyperf 提供了极其强大和方便灵活的 注解 功能，在路由的定义上也毫无疑问地提供了注解定义的方式，Hyperf 提供了 @Controller 和 @AutoController 两种注解来定义一个 Controller，此处仅做简单的说明，更多细节请查阅 路由 章节。

通过 @AutoController 注解定义路由
@AutoController 为绝大多数简单的访问场景提供路由绑定支持，使用 @AutoController 时则 Hyperf 会自动解析所在类的所有 public 方法并提供 GET 和 POST 两种请求方式。

使用 @AutoController 注解时需 use Hyperf\HttpServer\Annotation\AutoController; 命名空间；

驼峰命名的控制器，会自动转化为蛇形路由，以下为控制器与实际路由的对应关系示例：

控制器	注解	访问路由
MyDataController	@AutoController()	/my_data/index
MydataController	@AutoController()	/mydata/index
MyDataController	@AutoController(prefix="/data")	/data/index
<?php
declare(strict_types=1);

namespace App\Controller;

use Hyperf\HttpServer\Contract\RequestInterface;
use Hyperf\HttpServer\Annotation\AutoController;

/**
 * @AutoController()
 */
class IndexController
{
    // Hyperf 会自动为此方法生成一个 /index/index 的路由，允许通过 GET 或 POST 方式请求
    public function index(RequestInterface $request)
    {
        // 从请求中获得 id 参数
        $id = $request->input('id', 1);
        return (string)$id;
    }
}
Copy to clipboardErrorCopied
通过 @Controller 注解定义路由
@Controller 为满足更细致的路由定义需求而存在，使用 @Controller 注解用于表明当前类为一个 Controller 类，同时需配合 @RequestMapping 注解来对请求方法和请求路径进行更详细的定义。
我们也提供了多种快速便捷的 Mapping 注解，如 @GetMapping、@PostMapping、@PutMapping、@PatchMapping、@DeleteMapping 5 种便捷的注解用于表明允许不同的请求方法。

使用 @Controller 注解时需 use Hyperf\HttpServer\Annotation\Controller; 命名空间；
使用 @RequestMapping 注解时需 use Hyperf\HttpServer\Annotation\RequestMapping; 命名空间；
使用 @GetMapping 注解时需 use Hyperf\HttpServer\Annotation\GetMapping; 命名空间；
使用 @PostMapping 注解时需 use Hyperf\HttpServer\Annotation\PostMapping; 命名空间；
使用 @PutMapping 注解时需 use Hyperf\HttpServer\Annotation\PutMapping; 命名空间；
使用 @PatchMapping 注解时需 use Hyperf\HttpServer\Annotation\PatchMapping; 命名空间；
使用 @DeleteMapping 注解时需 use Hyperf\HttpServer\Annotation\DeleteMapping; 命名空间；

<?php
declare(strict_types=1);

namespace App\Controller;

use Hyperf\HttpServer\Contract\RequestInterface;
use Hyperf\HttpServer\Annotation\Controller;
use Hyperf\HttpServer\Annotation\RequestMapping;

/**
 * @Controller()
 */
class IndexController
{
    // Hyperf 会自动为此方法生成一个 /index/index 的路由，允许通过 GET 或 POST 方式请求
    /**
     * @RequestMapping(path="index", methods="get,post")
     */
    public function index(RequestInterface $request)
    {
        // 从请求中获得 id 参数
        $id = $request->input('id', 1);
        return (string)$id;
    }
}
Copy to clipboardErrorCopied
处理 HTTP 请求
Hyperf 是完全开放的，本质上没有规定您必须基于某种模式下去实现请求的处理，您可以采用传统的 MVC 模式，亦可以采用 RequestHandler 模式 来进行开发。
我们以 MVC 模式 来举个例子：
在 app 文件夹内创建一个 Controller 文件夹并创建 IndexController.php 如下，index 方法内从请求中获取了 id 参数，并转换为 字符串 类型返回到客户端。

<?php
declare(strict_types=1);

namespace App\Controller;

use Hyperf\HttpServer\Contract\RequestInterface;
use Hyperf\HttpServer\Annotation\AutoController;

/**
 * @AutoController()
 */
class IndexController
{
    // Hyperf 会自动为此方法生成一个 /index/index 的路由，允许通过 GET 或 POST 方式请求
    public function index(RequestInterface $request)
    {
        // 从请求中获得 id 参数
        $id = $request->input('id', 1);
        // 转换 $id 为字符串格式并以 plain/text 的 Content-Type 返回 $id 的值给客户端
        return (string)$id;
    }
}
Copy to clipboardErrorCopied
依赖自动注入
依赖自动注入是 Hyperf 提供的一个非常强大的功能，也是保持框架灵活性的根基。
Hyperf 提供了两种注入方式，一种是大家常见的通过构造函数注入，另一种是通过 @Inject 注解注入，下面我们举个例子并分别以两种方式展示注入的实现；
假设我们存在一个 \App\Service\UserService 类，类中存在一个 getInfoById(int $id) 方法通过传递一个 id 并最终返回一个用户实体，由于返回值并不是我们这里所需要关注的，所以不做过多阐述，我们要关注的是在任意的类中获取 UserService 并调用里面的方法，一般的方法是通过 new UserService() 来实例化该服务类，但在 Hyperf 下，我们有更优的解决方法。

通过构造函数注入
只需在构造函数的参数内声明参数的类型，Hyperf 会自动注入对应的对象或值。

<?php
declare(strict_types=1);

namespace App\Controller;

use Hyperf\HttpServer\Contract\RequestInterface;
use Hyperf\HttpServer\Annotation\AutoController;
use App\Service\UserService;

/**
 * @AutoController()
 */
class IndexController
{
    /**
     * @var UserService
     */
    private $userService;

    // 在构造函数声明参数的类型，Hyperf 会自动注入对应的对象或值
    public function __construct(UserService $userService)
    {
        $this->userService = $userService;
    }

    // /index/info
    public function info(RequestInterface $request)
    {
        $id = $request->input('id', 1);
        return $this->userService->getInfoById((int)$id);
    }
}
Copy to clipboardErrorCopied
通过 @Inject 注解注入
只需对对应的类属性通过 @var 声明参数的类型，并使用 @Inject 注解标记属性 ，Hyperf 会自动注入对应的对象或值。

使用 @Inject 注解时需 use Hyperf\Di\Annotation\Inject; 命名空间；

<?php
declare(strict_types=1);

namespace App\Controller;

use Hyperf\HttpServer\Contract\RequestInterface;
use Hyperf\HttpServer\Annotation\AutoController;
use Hyperf\Di\Annotation\Inject;
use App\Service\UserService;

/**
 * @AutoController()
 */
class IndexController
{
    /**
     * @Inject()
     * @var UserService
     */
    private $userService;

    // /index/info
    public function info(RequestInterface $request)
    {
        $id = $request->input('id', 1);
        return $this->userService->getInfoById((int)$id);
    }
}
Copy to clipboardErrorCopied
通过上面的示例我们不难发现 $userService 在没有实例化的情况下， 属性对应的类对象被自动注入了。
不过这里的案例并未真正体现出依赖自动注入的好处及其强大之处，我们假设一下 UserService 也存在很多的依赖，而这些依赖同时又存在很多其它的依赖时，new 实例化的方式就需要手动实例化很多的对象并调整好对应的参数位，而在 Hyperf 里我们就无须手动管理这些依赖，只需要声明一下最终使用的类即可。
而当 UserService 需要发生替换等剧烈的内部变化时，比如从一个本地服务替换成了一个 RPC 远程服务，也只需要通过配置调整依赖中 UserService 这个键值对应的类为新的 RPC 服务类即可。

启动 Hyperf 服务
由于 Hyperf 内置了协程服务器，也就意味着 Hyperf 将以 CLI 的形式去运行，所以在定义好路由及实际的逻辑代码之后，我们需要在项目根目录并通过命令行运行 php bin/hyperf.php start 来启动服务。
当 Console 界面显示服务启动后便可通过 cURL 或 浏览器对服务正常发起访问了，默认服务会提供一个首页 http://127.0.0.1:9501/，对于本章示例引导的情况下，也就是上面的例子所对应的访问地址为 http://127.0.0.1:9501/index/info?id=1。

重新加载代码
由于 Hyperf 是持久化的 CLI 应用，也就意味着一旦进程启动，已解析的 PHP 代码会持久化在进程中，也就意味着启动服务后您再修改的 PHP 代码不会改变已启动的服务，如您希望服务重新加载您修改后的代码，您需要通过在启动的 Console 中键入 CTRL + C 终止服务，再重新执行启动命令 php bin/hyperf.php start 完成启动和重新加载。

Tips: 您也可以将启动 Server 的命令配置在 IDE 上，便可直接通过 IDE 的 启动/停止 操作快捷的完成 启动服务 或 重启服务 的操作。 且非视图开发时可以采用 TDD(Test-Driven Development) 测试驱动开发来进行开发，这样不仅可以省略掉服务重启和频繁切换窗口的麻烦，还可保证接口数据的正确性。

另外，在文档 协程组件库 一章中提供了多种由社区开发者支持的 热更新/热重载 的解决方案，如仍希望采用 热更新/热重载 方案可再深入了解。

多端口监听
Hyperf 支持监听多个端口，但因为 callbacks 中的对象直接从容器中获取，所以相同的 Hyperf\HttpServer\Server::class 会在容器中被覆盖。所以我们需要在依赖关系中，重新定义 Server，确保对象隔离。

WebSocket 和 TCP 等 Server 同理。

config/autoload/dependencies.php

<?php

return [
    'InnerHttp' => Hyperf\HttpServer\Server::class,
];
Copy to clipboardErrorCopied
config/autoload/server.php

<?php
return [
    'servers' => [
        [
            'name' => 'http',
            'type' => Server::SERVER_HTTP,
            'host' => '0.0.0.0',
            'port' => 9501,
            'sock_type' => SWOOLE_SOCK_TCP,
            'callbacks' => [
                Event::ON_REQUEST => [Hyperf\HttpServer\Server::class, 'onRequest'],
            ],
        ],
        [
            'name' => 'innerHttp',
            'type' => Server::SERVER_HTTP,
            'host' => '0.0.0.0',
            'port' => 9502,
            'sock_type' => SWOOLE_SOCK_TCP,
            'callbacks' => [
                Event::ON_REQUEST => ['InnerHttp', 'onRequest'],
            ],
        ],
    ]
];
Copy to clipboardErrorCopied
同时 路由文件，或者 注解 也需要指定对应的 server，如下：

路由文件 config/routes.php
<?php
Router::addServer('innerHttp', function () {
    Router::get('/', 'App\Controller\IndexController@index');
});
Copy to clipboardErrorCopied
注解
<?php

declare(strict_types=1);

namespace App\Controller;

use Hyperf\HttpServer\Annotation\AutoController;

/**
 * @AutoController(server="innerHttp")
 */
class IndexController
{
    public function index()
    {
        return 'Hello World.';
    }
}
Copy to clipboardErrorCopied
事件
除上述提到的 Event::ON_REQUEST 事件，框架还支持其他事件，所有事件名如下。

事件名	备注
Event::ON_REQUEST
Event::ON_START	该事件在 SWOOLE_BASE 模式下无效
Event::ON_WORKER_START
Event::ON_WORKER_EXIT
Event::ON_PIPE_MESSAGE
Event::ON_RECEIVE
Event::ON_CONNECT
Event::ON_HAND_SHAKE
Event::ON_OPEN
Event::ON_MESSAGE
Event::ON_CLOSE
Event::ON_TASK
Event::ON_FINISH
Event::ON_SHUTDOWN
Event::ON_PACKET
Event::ON_MANAGER_START
Event::ON_MANAGER_STOP


常见问题
Inject 或 Value 注解不生效
2.0 使用了构造函数中注入 Inject 和 Value 的功能，以下两种场景，可能会导致注入失效，请注意使用。

原类没有使用 Inject 或 Value，但父类使用了 Inject 或 Value，且原类写了构造函数，同时又没有调用父类构造函数的情况。
这样就会导致原类不会生成代理类，而实例化的时候又调用了自身的构造函数，故没办法执行到父类的构造函数。 所以父类代理类中的方法 __handlePropertyHandler 就不会执行，那么 Inject 或 Value 注解就不会生效。

class ParentClass {
    /**
     * @Inject
     * @var Service
     */
    protected $value;
}

class Origin extends ParentClass
{
    public function __construct() {}
}
Copy to clipboardErrorCopied
原类没有使用 Inject 或 Value，但 Trait 中使用了 Inject 或 Value。
这样就会导致原类不会生成代理类，故没办法执行构造函数里的 __handlePropertyHandler，所以 Trait 的 Inject 或 Value 注解就不会生效。

trait OriginTrait {
    /**
     * @Inject
     * @var Service
     */
    protected $value;
}

class Origin
{
    use OriginTrait;
}
Copy to clipboardErrorCopied
基于上述两种情况，可见 原类 是否生成代理类至关重要，所以，如果使用了带有 Inject 或 Value 的 Trait 和 父类 时，给原类添加一个 Inject，即可解决上述两种情况。


use Hyperf\Contract\StdoutLoggerInterface;

trait OriginTrait {
    /**
     * @Inject
     * @var Service
     */
    protected $trait;
}

class ParentClass {
    /**
     * @Inject
     * @var Service
     */
    protected $value;
}

class Origin extends ParentClass
{
    use OriginTrait;

    /**
     * @Inject
     * @var StdoutLoggerInterface
     */
    protected $logger;
}
Copy to clipboardErrorCopied
Swoole 短名未关闭
[ERROR] Swoole short name have to disable before start server, please set swoole.use_shortname = 'Off' into your php.ini.
Copy to clipboardErrorCopied
您需要在您的 php.ini 配置文件增加 swoole.use_shortname = 'Off' 配置项

注意该配置必须于 php.ini 内配置，无法通过 ini_set() 函数来重写

当然，也可以通过以下的命令来启动服务，在执行 PHP 命令时关闭掉 Swoole 短名功能

php -d swoole.use_shortname=Off bin/hyperf.php start
Copy to clipboardErrorCopied
异步队列消息丢失
如果在使用 async-queue 组件时，发现 handle 中的方法没有执行，请先检查以下几种情况：

Redis 是否与其他人共用，消息被其他人消费走
本地进程是否存在残余，被其他进程消费掉
以下提供万无一失的解决办法：

killall php
修改 async-queue 配置 channel
使用 AMQP 组件报 Swoole\Error: API must be called in the coroutine 错误
可以在 config/autoload/amqp.php 配置文件中将 params.close_on_destruct 改为 false 即可。

使用 Swoole 4.5 版本和 view 组件时访问接口出现 404
使用 Swoole 4.5 版本和 view 组件如果出现接口 404 的问题，可以尝试删除 config/autoload/server.php 文件中的 static_handler_locations 配置项。

此配置下的路径都会被认为是静态文件路由，所以如果配置了/，就会导致所有接口都会被认为是文件路径，导致接口 404。

代码不生效
当碰到修改后的代码不生效的问题，请执行以下命令

composer dump-autoload -o
Copy to clipboardErrorCopied
开发阶段，请不要设置 scan_cacheable 为 true，它会导致 收集器缓存 存在时，不会再次扫描文件。另外，官方骨架包中的 Dockerfile 是默认开启这个配置的，Docker 环境下开发的同学，请注意这里。

当环境变量存在 SCAN_CACHEABLE 时，.env 中无法修改这个配置。

语法错误导致服务无法启动
当项目启动时，抛出类似于以下错误时

Fatal error: Uncaught PhpParser\Error: Syntax error, unexpected T_STRING on line 27 in vendor/nikic/php-parser/lib/PhpParser/ParserAbstract.php:315
Copy to clipboardErrorCopied
可以执行脚本 composer analyse，对项目进行静态检测，便可以找到出现问题的代码段。

此问题通常是由于 zircote/swagger 的 3.0.5 版本更新导致, 详情请见 #834 。 如果安装了 hyperf/swagger 建议将 zircote/swagger 的版本锁定在 3.0.4

内存限制太小导致项目无法运行
PHP 默认的 memory_limit 只有 128M，因为 Hyperf 使用了 BetterReflection，不使用扫描缓存时，会消耗大量内存，所以可能会出现内存不够的情况。

我们可以使用 php -dmemory_limit=-1 bin/hyperf.php start 运行, 或者修改 php.ini 配置文件

# 查看 php.ini 配置文件位置
php --ini

# 修改 memory_limit 配置
memory_limit=-1


编程须知
这里收集各种通过 Hyperf 编程前应该知晓的知识点或内容点。

不能通过全局变量获取属性参数
在 PHP-FPM 下可以通过全局变量获取到请求的参数，服务器的参数等，在 Hyperf 和 Swoole 内，都 无法 通过 $_GET/$_POST/$_REQUEST/$_SESSION/$_COOKIE/$_SERVER等$_开头的变量获取到任何属性参数。

通过容器获取的类都是单例
通过依赖注入容器获取的都是进程内持久化的，是多个协程共享的，所以不能包含任何的请求唯一的数据或协程唯一的数据，这类型的数据都通过协程上下文去处理，具体请仔细阅读 依赖注入 和 协程 章节。

项目部署
官方的 Dockerfile 已经完成了以下操作。

线上代码部署时，请务必开启 scan_cacheable。

开启此配置后，首次扫描时会生成代理类和注解缓存，再次启动时，则可以直接使用缓存，极大优化内存使用率和启动速度。因为跳过了扫描阶段，所以会依赖 Composer Class Map，故我们必须要执行 --optimize-autoloader 优化索引。

综上，线上更新代码，重启项目前，需要执行以下命令

# 优化 Composer 索引
composer dump-autoload -o
# 生成代理类和注解缓存
php bin/hyperf.php


生命周期
框架生命周期
Hyperf 是运行于 Swoole 之上的，想要理解透彻 Hyperf 的生命周期，那么理解 Swoole 的生命周期也至关重要。
Hyperf 的命令管理默认由 symfony/console 提供支持*(如果您希望更换该组件您也可以通过改变 skeleton 的入口文件更换成您希望使用的组件)*，在执行 php bin/hyperf.php start 后，将由 Hyperf\Server\Command\StartServer 命令类接管，并根据配置文件 config/autoload/server.php 内定义的 Server 逐个启动。
关于依赖注入容器的初始化工作，我们并没有由组件来实现，因为一旦交由组件来实现，这个耦合就会非常的明显，所以在默认的情况下，是由入口文件来加载 config/container.php 来实现的。

请求与协程生命周期
Swoole 在处理每个连接时，会默认创建一个协程去处理，主要体现在 onRequest、onReceive、onConnect 事件，所以可以理解为每个请求都是一个协程，由于创建协程也是个常规操作，所以一个请求协程里面可能会包含很多个协程，同一个进程内协程之间是内存共享的，但调度顺序是非顺序的，且协程间本质上是相互独立的没有父子关系，所以对每个协程的状态处理都需要通过 协程上下文 来管理。


协程
概念
Hyperf 是运行于 Swoole 4 的协程之上的，这也是 Hyperf 能提供高性能的其中一个很大的因素。

PHP-FPM 的运作模式
在聊协程是什么之前，我们先聊聊传统 PHP-FPM 架构的运作模式，PHP-FPM 是一个多进程的 FastCGI 管理程序，是绝大多数 PHP 应用所使用的运行模式。假设我们使用 Nginx 提供 HTTP 服务（Apache 同理），所有客户端发起的请求最先抵达的都是 Nginx，然后 Nginx 通过 FastCGI 协议将请求转发给 PHP-FPM 处理，PHP-FPM 的 Worker 进程 会抢占式的获得 CGI 请求进行处理，这个处理指的就是，等待 PHP 脚本的解析，等待业务处理的结果返回，完成后回收子进程，这整个的过程是阻塞等待的，也就意味着 PHP-FPM 的进程数有多少能处理的请求也就是多少，假设 PHP-FPM 有 200 个 Worker 进程，一个请求将耗费 1 秒的时间，那么简单的来说整个服务器理论上最多可以处理的请求也就是 200 个，QPS 即为 200/s，在高并发的场景下，这样的性能往往是不够的，尽管可以利用 Nginx 作为负载均衡配合多台 PHP-FPM 服务器来提供服务，但由于 PHP-FPM 的阻塞等待的工作模型，一个请求会占用至少一个 MySQL 连接，多节点高并发下会产生大量的 MySQL 连接，而 MySQL 的最大连接数默认值为 100，尽管可以修改，但显而易见该模式没法很好的应对高并发的场景。

异步非阻塞系统
在高并发的场景下，异步非阻塞就显得优势明显了，直观的优点表现就是 Worker 进程 不再同步阻塞的去处理一个请求，而是可以同时处理多个请求，无需 I/O 等待，并发能力极强，可以同时发起或维护大量的请求。那么最直观的缺点大家可能也都知道，就是永无止境的回调，业务逻辑必须在对应的回调函数内实现，如果业务逻辑存在多次的 I/O 请求，则会存在很多层的回调函数，下面示例一段 Swoole 1.x 下的异步回调型的伪代码片段。

$db = new swoole_mysql();
$config = array(
    'host' => '127.0.0.1',
    'port' => 3306,
    'user' => 'test',
    'password' => 'test',
    'database' => 'test',
);

$db->connect($config, function ($db, $r) {
    // 从 users 表中查询一条数据
    $sql = 'select * from users where id = 1';
    $db->query($sql, function(swoole_mysql $db, $r) {
        if ($r !== false) {
            // 查询成功后修改一条数据
            $updateSql = 'update users set name="new name" where id = 1';
            $db->query($updateSql, function (swoole_mysql $db, $r) {
                $rows = $db->affected_rows;
                if ($r === true) {
                    return $this->response->end('更新成功');
                }
            });
        }
        $db->close();
    });
});
Copy to clipboardErrorCopied
注意 MySQL 等异步模块已在4.3.0中移除，并转移到了swoole_async。

从上面的代码片段可以看出，每一个操作几乎就需要一个回调函数，在复杂的业务场景中回调的层次感和代码结构绝对会让你崩溃，其实不难看出这样的写法有点类似 JavaScript 上的异步方法的写法，而 JavaScript 也为此提供了不少的解决方案（当然方案是源于其它编程语言），如 Promise，yield + generator, async/await，Promise 则是对回调的一种封装方式，而 yield + generator 和 async/await 则需要在代码上显性的增加一些代码语法标记，这些相对比回调函数来说，不妨都是一些非常不错的解决方案，但是你需要另花时间来理解它的实现机制和语法。
Swoole 协程也是对异步回调的一种解决方案，在 PHP 语言下，Swoole 协程与 yield + generator 都属于协程的解决方案，协程的解决方案可以使代码以近乎于同步代码的书写方式来书写异步代码，显性的区别则是 yield + generator 的协程机制下，每一处 I/O 操作的调用代码都需要在前面加上 yield 语法实现协程切换，每一层调用都需要加上，否则会出现意料之外的错误，而 Swoole 协程的解决方案对比于此就高明多了，在遇到 I/O 时底层自动的进行隐式协程切换，无需添加任何的额外语法，无需在代码前加上 yield，协程切换的过程无声无息，极大的减轻了维护异步系统的心智负担。

协程是什么？
我们已经知道了协程可以很好的解决异步非阻塞系统的开发问题，那么协程本身到底是什么呢？从定义上来说，协程是一种轻量级的线程，由用户代码来调度和管理，而不是由操作系统内核来进行调度，也就是在用户态进行。可以直接的理解为就是一个非标准的线程实现，但什么时候切换由用户自己来实现，而不是由操作系统分配 CPU 时间决定。具体来说，Swoole 的每个 Worker 进程 会存在一个协程调度器来调度协程，协程切换的时机就是遇到 I/O 操作或代码显性切换时，进程内以单线程的形式运行协程，也就意味着一个进程内同一时间只会有一个协程在运行且切换时机明确，也就无需处理像多线程编程下的各种同步锁的问题。
单个协程内的代码运行仍是串行的，放在一个 HTTP 协程服务上来理解就是每一个请求是一个协程，举个例子，假设为请求 A 创建了 协程 A，为 请求 B 创建了 协程 B，那么在处理 协程 A 的时候代码跑到了查询 MySQL 的语句上，这个时候 协程 A 则会触发协程切换，协程 A 就继续等待 I/O 设备返回结果，那么此时就会切换到 协程 B，开始处理 协程 B 的逻辑，当又遇到了一个 I/O 操作便又触发协程切换，再回过来从 协程 A 刚才切走的地方继续执行，如此反复，遇到 I/O 操作就切换到另一个协程去继续执行而非一直阻塞等待。
这里可以发现一个问题就是，*协程 A 的 MySQL 查询操作必须得是一个异步非阻塞的操作，否则会由于阻塞导致协程调度器没法切换到另一个协程继续执行*，这个也是要在协程编程下需要规避的问题之一。

协程与普通线程有哪些区别？
都说协程是一个轻量级的线程，协程和线程都适用于多任务的场景下，从这个角度上来说，协程与线程很相似，都有自己的上下文，可以共享全局变量，但不同之处在于，在同一时间可以有多个线程处于运行状态，但对于 Swoole 协程来说只能有一个，其它的协程都会处于暂停的状态。此外，普通线程是抢占式的，哪个线程能得到资源由操作系统决定，而协程是协作式的，执行权由用户态自行分配。

协程编程注意事项
不能存在阻塞代码
协程内代码的阻塞会导致协程调度器无法切换到另一个协程继续执行代码，所以我们绝不能在协程内存在阻塞代码，假设我们启动了 4 个 Worker 来处理 HTTP 请求（通常启动的 Worker 数量与 CPU 核心数一致或 2 倍），如果代码中存在阻塞，暂且理论的认为每个请求都会阻塞 1 秒，那么系统的 QPS 也将退化为 4/s ，这无疑就是退化成了与 PHP-FPM 类似的情况，所以我们绝对不能在协程中存在阻塞代码。

那么到底哪些是阻塞代码呢？我们可以简单的认为绝大多数你所熟知的非 Swoole 提供的异步函数的 MySQL、Redis、Memcache、MongoDB、HTTP、Socket等客户端，文件操作、sleep/usleep 等均为阻塞函数，这几乎涵盖了所有日常操作，那么要如何解决呢？Swoole 提供了 MySQL、PostgreSQL、Redis、HTTP、Socket 的协程客户端可以使用，同时 Swoole 4.1 之后提供了一键协程化的方法 \Swoole\Runtime::enableCoroutine()，只需在使用协程前运行这一行代码，Swoole 会将 所有使用 php_stream 进行 socket 操作均变成协程调度的异步 I/O，可以理解为除了 curl 绝大部分原生的操作都可以适用，关于此部分可查阅 Swoole 文档 获得更具体的信息。

在 Hyperf 中我们已经为您处理好了这一切，您只需关注 \Swoole\Runtime::enableCoroutine() 仍无法协程化的阻塞代码即可。

不能通过全局变量储存状态
在 Swoole 的持久化应用下，一个 Worker 内的全局变量是 Worker 内共享的，而从协程的介绍我们可以知道同一个 Worker 内还会存在多个协程并存在协程切换，也就意味着一个 Worker 会在一个时间周期内同时处理多个协程（或直接理解为请求）的代码，也就意味着如果使用了全局变量来储存状态可能会被多个协程所使用，也就是说不同的请求之间可能会混淆数据，这里的全局变量指的是 $_GET/$_POST/$_REQUEST/$_SESSION/$_COOKIE/$_SERVER等$_开头的变量、global 变量，以及 static 静态属性。
那么当我们需要使用到这些特性时应该怎么办？

对于全局变量，均是跟随着一个 请求(Request) 而产生的，而 Hyperf 的 请求(Request)/响应(Response) 是由 hyperf/http-message 通过实现 PSR-7 处理的，故所有的全局变量均可以在 请求(Request) 对象中得到相关的值；

对于 global 变量和 static 变量，在 PHP-FPM 模式下，本质都是存活于一个请求生命周期内的，而在 Hyperf 内因为是 CLI 应用，会存在 全局周期 和 请求周期(协程周期) 两种长生命周期。

全局周期，我们只需要创建一个静态变量供全局调用即可，静态变量意味着在服务启动后，任意协程和代码逻辑均共享此静态变量内的数据，也就意味着存放的数据不能是特别服务于某一个请求或某一个协程；
协程周期，由于 Hyperf 会为每个请求自动创建一个协程来处理，那么一个协程周期在此也可以理解为一个请求周期，在协程内，所有的状态数据均应存放于 Hyperf\Utils\Context 类中，通过该类的 get、set 来读取和存储任意结构的数据，这个 Context(协程上下文) 类在执行任意协程时读取或存储的数据都是仅限对应的协程的，同时在协程结束时也会自动销毁相关的上下文数据。
最大协程数限制
对 Swoole Server 通过 set 方法设置 max_coroutine 参数，用于配置一个 Worker 进程最多可存在的协程数量。因为随着 Worker 进程处理的协程数目的增加，其对应占用的内存也会随之增加，为了避免超出 PHP 的 memory_limit 限制，请根据实际业务的压测结果设置该值，Swoole 的默认值为 100000（ Swoole 版本小于 v4.4.0-beta 时默认值为 3000 ）, 在 hyperf-skeleton 项目中默认设置为 100000。

使用协程
创建一个协程
只需通过 co(callable $callable) 或 go(callable $callable) 函数或 Hyperf\Utils\Coroutine::create(callable $callable) 即可创建一个协程，协程内可以使用协程相关的方法和客户端。

判断当前是否处于协程环境内
在一些情况下我们希望判断一些当前是否运行于协程环境内，对于一些兼容协程环境与非协程环境的代码来说会作为一个判断的依据，我们可以通过 Hyperf\Utils\Coroutine::inCoroutine(): bool 方法来得到结果。

获得当前协程的 ID
在一些情况下，我们需要根据 协程 ID 去做一些逻辑，比如 协程上下文 之类的逻辑，可以通过 Hyperf\Utils\Coroutine::id(): int 获得当前的 协程 ID，如不处于协程环境下，会返回 -1。

Channel 通道
类似于 go 语言的 chan，Channel 可为多生产者协程和多消费者协程模式提供支持。底层自动实现了协程的切换和调度。 Channel 与 PHP 的数组类似，仅占用内存，没有其他额外的资源申请，所有操作均为内存操作，无 I/O 消耗，使用方法与 SplQueue 队列类似。
Channel 主要用于协程间通讯，当我们希望从一个协程里返回一些数据到另一个协程时，就可通过 Channel 来进行传递。

主要方法：

Channel->push ：当队列中有其他协程正在等待 pop 数据时，自动按顺序唤醒一个消费者协程。当队列已满时自动 yield 让出控制权，等待其他协程消费数据
Channel->pop ：当队列为空时自动 yield，等待其他协程生产数据。消费数据后，队列可写入新的数据，自动按顺序唤醒一个生产者协程。
下面是一个协程间通讯的简单例子:

<?php
co(function () {
    $channel = new \Swoole\Coroutine\Channel();
    co(function () use ($channel) {
        $channel->push('data');
    });
    $data = $channel->pop();
});
Copy to clipboardErrorCopied
Defer 特性
当我们希望在协程结束时运行一些代码时，可以通过 defer(callable $callable) 函数或 Hyperf\Coroutine::defer(callable $callable) 将一段函数以 栈(stack) 的形式储存起来，栈(stack) 内的函数会在当前协程结束时以 先进后出 的流程逐个执行。

WaitGroup 特性
WaitGroup 是基于 Channel 衍生出来的一个特性，如果接触过 Go 语言，我们都会知道 WaitGroup 这一特性，在 Hyperf 里，WaitGroup 的用途是使得主协程一直阻塞等待直到所有相关的子协程都已经完成了任务后再继续运行，这里说到的阻塞等待是仅对于主协程（即当前协程）来说的，并不会阻塞当前进程。
我们通过一段代码来演示该特性：

<?php
$wg = new \Hyperf\Utils\WaitGroup();
// 计数器加二
$wg->add(2);
// 创建协程 A
co(function () use ($wg) {
    // some code
    // 计数器减一
    $wg->done();
});
// 创建协程 B
co(function () use ($wg) {
    // some code
    // 计数器减一
    $wg->done();
});
// 等待协程 A 和协程 B 运行完成
$wg->wait();
Copy to clipboardErrorCopied
注意 WaitGroup 本身也需要在协程内才能使用

Parallel 特性
Parallel 特性是 Hyperf 基于 WaitGroup 特性抽象出来的一个更便捷的使用方法，我们通过一段代码来演示一下。

<?php
use Hyperf\Utils\Exception\ParallelExecutionException;
use Hyperf\Utils\Coroutine;
use Hyperf\Utils\Parallel;

$parallel = new Parallel();
$parallel->add(function () {
    sleep(1);
    return Coroutine::id();
});
$parallel->add(function () {
    sleep(1);
    return Coroutine::id();
});

try{
    // $results 结果为 [1, 2]
   $results = $parallel->wait();
} catch(ParallelExecutionException $e){
    // $e->getResults() 获取协程中的返回值。
    // $e->getThrowables() 获取协程中出现的异常。
}
Copy to clipboardErrorCopied
注意 Hyperf\Utils\Exception\ParallelExecutionException 异常仅在 1.1.6 版本和更新的版本下会抛出

通过上面的代码我们可以看到仅花了 1 秒就得到了两个不同的协程的 ID，在调用 add(callable $callable) 的时候 Parallel 类会为之自动创建一个协程，并加入到 WaitGroup 的调度去。
不仅如此，我们还可以通过 parallel(array $callables) 函数进行更进一步的简化上面的代码，达到同样的目的，下面为简化后的代码。

<?php
use Hyperf\Utils\Coroutine;

// 传递的数组参数您也可以带上 key 便于区分子协程，返回的结果也会根据 key 返回对应的结果
$result = parallel([
    function () {
        sleep(1);
        return Coroutine::id();
    },
    function () {
        sleep(1);
        return Coroutine::id();
    }
]);
Copy to clipboardErrorCopied
注意 Parallel 本身也需要在协程内才能使用

限制 Parallel 最大同时运行的协程数
当我们添加到 Parallel 里的任务有很多时，假设都是一些请求任务，那么一瞬间发出全部请求很有可能会导致对端服务因为一瞬间接收到了大量的请求而处理不过来，有宕机的风险，所以需要对对端进行适当的保护，但我们又希望可以通过 Parallel 机制来加速这些请求的耗时，那么可以通过在实例化 Parallel 对象时传递第一个参数，来设置最大运行的协程数，比如我们希望最大设置的协程数为 5 ，也就意味着 Parallel 里最多只会有 5 个协程在运行，只有当 5 个里有协程完成结束后，后续的协程才会继续启动，直至所有协程完成任务，示例代码如下：

use Hyperf\Utils\Exception\ParallelExecutionException;
use Hyperf\Utils\Coroutine;
use Hyperf\Utils\Parallel;

$parallel = new Parallel(5);
for ($i = 0; $i < 20; $i++) {
    $parallel->add(function () {
        sleep(1);
        return Coroutine::id();
    });
}

try{
   $results = $parallel->wait();
} catch(ParallelExecutionException $e){
    // $e->getResults() 获取协程中的返回值。
    // $e->getThrowables() 获取协程中出现的异常。
}
Copy to clipboardErrorCopied
Concurrent 协程运行控制
Hyperf\Utils\Coroutine\Concurrent 基于 Swoole\Coroutine\Channel 实现，用来控制一个代码块内同时运行的最大协程数量的特性。

以下样例，当同时执行 10 个子协程时，会在循环中阻塞，但只会阻塞当前协程，直到释放出一个位置后，循环继续执行下一个子协程。

<?php

use Hyperf\Utils\Coroutine\Concurrent;

$concurrent = new Concurrent(10);

for ($i = 0; $i < 15; ++$i) {
    $concurrent->create(function () use ($count) {
        // Do something...
    });
}
Copy to clipboardErrorCopied
协程上下文
由于同一个进程内协程间是内存共享的，但协程的执行/切换是非顺序的，也就意味着我们很难掌控当前的协程是哪一个*(事实上可以，但通常没人这么干)*，所以我们需要在发生协程切换时能够同时切换对应的上下文。
在 Hyperf 里实现协程的上下文管理将非常简单，基于 Hyperf\Utils\Context 类的 set(string $id, $value)、get(string $id, $default = null)、has(string $id)、override(string $id, \Closure $closure) 静态方法即可完成上下文数据的管理，通过这些方法设置和获取的值，都仅限于当前的协程，在协程结束时，对应的上下文也会自动跟随释放掉，无需手动管理，无需担忧内存泄漏的风险。

Hyperf\Utils\Context::set()
通过调用 set(string $id, $value) 方法储存一个值到当前协程的上下文中，如下：

<?php
use Hyperf\Utils\Context;

// 将 bar 字符串以 foo 为 key 储存到当前协程上下文中
$foo = Context::set('foo', 'bar');
// set 方法会再将 value 作为方法的返回值返回回来，所以 $foo 的值为 bar
Copy to clipboardErrorCopied
Hyperf\Utils\Context::get()
通过调用 get(string $id, $default = null) 方法可从当前协程的上下文中取出一个以 $id 为 key 储存的值，如不存在则返回 $default ，如下：

<?php
use Hyperf\Utils\Context;

// 从当前协程上下文中取出 key 为 foo 的值，如不存在则返回 bar 字符串
$foo = Context::get('foo', 'bar');
Copy to clipboardErrorCopied
Hyperf\Utils\Context::has()
通过调用 has(string $id) 方法可判断当前协程的上下文中是否存在以 $id 为 key 储存的值，如存在则返回 true，不存在则返回 false，如下：

<?php
use Hyperf\Utils\Context;

// 从当前协程上下文中判断 key 为 foo 的值是否存在
$foo = Context::has('foo');
Copy to clipboardErrorCopied
Hyperf\Utils\Context::override()
当我们需要做一些复杂的上下文处理，比如先判断一个 key 是否存在，如果存在则取出 value 来再对 value 进行某些修改，然后再将 value 设置回上下文容器中，此时会有比较繁杂的判断条件，可直接通过调用 override 方法来实现这个逻辑，如下：

<?php
use Psr\Http\Message\ServerRequestInterface;
use Hyperf\Utils\Context;

// 从协程上下文取出 $request 对象并设置 key 为 foo 的 Header，然后再保存到协程上下文中
$request = Context::override(ServerRequestInterface::class, function (ServerRequestInterface $request) {
    return $request->withAddedHeader('foo', 'bar');
});
Copy to clipboardErrorCopied
Swoole Runtime Hook Level
框架在入口函数中提供了 SWOOLE_HOOK_FLAGS 常量，如果您需要修改整个项目的 Runtime Hook 等级，比如想要支持 CURL 协程 并且 Swoole 版本为 v4.5.4 之前的版本，可以修改这里的代码，如下。

<?php
! defined('SWOOLE_HOOK_FLAGS') && define('SWOOLE_HOOK_FLAGS', SWOOLE_HOOK_ALL | SWOOLE_HOOK_CURL);


配置
当您使用的是 hyperf/hyperf-skeleton 项目创建的项目时，Hyperf 的所有配置文件均处于根目录下的 config 文件夹内，每个选项都有说明，您可以随时查看并熟悉有哪些选项可以使用。

安装
composer require hyperf/config
Copy to clipboardErrorCopied
配置文件结构
以下结构仅为 Hyperf-Skeleton 所提供的默认配置的情况下的结构，实际情况由于依赖或使用的组件的差异，文件会有差异。

config
├── autoload // 此文件夹内的配置文件会被配置组件自己加载，并以文件夹内的文件名作为第一个键值
│   ├── amqp.php  // 用于管理 AMQP 组件
│   ├── annotations.php // 用于管理注解
│   ├── apollo.php // 用于管理基于 Apollo 实现的配置中心
│   ├── aspects.php // 用于管理 AOP 切面
│   ├── async_queue.php // 用于管理基于 Redis 实现的简易队列服务
│   ├── cache.php // 用于管理缓存组件
│   ├── commands.php // 用于管理自定义命令
│   ├── consul.php // 用于管理 Consul 客户端
│   ├── databases.php // 用于管理数据库客户端
│   ├── dependencies.php // 用于管理 DI 的依赖关系和类对应关系
│   ├── devtool.php // 用于管理开发者工具
│   ├── exceptions.php // 用于管理异常处理器
│   ├── listeners.php // 用于管理事件监听者
│   ├── logger.php // 用于管理日志
│   ├── middlewares.php // 用于管理中间件
│   ├── opentracing.php // 用于管理调用链追踪
│   ├── processes.php // 用于管理自定义进程
│   ├── redis.php // 用于管理 Redis 客户端
│   └── server.php // 用于管理 Server 服务
├── config.php // 用于管理用户或框架的配置，如配置相对独立亦可放于 autoload 文件夹内
├── container.php // 负责容器的初始化，作为一个配置文件运行并最终返回一个 Psr\Container\ContainerInterface 对象
└── routes.php // 用于管理路由
Copy to clipboardErrorCopied
server.php 配置说明
以下为 Hyperf-Skeleton 中的 config/autoload/server.php 所提供的默认 settings

<?php
declare(strict_types=1);

use Hyperf\Server\Server;
use Hyperf\Server\Event;

return [
    // 这里省略了该文件的其它配置
    'settings' => [
        'enable_coroutine' => true, // 开启内置协程
        'worker_num' => swoole_cpu_num(), // 设置启动的 Worker 进程数
        'pid_file' => BASE_PATH . '/runtime/hyperf.pid', // master 进程的 PID
        'open_tcp_nodelay' => true, // TCP 连接发送数据时会关闭 Nagle 合并算法，立即发往客户端连接
        'max_coroutine' => 100000, // 设置当前工作进程最大协程数量
        'open_http2_protocol' => true, // 启用 HTTP2 协议解析
        'max_request' => 100000, // 设置 worker 进程的最大任务数
        'socket_buffer_size' => 2 * 1024 * 1024, // 配置客户端连接的缓存区长度
    ],
];
Copy to clipboardErrorCopied
此配置文件用于管理 Server 服务，其中的 settings 选项可以直接使用由 Swoole Server 提供的选项，其他选项可参考 Swoole 官方文档 。

如需要设置守护进程化，可在 settings 中增加 'daemonize' => 1，执行 php bin/hyperf.php start后，程序将转入后台作为守护进程运行

单独的 Server 配置需要添加在对应 servers 的 settings 当中，如 jsonrpc 协议的 TCP Server 配置启用 EOF 自动分包和设置 EOF 字符串

<?php

use Hyperf\Server\Server;
use Hyperf\Server\Event;

return [
    // 这里省略了该文件的其它配置
    'servers' => [
        [
            'name' => 'jsonrpc',
            'type' => Server::SERVER_BASE,
            'host' => '0.0.0.0',
            'port' => 9503,
            'sock_type' => SWOOLE_SOCK_TCP,
            'callbacks' => [
                Event::ON_RECEIVE => [\Hyperf\JsonRpc\TcpServer::class, 'onReceive'],
            ],
            'settings' => [
                'open_eof_split' => true, // 启用 EOF 自动分包
                'package_eof' => "\r\n", // 设置 EOF 字符串
            ],
        ],
    ],
];
Copy to clipboardErrorCopied
config.php 与 autoload 文件夹内的配置文件的关系
config.php 与 autoload 文件夹内的配置文件在服务启动时都会被扫描并注入到 Hyperf\Contract\ConfigInterface 对应的对象中，配置的结构为一个键值对的大数组，两种配置形式不同的在于 autoload 内配置文件的文件名会作为第一层 键(Key) 存在，而 config.php 内的则以您定义的为第一层，我们通过下面的例子来演示一下。
我们假设存在一个 config/autoload/client.php 文件，文件内容如下：

return [
    'request' => [
        'timeout' => 10,
    ],
];
Copy to clipboardErrorCopied
那么我们想要得到 timeout 的值对应的 键(Key) 为 client.request.timeout；

我们假设想要以相同的 键(Key) 获得同样的结果，但配置是写在 config/config.php 文件内的，那么文件内容应如下：

return [
    'client' => [
        'request' => [
            'timeout' => 10,
        ],
    ],
];
Copy to clipboardErrorCopied
使用 Hyperf Config 组件
该组件是官方提供的默认的配置组件，是面向 Hyperf\Contract\ConfigInterface 接口实现的，由 hyperf/config 组件内的 ConfigProvider 将 Hyperf\Config\Config 对象绑定到接口上。

设置配置
只需在 config/config.php 与 config/autoload/server.php 与 autoload 文件夹内的配置，都能在服务启动时被扫描并注入到 Hyperf\Contract\ConfigInterface 对应的对象中，这个流程是由 Hyperf\Config\ConfigFactory 在 Config 对象实例化时完成的。

获取配置
Config 组件提供了三种方式获取配置，通过 Hyperf\Config\Config 对象获取、通过 @Value 注解获取和通过 config(string $key, $default) 函数获取。

通过 Config 对象获取配置
这种方式要求你已经拿到了 Config 对象的实例，默认对象为 Hyperf\Config\Config，注入实例的细节可查阅 依赖注入 章节；

/**
 * @var \Hyperf\Contract\ConfigInterface
 */
// 通过 get(string $key, $default): mixed 方法获取 $key 所对应的配置，$key 值可以通过 . 连接符定位到下级数组，$default 则是当对应的值不存在时返回的默认值
$config->get($key，$default);
Copy to clipboardErrorCopied
通过 @Value 注解获取配置
这种方式要求注解的应用对象必须是通过 hyperf/di 组件创建的，注入实例的细节可查阅 依赖注入 章节，示例中我们假设 IndexController 就是一个已经定义好的 Controller 类，Controller 类一定是由 DI 容器创建出来的；
@Value() 内的字符串则对应到 $config->get($key) 内的 $key 参数，在创建该对象实例时，对应的配置会自动注入到定义的类属性中。

class IndexController
{

    /**
     * @Value("config.key")
     */
    private $configValue;

    public function index()
    {
        return $this->configValue;
    }

}
Copy to clipboardErrorCopied
通过 config 函数获取
在任意地方可以通过 config(string $key, $default) 函数获取对应的配置，但这样的使用方式也就意味着您对 hyperf/config 和 hyperf/utils 组件是强依赖的。

判断配置是否存在
/**
 * @var \Hyperf\Contract\ConfigInterface
 */
// 通过 has(): bool 方法判断对应的 $key 值是否存在于配置中，$key 值可以通过 . 连接符定位到下级数组
$config->has($key);
Copy to clipboardErrorCopied
环境变量
对于不同的运行环境使用不同的配置是一种常见的需求，比如在测试环境和生产环境的 Redis 配置不一样，而生产环境的配置又不能提交到源代码版本管理系统中以免信息泄露。

在 Hyperf 里我们提供了环境变量这一解决方案，通过利用 vlucas/phpdotenv 提供的环境变量解析功能，以及 env() 函数来获取环境变量的值，这一需求解决起来是相当的容易。

在新安装好的 Hyperf 应用中，其根目录会包含一个 .env.example 文件。如果是通过 Composer 安装的 Hyperf，该文件会自动基于 .env.example 复制一个新文件并命名为 .env。否则，需要你手动更改一下文件名。

您的 .env 文件不应该提交到应用的源代码版本管理系统中，因为每个使用你的应用的开发人员 / 服务器可能需要有一个不同的环境配置。此外，在入侵者获得你的源代码仓库的访问权的情况下，这会导致严重的安全问题，因为所有敏感的数据都被一览无余了。

.env 文件中的所有变量均可被外部环境变量所覆盖（比如服务器级或系统级或 Docker 环境变量）。

环境变量类型
.env 文件中的所有变量都会被解析为字符串类型，因此提供了一些保留值以允许您从 env() 函数中获取更多类型的变量：

.env 值	env() 值
true	(bool) true
(true)	(bool) true
false	(bool) false
(false)	(bool) false
empty	(string) ''
(empty)	(string) ''
null	(null) null
(null)	(null) null
如果你需要使用包含空格的环境变量，可以通过将值括在双引号中来实现，比如：

APP_NAME="Hyperf Skeleton"
Copy to clipboardErrorCopied
读取环境变量
我们在上面也有提到环境变量可以通过 env() 函数获取，在应用开发中，环境变量只应作为配置的一个值，通过环境变量的值来覆盖配置的值，对于应用层来说应 只使用配置，而不是直接使用环境变量。
我们举个合理使用的例子：

// config/config.php
return [
    'app_name' => env('APP_NAME', 'Hyperf Skeleton'),
];
Copy to clipboardErrorCopied
发布组件配置
Hyperf 采用组件化设计，在添加一些组件进来骨架项目后，我们通常会需要为新添加的组件创建对应的配置文件，以满足对组件的使用。Hyperf 为组件提供了一个 组件配置发布机制，通过该机制，您只需通过一个 vendor:publish 命令即可将组件预设的配置文件模板发布到骨架项目中来。 比如我们希望添加一个 hyperf/foo 组件 (该组件实际并不存在，仅示例) 以及该组件对应的配置文件，在执行 composer require hyperf/foo 安装之后，您可通过执行 php bin/hyperf.php vendor:publish hyperf/foo 来将组件预设的配置文件，发布到骨架项目的 config/autoload 文件夹内，具体要发布的内容，由组件来定义提供。

配置中心
Hyperf 为您提供了分布式系统的外部化配置支持，目前支持由携程开源的 Apollo、阿里云 ACM 应用配置管理、ETCD 以及 Zookeeper 作为配置中心的支持。 关于配置中心的使用细节我们由 配置中心 章节来阐述。


注解
注解是 Hyperf 非常强大的一项功能，可以通过注解的形式减少很多的配置，以及实现很多非常方便的功能。

概念
什么是注解什么是注释？
在解释注解之前我们需要先定义一下 注解 与 注释 的区别：

注释：给程序员看，帮助理解代码，对代码起到解释、说明的作用。
注解：给应用程序看，用于元数据的定义，单独使用时没有任何作用，需配合应用程序对其元数据进行利用才有作用。
注解解析如何实现？
Hyperf 使用了 doctrine/annotations 包来对代码内的注解进行解析，注解必须写在下面示例的标准注释块才能被正确解析，其它格式均不能被正确解析。 注释块示例：

/**
 * @AnnotationClass()
 */
Copy to clipboardErrorCopied
在标准注释块内通过书写 @AnnotationClass() 这样的语法即表明对当前注释块所在位置的对象(类、类方法、类属性)进行了注解的定义， AnnotationClass 对应的是一个 注解类 的类名，可写全类的命名空间，亦可只写类名，但需要在当前类 use 该注解类以确保能够根据命名空间找到正确的注解类。

注解是如何发挥作用的？
我们有说到注解只是元数据的定义，需配合应用程序才能发挥作用，在 Hyperf 里，注解内的数据会被收集到 Hyperf\Di\Annotation\AnnotationCollector 类供应用程序使用，当然根据您的实际情况，也可以收集到您自定义的类去，随后在这些注解本身希望发挥作用的地方对已收集的注解元数据进行读取和利用，以达到期望的功能实现。

忽略某些注解
在一些情况下我们可能希望忽略某些 注解，比如我们在接入一些自动生成文档的工具时，有不少工具都是通过注解的形式去定义文档的相关结构内容的，而这些注解可能并不符合 Hyperf 的使用方式，我们可以通过在 config/autoload/annotations.php 内将相关注解设置为忽略。

return [
    'scan' => [
        // ignore_annotations 数组内的注解都会被注解扫描器忽略
        'ignore_annotations' => [
            'mixin',
        ],
    ],
];
Copy to clipboardErrorCopied
使用注解
注解一共有 3 种应用对象，分别是 类、类方法 和 类属性。

使用类注解
类注解定义是在 class 关键词上方的注释块内，比如常用的 @Controller 和 @AutoController 就是类注解的使用典范，下面的代码示例则为一个正确使用类注解的示例，表明 @ClassAnnotation 注解应用于 Foo 类。

/**
 * @ClassAnnotation()
 */
class Foo {}
Copy to clipboardErrorCopied
使用类方法注解
类方法注解定义是在方法上方的注释块内，比如常用的 @RequestMapping 就是类方法注解的使用典范，下面的代码示例则为一个正确使用类方法注解的示例，表明 @MethodAnnotation 注解应用于 Foo::bar() 方法。

class Foo
{
    /**
     * @MethodAnnotation()
     */
    public function bar()
    {
        // some code
    }
}
Copy to clipboardErrorCopied
使用类属性注解
类属性注解定义是在属性上方的注释块内，比如常用的 @Value 和 @Inject 就是类属性注解的使用典范，下面的代码示例则为一个正确使用类属性注解的示例，表明 @PropertyAnnotation 注解应用于 Foo 类的 $bar 属性。

class Foo
{
    /**
     * @PropertyAnnotation()
     */
    private $bar;
}
Copy to clipboardErrorCopied
注解参数传递
传递主要的单个参数 @DemoAnnotation("value")
传递字符串参数 @DemoAnnotation(key1="value1", key2="value2")
传递数组参数 @DemoAnnotation(key={"value1", "value2"})
自定义注解
创建一个注解类
在任意地方创建注解类，如下代码示例：

namespace App\Annotation;

use Hyperf\Di\Annotation\AbstractAnnotation;

/**
 * @Annotation
 * @Target({"METHOD","PROPERTY"})
 */
class Bar extends AbstractAnnotation
{
    // some code
}

/**
 * @Annotation
 * @Target("CLASS")
 */
class Foo extends AbstractAnnotation
{
    // some code
}
Copy to clipboardErrorCopied
注意注解类的 @Annotation 和 @Target 注解为全局注解，无需 use

其中 @Target 有如下参数：

METHOD 注解允许定义在类方法上
PROPERTY 注解允许定义在类属性上
CLASS 注解允许定义在类上
ALL 注解允许定义在任何地方
我们注意一下在上面的示例代码中，注解类都继承了 Hyperf\Di\Annotation\AbstractAnnotation 抽象类，对于注解类来说，这个不是必须的，但对于 Hyperf 的注解类来说，继承 Hyperf\Di\Annotation\AnnotationInterface 接口类是必须的，那么抽象类在这里的作用是提供极简的定义方式，该抽象类已经为您实现了注解参数自动分配到类属性、根据注解使用位置自动按照规则收集到 AnnotationCollector 这样非常便捷的功能。

自定义注解收集器
注解的收集时具体的执行流程也是在注解类内实现的，相关的方法由 Hyperf\Di\Annotation\AnnotationInterface 约束着，该接口类要求了下面 3 个方法的实现，您可以根据自己的需求实现对应的逻辑：

public function collectClass(string $className): void; 当注解定义在类时被扫描时会触发该方法
public function collectMethod(string $className, ?string $target): void; 当注解定义在类方法时被扫描时会触发该方法
public function collectProperty(string $className, ?string $target): void 当注解定义在类属性时被扫描时会触发该方法
因为框架实现了注解收集器缓存功能，所以需要您将自定义收集器配置到 annotations.scan.collectors 中，这样框架才能自动缓存收集好的注解，在下次启动时进行复用。 如果没有配置对应的收集器，就会导致自定义注解只有在首次启动 server 时生效，而再次启动时不会生效。

<?php

return [
    // 注意在 config/autoload 文件下的配置文件则无 annotations 这一层
    'annotations' => [
        'scan' => [
            'collectors' => [
                CustomCollector::class,
            ],
        ],
    ],
];
Copy to clipboardErrorCopied
利用注解数据
在没有自定义注解收集方法时，默认会将注解的元数据统一收集在 Hyperf\Di\Annotation\AnnotationCollector 类内，通过该类的静态方法可以方便的获取对应的元数据用于逻辑判断或实现。

ClassMap 功能
框架提供了 class_map 配置，可以方便用户直接替换需要加载的类。

比如以下我们实现一个可以自动复制协程上下文的功能：

首先，我们实现一个用于复制上下文的 Coroutine 类。其中 create() 方法，可以将父类的上下文复制到子类当中。

为了避免命名冲突，约定使用 class_map 做为文件夹名，后跟要替换的命名空间的文件夹及文件。

如： class_map/Hyperf/Utils/Coroutine.php

<?php

declare(strict_types=1);
/**
 * This file is part of Hyperf.
 *
 * @link     https://www.hyperf.io
 * @document https://doc.hyperf.io
 * @contact  group@hyperf.io
 * @license  https://github.com/hyperf/hyperf/blob/master/LICENSE
 */
namespace App\Kernel\Context;

use Hyperf\Contract\StdoutLoggerInterface;
use Hyperf\ExceptionHandler\Formatter\FormatterInterface;
use Hyperf\Utils;
use Psr\Container\ContainerInterface;
use Psr\Http\Message\ServerRequestInterface;
use Swoole\Coroutine as SwooleCoroutine;

class Coroutine
{
    /**
     * @var ContainerInterface
     */
    protected $container;

    /**
     * @var StdoutLoggerInterface
     */
    protected $logger;

    /**
     * @var null|FormatterInterface
     */
    protected $formatter;

    public function __construct(ContainerInterface $container)
    {
        $this->container = $container;
        $this->logger = $container->get(StdoutLoggerInterface::class);
        if ($container->has(FormatterInterface::class)) {
            $this->formatter = $container->get(FormatterInterface::class);
        }
    }

    /**
     * @return int Returns the coroutine ID of the coroutine just created.
     *             Returns -1 when coroutine create failed.
     */
    public function create(callable $callable): int
    {
        $id = Utils\Coroutine::id();
        $result = SwooleCoroutine::create(function () use ($callable, $id) {
            try {
                // 按需复制，禁止复制 Socket，不然会导致 Socket 跨协程调用从而报错。
                Utils\Context::copy($id, [
                    ServerRequestInterface::class,
                ]);
                call($callable);
            } catch (Throwable $throwable) {
                if ($this->formatter) {
                    $this->logger->warning($this->formatter->format($throwable));
                } else {
                    $this->logger->warning((string) $throwable);
                }
            }
        });
        return is_int($result) ? $result : -1;
    }
}
Copy to clipboardErrorCopied
然后，我们实现一个跟 Hyperf\Utils\Coroutine 一模一样的对象。其中 create() 方法替换成我们上述实现的方法。

class_map/Hyperf/Utils/Coroutine.php

<?php

declare(strict_types=1);
/**
 * This file is part of Hyperf.
 *
 * @link     https://www.hyperf.io
 * @document https://doc.hyperf.io
 * @contact  group@hyperf.io
 * @license  https://github.com/hyperf/hyperf/blob/master/LICENSE
 */
namespace Hyperf\Utils;

use App\Kernel\Context\Coroutine as Co;
use Swoole\Coroutine as SwooleCoroutine;
use Hyperf\Utils\ApplicationContext;

/**
 * @method static void defer(callable $callable)
 */
class Coroutine
{
    public static function __callStatic($name, $arguments)
    {
        if (! method_exists(SwooleCoroutine::class, $name)) {
            throw new \BadMethodCallException(sprintf('Call to undefined method %s.', $name));
        }
        return SwooleCoroutine::$name(...$arguments);
    }

    /**
     * Returns the current coroutine ID.
     * Returns -1 when running in non-coroutine context.
     */
    public static function id(): int
    {
        return SwooleCoroutine::getCid();
    }

    /**
     * Returns the parent coroutine ID.
     * Returns -1 when running in the top level coroutine.
     * Returns null when running in non-coroutine context.
     *
     * @see https://github.com/swoole/swoole-src/pull/2669/files#diff-3bdf726b0ac53be7e274b60d59e6ec80R940
     */
    public static function parentId(?int $coroutineId = null): ?int
    {
        if ($coroutineId) {
            $cid = SwooleCoroutine::getPcid($coroutineId);
        } else {
            $cid = SwooleCoroutine::getPcid();
        }
        if ($cid === false) {
            return null;
        }

        return $cid;
    }

    /**
     * @return int Returns the coroutine ID of the coroutine just created.
     *             Returns -1 when coroutine create failed.
     */
    public static function create(callable $callable): int
    {
        return ApplicationContext::getContainer()->get(Co::class)->create($callable);
    }

    public static function inCoroutine(): bool
    {
        return Coroutine::id() > 0;
    }
}
Copy to clipboardErrorCopied
然后配置一下 class_map，如下：

<?php

declare(strict_types=1);

use Hyperf\Utils\Coroutine;

return [
    'scan' => [
        'paths' => [
            BASE_PATH . '/app',
        ],
        'ignore_annotations' => [
            'mixin',
        ],
        'class_map' => [
            // 需要映射的类名 => 类所在的文件地址
            Coroutine::class => BASE_PATH . '/class_map/Hyperf/Utils/Coroutine.php',
        ],
    ],
];
Copy to clipboardErrorCopied
这样 co() 和 parallel() 等方法，就可以自动拿到父协程，上下文中的数据，比如 Request。

IDE 注解插件
因为 PHP 并不是原生支持 注解，所以 IDE 不会默认增加注解支持。但我们可以添加第三方插件，来让 IDE 支持 注解。

PhpStorm
我们到 Plugins 中搜索 PHP Annotations，就可以找到对应的组件 PHP Annotations。然后安装组件，重启 PhpStorm，就可以愉快的使用注解功能了，主要提供了为注解类增加自动跳转和代码提醒支持，使用注解时自动引用注解对应的命名空间等非常便捷有用的功能。

依赖注入
简介
Hyperf 默认采用 hyperf/di 作为框架的依赖注入管理容器，尽管从设计上我们允许您更换其它的依赖注入管理容器，但我们强烈不建议您更换该组件。
hyperf/di 是一个强大的用于管理类的依赖关系并完成自动注入的组件，与传统依赖注入容器的区别在于更符合长生命周期的应用使用、提供了 注解及注解注入 的支持、提供了无比强大的 AOP 面向切面编程 能力，这些能力及易用性作为 Hyperf 的核心输出，我们自信的认为该组件是最优秀的。

安装
该组件默认存在 hyperf-skeleton 项目中并作为主要组件存在，如希望在其它框架内使用该组件可通过下面的命令安装。

composer require hyperf/di
Copy to clipboardErrorCopied
绑定对象关系
简单对象注入
通常来说，类的关系及注入是无需显性定义的，这一切 Hyperf 都会默默的为您完成，我们通过一些代码示例来说明一下相关的用法。
假设我们需要在 IndexController 内调用 UserService 类的 getInfoById(int $id) 方法。

<?php
namespace App\Service;

class UserService
{
    public function getInfoById(int $id)
    {
        // 我们假设存在一个 Info 实体
        return (new Info())->fill($id);    
    }
}
Copy to clipboardErrorCopied
通过构造方法注入
<?php
namespace App\Controller;

use App\Service\UserService;

class IndexController
{
    /**
     * @var UserService
     */
    private $userService;

    // 通过在构造函数的参数上声明参数类型完成自动注入
    public function __construct(UserService $userService)
    {
        $this->userService = $userService;
    }

    public function index()
    {
        $id = 1;
        // 直接使用
        return $this->userService->getInfoById($id);    
    }
}
Copy to clipboardErrorCopied
注意使用构造函数注入时，调用方也就是 IndexController 必须是由 DI 创建的对象才能完成自动注入，而 Controller 默认是由 DI 创建的，所以可以直接使用构造函数注入

当您希望定义一个可选的依赖项时，可以通过给参数定义为 nullable 或将参数的默认值定义为 null，即表示该参数如果在 DI 容器中没有找到或无法创建对应的对象时，不抛出异常而是直接使用 null 来注入。*(该功能仅在 1.1.0 或更高版本可用)*

<?php
namespace App\Controller;

use App\Service\UserService;

class IndexController
{
    /**
     * @var null|UserService
     */
    private $userService;

    // 通过设置参数为 nullable，表明该参数为一个可选参数
    public function __construct(?UserService $userService)
    {
        $this->userService = $userService;
    }

    public function index()
    {
        $id = 1;
        if ($this->userService instanceof UserService) {
            // 仅值存在时 $userService 可用
            return $this->userService->getInfoById($id);    
        }
        return null;
    }
}
Copy to clipboardErrorCopied
通过 @Inject 注解注入
<?php
namespace App\Controller;

use App\Service\UserService;
use Hyperf\Di\Annotation\Inject;

class IndexController
{
    /**
     * 通过 `@Inject` 注解注入由 `@var` 注解声明的属性类型对象
     *
     * @Inject
     * @var UserService
     */
    private $userService;

    public function index()
    {
        $id = 1;
        // 直接使用
        return $this->userService->getInfoById($id);    
    }
}
Copy to clipboardErrorCopied
通过 @Inject 注解注入可作用于 DI 创建的（单例）对象，也可作用于通过 new 关键词创建的对象；

使用 @Inject 注解时需 use Hyperf\Di\Annotation\Inject; 命名空间；

Required 参数
@Inject 注解存在一个 required 参数，默认值为 true，当将该参数定义为 false 时，则表明该成员属性为一个可选依赖，当对应 @var 的对象不存在于 DI 容器或不可创建时，将不会抛出异常而是注入一个 null，如下：

<?php
namespace App\Controller;

use App\Service\UserService;
use Hyperf\Di\Annotation\Inject;

class IndexController
{
    /**
     * 通过 `@Inject` 注解注入由 `@var` 注解声明的属性类型对象
     * 当 UserService 不存在于 DI 容器内或不可创建时，则注入 null
     *
     * @Inject(required=false)
     * @var UserService
     */
    private $userService;

    public function index()
    {
        $id = 1;
        if ($this->userService instanceof UserService) {
            // 仅值存在时 $userService 可用
            return $this->userService->getInfoById($id);    
        }
        return null;
    }
}
Copy to clipboardErrorCopied
抽象对象注入
基于上面的例子，从合理的角度上来说，Controller 面向的不应该直接是一个 UserService 类，可能更多的是一个 UserServiceInterface 的接口类，此时我们可以通过 config/autoload/dependencies.php 来绑定对象关系达到目的，我们还是通过代码来解释一下。

定义一个接口类：

<?php
namespace App\Service;

interface UserServiceInterface
{
    public function getInfoById(int $id);
}
Copy to clipboardErrorCopied
UserService 实现接口类：

<?php
namespace App\Service;

class UserService implements UserServiceInterface
{
    public function getInfoById(int $id)
    {
        // 我们假设存在一个 Info 实体
        return (new Info())->fill($id);    
    }
}
Copy to clipboardErrorCopied
在 config/autoload/dependencies.php 内完成关系配置：

<?php
return [
    \App\Service\UserServiceInterface::class => \App\Service\UserService::class
];
Copy to clipboardErrorCopied
这样配置后就可以直接通过 UserServiceInterface 来注入 UserService 对象了，我们仅通过注解注入的方式来举例，构造函数注入也是一样的：

<?php
namespace App\Controller;

use App\Service\UserServiceInterface;
use Hyperf\Di\Annotation\Inject;

class IndexController
{
    /**
     * @Inject
     * @var UserServiceInterface
     */
    private $userService;

    public function index()
    {
        $id = 1;
        // 直接使用
        return $this->userService->getInfoById($id);    
    }
}
Copy to clipboardErrorCopied
工厂对象注入
我们假设 UserService 的实现会更加复杂一些，在创建 UserService 对象时构造函数还需要传递进来一些非直接注入型的参数，假设我们需要从配置中取得一个值，然后 UserService 需要根据这个值来决定是否开启缓存模式（顺带一说 Hyperf 提供了更好用的 模型缓存 功能）

我们需要创建一个工厂来生成 UserService 对象：

<?php
namespace App\Service;

use Hyperf\Contract\ConfigInterface;
use Psr\Container\ContainerInterface;

class UserServiceFactory
{
    // 实现一个 __invoke() 方法来完成对象的生产，方法参数会自动注入一个当前的容器实例
    public function __invoke(ContainerInterface $container)
    {
        $config = $container->get(ConfigInterface::class);
        // 我们假设对应的配置的 key 为 cache.enable
        $enableCache = $config->get('cache.enable', false);
        // make(string $name, array $parameters = []) 方法等同于 new ，使用 make() 方法是为了允许 AOP 的介入，而直接 new 会导致 AOP 无法正常介入流程
        return make(UserService::class, compact('enableCache'));
    }
}
Copy to clipboardErrorCopied
UserService 也可以在构造函数提供一个参数接收对应的值：

<?php
namespace App\Service;

class UserService implements UserServiceInterface
{

    /**
     * @var bool
     */
    private $enableCache;

    public function __construct(bool $enableCache)
    {
        // 接收值并储存于类属性中
        $this->enableCache = $enableCache;
    }

    public function getInfoById(int $id)
    {
        return (new Info())->fill($id);    
    }
}
Copy to clipboardErrorCopied
在 config/autoload/dependencies.php 调整绑定关系：

<?php
return [
    \App\Service\UserServiceInterface::class => \App\Service\UserServiceFactory::class
];
Copy to clipboardErrorCopied
这样在注入 UserServiceInterface 的时候容器就会交由 UserServiceFactory 来创建对象了。

当然在该场景中可以通过 @Value 注解来更便捷的注入配置而无需构建工厂类，此仅为举例

懒加载
Hyperf 的长生命周期依赖注入在项目启动时完成。这意味着长生命周期的类需要注意：

构造函数时还不是协程环境，如果注入了可能会触发协程切换的类，就会导致框架启动失败。

构造函数中要避免循环依赖（比较典型的例子为 Listener 和 EventDispatcherInterface），不然也会启动失败。

目前解决方案是：只在实例中注入 Psr\Container\ContainerInterface ，而其他的组件在非构造函数执行时通过 container 获取。但 PSR-11 中指出:

「用户不应该将容器作为参数传入对象然后在对象中通过容器获得对象的依赖。这样是把容器当作服务定位器来使用，而服务定位器是一种反模式」

也就是说这样的做法虽然有效，但是从设计模式角度来说并不推荐。

另一个方案是使用 PHP 中常用的惰性代理模式，注入一个代理对象，在使用时再实例化目标对象。Hyperf DI 组件设计了懒加载注入功能。

添加 config/autoload/lazy_loader.php 文件并绑定懒加载关系：

<?php
return [
    /**
     * 格式为：代理类名 => 原类名
     * 代理类此时是不存在的，Hyperf会在runtime文件夹下自动生成该类。
     * 代理类类名和命名空间可以自由定义。
     */
    'App\Service\LazyUserService' => \App\Service\UserServiceInterface::class
];
Copy to clipboardErrorCopied
这样在注入 App\Service\LazyUserService 的时候容器就会创建一个 懒加载代理类 注入到目标对象中了。

use App\Service\LazyUserService;

class Foo{
    public $service;
    public function __construct(LazyUserService $service){
        $this->service = $service;
    }
}
Copy to clipboardErrorCopied
您还可以通过注解 @Inject(lazy=true) 注入懒加载代理。通过注解实现懒加载不用创建配置文件。

use Hyperf\Di\Annotation\Inject;
use App\Service\UserServiceInterface;

class Foo{
    /**
     * @Inject(lazy=true)
     * @var UserServiceInterface
     */
    public $service;
}
Copy to clipboardErrorCopied
注意：当该代理对象执行下列操作时，被代理对象才会从容器中真正实例化。

// 方法调用
$proxy->someMethod();

// 读取属性
echo $proxy->someProperty;

// 写入属性
$proxy->someProperty = 'foo';

// 检查属性是否存在
isset($proxy->someProperty);

// 删除属性
unset($proxy->someProperty);
Copy to clipboardErrorCopied
短生命周期对象
通过 new 关键词创建的对象毫无疑问的短生命周期的，那么如果希望创建一个短生命周期的对象但又希望使用 构造函数依赖自动注入功能 呢？这时我们可以通过 make(string $name, array $parameters = []) 函数来创建 $name 对应的的实例，代码示例如下：

$userService = make(UserService::class, ['enableCache' => true]);
Copy to clipboardErrorCopied
注意仅 $name 对应的对象为短生命周期对象，该对象的所有依赖都是通过 get() 方法获取的，即为长生命周期的对象

获取容器对象
有些时候我们可能希望去实现一些更动态的需求时，会希望可以直接获取到 容器(Container) 对象，在绝大部分情况下，框架的入口类（比如命令类、控制器、RPC 服务提供者等）都是由 容器(Container) 创建并维护的，也就意味着您所写的绝大部分业务代码都是在 容器(Container) 的管理作用之下的，也就意味着在绝大部分情况下您都可以通过在 构造函数(Constructor) 声明或通过 @Inject 注解注入 Psr\Container\ContainerInterface 接口类都能够获得 Hyperf\Di\Container 容器对象，我们通过代码来演示一下：

<?php
namespace App\Controller;

use Hyperf\HttpServer\Annotation\AutoController;
use Psr\Container\ContainerInterface;

class IndexController
{
    /**
     * @var ContainerInterface
     */
    private $container;

    // 通过在构造函数的参数上声明参数类型完成自动注入
    public function __construct(ContainerInterface $container)
    {
        $this->container = $container;
    }
}
Copy to clipboardErrorCopied
在某些更极端动态的情况下，或者非 容器(Container) 的管理作用之下时，想要获取到 容器(Container) 对象还可以通过 \Hyperf\Utils\ApplicationContext::getContaienr() 方法来获得 容器(Container) 对象。

$container = \Hyperf\Utils\ApplicationContext::getContainer();
Copy to clipboardErrorCopied
注意事项
容器仅管理长生命周期的对象
换种方式理解就是容器内管理的对象都是单例，这样的设计对于长生命周期的应用来说会更加的高效，减少了大量无意义的对象创建和销毁，这样的设计也就意味着所有需要交由 DI 容器管理的对象均不能包含 状态 值。
状态 可直接理解为会随着请求而变化的值，事实上在 协程 编程中，这些状态值也是应该存放于 协程上下文 中的，即 Hyperf\Utils\Context。

@Inject 注入覆盖顺序
@Inject 覆盖顺序为子类覆盖 Trait 覆盖 父类，即 下述 Origin 的 foo 变量为本身注入的 Foo1。

同理，假如 Origin 不存在变量 $foo 时，$foo 会被第一个 Trait 完成注入，注入类 Foo2。

use Hyperf\Di\Annotation\Inject;

class ParentClass
{
    /**
     * @Inject
     * @var Foo4
     */
    protected $foo;
}

trait Foo1{
    /**
     * @Inject
     * @var Foo2
     */
    protected $foo;
}

trait Foo2{
    /**
     * @Inject
     * @var Foo3
     */
    protected $foo;
}

class Origin extends ParentClass
{
    use Foo1;
    use Foo2;
    /**
     * @Inject
     * @var Foo1
     */
    protected $foo;
}



事件机制
前言
事件模式必须基于 PSR-14 去实现。
Hyperf 的事件管理器默认由 hyperf/event 实现，该组件亦可用于其它框架或应用，只需通过 Composer 将该组件引入即可。

composer require hyperf/event
Copy to clipboardErrorCopied
概念
事件模式是一种经过了充分测试的可靠机制，是一种非常适用于解耦的机制，分别存在以下 3 种角色：

事件(Event) 是传递于应用代码与 监听器(Listener) 之间的通讯对象
监听器(Listener) 是用于监听 事件(Event) 的发生的监听对象
事件调度器(EventDispatcher) 是用于触发 事件(Event) 和管理 监听器(Listener) 与 事件(Event) 之间的关系的管理者对象
用通俗易懂的例子来说明就是，假设我们存在一个 UserService::register() 方法用于注册一个账号，在账号注册成功后我们可以通过事件调度器触发 UserRegistered 事件，由监听器监听该事件的发生，在触发时进行某些操作，比如发送用户注册成功短信，在业务发展的同时我们可能会希望在用户注册成功之后做更多的事情，比如发送用户注册成功的邮件等待，此时我们就可以通过再增加一个监听器监听 UserRegistered 事件即可，无需在 UserService::register() 方法内部增加与之无关的代码。

使用事件管理器
接下来我们会通过配置和注解两种方式介绍监听器，实际使用时，二者只需使用其一即可，如果既有注解又有配置，则会造成监听器被多次触发。

定义一个事件
一个事件其实就是一个用于管理状态数据的普通类，触发时将应用数据传递到事件里，然后监听器对事件类进行操作，一个事件可被多个监听器监听。

<?php
namespace App\Event;

class UserRegistered
{
    // 建议这里定义成 public 属性，以便监听器对该属性的直接使用，或者你提供该属性的 Getter
    public $user;

    public function __construct($user)
    {
        $this->user = $user;    
    }
}
Copy to clipboardErrorCopied
定义一个监听器
监听器都需要实现一下 Hyperf\Event\Contract\ListenerInterface 接口的约束方法，示例如下。

<?php
namespace App\Listener;

use App\Event\UserRegistered;
use Hyperf\Event\Contract\ListenerInterface;

class UserRegisteredListener implements ListenerInterface
{
    public function listen(): array
    {
        // 返回一个该监听器要监听的事件数组，可以同时监听多个事件
        return [
            UserRegistered::class,
        ];
    }

    /**
     * @param UserRegistered $event
     */
    public function process(object $event)
    {
        // 事件触发后该监听器要执行的代码写在这里，比如该示例下的发送用户注册成功短信等
        // 直接访问 $event 的 user 属性获得事件触发时传递的参数值
        // $event->user;

    }
}
Copy to clipboardErrorCopied
通过配置文件注册监听器
在定义完监听器之后，我们需要让其能被 事件调度器(Dispatcher) 发现，可以在 config/autoload/listeners.php 配置文件 （如不存在可自行创建） 内添加该监听器即可，监听器的触发顺序根据该配置文件的配置顺序:

<?php
return [
    \App\Listener\UserRegisteredListener::class,
];
Copy to clipboardErrorCopied
通过注解注册监听器
Hyperf 还提供了一种更加简便的监听器注册方式，就是通过 @Listener 注解注册，只要将该注解定义在监听器类上，且监听器类处于 Hyperf 注解扫描域 内即可自动完成注册，代码示例如下：

<?php
namespace App\Listener;

use App\Event\UserRegistered;
use Hyperf\Event\Annotation\Listener;
use Hyperf\Event\Contract\ListenerInterface;

/**
 * @Listener
 */
class UserRegisteredListener implements ListenerInterface
{
    public function listen(): array
    {
        // 返回一个该监听器要监听的事件数组，可以同时监听多个事件
        return [
            UserRegistered::class,
        ];
    }

    /**
     * @param UserRegistered $event
     */
    public function process(object $event)
    {
        // 事件触发后该监听器要执行的代码写在这里，比如该示例下的发送用户注册成功短信等
        // 直接访问 $event 的 user 属性获得事件触发时传递的参数值
        // $event->user;
    }
}
Copy to clipboardErrorCopied
在通过注解注册监听器时，我们可以通过设置 priority 属性定义当前监听器的顺序，如 @Listener(priority=1) ，底层使用 SplPriorityQueue 结构储存，priority 数字越大优先级越高。

使用 @Listener 注解时需 use Hyperf\Event\Annotation\Listener; 命名空间；

触发事件
事件需要通过 事件调度器(EventDispatcher) 调度才能让 监听器(Listener) 监听到，我们通过一段代码来演示如何触发事件：

<?php
namespace App\Service;

use Hyperf\Di\Annotation\Inject;
use Psr\EventDispatcher\EventDispatcherInterface;
use App\Event\UserRegistered;

class UserService
{
    /**
     * @Inject
     * @var EventDispatcherInterface
     */
    private $eventDispatcher;

    public function register()
    {
        // 我们假设存在 User 这个实体
        $user = new User();
        $result = $user->save();
        // 完成账号注册的逻辑
        // 这里 dispatch(object $event) 会逐个运行监听该事件的监听器
        $this->eventDispatcher->dispatch(new UserRegistered($user));
        return $result;
    }
}
Copy to clipboardErrorCopied
Hyperf 生命周期事件

![https://hyperf.wiki/2.1/zh-cn/imgs/hyperf-events.svg]()
注意事项
不要在 Listener 中注入 EventDispatcherInterface
因为 EventDispatcherInterface 依赖于 ListenerProviderInterface，而 ListenerProviderInterface 初始化的同时，会收集所有的 Listener。

而如果 Listener 又依赖了 EventDispatcherInterface，就会导致循坏依赖，进而导致内存溢出。

最好只在 Listener 中注入 ContainerInterface。
最好只在 Listener 中注入 ContainerInterface，而其他的组件在 process 中通过 container 获取。框架启动开始时，会实例化 EventDispatcherInterface，这个时候还不是协程环境，如果 Listener 中注入了可能会触发协程切换的类，就会导致框架启动失败。

BootApplication 事件尽量避免 IO 操作
1.1.6 版本及更新的版本已优化此问题

在 1.1.6 版本之前，因为 BootApplication 是在 Command 初始化 和 Server 启动前触发，所以当前环境一定是非协程环境，一旦使用了协程 API，则会导致启动失败。



AOP 面向切面编程
概念
AOP 为 Aspect Oriented Programming 的缩写，意为：面向切面编程，通过动态代理等技术实现程序功能的统一维护的一种技术。AOP 是 OOP 的延续，也是 Hyperf 中的一个重要内容，是函数式编程的一种衍生范型。利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

用通俗的话来讲，就是在 Hyperf 里可以通过 切面(Aspect) 介入到任意类的任意方法的执行流程中去，从而改变或加强原方法的功能，这就是 AOP。

注意这里所指的任意类并不是完全意义上的所有类，在 Hyperf 启动初期用于实现 AOP 功能的类自身不能被切入。

介绍
相对于其它框架实现的 AOP 功能的使用方式，我们进一步简化了该功能的使用不做过细的划分，仅存在 环绕(Around) 一种通用的形式：

切面(Aspect) 为对流程织入的定义类，包括要介入的目标，以及实现对原方法的修改加强处理
代理类(ProxyClass) ，每个被介入的目标类最终都会生成一个代理类，来达到执行 切面(Aspect) 方法的目的
定义切面(Aspect)
每个 切面(Aspect) 必须实现 Hyperf\Di\Aop\AroundInterface 接口，并提供 public 的 $classes 和 $annotations 属性，为了方便使用，我们可以通过继承 Hyperf\Di\Aop\AbstractAspect 来简化定义过程，我们通过代码来描述一下。

<?php
namespace App\Aspect;

use App\Service\SomeClass;
use App\Annotation\SomeAnnotation;
use Hyperf\Di\Annotation\Aspect;
use Hyperf\Di\Aop\AbstractAspect;
use Hyperf\Di\Aop\ProceedingJoinPoint;

/**
 * @Aspect
 */
class FooAspect extends AbstractAspect
{
    // 要切入的类或 Trait，可以多个，亦可通过 :: 标识到具体的某个方法，通过 * 可以模糊匹配
    public $classes = [
        SomeClass::class,
        'App\Service\SomeClass::someMethod',
        'App\Service\SomeClass::*Method',
    ];

    // 要切入的注解，具体切入的还是使用了这些注解的类，仅可切入类注解和类方法注解
    public $annotations = [
        SomeAnnotation::class,
    ];

    public function process(ProceedingJoinPoint $proceedingJoinPoint)
    {
        // 切面切入后，执行对应的方法会由此来负责
        // $proceedingJoinPoint 为连接点，通过该类的 process() 方法调用原方法并获得结果
        // 在调用前进行某些处理
        $result = $proceedingJoinPoint->process();
        // 在调用后进行某些处理
        return $result;
    }
}
Copy to clipboardErrorCopied
每个 切面(Aspect) 必须定义 @Aspect 注解或在 config/autoload/aspects.php 内配置均可发挥作用。

使用 @Aspect 注解时需 use Hyperf\Di\Annotation\Aspect; 命名空间；

您也可以通过 @Aspect 注解本身的属性来完成切入目标的配置，通过下面注解的形式可以达到与上面的示例一样的目的：

<?php
namespace App\Aspect;

use App\Service\SomeClass;
use App\Annotation\SomeAnnotation;
use Hyperf\Di\Annotation\Aspect;
use Hyperf\Di\Aop\AbstractAspect;
use Hyperf\Di\Aop\ProceedingJoinPoint;

/**
 * @Aspect(
 *   classes={
 *      SomeClass::class,
 *      "App\Service\SomeClass::someMethod",
 *      "App\Service\SomeClass::*Method"
 *   },
 *   annotations={
 *      SomeAnnotation::class
 *   }
 * )
 */
class FooAspect extends AbstractAspect
{
    public function process(ProceedingJoinPoint $proceedingJoinPoint)
    {
        // 切面切入后，执行对应的方法会由此来负责
        // $proceedingJoinPoint 为连接点，通过该类的 process() 方法调用原方法并获得结果
        // 在调用前进行某些处理
        $result = $proceedingJoinPoint->process();
        // 在调用后进行某些处理
        return $result;
    }
}
Copy to clipboardErrorCopied
代理类缓存
所有被 AOP 影响的类，都会在 ./runtime/container/proxy/ 文件夹内生成对应的 代理类缓存，是否在启动时自动生成取决于 config/config.php 配置文件中 scan_cacheable 配置项的值，默认值为 false，如果该配置项为 true 则 Hyperf 不会扫描和生成代理类缓存，而是直接以现有的缓存文件作为最终的代理类。如果该配置项为 false，则 Hyperf 会在每次启动应用时扫描注解扫描域并自动的生成对应的代理类缓存，当代码发生变化时，代理类缓存也会自动的重新生成。

通常在开发环境下，该值为 false，这样更便于开发调试，而在部署生产环境时，我们可能会希望 Hyperf 提前将所有代理类提前生成，而不是使用时动态的生成，可以通过 php bin/hyperf.php 命令来生成所有代理类，然后再通过环境变量 SCAN_CACHEABLE 为 true 修改该配置项的值，以达到启动时间更短、应用内存占用更低的目的。

基于以上，如果您使用 Docker 或 Kubernetes 等虚拟化技术来部署您的应用的话，您可以在镜像构建阶段就生成对应的代理类缓存并写入到镜像中去，在运行镜像实例时，可大大减少启动时间和应用内存。
