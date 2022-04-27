---
title: "再谈Vue数据响应式"
date: 2022-04-27T10:23:33+08:00
draft: false
---

# 再谈Vue数据响应式

上文[Vue数据响应式](http://yangyixuan.icu/posts/vue%E6%95%B0%E6%8D%AE%E5%93%8D%E5%BA%94%E5%BC%8F%E5%8E%9F%E7%90%86/)介绍了Vue通过代理和监听实现对数据的监控，从而进行响应式的渲染和更新操作。

然而变更数据时，具体又是如何进行渲染和更新的呢？本文将通过实现一个极简版的Vue响应式系统来一探究竟。

本文完整代码详见[Vue数据响应式核心原理实现](https://github.com/Howardyangyixuan/vue-reactive-demo-1)并附上[效果预览链接](http://yangyixuan.icu/vue-reactive-demo-1/)

## 核心架构
![Vue数据响应式核心原理](/images/vuedata.jpg)
Vue通过代理和监听实现对数据的监控，从而进行响应式的渲染和更新操作。而在变更数据时，组件的渲染和更新则是以Dep为核心，通过发布订阅模式实现的，如上图所示。

## 发布订阅模式
这在我们日常生活中十分常见，就以订报纸为例，我们订报纸付钱以后，邮递员会在本子上记录我们的信息（要调用的对象）和要订阅的报纸（需要执行的操作），之后每次有新的报纸（数据改变），他就按本子上的地址（找到要调用的对象），挨家挨户送报纸（执行响应的操作）。这就是发布订阅模式。

在Vue中Dep对象就是邮递员的小本，而watcher对象就是用户，watcher对象上有响应的方法用来重新渲染。

## 具体实现

### dep
实现依赖类Dep:发布订阅模式
Dep实例通过一个去重数组set记录依赖数据的组件（set实际保存的是组件的watcher对象），采用发布订阅模式，在数据被访问时使用depend()方法添加依赖，在数据被修改时使用notify()方法在改变时遍历所有依赖组件，并执行组件watcher的update方法，即执行渲染函数重新渲染。Dep类的静态变量Dep.target记录当前正在执行的组件。

```js
class Dep {
	// 需要额外配置webpack进行转译
  // static target = null

  constructor() {
    this.deps = new Set()
    console.log(this.deps)
  }

  depend() {
    //并非初始化组建时,Dep.target为null
    if(Dep.target)
    this.deps.add(Dep.target)
    console.log("depend:",this.deps)
  }

  notify() {
    for (const watcher of this.deps) {
      console.log("watcher:",watcher)
      watcher.update()
    }
  }
}
Dep.target = null

export {Dep}
```

### targetStack
每个组件都有一个watcher对象，全局变量targetStack是一个运行栈，记录未完成加载的组件（父组件），使用Dep.target存储当前watcher，表示当前运行的组件。使用两个函数维护运行栈，push和pop，作用更新targetStack以及更新Dep.target。（targetStack在组件嵌套时体现作用）

```js
import {Dep} from "./dep.js"

const targetStack = []

function popTarget() {
  Dep.target = targetStack.pop()
}

function pushTarget(target) {
  // 不会为空，不用额外检测
  // if (Dep.target)
  targetStack.push(Dep.target)
  Dep.target = target
}

export {targetStack, popTarget, pushTarget}
```

### watcher
watcher对象，保存当前组件渲染函数render，该对象有一个get方法，首先pushTarget，将当前组件设置为运行组件，然后调用数据render方法进行渲染，最后popTarget。在更新数据时，调用update方法，此处简化update方法内部仍是调用get方法。

```js
import {popTarget, pushTarget} from "./targetStack"

class Watcher {
  constructor(render) {
    //组件渲染函数
    this.render = render
    this.get()
  }

  get() {
    pushTarget(this)
    this.render()
    popTarget()
  }

  update() {
    this.get()
  }
}

export {Watcher}
```

### reactive方法实现数据响应式核心逻辑
对每个数据，通过setter和getter覆盖原先的属性，实现监听，并且使用Dep记录依赖该数据的组件，在get时记录（第一次渲染时记录），在set时，即改变数据时，通知依赖该数据的组件，触发重新执行渲染函数。

```js
import {Dep} from "./dep"

function isObject(data) {
  return data != null && typeof data == "object";
}

function defineReactive(data, key) {
  const dep = new Dep()
  let value = data[key]
  //用getter/setter覆盖原属性实现监听
  Object.defineProperty(data, key, {
    get() {
      dep.depend()
      return value
    },
    set(newVal) {
      value = newVal
      dep.notify()
    }
  })
  //如果值是对象，则递归调用reactive使其内部数据均为响应式数据
  if (isObject(value)) {
    reactive(value)
  }
}

function reactive(data) {
  if(isObject(data)){
    for (let key of Object.keys(data)) {
      defineReactive(data, key)
    }
  }
  return data
}

export {reactive}
```

## 测试接口
将data暴露给window.data，可以通过控制台修改对象和数组，查看数据响应式效果，测试各个接口是否正常工作。
```html
...
<h1>data</h1>
<h2>data已暴露给window.data，可以通过控制台修改对象和数组，查看数据响应式效果</h2>
<label>
	data.msg
	<input type="text" id="msg">
</label>
<div id="app"></div>
...
```
```js
import {reactive} from "../src/reactive"
import {Watcher} from "../src/watcher"

const data = reactive({
  msg: "Hello World",
  arr: [1, 2, 3],
  obj:{name:'howard'}
})

new Watcher(() => {
  document.getElementById("app").innerHTML = `
    msg is ${data.msg}
    arr is ${data.arr}
    obj is ${JSON.stringify(data.obj)}
     <br>
  `
})
const input = document.getElementById("msg")
input.addEventListener("keyup", (event) => {
  console.log(event.target.value)
  data.msg = event.target.value
})


window.data = data


```

## 封装为极简版Vue

最后为了让我们的数据响应式系统和Vue有类似的使用方式和体验，我们对接口进行封装，形成极简版的Vue。

注意，这里实现Vue类，对接口进行封装，模仿Vue2语法，其中template使用正则替换简化实现，未实现真正的编译。
```js
import {reactive} from "./reactive"
import {Watcher} from "./watcher"

class Vue {
  constructor({el, data, template}) {
    console.log(el)
    console.log(data)
    console.log(template)
    this.el = el
    let val = reactive(data)
    this.template = template
    //代理data
    for (let key of Object.keys(data)) {
      console.log(key)
      Object.defineProperty(this, key, {
        get() {
          console.log("proxy")
          return val[key]
        },
        set(newVal) {
          console.log(newVal)
          val[key] = newVal
        }
      })
      // console.log(this[key])
    }
    return this
  }

  render() {
    //编译器的简化，目的是能够使用template
    let str = this.template
    const reg = /{{([a-z]+)}}/g
    const matches = [...this.template.matchAll(reg)]
    console.log(matches)
    for (let match of matches) {
      str = str.replace(match[0], JSON.stringify(this[match[1]]))
    }
    document.getElementById(this.el).innerHTML = str
  }

  $mount() {
    new Watcher(this.render.bind(this))
    return this
  }
}

export {Vue}

```


<hr>
啊其实还是有很多可以深究的细节，比如computed和watch的实现，computed的缓存等，只要保持好奇心，逐步积累，相信一定可以不断进步，成为能够独当一面的工程师！