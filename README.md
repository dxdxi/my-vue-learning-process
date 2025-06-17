# Vue2 阶段

## 脚手架文件结构：

├── node_modules
├── public
│ ├── favicon.ico: 页签图标
│ └── index.html: 主页面
├── src
│ ├── assets: 存放静态资源
│ │ └── logo.png
│ │── component: 存放组件
│ │ └── HelloWorld.vue
│ │── App.vue: 汇总所有组件
│ │── main.js: 入口文件
├── .gitignore: git 版本管制忽略的配置
├── babel.config.js: babel 的配置文件
├── package.json: 应用包配置文件
├── README.md: 应用描述文件
├── package-lock.json：包版本控制文件

## 关于不同版本的 Vue：

- `vue.js`与`vue.runtime.xxx.js`的区别：
  (1) `vue.js`是完整版的`Vue`，包含：核心功能+模版分析器
  (2) `vue.runtime.xxx.js`是运行版的`Vue`，只包含：核心功能，没有模版分析器
- 因为`vue.runtime.xxx.js`没有模版分析器，所以不能使用`template`配置项，需要使用`render`函数接收到的`createElement`函数去指定具体内容

## vue.config.js 配置文件

- 使用 `vue inspect > output.js` 可以查看到 Vue 脚手架的默认设置
- 使用 `vue.config.js` 可以对脚手架进行个性化定制，详情见：https://cli.vuejs.org/zh

## ref 属性

1. 被用来给元素或子组件注册引用信息（id 的替代者） 2.应用在 html 标签上获取的是真实的 DOM 元素，应用在组件标签上是组件实例对象（vc） 3.使用方式：
   打标识：`<h2 ref="xxx">...</h2>`或`<School ref="xxx"></School>`
   获取：`this.$refs.xxx`

## 配置项 props

- 功能：让组件接收外部传过来的数据
  (1) 传递数据：
  `<Demo name="xxx"/>`
  (2) 接收数据：

  ````
  第一种方式(只接收)：
  props:['name']

          第二种方式(限制类型)：
              props:{
                  name:Number
              }
          第三种方式(限制类型、限制必要性、指定默认值)
              props:{
                  type:String, // 类型
                  required:true, // 必要性
                   default:"yy" // 默认值
              }
      ```

  备注： props 是只读的，Vue 底层会监测你对 props 的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制 props 的内容到 data 中一份，然后去修改 data 中的数据
  ````

## mixin（混入）

1.  功能：可以把多个组件公用的配置提取成一个混入对象
2.  使用方式：
    第一步定义混入，例如：
    `   {
    data(){......},
    methods(){......},
    ......
}`

                    第二部使用混入，例如：
                    (1) 全局混入：`Vue.mixin(xxx)`
                    (2) 局部混入：`mixins:['xxx']`

## 插件

1. 功能：用于增强`Vue`
2. 本质：包含 install 方法的一个对象，install 的第一个参数是 Vue，第二个以后得参数是插件使用者传递的数据
3. 定义插件：

   ```
   对象.install = function (Vue,options) {
       // 1. 添加全局过滤器
       Vue.filter(...)

       // 2. 添加全局指令
       Vue.directive(...)

       // 3. 配置全局混入(合)
       Vue.mixin(...)

       // 4.添加实例方法
       Vue.prototype.$myMethod = function () {...}
       Vue.prototype.$myProperty = xxx
   }
   ```

4. 使用插件：`Vue.use()`

## scoped 样式

1. 作用：让样式在局部生效，防止冲突
2. 写法：`<style scoped></style>`

## webStorage

1. 储存内容大小一半支持 <span style="color:red">5MB</span> 左右（不同浏览器可能还不一样）
2. 浏览器端通过`Window.sessionStorage`和`Window.localStorage`属性来实现本地存储机制
3. 相关 API：
   (1) `xxxStorage.setItem('key','value');`
   该方法接收一个键和值作为参数，会把键值对添加到存储中，如果键名存在，则更新其对应的值
   (2) `xxxStorage.getItem('key');`
   该方法接收一个键名作为参数，返回对应的值
   (3) `xxxStorage.removeItem('key');`
   该方法接收一个键名作为参数，并把该键名从存储中删除
   (4) `xxxStorage.clear()`
   该方法会清空存储中的所有数据
4. 备注：
   (1) `SessionStorage`存储的内容会随着浏览器窗口关闭而消失
   (2) `LocalStorage`存储的内容，需要手动清除才会消失
   (3) `xxxStorage.getItem(xxx)`如果对应的 value 获取不到，那么返回值是 null
   (4) `JSON.parse(null)`的结果依然是 null

## 组件的自定义事件

1. 一种组件间通信的方式，适用于：<span style="color:red">子组件 ===> 父组件</span>
2. 使用场景：A 是父组件，B 是子组件，B 想给 A 传数据，那么就要在 A 中给 B 绑定自定义事件（<span style="color:red">事件的回调在 A 中</span>）
3. 绑定自定义事件：
   (1) 第一种方式，在父组件中：`<Demo @xxx="test"/>`或`<Demo v-on:xxx="test" />`
   (2) 第二种方式，在父组件中：
   ```
   <Demo ref="demo" />
       ......
       mounted(){
           this.$refs.yyy.$on('xxx',this.test)
       }
   ```
   (3) 若想让自定义事件只能触发一次，可以使用`once`修饰符，或`$once`方法
4. 触发自定义事件：`this.$emit('xxx',数据)`
5. 解绑自定义事件：`this.$off('xxx')`
6. 组件上也可以绑定原生 DOM 事件，需要使用`native`修饰符
7. 注意：通过`this.$refs.yyy.$on('xxx',回调)`绑定自定义事件时，回调<span style="color:red">要么配置在 methods 中，要么用箭头函数</span>,否则 this 指向会出问题！

## 全局事件总线（GlobalEventBus）

1. 一种组件间通信的方式，适用于任意组件间的通信
2. 安装全局事件总线：
   ```
   new Vue({
       ......
       beforeCreate() {
           Vue.prototype.$bus = this //安装全局事件总线，$bus就是当前应用的vm
       },
       ......
   })
   ```
3. 使用事件总线：
   (1) 接收数据：A 组件想接收数据，则在 A 组件中给$bus 绑定自定义事件，事件的<span style="color:red">回调留在 A 组件自身</span>
   ```
   methods(){
       demo(data){......}
   },
   ......
   mounted(){
       this.$bus.$on('xxx',this.demo)
   }
   ```
   (2) 提供数据：`this.$bus.$emit('xxx',数据)`
4. 最好在 beforeDestroy 钩子中，用`$off`去解绑<span style="color:red">当前组件所用到的</span>事件

## 消息订阅与发布（publish）

1. 一种组件间的通信方式，适用于<span style="color:red">任意组件间通信</span>
2. 使用步骤：
   (1) 安装`pubsub`：`npm i putsub-js`
   (2) 引入：`import pubsub from 'pubsub-js'`
   (3) 接收数据：A 组件想接收数据，则在 A 组件中订阅消息，订阅的<span style="color:red">回调留在
   A 组件自身</span>
   ```
   methods(){
       demo(data){......}
   },
   ......
   mounted(){
       this.pid = pubsub.subscribe('xxx',this.demo) // 订阅消息
   }
   ```
   (4) 提供数据：`pubsub.publish('xxx',数据)`
   (5) 最好在`beforeDestory`钩子中，用`PunSub.unsubscribe(pid)`去<span style="color:red">取消订阅</span>

## Vue 封装的过渡和动画

1. 作用：在插入、更新或移除`DOM`元素时，在合适的时候给元素添加样式类名
2. 图示：
   [Enter 和 Leave](https://v2.cn.vuejs.org/images/transition.png)
3. 写法：
   (1) 准备好样式：
   - 元素进入的样式：
     `v-enter`：进入的起点
     `v-enter-active`：进入过程中
     `v-enter-to`：进入的终点
   - 元素离开的样式：
     `v-leave`：离开的起点
     `v-leave-active`：离开过程中
     `v-leave-to`：离开的终点
     (2) 使用`<transition></transition>`包裹要过渡的元素，并配置 name 属性：
     `   <transition name="hello">
    <h1 v-show="isShow">Hello!</h1>
