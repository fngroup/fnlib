# Fnlib - A simple specification for FaaS.

[English](https://github.com/fngroup/fnlib/blob/master/README.md) | [中文翻译](https://github.com/fngroup/fnlib/blob/master/CN_README.md) 

## Function as a Service Lib

Fnlib(Function Lib) is a simple specification that is designed to make it easy for individual developers and businesses to use it as a reference to implement specific functionality.

Only one client usage protocol is defined by fnlib, so fnlib doesn't care how your server is implemented. In order to give developers the best experience, it is best to follow the fnlib specification at the interface level.

The specification of fnlib includes API gateway, function context, function parameters, HTTP request, error handling, and GraphQL.

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

**context**

Context is the object used to access the function execution context, Such as the parameters in the url, http header.

If function is consumed over HTTP, ```context``` object **MUST** contain the following fields:

Field |type    | descriptions
------|--------|------------
params| Object | Parameters that are transmitted via http or function call
http  | Object | null if not accessed via http, otherwise an object
http.headers| Object | If accessed via HTTP, an object containing header values

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

This JSON fragment may indicate that the execution context specifically accesses authenticated user id and username. if ```currentUser``` is null, the developer can determine that the currently accessed user is not logged in. 

**callback**

```Callback``` is to end the function or send http response, which has two parameters.

``` javascript
callback(data[, code, httpHeaders]);
```

Parameter Name |type    | descriptions
------|--------|------------
data| Any | The data want to return
code | Number | HTTP Response Code, default is 200
httpHeaders  | Object | A key-value pairs that represent HTTP Headers.

For example, to return an html that's of type ```application/html```:

``` javascript
module.exports = (context, callback) => {
    const html = fs.readFileSync('./index.html');
    callback(html, { 'Content-Type': 'application/html' });    
}
```

#### Nullability

If the value of a field is null, then set it to null and do not remove this field from the object.

### API Gateway

In the API request, if it is a POST, PUT or DELETE request, the parameter must be sent in JSON form; if it is a GET request, the parameter must be sent in the form of a key-value pair (i.e, ```a=1&b=2```).

Each API must have CORS turned on (or provide a switch) to ensure that the JavaScript application is available.

### Errors

Errors returned by Fnlib-compliant services must follow the following JSON format:

``` javascript
{
  "error": {
    "type": "ErrorType",
    "message": "Something was oops",
    "payload": {}
  }
}
```

If there are no errors, the error should be null, Other fields are explained below:

Field Name |type    | descriptions
------|--------|------------
type| String | The type of error
message  | String | Detailed Error message
payload | Object | An optional object that can provide additional error details

The error type is as follows:

Type          | descriptions                                               | HTTP Code
--------------|------------------------------------------------------------|-----------
FatalError    | Errors thrown inside the program (usually caused by throw) | 500
AuthorizationError | Lack of authorization                                 | 4xx
ValueError    | Lack of parameters or not passing type-safety checks       | 400
OtherError    | Other missing error                                        | 500

If a parameter error occurs, we recommend describing it in the payload.

If a type error occurs or there is a missing parameter error, we recommend the following JSON schema:

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

Data ```type``` is as follow:

Type Name |
----------|
String    |
Object    |
Number    |
Boolean   |
Byte      |
Float     |
Null      |

### Development Tools

The FaaS platform requires convenient development tools for developers to use, and can be divided into two general categories:

1. Online code editor
2. Command line

#### Online code editor

An online code editor allows developers to debug FaaS programs online.

A complete online code editor must have at least the following features:

1. ```Function Manager```, used to manage all functions in the project, including creation, deletion, modification;
2. ```log output```, used to display debugging information and HTTP return information;
3. ```Debugger```, used to enter request data and view return data Header and Response;
4. ```Version Control```, used to manage the function version, and can be quickly launched and rolled back with one click;
5. ```Code editor```, used to write code;
6. ```Controllable UI```, the above five functions can be divided into five panels, which can drag and drop size and close, giving developers the ability to control as they like;

#### Command line

Command Exercising Developers can debug FaaS programs locally.

A complete command line must have at least the following features:

1. ```Initialize project``` to create a project structure that conforms to the implementation FaaS standard;
2. ```Create a function ```, create a file in which you can write the function directly;
3. ```Deploy function ```, deploy the function to the cloud;
4. ```rollback function ```, specify the version rollback function;
5. ```View the function version ```, view all versions of a function;
6. ``` Local emulation debugger ```, start a debugger locally for deployment-free debugging functions;

### TODO

1. GraphQL specification
2. Database specification

### Acknowledgements

The software contained within this repository has been developed and is MIT licensed. The specification itself is not intended to be owned by a specific corporate entity, and has been developed in conjunction with other developers and organizations.
