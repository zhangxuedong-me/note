# Vue基础第六天笔记

## 反馈 

| ***  | router-to属性赋值的这几种方式有点搞不懂 |
| ---- | --------------------------------------- |
| ***  | 什么是$router控制路由导航的思路？？     |

## 复习 

* router-link  => to  => 字符串/变量 / 对象 path/name
* 编程式导航 => 代码跳转 => this.$router =>路由实例对象 push/replace/go(n)
* 路由  => 动态路由
* 1 路由规则  =>   {  path: '/user/:参数名' }  2  传递参数  to =>/user/120 3  this.$route.params.参数名
* 路由的重定向 => 可以将一个地址强制跳转到另外一个地址  => 拦截谁在谁的路由规则写redirect
* 嵌套路由 => 一级路由 => 一级路由对应的组件 => 二级路由的容器 => 一级路由的路由规则里 =>  children => 二级路由的路由表 
* 脚手架 
* 2.0/3.0/4.0 => 创建项目 vue  init  webpack-simple  项目名
* vue create 项目名
* import 变量  from  路径(包名) / export default 变量/对象/数组
* 单文件组件 => *.vue文件就是一个组件 =>  html + js +css  
* template => 有且只有一个根节点
* script  =>export default  {  组件内容  data/methods/props/watch/mounted/....  }
* style => 组件的样式 
* import  组件变量名  from  '组件路径'  => 注册 => 全局注册/局部注册

## 基础-示例项目效果演示

> **`目标`**演示示例项目的最终效果 分拆功能
>
> ![1571813081192](assets/1571813081192.png)
>
> ![1571813237196](assets/1571813237196.png)
>
> 英雄项目演示 =>  功能拆分 => 路由 =>  列表 =>  新增 修改 删除  查询  
>
> spa => 路由
>
> 英雄列表 =>crud

## 基础-示例项目-导入素材处理样式

> **`目标-任务`**:将项目所需样式导入到项目中 
>
> * npm  i  包名  --save 或者 -S  **`运行时依赖`** => vuejs => 项目上线之后 依然被需要的文件
> * npm i 包名 --save--dev 或者 -D  **`开发时依赖`**  => 当项目上线时 不再需要
>
> - 安装 bootstrap固定版本
>
> ```bash 
> npm i  bootstrap@3.3.7 -S  或者  --save
> ```
>
> 安装完成之后 ,在main.js入口处引入js文件
>
> ```js
> import "./../node_modules/bootstrap/dist/css/bootstrap.css"; // 引入 bootstarp的样式文件
> import "./assets/index.css"; // 引入index.css
> 
> ```
>
> 重启运行,发现bootstrap.css文件 **`运行报错`** 
>
> 根据错误 需要在webpack.config.js增加对不识别文件的处理
>
> ```js	
> {
> test: /.(ttf|woff2|woff|eot)$/,
> loader: "file-loader",
> options: {
> name: "[name].[ext]?[hash]"
> }
> }
> ```
>
> 

## 基础-示例项目-提取公共组件-头部-侧边栏-列表,并预览效果

> **`目标-任务`**:将静态内容的 头部 侧边栏 , 列表分别封装成Vue组件 ,并在视图中显示
>
> **`路径`** 提取组件
>
> 1. 新建vue文件
> 2. 拷贝html静态内容到 template中
> 3. 在app.vue中引入注册组件
> 4. 注册在app.vue的组件中 
> 5. 在app.vue的模板中使用注册组件 
> 6. app-header/app-slidebar/hero-list

## 基础-示例项目-提取路由模块

**`目标-任务`** 在示例项目中 提取路由模块,并应用视图