</transition>`
     (3) 备注：若有多个元素需要过渡，则需要使用：`<transition-group></transition-group>`,且每个元素都要指定`key`值

## Vue 脚手架配置代理

### 方法一

编写`vue.config.js`配置具体代理规则：

```
    devServer:{
        proxy:"http://localhost:5000"
    }
```

说明：

1. 优点：配置简单，请求资源时直接发给前端（8080）即可
2. 缺点：不能配置多个代理，不能灵活的控制请求是否走代理
3. 工作方式：若按照上述代理，当请求了前端不存在的资源时，那么该请求会转发给服务器（有限匹配前端资源）

### 方法二

编写`vue.config.js`配置具体代理规则：

```
    module.exports = {
        devServer: {
            proxy: {
                '/api1': {//匹配所有以'/api'开头的请求路径
                    target:'http://localhost:5000',// 代理目标的基础路径
                    changeOrigin: true,
                    pathRewrite: {'^/api1':''}
                },
                'api2':{//匹配所有以'/api'开头的请求路径
                    target:'http://localhost:5001',// 代理目标的基础路径
                    changeOrigin: true,
                    pathRewrite: {'^/api2':''}
                }
            }
        }
    }
    /*
        changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
        changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:8000
        changeOrigin默认值为true
    */
```

说明：

1. 优点：可以配置多个代理，且可以灵活控制请求是否代理
2. 缺点：配置略微繁琐，请求资源时必须加前缀

## 插槽

1. 作用：让父组件可以向子组件指定位置插入`html`结构，也是一种组件间通信的方式，适用于<span style="color:red">父组件 ===> 子组件</span>
2. 分类：默认插槽、具名插槽、作用域插槽
3. 使用方式：
   (1) 默认插槽：

   ```
   父组件中：
       <Category>
           <div>html结构1</div>
       </Category>
   子组件中：
       <template>
           <div>
               <!--定义插槽-->
               <slot>插槽默认内容</slot>
           </div>
       </template>
   ```

   (2) 具名插槽：

   ```
   父组件中：
       <Category>
           <template slot="center">
               <div>html结构1</div>
           </template>

           <template v-slot:footer>
               <div>html结构2</div>
           </template>
       </Category>
   子组件中：
       <template>
           <div>
               <!--定义插槽-->
               <slot name="center">插槽默认内容</slot>
               <slot name="footer">插槽默认内容</slot>
           </div>
       </template>
   ```

   (3) 作用域插槽：

   - 理解：<span style="color:red">数据在组件的自身，但根据数据生成的结构需要组件的使用者来决定</span>。（games 数据在 Category 组件中，但使用数据所遍历出来的结构由 App 组件决定）
   - 具体编码：

   ```
   父组件中：
       <Category>
           <template scope="scopeData">
               <!--生成的是ul列表-->
               <ul>
                   <li v-for="g in scopeData.games" :key="g">{{g}}</li>
               </ul>
           </template>
       </Category>

       <Category>
           <template slot-scope="scopeData">
               <!--生成的是h4标题-->
               <h4 v-for="g in scopeData.games" :key="g">{{g}}</h4>
           </template>
       </Category>
   子组件中：
       <template>
           <div>
               <slot :games="games"></slot>
           </div>
       </template>

       <script>
           export default {
               name:'Category',
               props:['title'],
               // 数据在子组件自身
               data() {
                   return {
                       games:['xxx','yyy',zzz'','nnn']
                   }
               },
           }
       </script>
   ```

## Vuex

### 概念

在 Vue 中实现集中式状态（数据）管理的一个 Vue 插件，对 Vue 应用中多个组件的共享状态进行集中式管理（读/写），也是一种组件间通信的方式，且适用于任意组件间通信

### 何时使用？

多个组件需要共享数据时

### 搭建 vuex 环境

1. 创建文件：`src/store/index.js`

```
    // 引入Vue核心库
    import Vue from 'vue'
    // 引入Vuex
    import Vuex form 'vuex'
    // 应用Vuex插件
    Vue.use(Vuex)

    // 准备action对象——响应组件中用户的动作
    const action = {}
    // 准备mutations对象——修改state中的数据
    const mutations = {}
    // 准备state对象——保存具体的数据
    const state = {}

    // 创建并暴露store
    export default new Vuex.Store({
        actions,
        mutations,
        state
    })
