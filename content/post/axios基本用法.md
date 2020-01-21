---
title: "axios 基本用法"
date: 2020-01-20T12:23:26+08:00
draft: false
---

### 安装

使用 npm:

`$ npm install axios`

使用 bower:

`$ bower install axios`

使用 cdn:

`<script src="https://unpkg.com/axios/dist/axios.min.js"></script>`

### 执行 GET 请求

```
// 为给定 ID 的 user 创建请求
axios.get('/user?ID=12345')
.then(function (response) {
console.log(response);
})
.catch(function (error) {
console.log(error);
});

// 上面的请求也可以这样做
axios.get('/user', {
params: {
ID: 12345
}
})
.then(function (response) {
console.log(response);
})
.catch(function (error) {
console.log(error);
});
```

### 执行 POST 请求

```
axios.post('/user', {
firstName: 'Fred',
lastName: 'Flintstone'
})
.then(function (response) {
console.log(response);
})
.catch(function (error) {
console.log(error);
});
执行多个并发请求
function getUserAccount() {
return axios.get('/user/12345');
}

function getUserPermissions() {
return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()])
.then(axios.spread(function (acct, perms) {
// 两个请求现在都执行完成
}));
```

### 创建实例

可以使用自定义配置新建一个 axios 实例

```
axios.create([config])
const instance = axios.create({
baseURL: 'https://some-domain.com/api/',
timeout: 1000,
headers: {'X-Custom-Header': 'foobar'}
});
```

### 响应结构

某个请求的响应包含以下信息

```
{
// `data` 由服务器提供的响应
data: {},

// `status` 来自服务器响应的 HTTP 状态码
status: 200,

// `statusText` 来自服务器响应的 HTTP 状态信息
statusText: 'OK',

// `headers` 服务器响应的头
headers: {},

// `config` 是为请求提供的配置信息
config: {},
// 'request'
// `request` is the request that generated this response
// It is the last ClientRequest instance in node.js (in redirects)
// and an XMLHttpRequest instance the browser
request: {}
}
```

使用 then 时，你将接收下面这样的响应 :

```
axios.get('/user/12345')
.then(function(response) {
console.log(response.data);
console.log(response.status);
console.log(response.statusText);
console.log(response.headers);
console.log(response.config);
});
```

### 配置默认值

你可以指定将被用在各个请求的配置默认值

全局的 axios 默认值

```
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```

自定义实例默认值

```
// Set config defaults when creating the instance
const instance = axios.create({
baseURL: 'https://api.example.com'
});

// Alter defaults after instance has been created
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
```

配置的优先顺序

配置会以一个优先顺序进行合并。这个顺序是：在 lib/defaults.js 找到的库的默认值，然后是实例的 defaults 属性，最后是请求的 config 参数。后者将优先于前者。这里是一个例子：

```
// 使用由库提供的配置的默认值来创建实例
// 此时超时配置的默认值是 `0`
var instance = axios.create();

// 覆写库的超时默认值
// 现在，在超时前，所有请求都会等待 2.5 秒
instance.defaults.timeout = 2500;

// 为已知需要花费很长时间的请求覆写超时设置
instance.get('/longRequest', {
timeout: 5000
});
```

### 拦截器

在请求或响应被 then 或 catch 处理前拦截它们。

```
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
// 在发送请求之前做些什么
return config;
}, function (error) {
// 对请求错误做些什么
return Promise.reject(error);
});

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
// 对响应数据做点什么
return response;
}, function (error) {
// 对响应错误做点什么
return Promise.reject(error);
});
如果你想在稍后移除拦截器，可以这样：

const myInterceptor = axios.interceptors.request.use(function () {/_..._/});
axios.interceptors.request.eject(myInterceptor);
可以为自定义 axios 实例添加拦截器

const instance = axios.create();
instance.interceptors.request.use(function () {/_..._/});
```

### 取消

使用 cancel token 取消请求

Axios 的 cancel token API 基于 cancelable promises proposal，它还处于第一阶段。
可以使用 CancelToken.source 工厂方法创建 cancel token，像这样：

```
const CancelToken = axios.CancelToken;
const source = CancelToken.source();

axios.get('/user/12345', {
cancelToken: source.token
}).catch(function(thrown) {
if (axios.isCancel(thrown)) {
console.log('Request canceled', thrown.message);
} else {
// 处理错误
}
});

axios.post('/user/12345', {
name: 'new name'
}, {
cancelToken: source.token
})

// 取消请求（message 参数是可选的）
source.cancel('Operation canceled by the user.');
还可以通过传递一个 executor 函数到 CancelToken 的构造函数来创建 cancel token：

const CancelToken = axios.CancelToken;
let cancel;

axios.get('/user/12345', {
cancelToken: new CancelToken(function executor(c) {
// executor 函数接收一个 cancel 函数作为参数
cancel = c;
})
});

// cancel the request
cancel();
```

参考文章： axios 中文文档
