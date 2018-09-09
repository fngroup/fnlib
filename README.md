# Fnlib - A simple specification for FaaS.

## Function as a Service Lib

Fnlib(Function Lib) is a simple specification that is designed to make it easy for individual developers and businesses to use it as a reference to implement specific functionality.

Only one client usage protocol is defined by fnlib, so fnlib doesn't care how your server is implemented. In order to give developers the best experience, it is best to follow the fnlib specification at the interface level.

The specification of fnlib includes API gateway, function context, function parameters, HTTP request, error handling, security, and GraphQL.

## What is FaaS?

Function as a service (FaaS) refers to cloud services that enable serverless app development and management. This basically means that FaaS users are able to conduct their programming (and other tasks) without the hassle of managing their own server(s). Strings of code are triggered by events on the user end, and basically outsourced to remote servers that are able to execute the intended functions.

FaaS was first introduced in 2014 with hook.io, but was popularized by Amazon’s AWS Lambda, as well as Google Cloud Functions and Microsoft Azure Functions. In addition to those, IBM has an open-source FaaS system called OpenWhisk, and the rideshare company Uber has a FaaS that runs over their private platform.

In addition to US companies, China's Aliyun and Tencent Cloud have also launched the FaaS platform, in addition to two startups doing related work (bomb and miniapp cloud).

## Why Fnlib?

### Reduced design cost

Fnlib is, as its most simple specification, an outsourcing solution. Since you're using a predefined specification that many other people will also be using we see an effect: You don't have to care about project documentation and business logic, because these are already designed.

### well-designed specification for human

Serverless has grown wildly in recent years, and every manufacturer has its own standards. In order to be compatible with multiple platforms, developers inevitably have to write a lot of logic to be compatible. All of them also derive some open source software to be compatible with multiple implementations with one interface, but these open source software are currently independent of each other.

Fnlib wants to provide a standard set of specifications that let developers use the FaaS interface just like building a Web page.

## Specifications

### Function

The **Function** is the most basic component of each FaaS program.

Given a **Function** in JavaScript is like this:

``` javascript
module.exports = async function demo_function (context, callback) {
    /*your code*/
}
```

In an asynchronous language like JavaScript, fnlib requires that each function use **async** to avoid problems caused by asynchronous.

#### Parameters

As seen in the function above, each function has two parameters: ```context``` and ```callback```.

##### context

Context is the object used to access the function execution context, Such as the parameters in the url, http header.

If function is consumed over HTTP, ```context``` object **MUST** contain the following fields:

Field |type    | descriptions
------|--------|------------
params| object | Parameters that are transmitted via http or function call
http  | object | null if not accessed via http, otherwise an object
http.headers| object | If accessed via HTTP, an object containing header values

Vendors can also add other custom objects to the context, such as current user information, system environment variables, and so on. 

For example:

``` javascript
"context": {
    "currentUser": {
        "_id": "000000", 
        "username": "fnlib"
    }
}
```

This JSON fragment may indicate that the execution context specifically accesses authenticated user id and email addresses. if ```currentUser``` is null, the developer can determine that the currently accessed user is not logged in. 

##### callback

```Callback``` is to end the function or send http response, which has two parameters.

``` javascript
callback(data[, httpHeaders]);
```

Parameter |type    | descriptions
------|--------|------------
data| Any | The data want to return
httpHeaders  | object | A key-value pairs that represent HTTP Headers.

For example, to return an html that's of type ```application/html```:

``` javascript
module.exports = (context, callback) => {
    const html = fs.readFileSync('./index.html');
    callback(html, { 'Content-Type': 'application/html' });    
}
```

### API Gateway

### Context

### HTTP

#### Request

#### Response

### GraphQL

### Errors

### Security


