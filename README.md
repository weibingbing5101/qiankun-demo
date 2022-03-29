# qiankun-example

qiankun 实战 demo，父应用 vue，子应用使用 `react`, `vue` 和 `原生HTML`。


## 开始
安装根目录工程依赖
```
npm i
```
一键安装所有主子应用的依赖
```
npm run install
```

一键启动所有所有应用
```
npm start
```

通过 [http://localhost:8080/](http://localhost:8080/) 访问主应用。

## 发布
一键构建并打包所有主子应用
```
npm run build
```

## 说明

### 状态管理
#### 主应用
main文件 store.js 
  
```
import { initGlobalState } from 'qiankun'
import Vue from 'vue'

// 父应用的初始state
// Vue.observable是为了让initialState变成可响应：https://cn.vuejs.org/v2/api/#Vue-observable。
const initialState = Vue.observable({
  user: {
    name: '主应用 全局状态   5秒后主应用更新此值，现在是默认值'
  }
})

const actions = initGlobalState(initialState)

// 状态改变监听
actions.onGlobalStateChange((newState, prev) => {
  // state: 变更后的状态; prev 变更前的状态
  console.log('main change', JSON.stringify(newState), JSON.stringify(prev))

  for (const key in newState) {
    initialState[key] = newState[key]
  }
})

// 状态设置
actions.setGlobalStates = (state = {}) => {
  if (!Object.keys(state).length) {
    return
  }
  const temp = Object.assign(initialState, state)
  actions.setGlobalState(temp)
}

// 定义一个获取state的方法下发到子应用
actions.getGlobalState = key => {
  // 有key，表示取globalState下的某个子级对象
  // 无key，表示取全部

  return key ? initialState[key] : initialState
}

export default actions

```

#### 子应用
子应用共用一个状态管理管理方法， 根目录下common下的global-register.js
子应用通过vuex创建数据池，并通过上面的方法注册，结合qiankun提供的状态管理方法，实现主子应用的双向通信。
子应用修改状态会触发vue.observer的双向绑定机制。