```

2. 在`main.js`中创建`vm`时传入`store`配置项

```
    ......
    // 引入store
    import store form './store'
    ......

    // 创建vm
    new Vue({
        el:'#app',
        render: h => h(App),
        store
    })
```

### 基本使用

1. 初始化数据，配置`actions`，配置`mutations`，操作文件`store.js`

```
    // 引入Vue核心库
    import Vue from 'vue'
    // 引入Vuex
    import Vuex form 'vuex'
    // 应用Vuex插件
    Vue.use(Vuex)

    const actions = {
        // 响应组件中加的动作
        jia(context,value){
            // console.log('actions中的jia被调用了',miniStore,value)
            context.commit('JIA',value)
        }
    }

    const mutations = {
        // 执行JIA
        JIA(state,value){
            // console.log('actions中的JIA被调用了',state,value)
            state.sum += value
        }
    }

    // 初始化数据
    const state = {
        sum:0
    }

    // 创建并暴露store
    export default new Vuex.Store({
        actions,
        mutations,
        state
    })
```

2. 组件中读取 vuex 中的数据：`$store.state.sum`
3. 组件中修改 vuex 中的数据：`$store.dispatch('action中的方法名',数据)`或`$store.commit('mutations中的方法名',数据)`
   <span style="color:red">备注：若没有网络请求或其他物业逻辑，组件中也可以越过 actions，既不写`dispatch`，直接编写`commit`</span>

### getters 的使用

1. 概念：当`state`中的数据需要经过加工后再使用，可以使用`getters`加工
2. 在`store.js`中追加`getters`配置

```
    ......
    const getters = {
        bigSum(state) {
            return state.sum * 10
        }
    }

    // 创建并暴露store
    export default new Vuex.Store({
        ......
        getters
    })
```

3. 组件中读取数据：`$store.getters.bigSum`

### 四个 map 方法的使用

1. mapState 方法：用于帮助我们映射`state`中的数据为计算属性

```
    computed: {
        // 借助mapState生成计算属性：sum、school、subject（对象写法）
        ...mapState({sum:'sum',school:'school',subject:'subject'})

        // 借助mapState生成计算属性：sum、school、subject（数组写法）
        ...mapState(['sum','school','subject'])
    }
```

2. mapGetters 方法：用于帮助我们映射`getters`中的数据为计算属性

```
    computed: {
        // 借助mapGetters生成计算属性：bigSum（对象写法）
        ...mapGetters({bigSum:'bigSum'})

        // 借助mapGetters生成计算属性：bigSum（数组写法）
        ...mapGetters(['bigSum'])
    }
```

3. mapActions 方法：用于帮助我们生成与`actions`对话的方法，即：包含`$store.dispatch(xxx)`的函数

```
    methods: {
        // 靠mapActions生成：incrementOdd、incrementWait（对象写法）
        ...mapActions({incrementOdd:'jiaOdd',incrementWait:'jiaWait'})

        // 靠mapActions生成：incrementOdd、incrementWait（数组写法）
        ...mapActions(['jiaOdd','jiaWait'])
    }
```

4. mapMutations 方法：用于帮助我们生成与`mutations`对话的方法，即：包含`$store.commit(xxx)`的函数

```
    methods: {
        // 靠mapMutations生成：incrementOdd、decrment（对象写法）
        ...mapMutations({incrementOdd:'JIA',decrment:'JIAN'})

        // 靠mapMutations生成：JIA、JIAN（数组写法）
        ...mapMutations(['JIA','JIAN'])
    }
```

<span style="color:red">备注：mapActions 与 mapMutations 使用时，若需要传递参数：在模版中绑定事件时传递好参数，否则参数是事件对象</span>

### 模块化+命名空间

1. 目的：让代码更好维护，让多种数据分类更加明确
2. 修改`store.js`

```
    const countAbout = {
        namespaced:true,// 开启命名空间
        state:{x:1},
        mutations:{...},
        actions:{...},
        getters:{
            bugSum(state){
                return state.sum*10
            }
        }
    }

    const personAbout = {
        namespaced:true,// 开启命名空间
        state:{x:1},
        mutations:{...},
        actions:{...},
    }

    const store = new Vuex.Store({
        modules:{
            countAbout,
            personAbout
        }
    })
