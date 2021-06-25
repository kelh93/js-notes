# BroadcastChannel 广播频道

Broadcast Channel 可以实现 **同源** 下浏览器不同窗口，Tab页，frame或者iframe下的 浏览器上下文之间的简单通讯。

> 此特性在webWorkder中可用。

双工通讯

![image-20210625181534981](..\images\image-20210625181534981.png)

## 使用方法

#### 创建或加入某个频道

通过创建一个`BroadcastChannel`对象，一个客户端就加入了某个指定的频道。只需要向构造函数传入一个'频道名称'。如果是首次连接到该广播频道，相应资源会被自动创建。

```javascript
// 连接到广播频道
var bc = new BroadcastChannel('test_channel');
```

### 发送消息

使用`postMessage`方法发送消息。

```javascript
bc.postMessage('This is a bc message');
```

### 接收消息

使用监听`onmessage`方法。

```javascript
bc.onmessage = function(event){
    console.log('event');
}
```

### 断开连接

```javascript
bc.close();



## 兼容性

**IE**不支持 、**Safari**不支持、**Safari IOS**不支持