>**`路径`**  提取路由模块
>
>1  安装路由 
>
>```bash 
>npm i vue-router -S // 安装路由模块
>```
>
>2   在main.js中引入 路由模块
>
>```js
>import VueRouter from 'vue-router ' // 引用router
>```
>
>3  使用router 
>
>```js 
>Vue.use(VueRouter) // 使用router
>```
>
>4   实例化 router 
>
>```js 
>const router = new VueRouter({
>routes:[] //实例化routes
>})
>```
>
>5  配置理由表
>
>```js
>const router = new VueRouter({
>routes: [
>{ path: "/heroes", component: AppList },
>{ path: "/foo", component: Foo },
>{ path: "/bar", component: Bar }
>] // 路由表
>}); // 实例化router
>```
>
>
>
>**注意** 一般来说 路由表 需要单独一个文件   可以将router提取成一个js文件 
>
>6   提取 三个组件 appList(主要 )  weapon(组件) equipment(组件) 完善路由表
>
>```html
><template>
><div>Bar组件</div>
></template>
>
><script>
>export default {};
></script>
>
><style>
></style>
>
>```
>
>7   在App.vue中路由承载视图
>
>* router-link 默认生成 a标签  但是 有一个属性可以决定生成的标签 
>* router-link  => tag => 决定生成的标签是什么
>
>```html
><div>
><AppHeader></AppHeader>
><div class="row">
><AppSilder></AppSilder>
><div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">
> <router-view></router-view> // 加入承载视图
></div>
></div>
></div>
>```

* 教学  => 一个文件 
* main.js => 整个项目入口 => new Vue() =>  挂载到main.js
* router => index.js => 配置路由表 => Vue.use(VueRouter) => 实例化路由 配置路由表 => export  default  路由实例对象  =>main.js 引入  => 挂载
* App.vue => 整个项目的根组件 => 容器 => router-view 
* router=> index.js => 配置路由表 => path  => component(  ) => 单文件组件 => *.vue就是一个组件 =>引入很多组件 => 挂载 路由上 path => 组件

##  基础-示例项目-json-server-启动接口服务器

>**`目标-任务`**准备json-server服务器.启动实现 数据接口 增删改查的联通
>
>**`路径`**: 启动json-server服务器
>
>1  安装json-server  
>
>**注意** json-server 是一个命令行工具,和vue以及vue-cli没有任何关系 所以安装在任何位置都可以
>
>```bash 
>npm i -g json-server // 安装json-server 
>```
>
>2  新建json文件 
>
>```json
>{
>"heroes": [
>{ "name": "张三", "id": 1, "gender": "男" },
>{ "name": "李白", "id": 2, "gender": "女" },
>{ "name": "吕布", "id": 3, "gender": "男" }
>]
>}
>
>```
>
>3  启动json-server
>
>```bash
>json-server --watch db.json 
>```
>
>

## 基础-示例项目-列表渲染

>**`目标-任务`**完成英雄列表的数据加载及渲染
>
>**`路径`**:
>
>1 安装axios 插件 
>
>```bash
>npm i axios // 安装axios插件
>```
>
>2  英雄列表组件中引入 axios , 
>
>```js 
>import axiod from 'axios' // 引入axios
>```
>
>3  定义数据list
>
>```js
>data() {
>return {
> list: []
>};
>}
>```
>
>4  请求英雄列表的方法封装 
>
>```js
>loadData() {
> //restful规则
> axois.get("http:localhost:3000/heros").then(result => {
>   this.list = result.data;
> });
>}
>```
>
>5  在事件中加入 请求方法  mounted(组件渲染完毕之后) created(组件实例创建完毕之后执行)
>
>```js
>// 实例完成事件
>created() {
>//可以加
>this.loadData();
>},
>```
>
>6  渲染列表list

## 基础-示例项目-删除功能

>**`目标`**实现英雄列表的删除功能
>
>路径: 删除功能
>
>1  注册删除事件 
>
>```html
><a href="javascript:void(0)" @click="delItem(item.id)">删除</a>
>```
>
>2 定义删除方法  实现删除逻辑
>
>```js
>// 定义删除方法
>// id为要删除id的方法
>delItem(id) {
> // restful规则
> if (confirm("确认删除此条数据")) {
>   axios.delete("http://localhost:3000/heroes" + id).then(result => {
>     if (result.status === 200) {
>       // 判断删除状态 是否成功
>       this.loadData(); // 刷新数据
>     }
>   });
> }
>}
>```
>
>3  根据状态 进行刷新页面
>
>```js
>this.loadData(); // 刷新数据
>```
>
>

