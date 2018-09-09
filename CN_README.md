# Fnlib - 一个简单的 FaaS 规范

[English](https://github.com/fngroup/fnlib/blob/master/README.md) | [中文翻译](https://github.com/fngroup/fnlib/blob/master/CN_README.md) 

## Function as a Service Lib

Fnlib（Function Lib）是一个简单的 FaaS 规范，旨在使个人开发者和企业可以轻松地将其作为参考以实现具体的 FaaS 功能。

Fnlib 只定义了一个客户端使用协议，因此 Fnlib 不关心服务器的实现方式。但是为了给开发人员提供最佳体验，最好在接口级别遵循 fnlib 规范。

The specification of fnlib includes API gateway, function context, function parameters, HTTP request, error handling, and GraphQL.

Fnlib 的规范包括 API 网关，函数上下文，函数参数，HTTP 请求，错误处理和 GraphQL，目前 GraphQL 正在制定中。

这是一个早期规范，未来可能会进行修改。

## 什么是 FaaS ?

功能即服务（FaaS）是指支持无服务器应用程序开发和管理的云服务。这意味着开发者只用操心业务逻辑，而无需管理自己的服务器。

FaaS 于 2014 年由 hook.io 首次引入，但由亚马逊的 AWS Lambda 以及 Google Cloud Functions 和M icrosoft Azure Functions 扩大规模。 除此之外，IBM 还有一个名为 OpenWhisk 的开源 FaaS 系统，而 Uber 拥有一个在私有云平台上运行的FaaS。

除了美国公司之外，中国的阿里云和腾讯云也推出了 FaaS 平台，此外还有两家创业公司从事相关工作（bomb 和知晓云）。

## 为什么要使用 Fnlib ?

### 减少设计时间

Fnlib 是一个最简单的规范，也是一种外包解决方案。由于你使用的是很多人使用过的规范，我们所预估的结果是：你不必关心项目文档和业务设计，因为这些已经设计好了。

### 为人类编写的拥有良好设计的规范

无服务器近年来发展迅速，每个制造商都有自己的标准。 为了与多个平台兼容，开发人员不可避免地要编写大量逻辑以实现兼容。 所有这些软件还衍生出一些开源软件，以便与一个接口的多个实现兼容，但这些开源软件目前彼此独立。

Fnlib 希望提供一套标准规范，让开发人员可以像构建网页一样构建 FaaS 程序。

## 规范正文

### 函数

**函数** 是每个 FaaS 程序中最基本的组成部分。

一个典型的 JavaScript 函数就像这样：

``` javascript
module.exports = async function demo_function (context, callback) {
    /*your code*/
}
```

在像 JavaScript 这样的异步语言中，fnlib 要求每个函数使用 **async** 来避免异步引起的问题。

#### 参数

如上面的函数所示，每个函数都有两个参数：```context```和```callback```。

**context**

Context是用于访问函数执行上下文的对象，例如url，http头中的参数。

如果函数是通过 HTTP 触发的，```context```对象 **必须** 包含以下字段：

字段名 |类型    | 描述
------|--------|------------
params| Object | 通过 HTTP 或函数调用传输的参数
http  | Object | 如果没有通过 HTTP 访问，则为 null，否则为对象
http.headers| Object | 如果通过 HTTP 访问，则为包含 HTTP Header 的对象

具体实现时还可以向上下文添加其他自定义对象，例如当前用户信息，系统环境变量等。

举个例子:

``` javascript
"context": {
    "currentUser": {
        "_id": "000000", 
        "username": "fnlib"
    }
}
```

此 JSON 片段可以获取已登录用户的信息，如 id 和 username。 如果``currentUser```为 null，开发人员可以确定当前访问 API 的用户没有登录。

**callback**

```Callback``` 用来结束函数或发送 HTTP Response，他有三个参数。

``` javascript
callback(data[, code, httpHeaders]);
```

参数名称 |类型    | 描述
------|--------|------------
data| Any | 想要返回的数据
code | Number | HTTP 状态码, 默认值是 200
httpHeaders  | Object | 一个 key-value 形式的 HTTP Headers 对象.

假如用户想返回一个类型为 ```application/html``` 的 HTML 文本，那么应该这么写:

``` javascript
module.exports = (context, callback) => {
    const html = fs.readFileSync('./index.html');
    callback(html, { 'Content-Type': 'application/html' });    
}
```

#### Nullability

如果字段的值为 null，则将其设置为 null，不要对象中删除此字段。

### API 网关

在 API 请求中，如果是 POST，PUT 或 DELETE 请求，则必须以 JSON 格式发送参数; 如果是 GET 请求，则必须以键值对的形式发送参数（即，``a = 1＆b = 2```）。

每个 API 必须打开 CORS（或提供开关）以确保 JavaScript 应用程序可用。

### 错误处理

兼容 Fnlib 规范的 FaaS 服务若发生错误，则必须按以下 JSON 格式返回错误信息：

``` javascript
{
  "error": {
    "type": "ErrorType",
    "message": "Something was oops",
    "payload": {}
  }
}
```

如果没有错误，则错误应为null，其他字段说明如下：

字段名城 |类型    | 描述
------|--------|------------
type| String |  错误类型名称
message  | String | 相惜的错误信息
payload | Object | 一个可选对象，用来描述详细的错误信息

错误类型如下：

类型名称          | 描述                                               | HTTP 状态码
--------------|------------------------------------------------------------|-----------
FatalError    | 程序内部抛出的错误（通常由throw引起） | 500
AuthorizationError | 权限不足                                 | 4xx
ValueError    | 类型或字段检查错误       | 400
OtherError    | 其他错误                                        | 500

如果发生参数错误，我们建议在```paylad```中对其进行描述。

如果发生类型错误或缺少参数错误，我们建议使用以下 JSON 模式：

``` javascript
{
    "payload": {
        "required": [{
            "param": "PARAM_NAME",
            "type": "TYPE_NAME"
        }],
        "invalid": [{
            "param": "PARAM_NAME",
            "expected": {
                "type": "TYPE_NAME"
            },
            "received": {
                "type": "string",
                "value": "hello world"
            }
        }]
    }
}
```

字段 ```type``` 的可选值如下:

类型名称 |
----------|
String    |
Object    |
Number    |
Boolean   |
Byte      |
Float     |
Null      |

### TODO

1. GraphQL specification

### Acknowledgements

此存储库中包含的软件已经开发并获得MIT许可。规范本身并非由特定公司拥有，而是由其他开发人员和组织共同拥有。