```

3. 开启命名空间后，组件中读取`state`数据：

```
    // 方式一：自己直接读取
    this.$store.state.personAbout.list
    // 方式二：借助mapState读取
    ...mapState('countAbout',['sum','school','subject'])
```

4. 开启命名空间后，组件中读取`getters`数据：

```
    // 方式一：自己直接读取
    this.$store.getters['personAbout/firstPersonName']
    // 方式二：借助mapGetters读取
    ...mapGetters('countAbout',['bigSum'])
```

5. 开启命名空间后，组件中调用`dispatch`

```
    // 方式一：自己直接dispatch
    this.$store.dispatch('personAbout/addPersonWang',person)
    // 方式二：借助mapActions
    ...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
```

6. 开启命名空间后，组件中调用`commit`

```
    // 方式一：自己直接commit
    this.$store.commit('personAbout/ADD_PERSON',person)
    // 方式二：借助mapMutations
    ...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'})
```

## 路由

- 理解：一个路由（route）就是一组映射关系（key-value），多个路由需要路由器（router）进行管理
- 前端路由：key 是路径，value 是组件

### 基本使用

1. 安装`vue-router`，命令：`npm i vue-router`
2. 应用插件：`Vue.use(VueRouter)`
3. 编写`router`配置项：

```
    // 引入VueRouter
    import VueRouter from 'vue-router'
    // 引入路由组件
    import About from '../components/About'
    import Home from '../components/Home'

    // 创建router实例对象，去管理一组一组的路由规则
    const router = new VueRouter({
        routes:[
            {
                path:'/about',
                component:About
            },
            {
                path:'/home',
                component:Home
            }
        ]
    })

    // 暴露router
    export default router
```

4. 实现切换（active-class 可配置高亮样式）

```
    <router-link actvie-class="active" to="/about">About</router-link>
```

5. 指定展示位置

```
    <router-view></router-view>
```

### 几个注意点

1. 路由组件通常放在`pages`文件夹中，一般组件通常放在`components`文件夹
2. 通过切换，“隐藏”了的路由组件，默认是被销毁的，需要的时候再去挂载
3. 每个组件都有自己的`$route`属性，里面储存着自己的路由信息
4. 整个应用只有一个`router`，可以通过组件的`$route`属性获取到

### 多级路由

1. 配置路由规则，使用 childern 配置项：

```
    routes:[
        {
            path:'/about',
            component:About
        },
        {
            path:'/home',
            component:Home,
            children:[// 通过children配置子路由
                {
                    path:'news',// 此处一定不要写：/news
                    component:News
                },
                {
                    path:'message',// 此处一定不要写：/message
                    component:Message
                }
            ]
        }
    ]
```

2. 跳转（要写完整路径）

```
    <router-link to="/home/news">News</router-link>
```

### 路由的 query 参数

1. 传递参数

```
    <!--跳转并携带query参数，to的字符串写法-->
    <router-link :to="/home/message/detail?id=666&title=你好">跳转</router-link>

    <!--跳转并携带query参数，to的对象写法-->
    <router-link
        :to="{
            path:'/home/message/detail',
            query:{
                id:666,
                title:"你好"
            }
        }"
    >跳转</router-link>
```

2. 接收参数：

```
    $route.query.id
    $route.query.title
```

### 命名路由

- 作用：可以简化路由的跳转
- 如何使用

  1. 给路由命名：

  ```
      {
          path:'/demo',
          component:Demo,
          children:[
              {
                  path:'test',
                  component:Test,
                  childern:[
                      {
                          name:'hello',// 给路由命名
                          path:'welcome',
                          component:Hello
                      }
                  ]
              }
          ]
      }
  ```

  2. 简化跳转

  ```
      <!--简化前，需要写完整的路径-->
      <router-link to="/demo/test/welcome">跳转</router-link>

      <!--简化后，直接通过名字跳转-->
      <router-link :to="{name:'hello'}">跳转</router-link>

      <!--简化写法配合传递参数-->
      <router-link
          :to="{
              name:'hello',
              query:{
                  id:666,
                  title:'你好'
              }
          }"
      >跳转</router-link>
  ```

### 路由的 params 参数

1. 配置路由，声明接收`params`参数

```
    {
        path:'/home',
        component:Home,
        children:[
            {
                path:'news',
                component:News
            },
            {
                path:'message',
                component:Message,
                children:[
                    name:'xiangqing',
                    path:'detail/:id/:title',// 使用占位符声明接收params参数
                    component:Detail
                ]
            }
        ]
    }
```

2. 传递参数

```
    <!--跳转并携带params参数，to的字符串写法-->
    <router-link :to="/home/message/detail/666/你好">跳转</router-link>

    <!--跳转并携带params参数，to的对象写法-->
    <router-link
        :to="{
            name:'xiangqing',
            params:{
                id:666,
                title:'你好'
            }
        }"
    >跳转</router-link>
```

<span style="color:red">特别注意：路由携带 params 参数时，若使用 to 的对象写法，则不能使用 path 配置项，必须使用 name 配置</span>

3. 接收参数

```
    $route.params.id
    $route.params.title
```

### 路由的 props 配置

作用：让路由组件更方便的收到参数

```
    {
        name:'xiangqing',
        path:'detail/:id',
        component:Detail,

        // 第一种写法：props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件
        // props:{a:900}

        // 第二种写法：props值为布尔值，布尔值为true，则把路由收到的所有params参数通过props传给Detail组件
        // props:true

        // 第三种写法：props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
        prop(route){
            return {
                id:route.query.id,
                title:route.query.title
            }
        }
    }