## 基础-示例项目-添加-渲染添加组件

>**`目标-任务`**添加组件功能的静态实现
>
>路径: 添加视图的实现
>
>1 新建add.vue组件 并写入静态内容(拷贝)
>
>```html
><!-- 添加静态内容到template模板下 -->
><div>
><h2 class="sub-header">添加英雄</h2>
><form>
><div class="form-group">
><label for="exampleInputEmail1">用户名</label>
><!-- 使用v-model的方式来绑定表单 -->
><input
>v-model="formData.name"
>type="text"
>class="form-control"
>id="exampleInputEmail1"
>placeholder="请输入姓名"
>/>
></div>
><div class="form-group">
><label for="exampleInputPassword1">性别</label>
><input
>v-model="formData.gender"
>type="text"
>class="form-control"
>id="exampleInputPassword1"
>placeholder="请输入性别"
>/>
></div>
><!-- 给添加英雄按钮注册一个事件 -->
><button type="submit" class="btn btn-success" @click="addHero">添加英雄</button>
></form>
></div>
>
>```
>
>2  在路由表中配置添加功能的路由
>
>```js
>{ path: "/add", component: Add }  // 引入组件 配置路由
>
>```
>
>3  给列表组件中的添加按钮 添加l导航 到添加功能路由的导航
>
>```html
><!-- 给添加功能添加路由导航 -->
><!-- a连接形式的跳转 -->
>
><a class="btn btn-success" href="#/add">添加</a>
><!-- router-link的跳转 -->
>
><router-link class="btn btn-success" to="/addHero">添加英雄</router-link>
>
>```
>
>4  根据业务场景调整页面模板
>
>```html
><div>
><h2 class="sub-header">添加英雄</h2>
><form>
><div class="form-group">
><label for="exampleInputEmail1">用户名</label>
><input type="email" class="form-control" id="exampleInputEmail1" placeholder="请输入姓名" />
></div>
><div class="form-group">
><label for="exampleInputPassword1">性别</label>
><input type="password" class="form-control" id="exampleInputPassword1" placeholder="请输入性别" />
></div>
><button type="submit" class="btn btn-success">添加英雄</button>
></form>
></div>
>```
>
>```
>
>```

## 基础-示例项目-添加-功能实现

>**`目标-任务`** 实现添加英雄的功能
>
>**`路径`**:  添加功能的实现
>
>1  定义表单数据  和  表单进行绑定 
>
>```js
>data() {
>return {
> // 定义一个数据对象 存储 姓名和性别
> formData: {
>   name: "", // 姓名
>   gender: "" // 性别
> }
>};
>}  //定义一个数据对象
>```
>
>2 	注册添加按钮的点击事件 
>
>```html
><!-- 给添加英雄按钮注册一个事件 -->
> <button type="submit" class="btn btn-success" @click="addHero">添加英雄</button>
>```
>
>3   实现 添加的前后逻辑
>
>```js
>// 添加英雄方法
>addHero() {
> // 判断填报信息是否为空
> if (this.formData.name && this.formData.gender) {
>   // 该判断条件是判断 当前的姓名和 性别都不为空
>   // restful规则
>   axios
>     .post("http://localhost:3000/heroes", this.formData)
>     .then(result => {
>       // 注意这里添加成功的状态码 是 201
>       if (result.status === 201) {
>         // 添加成功之后 要跳转回列表页
>         // 编程式导航
>         this.$router.push({ path: "/heroes" });
>       } else {
>         alert("添加失败");
>       }
>     });
> } else {
>   alert("提交信息不能为空");
> }
>}
>```
>
>

