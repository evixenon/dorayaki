
你可以记住的通用模板

对于一个节点内的**训练轮次资源**：

```
构造函数：
    创建 callback group

configure：
    创建 subscription
    创建 publisher
    创建 client

activate：
    创建 worker
    callback_gate_.open()
    发起需要 response callback 的异步请求

deactivate：
    callback_gate_.close_and_wait()
    request_stop()
    stop/join worker
    reset subscriptions
    reset publishers
    清理本轮状态

析构：
    callback group 随 node 一起销毁
```

每个 ROS callback：

```
void callback(...)
{
    auto guard = callback_gate_.try_enter();
    if (!guard || stopped)
    {
        return;
    }

    // 访问本轮资源
}
```

但要记住一个例外：

```
callback_gate_ 只保护 executor callback
```

如果资源由 worker 使用：

```
必须使用 request_stop() + join
```

如果资源由 callback 和 worker 都使用：

```
两种保护都需要
```