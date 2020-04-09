---
title: "番茄土豆项目总结"
date: 2020-03-03T12:12:26+08:00
draft: false
---

初学 React 和 TypeScript 于是像找个项目练练手，尝试做了一个番茄土豆。

### 已部署的版本

点这里，点这里：[Tomatodo - 番茄土豆](https://kcvo.top/tomatodo/#/)

### 源代码

[Github](https://github.com/KCVO1995/tomatodo)

# 项目介绍

本项目是一个基于 React 的单页面应用，是一款非常好用的时间管理工具，它由三部分组成，分别是番茄闹钟、土豆任务和统计模块。

在番茄部分可以定义一个番茄闹钟、查看番茄历史，在土豆部分可以新增土豆任务，查看任务历史，提供修改和删除功能，在统计模块，可以分别查看番茄闹钟和土豆任务的记录并提供增删改查，还有提供折线统计图。

此外还具有登陆和注册功能，可以同步信息，在 PC 端和移动端都有良好的适配性。

# 技术栈

* **React**
* **React Router**
* **Redux**
* **TypeScript**
* **axiosLess**
* **Less**
* **Ant Design**


# 思路与经验


## 如何在 React 项目中配置别名

React Create App 官方似乎不支持 import alias, 但是每次 import 路径都是 '../../xxx.ts'，这样太麻烦了，于是搜了配置 alias 的方法

由于这个项目是 create-react-app 创建的，配置文件没有暴露

在网上搜索了一下，目前有两种方法修改配置

**方法一** `yarn eject`

这样可以让配置文件暴露处理，但是这种方法不可逆

**方法二 react-app-rewired**

* 安装 react-app-rewired

  ```bash
  yarn add react-app-rewired
  ```

* 修改 package.json, 将原本的react-script 改为 react-app-rewired

  ```json
  "scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test",
    "eject": "react-app-rewired eject"
  }
  ```

* 最后在更目录下新建 config-overrides.js, 在 config-overrides.js 里更改配置项，项目启动的时候会先在 config-overrides.js 里读取数据，对 webpack 里的默认文件进进行整合，最后才会启动

  

网上的答案是这样的

* 第一步：config-overrides.js 文件

  ```javascript
  const { override, addWebpackAlias, addDecoratorsLegacy } = require('customize-cra');
  const path = require('path')
  
  module.exports = override(
    addWebpackAlias({
      "@": path.resolve(__dirname, 'src')
    }),
    addDecoratorsLegacy(), 
  );
  ```

* 第二步: 在tsconfig.json 文件中新增 paths

  ```json
  {
    "compilerOptions": {
      "experimentalDecorators": true,
      "baseUrl": "src",
      "paths": {
        "@/*": ["src/*"]
      },
      ...
    }
  }
  ```

完成上述步骤后，当 `yarn start` 启动项目时，发现 `tsconfig.json` 文件中的配置的 paths 不见了

最后在 CRA 的  issus 下找到了解决方法

在根目录新建一个paths.json文件，加入以下内容

```json
{
  "compilerOptions": {
    "baseUrl": "src",
    "paths": {
      "@/*": ["*"]
    }
  }
}
```

然后在 tsconfig.json 中继承

```json
"extends": "./paths.json"
```



## 页面跳转的实现

```javascript
 <HashRouter>
    <Switch>
       <Route path="/login">
          <Login />
       </Route>
       <Route path="/signUp">
          <SignUp />
       </Route>
       <Route exact path="/">
          <Index />
       </Route>
    </Switch>
</HashRouter>
```

一开始按照官方文档写的 `Router` 搭建路由，折腾了半天时间弄好了，使用到 `gh-pages` 部署之后，发现页面直接是一片空白，我在想估计是 `Router` 出问题了，于是翻了一下文档，发现 React 的 `Router` 是分为 `BrowerRouter` 和 `HashRouter` 的，我对 `BrowerRrouter` 还不是很了解，但这个估计是跟 `Vue Router` 的 history 模式比较类似，是需要后端的支持才能实现，后来选择了 `HashRouter` 再根据文档进行配置。

还有就是在子组件中如何进行跳转，类似 Vue `route.push`  的功能，我使用了 `Hooks` 的 `useHistory` 实现了

```javascript
import React from 'react';
import {useHistory} from 'react-router-dom';

const Index = () => {
  const history = useHistory();
  const login = () => {
    history.push('/login');
  };
  return (
    <div>
      <button onClick={login}>登陆</button>
      Index
    </div>
  );
};

export default Index
```


# 配置 axios

## x-token 发送和响应

注册时，会收到服务器发来的 `x-token` 

在用户登陆后，会收到服务器发来的 `x-token` , 通过**响应拦截器**，将这个 `x-token` 存入 `locakStorage` 中，当用户再次发送请求时，通过**请求拦截器**把 `localStorage` 的 `x-token` 设置到请求头中发送，服务器处理请求后会返回一个新的 `x-token` 

用户再次发送请求时，重复上述操作

```javascript
// 请求拦截器
instance.interceptors.request.use( config =>  {
  const xToken = localStorage.getItem('x-token');
  if (xToken) {config.headers.Authorization = `Bearer ${xToken}`;}
  return config;
}, error => {
  console.error(error)
  return Promise.reject(error);
});

// 响应拦截器
instance.interceptors.response.use( response => {
  if (response.headers['x-token']) {
    localStorage.setItem('x-token', response.headers['x-token'])
  }
  return response;
}, error => {
  return Promise.reject(error);
});
```


# 注册页面

## Input 框中遇到的 TypeScript 类型警告

这是一个 Input 框 `onChange` 事件触发的函数，一开始我是这样写的

```javascript
const onAccountChange = (e: KeyboardEvent) => {
  setAmount(e.target.value);
};
```

但是遇到了这样的警告

`TS2339: Property 'value' does not exist on type 'EventTarget'`

原因是 TypeScript 判断错了 `e.target` 的类型

于是我把它改成这样

```javascript
const onAccountChange = (e: KeyboardEvent) => {
  const element = e.target as HTMLInputElement;
  const value = element.value;
  setAccount(value);
};
```


## 在React + TypeScript 中使用 antd Button 报错的解决方法

![ant-error](/images/ant-error.png) 

```html
<Button href="antd" type="primary" onClick={commit}>提交</Button>
// 添加一个 href 属性
```


##  如何验证登陆状态


axios 获取用户数据，如果获取成功是验证登陆，获取失败非法登录 status 返回401，需要跳转回登陆页面

当用户没登陆就进入主页时，服务器返回 `401` ，通过响应拦截器，判断 `status` 进行**全局拦截**

有一个比较棘手的问题是，`HashRouter` 怎么组件外跳转，我搜了官方提供的方法，可是官方文档的方法是针对 `BrowserRouter` 的，于是我试着改造了一下，运行成功了

由于 Router v4.0 剥离了 history，所有要先安装 history

```
yarn ad history
```

响应拦截器的代码是这样

```javascript
instance.interceptors.response.use(response => {
  if (response.headers['x-token']) {
    localStorage.setItem('x-token', response.headers['x-token']);
  }
  return response;
}, error => {
  if (error.response.status === 401) {
    // 如果 status 是 401，立刻返回登陆页面
    createHashHistory().push('/login');
  }
  return Promise.reject(error);
});

export default instance;
```

## 在使用 ant-design 的下拉组件时，遇到的报错


![ant-error](/images/ant-error.png) 

是因为严格模式下，不支持旧的api，在antd 的 issus 下看到，最近很多人都在问这个问题，react 默认开启了严格模式，需要在 index.tsx 中把 `React.StrictMode` 便签删了



# 土豆任务界面

## 如何新增 Todo

新增 Todo 需要用户在输入框输入任务名称，然后点击提交

HTML 是使用 antd 的 Input 组件

```html
<div className="todo-input">
  <Input size="large"
         placeholder="添加新任务"
         // 这是input 的后缀图标，当输入框有内容时显示，无内容时隐藏
         suffix={description ? <EnterOutlined onClick={commit}/> <span/>}
         onChange={e => setDescription(e.target.value)}
         onPressEnter={commit}
         value={description}
      />
</div>
```

当用户按下回车，或者点击 suffix 的 回车图案时，执行 commit

```javascript
const commit = () => {
  if (description !== '') {
    props.addTodo({description});
    // 完成 addTodo 后，重置输入框内容
    setDescription('');
  } else {
    alert('请指定一个todo');
  }
};
```

addTodo 由父组件提供

```javascript
const addTodo = async (params: {}) => {
  try {
    const response = await axios.post('todos', params);
    // 将新增的 todo 和已有的 todoList 合并 
    reset([response.data.resource,...todoList])
  } catch (e) {throw new Error(e);}
};
```


## 如何切换 TodoItem 的 editing 状态

当用户编辑 Todo 时，Todo 进入 editing 状态，这时需要动态新增一个 `editing` 类名，然后结束编辑时，Todo 取消 `editing` 状态，移除 `editing` 类名。我们可以使用 `classnams` 这个库来帮我们动态添加 `className`

```typescript
import classNames from 'classnames';
const todoItemClass = classNames({
    'input-todos': true,
    editing: props.todo.editing,
});
return (
  <div className={todoItemClass}>
    ...
  </div>
);
```


## 如何将 input 框设为自动对焦

用户双击 Todo 时，可以进入 `editing` 状态，此时 input 框出现并自动对焦，自动对焦 input 框是一个小细节，但是却能提高用户体验

```javascript
<Input autoFocus={true}/>
```


# 番茄闹钟页面

## 如何实现倒计时

首先计算 tomato 的 `started_at` 和 现在时间的差值，再对比 `duration` 获得倒计时需要的时间戳，然后使用 `setTimeInverval` 每隔 1000ms 对时间戳减 1000，实现倒计时，结束后再 `clearInterval`

还有一个小细节就是，网页便签上也能显示时间，方便用户查看，可以使用 `doucment.title` 进行设置

## 怎么将已完成的番茄按天分组

我使用了 `lodash` 库的 `groupBy` 进行分组，分组条件使用了 `date-fns` 库的 `format` 进行对时间格式化处理

```javascript
const getfinishedTomato = () => {
  const finished = state.tomatoes.filter(t => t.description && t.ended_at && !t.aborted);
  return _.groupBy(finished, (tomato) => {
    return format(new Date(tomato.started_at), 'yyyy-MM-d');
  });
};
```

### 折线统计图如何做

绘制折线统计图，我使用了svg  `polygon` 标签来画

```html
<svg>
   <polygon fill="rgba(215,78,78,0.1)" stroke="rgba(215,78,78,0.5)"
            strokeWidth="1"
            points={getPoints()}/>
</svg>
```

通过计算生成 `pointArr`， `pointArr` 是横纵坐标组成的数组

```javascript
['0,60', ...pointArr,'240,0','240,60'].join(' ')
```




