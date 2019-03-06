# 服务器启动流程

服务器启动流程，这里是简单的文字描述

```text
Start
new SwoftApplication(init, add processor)
    -> Env processor(Env info init)
    -> Annotation processor(scan,load,parse)
        -> Find and collect component ClassLoader(from composer's ClassLoader object)
        -> Scan and collect annotation classes
        -> Collect beans config of the component
    -> Config processor(load,parse config)
        -> Load application config
    -> Bean processor(init container, init beans)
        -> Collect bean definitions by array
        -> Collect bean annotations
        -> Init bean container
    -> Event processor(register, init)
        -> Register event listeners
        -> Trigger 'swoft.app.init.after' event.
    -> Console processor
        -> Register console routes
        -> Running by input command
            -> Server command(start server)
                -> Main server start
                -> ...
            -> Other command ... exec 
End
```
