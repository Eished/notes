# Firebase Cloud Messaging

## FCM 消息简介

### 消息类型

您可以使用 FCM 向客户端发送两种类型的消息：

- 通知消息，有时被称为“显示消息”。此类消息由 FCM SDK 自动处理。
- 数据消息，由客户端应用处理。

通知消息包含一组预定义的用户可见的键。 与其相对，数据消息只包含用户定义的自定义键值对。通知消息可以包含可选的数据载荷。两种消息类型的载荷上限均为 4KB，但从 Firebase 控制台发送消息时会强制执行 1024 个字符的限制。

```
npm install firebase
```



```
firebase login --no-localhost
```



```
firebase use --add 
```



```
export http_proxy=http://localhost:1081

var Client = function(_url, protocols, options) {
    options = options || {};
    // 添加proxy配置
    options.proxy = {
        origin:  'http://localhost:1087',
    };
    …
}

NODE_PATH=`npm prefix -g`
$NODE_PATH/lib/node_modules/firebase-tools/node_modules/firebase/node_modules/faye-websocket/lib/faye/websocket/client.js

export NODE_TLS_REJECT_UNAUTHORIZED=0
```

