# dva  [中文查看](./README_zh-CN.md) 

[![NPM version](https://img.shields.io/npm/v/dva.svg?style=flat)](https://npmjs.org/package/dva)
[![Build Status](https://img.shields.io/travis/dvajs/dva.svg?style=flat)](https://travis-ci.org/dvajs/dva)
[![Coverage Status](https://img.shields.io/coveralls/dvajs/dva.svg?style=flat)](https://coveralls.io/r/dvajs/dva)
[![NPM downloads](http://img.shields.io/npm/dm/dva.svg?style=flat)](https://npmjs.org/package/dva)
[![Dependencies](https://david-dm.org/dvajs/dva/status.svg)](https://david-dm.org/dvajs/dva)
[![Join the chat at https://gitter.im/dvajs/Lobby](https://img.shields.io/gitter/room/dvajs/Lobby.svg?style=flat)](https://gitter.im/dvajs/Lobby?utm_source=share-link&utm_medium=link&utm_campaign=share-link)


* [Why dva and what's dva](https://github.com/dvajs/dva/issues/1)
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
view example [https://github.com/fangkyi03/dva/tree/master/packages/dva-example]

## Installl 
> npm i --save dvajs@${version}
## Instructions
* **Compatible DVA projects want to experience all the functions of rxjs in the current project with only the need to add epics nodes in the model file and modify the original DVA to dvajs**
* **The introduction of this package will bring about a larger volume, perhaps 60K or so.**  

## Quick Start 

[12 steps and 30 minutes to complete user managed CURD application. (react+dva+antd)](https://github.com/sorrycc/blog/issues/18)

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



## License

[MIT](https://tldrlegal.com/license/mit-license)
