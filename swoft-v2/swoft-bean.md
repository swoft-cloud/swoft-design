# bean

swoft 2.0 支持4种生命周期类型的bean:

- `SINGLETON` 全局单例
- `PROTOTYPE` 工厂模式，每次是新的bean对象
  - swoft 内部做了一些优化，初始化时会new一个基础对象，后面获取都是从它clone出的新对象
- `REQUEST` 请求生命周期级别的对象
  - 这个周期涵盖： http 请求, ws message通信请求阶段，tcp 消息收发阶段
  - 同一个请求内这个bean对象是固定的，不同的请求里，则会是不同的对象
  - 请求开始时，初始化bean；请求结束时，销毁bean对象
- `SESSION` 会话周期级别的对象
  - 比如 TCP, WS 连接生命周期；（HTTP-SESSION）一个用户从登录到退出的会话周期
  - bean 在这个会话期内都是固定的，因此它是可以跨请求使用的
