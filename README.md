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

### API Gateway

### Context

### Parameters

### HTTP

#### Request

#### Response

### GraphQL

### Errors

### Security