```

### <router-link>的 replace 属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式
2. 浏览器的历史记录有两种写入方式：分别是`push`和`replace`，`push`是追加历史记录，`replace`是替换当前记录。路由器跳转时默认为`push`
3. 如何开启`replace`模式：`<router-link replace ......>News</router-link>`

### 编辑式路由导航

1. 作用：不借助`<router-link>`实现路由跳转，让路由跳转更加灵活
2. 具体编码：

```
    // $router的两个API
    this.$router.push({
        name:'xiangqing',
        params:{
            id:xxx,
            title:xxx
        }
    })

    this.$router.replace({
        name:'xiangqing',
        params:{
            id:xxx,
            title:xxx
        }
    })
    this.$router.forward() // 前进
    this.$router.back() // 后退
    this.$router.go() // 可前进可后退
```

### 缓存路由组件

1. 作用：让不展示的路由组件保持挂载，不被销毁
2. 具体编码：

```
    <keep-alive include="News">
        <router-view></router-view>
    </keep-alive>
```

### 两个新的生命周期钩子

1. 作用：路由组件所独有的两个钩子，用于捕获路由组件的激活状态
2. 具体名字：
   (1) `activated`路由组件被激活时触发
   (2) `deactivated`路由组件失活时触发

### 路由守卫

1. 作用：对路由进行权限控制
2. 分类：全局守卫、独享守卫、组件内守卫

   - 全局守卫：

   ```
       // 全局前置守卫：初始化时执行、每次路由切换前执行
       router.beforeEach((to,from,next)=>{
           console.log('beforeEach',to,from)
           if(to.meta.isAuth){ // 判断当前路由是否需要进行权限控制
               if(localStorage.getItem('school') === 'abc'){ // 权限控制的具体规则
                   next() // 放行
               }else{
                   alert('暂无权限查看')
                   // next({name:'guanyu'})
               }
           }else{
               next() // 放行
           }
       })

       // 全局后置守卫：初始化时执行、每次路由切换后执行
       router.afterEach((to,from)=>{
           console.log('afterEach',to,from)
           if(to.meta.title){
               document.title = to.meta.title // 修改网页的title
           }else{
               document.title = 'vue_test'
           }
       })
   ```

   - 独享守卫：

   ```
       beforeEnter(to,from,next){
           console.log('beforeEnter',to,from)
           if(to.meta.isAuth){ // 判断当前路由是否需要进行权限控制
               if(localStorage.getItem('school') === 'abc'){
                   next()
               }else{
                   alert('暂无权限查看')
                   // next({name:'guanyu'})
               }
           }else{
               next()
           }
       }
   ```

   - 组件内守卫

   ```
       // 进入守卫：通过路由规则，进入该组件时被调用
       beforeRouteEnter (to,from,next) {

       },
       // 离开守卫：通过路由规则，离开该组件时被调用
       beforeRouteLeave (to,from,next) {

       }
   ```

### 路由器的两种工作方式

1. 对于一个`URL`来说，什么是`hash`值？——`#`及其后面的内容就是`hash`值
2. `hash`值不会包含在`HTTP`请求中，即：`hash`值不会带给服务器
3. `hash`模式：
   - 地址中永远带着`#`号，不美观
   - 若以后将地址通过第三方收集 app 分享，若 app 校验严格，则地址会被标记为不合法
   - 兼容性较好
4. `history`模式：
   - 地址干净，美观
   - 兼容性和`hash`模式相比较差
   - 应用部署上线时需要后端人员支持，解决刷新页面服务器 404 的问题

# Vue3

## 常用的 Composition API

### 拉开序幕的 setup

1. 理解：Vue3.0 中一个新的配置项，值为一个函数
2. `setup`是所有<span style="color:red">Composition API(组合 API)</span>
3. 组件中所用到的：数据、方法等，均要配置在`setup`中
4. `setup`函数的两种返回值：
   - 若返回一个对象，则对象中的属性、方法，在模版中均可以直接使用
   - 若返回一个渲染函数：则可以自定义渲染内容
5. 注意点：
   - 尽量不要与 Vue2.x 配置混用
   - `setup`不能是一个`async`函数，因为返回值不再是`return`的对象，而是`promise`，模版看不到`return`对象中的属性

### ref 函数

- 作用：定义一个响应式的数据
- 语法：`const xxx = ref(initValue)`
  - 创建一个包含响应式数据的<span style="color:red">引用对象（reference 对象）</span>
  - JS 中操作数据：`xxx.value`
  - 模版中读取数据：不需要`.value`，直接：`<div>{{xxx}}</div>`
- 备注：
  - 接受的数据可以是：基本类型，也可以是对象类型
  - 基本类型的数据：响应式依然是靠`Object.defineProperty()`的`get`与`set`完成的
  - 对象类型的数据：内部“求助”了 Vue3.0 中的一个新函数——`reactive`函数

### reactive 函数

- 作用：定义一个<span style="color:red">对象类型</span>的响应式数据（基本类型别用它，用`ref`函数）
- 语法：`const 代理对象 = reactive(被代理对象)`接收一个对象（或数组），返回一个<span style="color:red">代理器对象（proxy 对象）</span>
- `reactive`定义的响应式数据是“深层次的”
- 内部基于`ES6`的`Proxy`实现，通过代理对象操作源对象内部数据都是响应式的

