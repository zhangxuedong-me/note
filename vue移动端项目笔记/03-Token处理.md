# 三、Token 处理

- 什么是 Token？
  - 一个令牌，用来请求需要权限的接口或者判断登录状态使用
- 我们项目中哪里需要使用呢？
  - 请求需要权限的接口
  - 底部导航栏中也需要使用判断登录状态给出不同的提示
    - 已登录：我的
    - 未登录：未登录
  - 我的页面也要使用，判断登录状态给出不同的页面展示
  - ...
- 所以需要在登录成功以后，把 token 存储起来
- 往哪儿存？
  - 本地存储
  - Vuex 容器（推荐，专业工具干专业事儿）

![1568949544263](assets/1568949544263.png)

- 登录成功，将 token 存储到本地存储
- 为了持久化，还需要把 token 放到本地存储

## 使用 Vuex 容器存储 token

1、在 `src/store/index.js` 中

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    // 登录用户，一个对象，包含 token 信息
+    user: null
  },
  mutations: {
+    setUser (state, data) {
+      state.user = data
+    }
  },
  actions: {
  },
  modules: {
  }
})

```

2、登录成功以后将后端返回的 token 相关数据存储到容器中

```js
async onLogin () {
  // const loginToast = this.$toast.loading({
  this.$toast.loading({
    duration: 0, // 持续时间，0表示持续展示不停止
    forbidClick: true, // 是否禁止背景点击
    message: '登录中...' // 提示消息
  })

  try {
    const res = await login(this.user)

    // res.data.data => { token: 'xxx', refresh_token: 'xxx' }
+    this.$store.commit('setUser', res.data.data)

    // 提示 success 或者 fail 的时候，会先把其它的 toast 先清除
    this.$toast.success('登录成功')
  } catch (err) {
    console.log('登录失败', err)
    this.$toast.fail('登录失败，手机号或验证码错误')
  }

  // 停止 loading，它会把当前页面中所有的 toast 都给清除
  // loginToast.clear()
}
```

完了，在浏览器中点击登录，通过 VueDevTools 查看数据是否能正常的存储到 Vuex 容器中。

## 持久化 token 存储

Vuex 容器中的数据只是为了方便在其他任何地方能获取登录状态数据，但是页面刷新还是会丢失数据状态，所以我们还要把数据进行持久化中以防止页面刷新丢失状态的问题。

前端持久化常见的方式就是：

- 本地存储
- Cookie

这里我们以使用本地存储持久化用户状态为例。

为了方便，这里先封装一个用于操作本地存储的工具模块。

1、创建 `src/utils/storage.js`

```js
export const getItem = name => {
  return JSON.parse(window.localStorage.getItem(name));
};

export const setItem = (name, value) => {
  return window.localStorage.setItem(name, JSON.stringify(value));
};

export const removeItem = name => {
  window.localStorage.removeItem(name);
};
```

2、然后在容器中使用使用本地存储持久化 token 数据

```js
import Vue from 'vue'
import Vuex from 'vuex'
+ import { setItem, getItem } from '@/utils/storage'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    // 登录用户，一个对象，包含 token 信息
+    user: getItem('user')
    // user: null
  },
  mutations: {
    setUser (state, data) {
      state.user = data

      // 为了防止刷新丢失 state 中的 user 状态，我们把它放到本地存储
+      setItem('user', state.user)
    }
  },
  actions: {
  },
  modules: {
  }
})

```

测试：重新登录，查看本地存储是否有 token 数据，刷新浏览器，查看 Vuex 容器是否有 token 数据。

经过以上处理之后，接下来我们项目中所有需要使用 token 的业务，直接找 Vuex 容器拿，不需要关心数据从哪儿来的。

## 使用请求拦截器统一添加 token

很多接口都需要提供 token 才能访问。

方式一：在每次请求的时候手动添加（麻烦）

```js
axios({
  method: "",
  url: "",
  headers: {
    token数据
  }
});
```

方式二：使用请求拦截器统一添加（推荐，更方便）

在 `src/utils/request.js` 中添加拦截器统一设置 token：

```js
...
// 在非组件模块中访问容器，直接 import 加载即可
import store from '@/store'

// 请求拦截器
request.interceptors.request.use(
  function (config) {
    const user = store.state.user
    if (user) {
      // 注意：后端要求 Bearer 后面有个空格，多了少了都不行
      // Authorization 也是后端要求的名字，不能乱写
      config.headers.Authorization = `Bearer ${user.token}`
    }
    // Do something before request is sent
    return config
  },
  function (error) {
    // Do something with request error
    return Promise.reject(error)
  }
)
```

## 处理 token 过期（在功能优化中进行讲解）

- 为什么 token 过期时间这么短？
  - 为了安全
- 过期了怎么办？
  - 通过登录页面重新登录获取 token
  - 使用 refresh_token 重新获取新的 token

![1567481874811](./assets/1567481874811.png)

在请求的响应拦截器中统一处理 token 过期：

```js
/**
 * 封装 axios 请求模块
 */
import axios from "axios";
import jsonBig from "json-bigint";
import store from "@/store";
import router from "@/router";

// axios.create 方法：复制一个 axios
const request = axios.create({
  baseURL: "http://ttapi.research.itcast.cn/" // 基础路径
});

/**
 * 配置处理后端返回数据中超出 js 安全整数范围问题
 */
request.defaults.transformResponse = [
  function(data) {
    try {
      return jsonBig.parse(data);
    } catch (err) {
      return {};
    }
  }
];

// 请求拦截器
request.interceptors.request.use(
  function(config) {
    const user = store.state.user;
    if (user) {
      config.headers.Authorization = `Bearer ${user.token}`;
    }
    // Do something before request is sent
    return config;
  },
  function(error) {
    // Do something with request error
    return Promise.reject(error);
  }
);

// 响应拦截器
request.interceptors.response.use(
  // 响应成功进入第1个函数
  // 该函数的参数是响应对象
  function(response) {
    // Any status code that lie within the range of 2xx cause this function to trigger
    // Do something with response data
    return response;
  },
  // 响应失败进入第2个函数，该函数的参数是错误对象
  async function(error) {
    // Any status codes that falls outside the range of 2xx cause this function to trigger
    // Do something with response error
    // 如果响应码是 401 ，则请求获取新的 token

    // 响应拦截器中的 error 就是那个响应的错误对象
    console.dir(error);
    if (error.response && error.response.status === 401) {
      // 校验是否有 refresh_token
      const user = store.state.user;

      if (!user || !user.refresh_token) {
        router.push("/login");

        // 代码不要往后执行了
        return;
      }

      // 如果有refresh_token，则请求获取新的 token
      try {
        const res = await axios({
          method: "PUT",
          url: "http://ttapi.research.itcast.cn/app/v1_0/authorizations",
          headers: {
            Authorization: `Bearer ${user.refresh_token}`
          }
        });

        // 如果获取成功，则把新的 token 更新到容器中
        console.log("刷新 token  成功", res);
        store.commit("setUser", {
          token: res.data.data.token, // 最新获取的可用 token
          refresh_token: user.refresh_token // 还是原来的 refresh_token
        });

        // 把之前失败的用户请求继续发出去
        // config 是一个对象，其中包含本次失败请求相关的那些配置信息，例如 url、method 都有
        // return 把 request 的请求结果继续返回给发请求的具体位置
        return request(error.config);
      } catch (err) {
        // 如果获取失败，直接跳转 登录页
        console.log("请求刷线 token 失败", err);
        router.push("/login");
      }
    }

    return Promise.reject(error);
  }
);

export default request;
```
