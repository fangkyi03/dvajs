# dva

[![NPM version](https://img.shields.io/npm/v/dva.svg?style=flat)](https://npmjs.org/package/dva)
[![Build Status](https://img.shields.io/travis/dvajs/dva.svg?style=flat)](https://travis-ci.org/dvajs/dva)
[![Coverage Status](https://img.shields.io/coveralls/dvajs/dva.svg?style=flat)](https://coveralls.io/r/dvajs/dva)
[![NPM downloads](http://img.shields.io/npm/dm/dva.svg?style=flat)](https://npmjs.org/package/dva)
[![Dependencies](https://david-dm.org/dvajs/dva/status.svg)](https://david-dm.org/dvajs/dva)
[![Join the chat at https://gitter.im/dvajs/Lobby](https://img.shields.io/gitter/room/dvajs/Lobby.svg?style=flat)](https://gitter.im/dvajs/Lobby?utm_source=share-link&utm_medium=link&utm_campaign=share-link)

[View README in English](README.md)

基于 [redux](https://github.com/reactjs/redux)、[redux-saga](https://github.com/redux-saga/redux-saga) 和 [react-router](https://github.com/ReactTraining/react-router) 的轻量级前端框架。(Inspired by [elm](http://elm-lang.org/) and [choo](https://github.com/yoshuawuyts/choo))

---
## 新功能
* **完整支持rxjs所有功能**
* **model中增加epics类型用来存放rxjs数据**
* 增加fetch Model
* [rxjs demo](https://github.com/fangkyi03/dva/tree/master/packages/dva-example) 简单rxjs-demo演示

fetch Model 使用方法
兼容现有的dva 可以直接进行移植使用
```
fetch Model 使用方法 兼容现有的dva 可以直接进行移植使用

import * as request from './utils/request'
import * as netaApi from './utils/netaApi';
// 1. Initialize
const app = dva({
    fetchConfig:{
            // 这里填写你封装好的request文件 
            // 如get post delete等得 之后的method将会直接读取你这里暴露的名字
            netTool:request,
            // url传递有两种模式
            // 1.如果netApi为空的话 url:'/book'
            // 2.如果netApi不为空的话 需要传netApi暴露出来的函数名字 url:'book'
            netApi:netaApi,
            // 全局的网络开始处理
            // 每个人对于网络请求的处理规格都是不同的 
            // 如果返回false则会走到对应的onError中不会进行数据合并
            // 如果你返回任何其他的数据 都会直接跟对应网络请求target所对应的model原有state进行合并
            onGLNetStart:({retData})=>{
                debugger
                // 不要在这里做除了逻辑判断以外的多余操作
                if (retData.error_code == 0){
                    return retData.result
                }else {
                    return false
                }
            },
            // 全局错误处理
            // 如果上面的条件不符合的话 你可以在这里 直接中断下面的数据请求
            // 然后会进入到数据合并阶段
            onGLNetError:({retData})=>{
                // 如果数据走到这里的话 会继续数据合并 但是因为那条数据出现了错误 所以
                // 出错的那条网络请求是不会合并到model中 也就不会刷新数据
                // 避免因为接口出错 导致页面重新刷新 奔溃的问题
                // 你也可以在每个接口的fetch/sendData中截获单条请求的onError
                // 出错的onError在fetch这个model中将会被记录下来 你可以直接通过fetch.isNetError(接口名字来重放)
            },
            // 上面的网络错误仅仅只是不符合netStart的条件的一种错误 还有一种是直接catch抛出的 这种就比较严重了
            onGLNetCatch:()=>{
                
            },
            // 统一单条网络结束事件
            // 即使之前的网络请求已经进入到了onNetError中 依旧会继续执行
            // 除非你这个网络请求已经直接需要抛出异常了
            onGLNetFinall:()=>{

            },
            // 扩展属性
            // 如果你想在网络生命周期中使用对应的api 但是又不想每个文件都引入一次的话 就可以使用这个功能
            // 在这里将会帮你在所有的fetch函数中 都统一放入 通过这样的方式避免 每次使用一个功能 都需要先引入一遍的尴尬
            extendAttr:()=>({a:'1'}),
            // 全局的params
            // 在这里你可以全局传递对应的model数据 让你的fetch请求更加的干净
            // 比如你不在需要每次get 或者 post的时候 传递一个token等数据过去了
            GLParams:()=>({}),
            // 全局网络请求延迟处理 默认10秒
            GLTimeOut:10000,
            // 全局超时请求
            // onGLTimeOut
    }
});
```
fetchModel使用方法
```
    this.props.dispatch({type: 'fetch/sendData',payload:[
      {
        // 要修改的model名字 需要跟model中的namespace字段名字保持一致
        target: 'example',
        // 要请求的url地址名字 这里有两种写法
        // 如果在dva初始化时的fetchConfig中传入了netApi的话 则这里的名字需要跟netApi暴露出来的函数名字保持一致
        // 否则这里就等于直接传递url
        url: 'movie',

        // 需要传递过去的参数
        // params:{}, object
        
        // 单条网络请求的延迟时间
        // timeOut: number
        // 优先级 timeout > fetchConfig > 默认10秒

        // tranData:function
        // ts:转换网络请求以后数据的接口
        // 可以通过这种方式对网络请求返回的数据进行格式转换
        // tranData:(data)=>({list:data.list})

        // tranUrl : false | true
        // 如果你的url是这样的{specId}/id 只要tranUrl为true
        // 就会自动帮你从params中找到对应的specId进行转换

        // method 这个参数会跟fetchConfig中的netTool进行绑定
        // 如method:'get' 实际上就是读取netTool中的get函数来进行网络请求
        // 这里的名字可以你自己自定义 只要你netTool中有暴露出来就可以

        // isOnlyNet : bool 
        // 是否只是进行网络请求而不刷新页面 默认为false
        // 如果你只是想进行网络请求的话 设为true就可以

        // onError : function
        // 如果你相对单条接口进行错误管理的话 可以在这里进行
        // 这里并不会阻塞全局的错误管理 全局依旧可以收到
        // 错误管理的条件是fetchConfig.onNetStart不符合自定义要求的才会进入这里

        // onCallBack ：function
        // tranData是用来做数据转换的 如果你想在数据正常获取的情况下 执行一些操作 
        // 比如this.setState的话 可以在这里进行 如果网络出现错误 是不会走到这里的

        // 小计
        // 因为这个payload是一个数组结构 所以你的多次网络请求会进行合并 只会在最后刷新一次
        // 并不会因为你有多个网络请求就会导致刷新多次
        // 其次 如果你想在发起一个网络请求以后取消这个请求的话 你只需要在重新发送一个
        // this.props.dispatch({type:'CANCEL_FETCH'}) 就可以取消那个队列的所有网络请求

        // 你也可以在fetchConfig.onNetStart中对当前的状态做出判断 只需要返回false 
        // 比如当前的token等不符合条件的时候 放弃所有请求 直到token符合要求
        // 就会走到对应的onError 而不会进行数据合并 也不会导致页面重新刷新
      }
    ]})
```

开发这个的目的 主要是为了解决在dva中 进行请求的时候 一些不必要的麻烦 目前有的功能
1.如果你当前token失效 想拦截之后的所有网络请求的话
只需要在fetchConfig.onNetStart中做处理即可 只要你返回false就会放弃所有请求

2.如果你想在网络请求以后 放弃掉当前队列的网络请求的
可以使用this.props.dispatch({type:'CANCEL_FETCH'})来放弃网络请求

3.如果你需要对全局的网络错误 或者单条网络错误进行管理的话
全局可以在fetchConfig.onGLError中或者单条onError中做出处理即可

4.全局网络异常处理
因为同属于同一个try catch的关系 如果你在onError中抛出异常的话并做说明的话 在onGLCatch中就会直接拦截到 你就可以直接知道到底是哪个接口出现了错误

5.全局loading状态处理 如果你在进行网络请求的时候 想获取当前网络请求状态的话
只需要connect({fetch})=>({...fetch})即可
fetch.isShow就是网络的状态 默认为true代表需要显示loading

6.当我们进入了对应的网络生命周期的时候 我们一般会进行一定的操作 比如弹起一个toast等
这个时候 如果我们每个文件都去引入一次 很烦 也很麻烦 
fetchConfig.extendAttr这个属性 只要你返回一个对象 所有的网络生命周期 都会直接合并这个对象里面的内容 这样你就不需要一遍遍的引入了

7.GLParams全局params传递 如果我们的部分params是可以被公用 并且需要每个接口都带上的话
就可以直接用这个属性了

8.GLTimeOut全局网络状态设置 默认fetchModel中设置为10秒 你可以在这边在进行全局设置
不过单条网络请求的timeout的优先级要高于这个全局的

9.如果你想改变一个对应model中state的状态的话 你也不需要在去一个个的写reduce了 只需要直接
this.props.dispatch({type:'xxx/generalState'},payload:{xxx})
即可 每个通过这个版本的dva载入的model都会加上这个reduce

10.丰富的网络请求生命周期
onGLNetStart 网络请求被调用的时候
onGLNetError 网络请求返回数据不符合onGLNetStart要求的时候
onGLNetCatch 网络请求中遇到了抛出异常的时候
onGLNetFinall 除非遇到网络请求抛出异常 否则任何时候都会被执行
extendAttr 扩展属性
GLParams 扩展params
GLTimeOut 全局网络延迟

11.如果当前的网络请求出现了错误 你可以使用fetch.isNetError来判断
如果你想重放错误的那个网络请求的话 可以直接使用fetch.isNetErrorData
来重放对应的那条网络请求

12.简化网络请求操作的麻烦 避免进行批量网络请求的时候需要编写非常多的代码 让你可以更加好的只关注网络请求的数据结果

demo地址在[https://github.com/fangkyi03/dva/tree/master/packages/dva-example]

## 安装体验
>npm i --save dvajs@0.0.6
## 使用说明
* **兼容原有的dva项目 想要在当前项目中体验 只需要在model文件中增加epics节点 并且将原有的dva修改成dvajs即可使用 rxjs的所有功能**
* **引入这个包会带来一定的体积变大 大概有60K左右 自己酌情考虑**
## 特性

* **易学易用**：仅有 6 个 api，对 redux 用户尤其友好
* **elm 概念**：通过 `reducers`, `effects` 和 `subscriptions` 组织 model
* **支持 mobile 和 react-native**：跨平台 ([react-native 例子](https://github.com/sorrycc/dva-example-react-native))
* **支持 HMR**：目前基于 [babel-plugin-dva-hmr](https://github.com/dvajs/babel-plugin-dva-hmr) 支持 components、routes 和 models 的 HMR
* **动态加载 Model 和路由**：按需加载加快访问速度 ([例子](https://github.com/dvajs/dva/blob/master/docs/API_zh-CN.md#dvadynamic))
* **插件机制**：比如 [dva-loading](https://github.com/dvajs/dva/tree/master/packages/dva-loading) 可以自动处理 loading 状态，不用一遍遍地写 showLoading 和 hideLoading
* **完善的语法分析库 [dva-ast](https://github.com/dvajs/dva-ast)**：[dva-cli](https://github.com/dvajs/dva-cli) 基于此实现了智能创建 model, router 等
* **支持 TypeScript**：通过 d.ts ([例子](https://github.com/sorrycc/dva-boilerplate-typescript))

## 为什么用 dva ?

* [Why dva and what's dva](https://github.com/dvajs/dva/issues/1)
* [支付宝前端应用架构的发展和选择](https://www.github.com/sorrycc/blog/issues/6)

## Demos
* [rxjs demo](https://github.com/fangkyi03/dva/tree/master/packages/dva-example) 简单rxjs-demo演示
* [Count](https://stackblitz.com/edit/dva-example-count): 简单计数器
* [User Dashboard](https://github.com/dvajs/dva/tree/master/packages/dva-example-user-dashboard): 用户管理
* [HackerNews](https://github.com/dvajs/dva-hackernews):  ([Demo](https://dvajs.github.io/dva-hackernews/))，HackerNews Clone
* [antd-admin](https://github.com/zuiidea/antd-admin): ([Demo](http://antd-admin.zuiidea.com/))，基于 antd 和 dva 的后台管理应用
* [github-stars](https://github.com/sorrycc/github-stars): ([Demo](http://sorrycc.github.io/github-stars/#/?_k=rmj86f))，Github Star 管理应用
* [react-native-dva-starter](https://github.com/nihgwu/react-native-dva-starter): 集成了 dva 和 react-navigation 典型应用场景的 React Native 实例
* [dva-example-nextjs](https://github.com/dvajs/dva/tree/master/packages/dva-example-nextjs): 和 next.js 整合使用
* [Account System](https://github.com/yvanwangl/AccountSystem.git): 小型库存管理系统

## 快速上手

[12 步 30 分钟，完成用户管理的 CRUD 应用 (react+dva+antd)](https://github.com/sorrycc/blog/issues/18)

## FAQ

### 命名由来？

> D.Va拥有一部强大的机甲，它具有两台全自动的近距离聚变机炮、可以使机甲飞跃敌人或障碍物的推进器、 还有可以抵御来自正面的远程攻击的防御矩阵。

—— 来自 [守望先锋](http://ow.blizzard.cn/heroes/dva) 。

<img src="https://zos.alipayobjects.com/rmsportal/psagSCVHOKQVqqNjjMdf.jpg" width="200" height="200" />

### 是否可用于生产环境？

当然！公司内用于生产环境的项目估计已经有 200+ 。

### 是否支持 IE8 ？

不支持。

## 下一步

以下能帮你更好地理解和使用 dva ：

* 理解 dva 的 [8 个概念](https://github.com/dvajs/dva/blob/master/docs/Concepts_zh-CN.md) ，以及他们是如何串起来的
* 掌握 dva 的[所有 API](https://github.com/dvajs/dva/blob/master/docs/API_zh-CN.md)
* 查看 [dva 知识地图](https://github.com/dvajs/dva-knowledgemap) ，包含 ES6, React, dva 等所有基础知识
* 查看 [更多 FAQ](https://github.com/dvajs/dva/issues?q=is%3Aissue+is%3Aclosed+label%3Afaq)，看看别人通常会遇到什么问题
* 如果你基于 dva-cli 创建项目，最好了解他的 [配置方式](https://github.com/sorrycc/roadhog#配置)


还要了解更多?

* 看看 dva 的前身 [React + Redux 最佳实践](https://github.com/sorrycc/blog/issues/1)，知道 dva 是怎么来的
* 在 gitc 分享 dva 的 PPT ：[React 应用框架在蚂蚁金服的实践](http://slides.com/sorrycc/dva)
* 如果还在用 dva@1.x，请尽快 [升级到 2.x](https://github.com/sorrycc/blog/issues/48)

## License

[MIT](https://tldrlegal.com/license/mit-license)
