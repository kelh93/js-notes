# XMLHttpRequest

最简单的发起请求例子

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', '/api', true); // true：同步请求 ; false: 异步请求
xhr.setRequestHeader('Content-type', 'application/json'); // 设置请求头
xhr.onreadystatechange = function(){
    if(xhr.readyState == XMLHttpRequest.DONE && xhr.status == 200){
        console.log(xhr.responseText);
    }
}
xhr.send();
xhr.onload = function(){
    // 请求成功之后要做的事情
    console.log('readyState', xhr.readyState);
}
// xhr.abort();// 取消请求
```

## 属性

### readyState(只读)

| 值   | 状态             | 描述                                            |
| ---- | ---------------- | ----------------------------------------------- |
| 0    | UNSET            | 代理被创建，但尚未调用 open() 方法              |
| 1    | OPENED           | open() 方法已经被调用                           |
| 2    | HEADERS_RECEIVED | send() 方法已经被调用，并且头部和状态已经可获得 |
| 3    | LOADING          | 下载中； responseText 属性已经包含部分数据      |
| 4    | DONE             | 下载操作已完成                                  |

### Status

> http状态码 [点击查看HTTP 状态码](./HttpStatusCode.md)

200 ok

### responseType

**响应数据类型**

- ""

  空字符串

- arraybuffer

  `response`是一个包含二进制数据的JavaScript `ArrayBuffer`

- blob

  `response`是一个包含二进制数据的`Blob`对象。

  > 处理下载二进制数据。XMLHttpRequest.responseType = 'blob';

- document

  `response`是一个HTML Document 或者 XML Document。

- json

  `response`是一个JavaScript对象。

- text

  `response`是一个`DomString`对象表示的文本。



## 方法

### send(body)

body参数可选，body的类型可以是 **arraybuffer**、**blob**、**string**、**formdata**等

#### 语法

> XMLHttpRequest.send(body);



```javascript
// GET/POST

XMLHttpRequest.send();
XMLHttpRequest.send(ArrayBuffer data);
XMLHttpRequest.send(ArrayBufferView data);
XMLHttpRequest.send(Blob data);
XMLHttpRequest.send(Document data);
XMLHttpRequest.send(DOMString? data);
XMLHttpRequest.send(FormData data);
```

### onreadystatechange

#### 语法

> XMLHttpRequest.onreadystatechange = callback

当 `readyState`改变的时候，`callback`会被调用。

### setRequestHeader

#### 语法

> XMLHttpRequest.setRequestHeader(header, value);

此方法用于设置请求头信息，必须在`xhr.open()`之后，`xhr.send()`	之前调用。

如果多次对同一个请求头赋值，只会生成一个合并了多个值的请求头。

### getResponseHeader

#### 语法

> var myHeader =  XMLHttpRequest.getResponseHeader(name);

获取指定的请求头。

### getAllResponseHeaders

获取所有的请求头。









