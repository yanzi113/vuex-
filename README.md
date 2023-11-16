# vuex-学习笔记
闲着没事放上来
# Vuex概述
## 组件之间共享数据的方式

父向子传值:
子向父传值:
兄弟之间共享数据:

## Vuex是什么

Vuex是实现组件全局状态(数据)管理的一种机制,可以方便的实现组件之间数据的共享

## 使用Vuex统一管理状态的好处

1.能够在Vuex中集中管理共享的数据,易于开发和后期维护
2.能够高效的实现组件之间的数据共享,提高开发效率
3.存储在Vuex中的数据都是响应式的,能实时保持数据与页面的同步

## 适合存储到Vuex中的数据

一般情况下,对于组件之间共享的数据会存储到Vuex中,对于组件中的私有数据,依旧存储在组件自身的data中
# Vuex的基本使用

1.安装vuex依赖包
`npm install vuex --save`
2.导入vuex包
```
import Vuex from 'vuex'
Vue.use(Vuex)    

```
3.创建store对象

```
const store = new Vuex.store({
    //state 中存放的就是全局共享的数据
    state: {count:0}
    })
    
```
4.将store对象挂载到vue实例中
```
new Vue({
    el:'#app',
    render: h =>h(app)
    router,
    //将创建的共享数据对象,挂载到vue实例中
    //所有组件可以直接从store中获取全局数据了
    store
    })

```
创建完vuex项目后,会有一个store.js文件,里面包含了date,mutation,action等,然后把它导入到main.js(入口)中去,然后挂载new Vue({store....})

## 案例一 计算器

app.vue

```
<temple>
<div>
<my-addition></my-addition>
<my-subtraction></my-subtraction>
</div>
</temple>
<script>
import Addition from './components/Addition.vue'
import Subtraction from './components/Subtraction.vue'
export default  {
data(){
return{}
},
components:['my-addition': Addition,my-subtraction': Subtraction}}
</script>
```
Addition.vue
```
<temple>
<div>
<h3>当前最新的count值为:</h3>
<button>-1<button>
</div>
</temple>
data(){
return{}
}
</script>
```
减法同加法...

# Vuex核心概念

## state
state提供唯一的公共数据源,所有共享的数据都统一放到Store中的State中进行存储

```
//创建store数据源,提供唯一公共数据
const store =new Vuex.store({
    state: {count:0}
    })
```
组件访问state中数据的第一种方式

```
this.$store.state.全局数据名称

```
访问state中数据的第二种方式:
```
//1:从vuex中按需导入mapState函数
import { mapState } from 'vuex'

```
通过导入的mapState函数,将当前组件需要的*全局数据*映射为当前组件的*computed*计算属性:
```
//2.将全局数据,映射为当前组件的计算属性
computed:{
    ...mapState(['count'])
    }
```


![1699944210811.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/916d3fe06c9f4121acaf21665a779b06~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=504&h=456&s=110755&e=png&b=243137)

## mutation

这种操作是完全不合法的,因为vuex不允许组件直接操作state里面的数据,只能通过mutation提交

```
//定义Mutation
const store = new Vuex.Store({
    state:{
     count: 0
     },
     mutations:{
     add(state){
     //变更状态
     state.count++
     }
    }
   })
```
```
//触发mutation
methods:{
 handle1(){
    //触发mutation的第一种方式
    this.store.commit('add')
    }
   }
```
在触发mutation时传递参数
```
//定义Mutation
const store = new Vuex.ctore({
    state:{
     count: 0
     },
     mutations:{
     add(state,step){
     //变更状态
      state.count +=step
      )
     )
    })
```
```
//触发mutation
methods:{
  handle2(){
      //在调用commit函数
      //触发mutation时携带参数
      this.$store.commit('addN',3)
      }
     }
```

this.$store.commit()是触发/提交mutations的第一种方式,触发mutations的第二种方式
```
//1.从vuex中按需导入mapMutations函数
import{mapMutations} from 'vuex'
```
通过刚才导入的mapMutations函数,将需要的mutations函数,映射为当前组件的methods方法

```
//2.将指定的在store中的mutations函数,映射为当前组件的methods函数,从而在函数中使用..
methods:{
...mapMutations(['add,'addN'])
}
```
此时新增需求,需要加的操作为延时一秒加一,此时一般会在mutation里面加的函数直接写一个定时器,但是这种写法是不对的,

![1699945978135.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cbb9bc662ca740aea0bbb50bcad25e87~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=201&h=130&s=25212&e=png&b=253238)

因为mutation里面不允许异步操作(定时器属于异步)

## action

action用于处理异步操作,如果通过异步操作变更数据,必须通过Action,而不能使用Mutation,但在Action中还是通过提交mutation的方法去间接改变数据

```
// 定义Action
const store = new Vuex.Store({
    //...
    mutations:{
        add(state)
        {state.count ++}
        },
    actions: {
       addAsync(context){
          setTimeout(() =>{
              context.commit('add')
              },1000)
              }
              }
              })
 ```
 
 ```
 methods:{
     handle(){
         this.$store.dispatch('addAsync')
         }
        }
 ```
 关键字:调用:dispatch context是与 store 实例具有相同方法和属性的对象
 
 使用action时携带参数(同mutation)
 

![1699947068598.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ec481419389249928876e777ed5616bb~tplv-k3u1fbpfcp-jj-mark:0:0:0:0:q75.image#?w=741&h=294&s=107726&e=png&b=e4f0fe)

this.$store.dispatch()是触发Action的第一种方式,触发action的第二种方式同mutation
```
//1.先从vuex中按需导入mapActions函数
import {mapActions} from 'vuex'
```
通过导入的mapAction函数,将需要的action函数,映射为当前组件的methods方法
```
//2.将指定的action函数,映射为当前组件的methods函数
methods:{
...mapActions(['addASync','addNASync'])}

```
## Getter

Getter用于对Store中的数据进行加工处理形成的数据

1.getter可以对Store中已有的数据包装形成新的数据,类似于vue的计算属性

2.Store中数据变化,Getter的数据也会响应式变化

```
//定义Getter
const store = new Vuex.Store({
    state:{
       count:0
      },
    getters:{
       showNum: state =>{
        return '当前最新的数量是:'+state.count+'';
        }
        }
      })
```
getter的第一种使用方式
`this.$store.getters.名称`

getter的第二种使用方式
```
import{ mapGetters } from 'vuex'
computed:{
    ...mapGetters(['showNum])}
```