### Vue3.0 中的响应式原理

#### vue2.x 的响应式

- 实现原理：

  - 对象类型：通过`Object.defineProperty()`对属性的读取、修改进行拦截（数据劫持）。

  - 数组类型：通过重写更新数组的一系列方法来实现拦截。（对数组的变更方法进行了包裹）。

    ```js
    Object.defineProperty(data, "count", {
      get() {},
      set() {},
    });
    ```

- 存在问题：
  - 新增属性、删除属性, 界面不会更新。
  - 直接通过下标修改数组, 界面不会自动更新。

#### Vue3.0 的响应式

- 实现原理:

  - 通过 Proxy（代理）: 拦截对象中任意属性的变化, 包括：属性值的读写、属性的添加、属性的删除等。
  - 通过 Reflect（反射）: 对源对象的属性进行操作。
  - MDN 文档中描述的 Proxy 与 Reflect：

    - Proxy：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy

    - Reflect：https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect

      ```js
      new Proxy(data, {
        // 拦截读取属性值
        get(target, prop) {
          return Reflect.get(target, prop);
        },
        // 拦截设置属性值或添加新属性
        set(target, prop, value) {
          return Reflect.set(target, prop, value);
        },
        // 拦截删除属性
        deleteProperty(target, prop) {
          return Reflect.deleteProperty(target, prop);
        },
      });

      proxy.name = "tom";
      ```

### reactive 对比 ref

- 从定义数据角度对比：
  - ref 用来定义：<strong style="color:#DD5145">基本类型数据</strong>。
  - reactive 用来定义：<strong style="color:#DD5145">对象（或数组）类型数据</strong>。
  - 备注：ref 也可以用来定义<strong style="color:#DD5145">对象（或数组）类型数据</strong>, 它内部会自动通过`reactive`转为<strong style="color:#DD5145">代理对象</strong>。
- 从原理角度对比：
  - ref 通过`Object.defineProperty()`的`get`与`set`来实现响应式（数据劫持）。
  - reactive 通过使用<strong style="color:#DD5145">Proxy</strong>来实现响应式（数据劫持）, 并通过<strong style="color:#DD5145">Reflect</strong>操作<strong style="color:orange">源对象</strong>内部的数据。
- 从使用角度对比：
  - ref 定义的数据：操作数据<strong style="color:#DD5145">需要</strong>`.value`，读取数据时模板中直接读取<strong style="color:#DD5145">不需要</strong>`.value`。
  - reactive 定义的数据：操作数据与读取数据：<strong style="color:#DD5145">均不需要</strong>`.value`。

### setup 的两个注意点

- setup 执行的时机

  - 在 beforeCreate 之前执行一次，this 是 undefined。

- setup 的参数
  - props：值为对象，包含：组件外部传递过来，且组件内部声明接收了的属性。
  - context：上下文对象
    - attrs: 值为对象，包含：组件外部传递过来，但没有在 props 配置中声明的属性, 相当于 `this.$attrs`。
    - slots: 收到的插槽内容, 相当于 `this.$slots`。
    - emit: 分发自定义事件的函数, 相当于 `this.$emit`。

### 计算属性与监视

#### computed 函数

- 与 Vue2.x 中 computed 配置功能一致

- 写法

  ```js
  import {computed} from 'vue'

  setup(){
      ...
  	//计算属性——简写
      let fullName = computed(()=>{
          return person.firstName + '-' + person.lastName
      })
      //计算属性——完整
      let fullName = computed({
          get(){
              return person.firstName + '-' + person.lastName
          },
          set(value){
              const nameArr = value.split('-')
              person.firstName = nameArr[0]
              person.lastName = nameArr[1]
          }
      })
  }
  ```

#### watch 函数

- 与 Vue2.x 中 watch 配置功能一致

- 两个小“坑”：

  - 监视 reactive 定义的响应式数据时：oldValue 无法正确获取、强制开启了深度监视（deep 配置失效）。
  - 监视 reactive 定义的响应式数据中某个属性时：deep 配置有效。

  ```js
  //情况一：监视ref定义的响应式数据
  watch(
    sum,
    (newValue, oldValue) => {
      console.log("sum变化了", newValue, oldValue);
    },
    { immediate: true }
  );

  //情况二：监视多个ref定义的响应式数据
  watch([sum, msg], (newValue, oldValue) => {
    console.log("sum或msg变化了", newValue, oldValue);
  });

  /* 情况三：监视reactive定义的响应式数据
  			若watch监视的是reactive定义的响应式数据，则无法正确获得oldValue！！
  			若watch监视的是reactive定义的响应式数据，则强制开启了深度监视 
  */
  watch(
    person,
    (newValue, oldValue) => {
      console.log("person变化了", newValue, oldValue);
    },
    { immediate: true, deep: false }
  ); //此处的deep配置不再奏效

  //情况四：监视reactive定义的响应式数据中的某个属性
  watch(
    () => person.job,
    (newValue, oldValue) => {
      console.log("person的job变化了", newValue, oldValue);
    },
    { immediate: true, deep: true }
  );

  //情况五：监视reactive定义的响应式数据中的某些属性
  watch(
    [() => person.job, () => person.name],
    (newValue, oldValue) => {
      console.log("person的job变化了", newValue, oldValue);
    },
    { immediate: true, deep: true }
  );

  //特殊情况
  watch(
    () => person.job,
    (newValue, oldValue) => {
      console.log("person的job变化了", newValue, oldValue);
    },
    { deep: true }
  ); //此处由于监视的是reactive素定义的对象中的某个属性，所以deep配置有效
  ```

