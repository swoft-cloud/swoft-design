# interface refer

## context

```text
ContextInterface {
    public function has();
    public function get();
    public function destroy();
}

class ConsoleContext implements ContextInterface
{
    public function getInput();
    public function getOutput();
}

// TcpContext RpcContext
class HttpContext implements ContextInterface
{
    public function getRequest(): psr7 request;
    public function getResponse(): psr7 response;
}

class SocketContext implements ContextInterface
{
    public function getRequest(): psr7 request;
}

// 全局
SwoftContext::get();
ApplicationContext::get();

// 运行时的
RuntimeContext::get();
```

## server

```text

ServerInterface {
    public function destroyContext();
}

AbstractServer implements ServerInterface {
    
}

HttpServerInterface {
    public function handleRequest(psr7 request, psr7 response): psr7 response;
}

WebSocketServerInterface {
    public function handleHandshake(psr7 request, psr7 response): psr7 response;
    public function handleOpen(conn Connection);
    public function handleMessage(conn Connection);
    public function handleClose(conn Connection);
}
```