## 基础-示例项目-编辑-添加编辑组件

>**`目标-任务`**实现英雄列表的编辑功能组件渲染
>
>路径: 编辑功能渲染
>
>新建编辑组件
>
>**注意** 由于 编辑组件和添加组件页面结构基本一致  可以 直接拷贝添加组件的内容
>
>```html
><!-- 添加静态内容到template模板下 -->
><div>
><h2 class="sub-header">添加英雄</h2>
><form>
> <div class="form-group">
>   <label for="exampleInputEmail1">用户名</label>
>   <!-- 使用v-model的方式来绑定表单 -->
>   <input
>     v-model="formData.name"
>     type="text"
>     class="form-control"
>     id="exampleInputEmail1"
>     placeholder="请输入姓名"
>   />
> </div>
> <div class="form-group">
>   <label for="exampleInputPassword1">性别</label>
>   <input
>     v-model="formData.gender"
>     type="text"
>     class="form-control"
>     id="exampleInputPassword1"
>     placeholder="请输入性别"
>   />
> </div>
> <!-- 给添加英雄按钮注册一个事件 -->
> <button type="submit" class="btn btn-success" @click="editHero">编辑英雄</button>
></form>
></div>
>```
>
>

## 基础-示例项目-编辑-显示编辑数据

>**`目标-任务`**实现英雄列表的编辑功能
>
>**`路径`**:  实现编辑的显示数据 
>
>1. 添加编辑路由  **注意** 由于需要拿到编辑数据的标识 所以需要动态路由
>
>```js
>{ path: "/edit/:id", component: Edit } // 编辑组件 动态路由
>```
>
>2. 编辑按钮添加跳转路由的属性
>
>```html
><router-link :to="`/editHero/${item.id}`">编辑</router-link>
>```
>
>3. 定义加载英雄方法 **注意** 通过 $router.params来获取参数
>
>```js
>// 加载英雄
>loadHero() {
> const { id } = this.$route.params; // 通过参数获取id
> if (id) {
>   //判断id
>   axios.get("http://localhost:3000/heroes/" + id).then(result => {
>     this.formData = result.data; // 获取数据并赋值给表单对象
>   });
> }
>}
>```
>
>3.  在初始化事件中 调用loadHero 方法
>
>```js
>// 实例完成事件
>created() {
>this.loadHero(); // 加载英雄
>}
>```

## 基础-示例项目-编辑-表单处理

>**`目标-任务`**实现英雄列表编辑数据的提交
>
>**`路径`**:实现编辑方法
>
>定义实现编辑提交方法
>
>```js
>// 编辑英雄
>editHero() {
> if (this.formData.name && this.formData.gender) {
>   const { id } = this.$route.params;
>   //restful规则
>   axios
>     .put("http://localhost:3000/heroes/" + id, this.formData)
>     .then(result => {
>       if (result.status === 200) {
>         this.$router.push({ path: "/heroes" });
>       } else {
>         alert("编辑失败");
>       }
>     });
> } else {
>   alert("提交内容不能为空");
> }
>}
>```
>
>

## 基础-示例项目-优化-axios统一导入

>**`目标-任务`**实现axios的统一导入
>
>**`路径`**: axios的统一导入 和使用
>
>1 在入口main.js文件中引入axios,并给全局Vue对象的原型链赋值 

 

>```js
>Vue.prototype.$http = Axios; //所有的实例都直接共享拥有了 这个方法
>
>```
>
>2  调用接口时  采用 实例.属性的方式即可调用 
>
>```js
>addHero() {
>// 判断填报信息是否为空
>if (this.formData.name && this.formData.gender) {
>// 该判断条件是判断 当前的姓名和 性别都不为空
>// restful规则
>this.$http
>.post("http://localhost:3000/heroes", this.formData)
> .then(result => {
>   // 注意这里添加成功的状态码 是 201
>     if (result.status === 201) {
>       // 添加成功之后 要跳转回列表页
>       // 编程式导航
>         this.$router.push({ path: "/heroes" });
>       } else {
>         alert("添加失败");
>       }
>     });
>     } else {
>     alert("提交信息不能为空");
>     }
>     }
>     ```
>     
> 

