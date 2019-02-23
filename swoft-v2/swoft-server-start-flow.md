# 服务器启动流程

```text
new SwoftApplication()
 -> Env handle(Env info init)
 -> Annotation handle(scan,load,parse)
 -> Config handle(load,parse config)
 -> Bean handle(init container, init beans)
 -> Event handle(register, init)
 -> Console handle(register routes, running)
running ...
```