#### watchEffect 函数

- watch 的套路是：既要指明监视的属性，也要指明监视的回调。

- watchEffect 的套路是：不用指明监视哪个属性，监视的回调中用到哪个属性，那就监视哪个属性。

- watchEffect 有点像 computed：

  - 但 computed 注重的计算出来的值（回调函数的返回值），所以必须要写返回值。
  - 而 watchEffect 更注重的是过程（回调函数的函数体），所以不用写返回值。

  ```js
  //watchEffect所指定的回调中用到的数据只要发生变化，则直接重新执行回调。
  watchEffect(() => {
    const x1 = sum.value;
    const x2 = person.age;
    console.log("watchEffect配置的回调执行了");
  });
  ```

### 生命周期

<div style="border:1px solid black;width:380px;float:left;margin-right:20px;"><strong>vue2.x的生命周期</strong><img src="https://cn.vuejs.org/images/lifecycle.png" alt="lifecycle_2" style="zoom:33%;width:1200px" /></div><div style="border:1px solid black;width:510px;height:985px;float:left"><strong>vue3.0的生命周期</strong><img src="https://v3.cn.vuejs.org/images/lifecycle.svg" alt="lifecycle_2" style="zoom:33%;width:2500px" /></div>

- Vue3.0 中可以继续使用 Vue2.x 中的生命周期钩子，但有有两个被更名：
  - `beforeDestroy`改名为 `beforeUnmount`
  - `destroyed`改名为 `unmounted`
- Vue3.0 也提供了 Composition API 形式的生命周期钩子，与 Vue2.x 中钩子对应关系如下：
  - `beforeCreate`===>`setup()`
  - `created`=======>`setup()`
  - `beforeMount` ===>`onBeforeMount`
  - `mounted`=======>`onMounted`
  - `beforeUpdate`===>`onBeforeUpdate`
  - `updated` =======>`onUpdated`
  - `beforeUnmount` ==>`onBeforeUnmount`
  - `unmounted` =====>`onUnmounted`

### 自定义 hook 函数

- 什么是 hook？—— 本质是一个函数，把 setup 函数中使用的 Composition API 进行了封装。

- 类似于 vue2.x 中的 mixin。

- 自定义 hook 的优势: 复用代码, 让 setup 中的逻辑更清楚易懂。

### toRef

- 作用：创建一个 ref 对象，其 value 值指向另一个对象中的某个属性。
- 语法：`const name = toRef(person,'name')`
- 应用: 要将响应式对象中的某个属性单独提供给外部使用时。

- 扩展：`toRefs` 与`toRef`功能一致，但可以批量创建多个 ref 对象，语法：`toRefs(person)`

## 其它 Composition API

### shallowReactive 与 shallowRef

- shallowReactive：只处理对象最外层属性的响应式（浅响应式）。
- shallowRef：只处理基本数据类型的响应式, 不进行对象的响应式处理。

- 什么时候使用?
  - 如果有一个对象数据，结构比较深, 但变化时只是外层属性变化 ===> shallowReactive。
  - 如果有一个对象数据，后续功能不会修改该对象中的属性，而是生新的对象来替换 ===> shallowRef。

### readonly 与 shallowReadonly

- readonly: 让一个响应式数据变为只读的（深只读）。
- shallowReadonly：让一个响应式数据变为只读的（浅只读）。
- 应用场景: 不希望数据(尤其是这个数据是来自与其他组件时)被修改时。

### toRaw 与 markRaw

- toRaw：
  - 作用：将一个由`reactive`生成的<strong style="color:orange">响应式对象</strong>转为<strong style="color:orange">普通对象</strong>。
  - 使用场景：用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面更新。
- markRaw：
  - 作用：标记一个对象，使其永远不会再成为响应式对象。
  - 应用场景:
    1. 有些值不应被设置为响应式的，例如复杂的第三方类库等。
    2. 当渲染具有不可变数据源的大列表时，跳过响应式转换可以提高性能。

## customRef

- 作用：创建一个自定义的 ref，并对其依赖项跟踪和更新触发进行显式控制。

- 实现防抖效果：

  ```vue
  <template>
    <input type="text" v-model="keyword" />
    <h3>{{ keyword }}</h3>
  </template>

  <script>
  import { ref, customRef } from "vue";
  export default {
    name: "Demo",
    setup() {
      // let keyword = ref('hello') //使用Vue准备好的内置ref
      //自定义一个myRef
      function myRef(value, delay) {
        let timer;
        //通过customRef去实现自定义
        return customRef((track, trigger) => {
          return {
            get() {
              track(); //告诉Vue这个value值是需要被“追踪”的
              return value;
            },
            set(newValue) {
              clearTimeout(timer);
              timer = setTimeout(() => {
                value = newValue;
                trigger(); //告诉Vue去更新界面
              }, delay);
            },
          };
        });
      }
      let keyword = myRef("hello", 500); //使用程序员自定义的ref
      return {
        keyword,
      };
    },
  };
  </script>
  ```

## provide 与 inject

<img src="https://v3.cn.vuejs.org/images/components_provide.png" style="width:300px" />

- 作用：实现<strong style="color:#DD5145">祖与后代组件间</strong>通信

- 套路：父组件有一个 `provide` 选项来提供数据，后代组件有一个 `inject` 选项来开始使用这些数据

- 具体写法：

  1. 祖组件中：

     ```js
     setup(){
         ......
         let car = reactive({name:'奔驰',price:'40万'})
         provide('car',car)
         ......
     }
     ```

  2. 后代组件中：

     ```js
     setup(props,context){
         ......
         const car = inject('car')
         return {car}
         ......
     }
     ```

