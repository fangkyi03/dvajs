# dva  [中文查看](./README_zh-CN.md) 

[![NPM version](https://img.shields.io/npm/v/dva.svg?style=flat)](https://npmjs.org/package/dva)
[![Build Status](https://img.shields.io/travis/dvajs/dva.svg?style=flat)](https://travis-ci.org/dvajs/dva)
[![Coverage Status](https://img.shields.io/coveralls/dvajs/dva.svg?style=flat)](https://coveralls.io/r/dvajs/dva)
[![NPM downloads](http://img.shields.io/npm/dm/dva.svg?style=flat)](https://npmjs.org/package/dva)
[![Dependencies](https://david-dm.org/dvajs/dva/status.svg)](https://david-dm.org/dvajs/dva)
[![Join the chat at https://gitter.im/dvajs/Lobby](https://img.shields.io/gitter/room/dvajs/Lobby.svg?style=flat)](https://gitter.im/dvajs/Lobby?utm_source=share-link&utm_medium=link&utm_campaign=share-link)


* [Why dva and what's dva](https://github.com/dvajs/dva/issues/1)
* [支付宝前端应用架构的发展和选择](https://www.github.com/sorrycc/blog/issues/6)

## Demos

* [Count](https://stackblitz.com/edit/dva-example-count): Simple count example
* [User Dashboard](https://github.com/dvajs/dva/tree/master/examples/user-dashboard): User management dashboard
* [HackerNews](https://github.com/dvajs/dva-hackernews): ([Demo](https://dvajs.github.io/dva-hackernews/)): HackerNews Clone
* [antd-admin](https://github.com/zuiidea/antd-admin): ([Demo](http://antd-admin.zuiidea.com/)): Admin dashboard based on antd and dva
* [github-stars](https://github.com/sorrycc/github-stars): ([Demo](http://sorrycc.github.io/github-stars/#/?_k=rmj86f))，Github Star management tool
* [react-native-dva-starter](https://github.com/nihgwu/react-native-dva-starter): A React Native starter powered by dva and react-navigation
* [dva-example-nextjs](https://github.com/dvajs/dva/tree/master/examples/with-nextjs): Integrate dva with next.js
* [Account System](https://github.com/yvanwangl/AccountSystem.git): A small inventory management system

## Quick Start

- [Getting Started](https://github.com/dvajs/dva/blob/master/docs/GettingStarted.md)
- [12 步 30 分钟，完成用户管理的 CURD 应用 (react+dva+antd)](https://github.com/sorrycc/blog/issues/18)
=======
* [Alipay front application architecture development and selection](https://www.github.com/sorrycc/blog/issues/6)




* [Count](https://stackblitz.com/edit/dva-example-count):Simple counter
* [User Dashboard](https://github.com/dvajs/dva/tree/master/packages/dva-example-user-dashboard): User management
* [HackerNews](https://github.com/dvajs/dva-hackernews):  ([Demo](https://dvajs.github.io/dva-hackernews/))，HackerNews Clone
* [antd-admin](https://github.com/zuiidea/antd-admin): ([Demo](http://antd-admin.zuiidea.com/))，Background management application based on antd and DVA
* [github-stars](https://github.com/sorrycc/github-stars): ([Demo](http://sorrycc.github.io/github-stars/#/?_k=rmj86f))，Github Star management application
* [react-native-dva-starter](https://github.com/nihgwu/react-native-dva-starter): React Native example that integrates DVA and react-navigation typical application scenarios
* [dva-example-nextjs](https://github.com/dvajs/dva/tree/master/packages/dva-example-nextjs): Integrate with next.js
* [Account System](https://github.com/yvanwangl/AccountSystem.git): Small inventory management system

## New Features 
* **Full support for all rxjs functions**
* **The epics type is added to the model to store rxjs data**
* add fetch Model
* [rxjs demo](https://github.com/fangkyi03/dva/tree/master/packages/dva-example) Simple rxjs-demo demonstration

fetch Model Usage method
Compatible with existing DVA can be used directly for migration
```
import * as request from './utils/request'
import * as netaApi from './utils/netaApi';
```
```js
// 1. Initialize
const app = dva({
    fetchConfig:{
            // Here fill out your packaged request file
            // get post delete...  after that, method will read your exposed name directly.
            netTool:request,
            // URL transfer mode
            // 1.If netApi is empty     url:'/book'
            // 2.If netApi is not empty The name of the function that needs to be passed out by the netApi url:'book'
            netApi:netaApi,
            // Global network start processing
            // Each person has different processing specifications for network requests. 
            // Here you can directly write your corresponding processing logic. true is ok false is non conformity goto onNetError function 
            onGLNetStart:({retData})=>{
                debugger
                // Do not do redundant operations besides logical judgment here
                if (retData.error_code == 0){
                    return retData.result
                }else {
                    return false
                }
            },
            //Global error handling
            onGLNetError:({retData})=>{
              // if the data is here, the data will continue to merge, but because the data is wrong,
              // the wrong network request is not merged into model, nor does it refresh data.
              // avoid the problem of re refreshing the page due to interface errors.
              // you can also intercept the onError of a single request in the fetch/sendData of each interface.
              // the error onError will be recorded in the fetch model. You can directly replay through fetch.isNetError (interface name).
            },
            // The above network error is just a mistake that does not conform to the conditions of netStart, and the other is a direct catch thrown out of the more serious.
            onGLNetCatch:()=>{
                
            },
            // Unified single network end event
            // Even if the previous network request has entered onNetError, it will continue to perform.
            // Unless your network request has directly thrown an exception.
            onGLNetFinall:()=>{

            },
            // Extended attributes
            // If you want to send a single data to all events, you can put it here.
            //Here you will be unified in all fetch functions.
            extendAttr:()=>({a:'1'}),
            // Global parameters
            // Here you can send the corresponding model data globally to make your fetch request cleaner.
            // For example, you don't need to pass a token and other data when you need get or post each time.
            GLParams:()=>({}),
            // Global network request delay processing default 10 seconds
            GLTimeOut:10000,
            // Global timeout request
            // onGLTimeOut
    }
});
```
fetchModel Usage method
```js
    this.props.dispatch({type: 'fetch/sendData',payload:[
      {
        // The name of the model to be modified needs to be consistent with the name of the namespace field in model.
        target: 'example',
        // The name of the URL address to be requested has two ways of writing
        // If netApi is introduced into fetchConfig in DVA initialization, the name here needs to be consistent with the name of the function exposed by netApi.
        // Otherwise, it's equal to passing the URL directly.
        url: 'movie',

        // You need to pass the past parameters
        // params:{}, object
        
        // Delay time of a single network request
        // timeOut: number
        // priority timeout > fetchConfig > default 10 seconds

        // tranData:function
        // ts:The interface for converting the network to the later data
        // In this way, the data returned by the network request can be converted to format.
        // tranData:(data)=>({list:data.list})

        // tranUrl : false | true
        // If your URL is {specId}/id like this, as long as tranUrl is true
        // It will automatically help you find the corresponding specId from params.

        // method this parameter will bind to the netTool in fetchConfig.
        // For example, method:'get'is actually reading the get function in netTool for network request.
        // You can customize the name here, as long as you expose it in netTool.
        // isOnlyNet : bool 
        // Is it just a network request without refreshing the page by default false?
        // If you just want to make a network request, set it to true.

        // onError : function
        // If you compare the error management with the single interface, you can do it here.
        // It does not block global error management. It can still be received.
        // The condition of error management is that fetchConfig.onNetStart does not conform to custom requirements before entering here.

        // onCallBack ：function
        // tranData It is used to do data conversion, if you want to perform some operations in the case of normal data acquisition.
        // For example, this.setState can be carried out here. If there is a mistake in the network, it will not come here.

        // because this payload is an array structure, your multiple network requests will be merged and will only be refreshed at the end.
        // not because you have multiple network requests, which will cause many refreshes.
        // secondly, if you want to cancel this request after launching a network request, you only need to resend one.
        // this.props.dispatch ({type:'CANCEL_FETCH'}) can cancel all network requests of that queue.
        // you can also judge the current state in fetchConfig.onNetStart only need to return to false.
        // for example, when the current token does not meet the requirements, give up all requests until token meets the requirements.
        // it will go to the corresponding onError without data merging, and will not cause the page to be refreshed again.
      }
    ]})
```
The purpose of developing this is mainly to solve some unnecessary troubles existing in the request during DVA.

1. if your current token fails to intercept all requests after the network.
It only needs to be processed in fetchConfig.onNetStart, so you can give up all requests as long as you return to false.

2. if you want to give up the current queue's network request after the network request.
You can use this.props.dispatch ({type:'CANCEL_FETCH'}) to discard network requests.

3. if you need to manage global network errors or single network errors,
The whole world can be processed in fetchConfig.onGLError or single onError.

4. global network exception handling
Because it's the same relationship with the same try catch, if you throw an exception in onError and make a description, you will be directly intercepted in the onGLCatch and you can directly know which interface is wrong.

5. global loading status processing if you want to get the current network request state when you are making a network request.
Only connect ({fetch}) = > ({... Fetch}) can be used
Fetch.isShow is the state of the network default by true representing the need to display loading.

6. when we enter the corresponding network life cycle, we usually perform certain operations, such as playing a toast.
At this time, if every file is introduced, it will be very annoying and troublesome.
FetchConfig.extendAttr this property, as long as you return to all the network lifecycle of an object, will directly merge the contents of the object so that you do not need to introduce it over and over again.

7. GLParams global params delivery, if part of our params can be shared and need to be brought to each interface.
You can use this property directly

8. GLTimeOut global network state setting is set to 10 seconds in default fetchModel. You can make global settings here.
However, the timeout of a single network request is higher than the global one.

9. if you want to change the state of state corresponding to model, you do not need to write reduce directly.
This.props.dispatch ({type:'xxx/generalState'}, payload:{xxx})
Every model loaded by this version of DVA will be added to this reduce.

10. rich network request lifecycle
When the onGLNetStart network request is called
OnGLNetError network request return data does not meet onGLNetStart requirements.
The onGLNetCatch network request encountered an exception.
OnGLNetFinall will be executed at any time unless an exception is encountered by a network request.
ExtendAttr extended attributes
GLParams extended params
GLTimeOut global network delay

11. if there is a mistake in the current network request, you can use fetch.isNetError to judge.
If you want to replay the wrong network request, you can use fetch.isNetErrorData directly.
To replay the corresponding network request

12. the trouble of simplifying the network request operation and avoiding mass network requests need to write a lot of code so that you can pay more attention to the data results of the network request.

view example [https://github.com/fangkyi03/dva/tree/master/packages/dva-example]

## Installl 
> npm i --save dvajs@0.1.2
## Instructions
* **Compatible DVA projects want to experience all the functions of rxjs in the current project with only the need to add epics nodes in the model file and modify the original DVA to dvajs**
* **The introduction of this package will bring about a larger volume, perhaps 60K or so.**  

## Quick Start 

[12 steps and 30 minutes to complete user managed CURD application. (react+dva+antd)](https://github.com/sorrycc/blog/issues/18)
>>>>>>> master

## FAQ

### The reason for the naming ?

> D.Va It has a powerful machine armor, which has two fully automatic close range fusion guns, a propeller that can make the machine leap to an enemy or an obstacle, and a defense matrix that can resist a long distance attack from the front.

—— Come from [Overwatch](http://ow.blizzard.cn/heroes/dva) 。

<img src="https://zos.alipayobjects.com/rmsportal/psagSCVHOKQVqqNjjMdf.jpg" width="200" height="200" />

### Can it be used in the production environment?

Yes, it can be used

### Do you support IE8?

I won't support it

## Next step

The following can help you better understand and use DVA：

* Understanding DVA [8 concepts](https://github.com/dvajs/dva/blob/master/docs/Concepts_zh-CN.md) And how they're strung together
* Mastering DVA [ all API](https://github.com/dvajs/dva/blob/master/docs/API_zh-CN.md)
* Look at the [dva knowledge map](https://github.com/dvajs/dva-knowledgemap) all the basic knowledge, such as ES6, React, DVA, and so on
* Look at [more FAQ](https://github.com/dvajs/dva/issues?q=is%3Aissue+is%3Aclosed+label%3Afaq)Look at what other people usually have.
* If you create projects based on dva-cli, you'd better know him.[collocation method](https://github.com/sorrycc/roadhog#配置)



## Community（中文）

> 之前在 dvaantdpro 1、2 群的请勿重复加入。

<img src="https://gw.alipayobjects.com/zos/rmsportal/WdeuPFPzlKkfEWtxxeGL.jpg" width="200" />

扫码加微信群。(群满 100 人后，可加 `sorryccpro` 备注 `dva` 邀请加入）

## License

[MIT](https://tldrlegal.com/license/mit-license)