## 基础-示例项目-优化-设置baseUrl

>**`目标-任务`**通过配置**`baseUrl`**将所有的请求地址进行优化 
>
>**`路径`**: axios中配置统一的**`请求路径头`**
>
>1. 给axios中的baseUrl设置常态值
>
>```js
>Axios.defaults.baseURL = "http://localhost:3000"; // 设置共享的方法
>
>```
>
>2. 改造所有的的请求
>
>```js
>this.$http.put("heroes/" + id, this.formData).then(result => {
>     if (result.status === 200) {
>       this.$router.push({ path: "/heroes" });
>     } else {
>       alert("编辑失败");
>     }
>   });
>```
>
>

## 基础--示例项目-优化-目录划分-统一设置激活样式

>**`目标-任务`**将组件的目录进行整理划分,并统一当前路由的激活样式
>
>路径:   左侧导航激活样式  目录划分
>
>* 路由级组件 =>直接挂在路由级组件 => views 目录名
>* 普通组件 => 在路由级组件中使用的组件 => components目录名
>* ![image-20191215174641385](assets/image-20191215174641385.png)
>
>1  统一激活样式
>
>```js
>linkActiveClass: "active", // active为bootstrap中的 一个class样式
>
>```
>
>2  整理目录   分门别类
>
>**注意**同一类 组件放入同一个文件夹下  修改引用地址

## 给切换路由增加过渡效果 (扩展)

>**`目标-任务`**实现项目中的路由切换过渡
>
>过滤也可以对于有显示/隐藏效果 进行过度
>
>**`路径`**:  给路由切换加入 过渡效果 
>
>1  用过渡组件包裹路由视图
>
>```html
><transition name="slide">
><router-view></router-view>
></transition>
>```
>
>2  编写 过渡效果
>
>```css
>.slide-enter,
>.slide-leave-to {
>opacity: 0;
>}
>.slide-enter-to,
>.slide-leave {
>opacity: 1;
>}
>.slide-enter-active {
>transition: all 1s;
>}
>```
>
>

## 钩子函数

>**`目标`**掌握Vue的生命周期及其**`钩子函数`**
>
>* 4个周期
>
>* 生命周期是指Vue实例或者组件从诞生到消亡经历的每一个阶段，在这些阶段的前后可以设置一些函数当做事件来调用。
>
>![](.\lifecycle.png)

* beforeCreate (实例被创建前)
* created(实例被创建后)
* beforeMount(文档被挂载前)
* mounted(文档被挂载后)
* beforeUpdate(数据变化 页面更新前)
* updated(数据变化 页面更新后)
* beforeDestory(视图销毁前)
* destoryed(视图销毁后)

**`任务`**

1. 分别在以上不同的生命周期中 输出不同内容 查看不同变化

## 总结

* 英雄菜品列表 => crud
* 提取素材 => app-header /app-slidebar/hero-list
* 提取路由模块 => 一个文件  => 拆成多个文件
* main.js => 入口 =>  new Vue() => 挂载路由  => import  router  from './router' 
* router => index.js =>  配置路由表 => export default  new VueRouter({  routes: [] })
* App.vue => 根组件 => 路由容器 => 一级路由容器
* views => 路由级组件 
* router-link => tag => 可以定义生成的标签tag
* crud => axios.get/put/post/delete
* axios的优化 => Vue.prototype(原型属性).$axios = axios (相当于 所有的组件和Vue实例都拥有了axios属性)
* axios的 baseURL设置 常态值 => 只写需要写的业务地址即可
* 路由的导航切换动画 =>  transtion => router-view => .v-enter(起始样式)  .v-enter-active (过渡样式)
* 生命周期 => 四个阶段 八个事件 => created /mounted /beforeDestory/beforeMount