## 响应式数据的判断

- isRef: 检查一个值是否为一个 ref 对象
- isReactive: 检查一个对象是否是由 `reactive` 创建的响应式代理
- isReadonly: 检查一个对象是否是由 `readonly` 创建的只读代理
- isProxy: 检查一个对象是否是由 `reactive` 或者 `readonly` 方法创建的代理

## Composition API 的优势

### Options API 存在的问题

使用传统 OptionsAPI 中，新增或者修改一个需求，就需要分别在 data，methods，computed 里修改 。

<div style="width:600px;height:370px;overflow:hidden;float:left">
    <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f84e4e2c02424d9a99862ade0a2e4114~tplv-k3u1fbpfcp-watermark.image" style="width:600px;float:left" />
</div>
<div style="width:300px;height:370px;overflow:hidden;float:left">
    <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e5ac7e20d1784887a826f6360768a368~tplv-k3u1fbpfcp-watermark.image" style="zoom:50%;width:560px;left" /> 
</div>

### Composition API 的优势

我们可以更加优雅的组织我们的代码，函数。让相关功能的代码更加有序的组织在一起。

<div style="width:500px;height:340px;overflow:hidden;float:left">
    <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bc0be8211fc54b6c941c036791ba4efe~tplv-k3u1fbpfcp-watermark.image"style="height:360px"/>
</div>
<div style="width:430px;height:340px;overflow:hidden;float:left">
    <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6cc55165c0e34069a75fe36f8712eb80~tplv-k3u1fbpfcp-watermark.image"style="height:360px"/>
</div>

## 新的组件

### Fragment

- 在 Vue2 中: 组件必须有一个根标签
- 在 Vue3 中: 组件可以没有根标签, 内部会将多个标签包含在一个 Fragment 虚拟元素中
- 好处: 减少标签层级, 减小内存占用

### Teleport

- 什么是 Teleport？—— `Teleport` 是一种能够将我们的<strong style="color:#DD5145">组件 html 结构</strong>移动到指定位置的技术。

  ```vue
  <teleport to="移动位置">
  	<div v-if="isShow" class="mask">
  		<div class="dialog">
  			<h3>我是一个弹窗</h3>
  			<button @click="isShow = false">关闭弹窗</button>
  		</div>
  	</div>
  </teleport>
  ```

### Suspense

- 等待异步组件时渲染一些额外内容，让应用有更好的用户体验

- 使用步骤：

  - 异步引入组件

    ```js
    import { defineAsyncComponent } from "vue";
    const Child = defineAsyncComponent(() => import("./components/Child.vue"));
    ```

  - 使用`Suspense`包裹组件，并配置好`default` 与 `fallback`

    ```vue
    <template>
      <div class="app">
        <h3>我是App组件</h3>
        <Suspense>
          <template v-slot:default>
            <Child />
          </template>
          <template v-slot:fallback>
            <h3>加载中.....</h3>
          </template>
        </Suspense>
      </div>
    </template>
    ```

## 其他

### 全局 API 的转移

- Vue 2.x 有许多全局 API 和配置。

  - 例如：注册全局组件、注册全局指令等。

    ```js
    //注册全局组件
    Vue.component('MyButton', {
      data: () => ({
        count: 0
      }),
      template: '<button @click="count++">Clicked {{ count }} times.</button>'
    })

    //注册全局指令
    Vue.directive('focus', {
      inserted: el => el.focus()
    }
    ```

- Vue3.0 中对这些 API 做出了调整：

  - 将全局的 API，即：`Vue.xxx`调整到应用实例（`app`）上

    | 2.x 全局 API（`Vue`）    | 3.x 实例 API (`app`)                        |
    | ------------------------ | ------------------------------------------- |
    | Vue.config.xxxx          | app.config.xxxx                             |
    | Vue.config.productionTip | <strong style="color:#DD5145">移除</strong> |
    | Vue.component            | app.component                               |
    | Vue.directive            | app.directive                               |
    | Vue.mixin                | app.mixin                                   |
    | Vue.use                  | app.use                                     |
    | Vue.prototype            | app.config.globalProperties                 |

### 其他改变

- data 选项应始终被声明为一个函数。

- 过度类名的更改：

  - Vue2.x 写法

    ```css
    .v-enter,
    .v-leave-to {
      opacity: 0;
    }
    .v-leave,
    .v-enter-to {
      opacity: 1;
    }
    ```

  - Vue3.x 写法

    ```css
    .v-enter-from,
    .v-leave-to {
      opacity: 0;
    }

    .v-leave-from,
    .v-enter-to {
      opacity: 1;
    }
    ```

- <strong style="color:#DD5145">移除</strong>keyCode 作为 v-on 的修饰符，同时也不再支持`config.keyCodes`

- <strong style="color:#DD5145">移除</strong>`v-on.native`修饰符

  - 父组件中绑定事件

    ```vue
    <my-component
      v-on:close="handleComponentEvent"
      v-on:click="handleNativeClickEvent"
    />
    ```

  - 子组件中声明自定义事件

    ```vue
    <script>
    export default {
      emits: ["close"],
    };
    </script>
    ```

- <strong style="color:#DD5145">移除</strong>过滤器（filter）

  > 过滤器虽然这看起来很方便，但它需要一个自定义语法，打破大括号内表达式是 “只是 JavaScript” 的假设，这不仅有学习成本，而且有实现成本！建议用方法调用或计算属性去替换过滤器。
