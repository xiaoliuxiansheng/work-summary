## vue 项目中如何在页面刷新的状态下保留数据
### 解决方法: 

  * 使用vuex作状态管理: 将vuex里面的数据同步更新到localStorage里面,改变vuex里的数据,便触发localStorage.setItem 方法
  ``` javascript
  import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)

function storeLocalStore (state) {
  window.localStorage.setItem("userMsg",JSON.stringify(state));
}
const store = new Vuex.Store({
    modules: {
    tags:[],
    curTagsIndex:"-1",
    },
  mutations: {
    SET_CURTAGS: (state, index) => {
      state.curTagsIndex = index
    },
  }
})

export default store
```
* 在 App.vue 的 created 钩子函数里写下了如下代码;
```
//在页面加载时读取localStorage里的状态信息
    localStorage.getItem("userMsg") && this.$store.replaceState(Object.assign(this.$store.state,JSON.parse(localStorage.getItem("userMsg"))));
    
    //在页面刷新时将vuex里的信息保存到localStorage里
    window.addEventListener("beforeunload",()=>{
        localStorage.setItem("userMsg",JSON.stringify(this.$store.state))
    })
```