---
title: "Vue数据响应式原理"
date: 2021-02-28T10:22:16+08:00
draft: false
---

# Vue数据响应式原理
> TLDR: Vue数据响应式指的是，Vue通过创建Vue实例对数据进行【监听】和【代理】，使得在数据发生变化时，UI可以对数据变化进行响应，重新渲染页面。

> 具体方法：
> 创建Vue实例vm的过程中
> 
> ``let vm = new Vue({data:{key:value}})``
> 1. 通过闭包，用同名计算属性覆盖构造选项中data的属性，对原始数据进行监控
> 2. 创建一个对象vm（Vue实例），为该对象构造同名计算属性，返回这个对象，使其成为原数据的代理，在数据变化时，调用调用render(data)，自动更新UI

## 基础知识: 访问器属性
ES6对象有两种属性：**数据属性** 和 **访问器属性**。 数据属性就是我们一般使用的键值对，不再赘述。而访问器属性则不包含数据值，而是通过获取函数getter和设置函数setter进行访问和写入。

```js
let person = {
  姓: "Y",
  名: "yx",
  get 姓名() {
    return this.姓 + this.名
  },
  set 姓名(name) {
    this.姓 = name[0]
    this.名 = name.substring(1)
  }
}
person.姓名 = "Hjx"
console.log("使用set,计算属性:" + person.姓名)
```

## Vue对data做了什么？
```js
const myData = {
	n:0
}
console.log(myData)
const vm = new Vue({
	data:myData
})
setTimeout(()=>console.log(myData),0)

```
查看打印结果发现，myData不再是原来简单的对象，上面出现了很多我们没有定义的东西。那么``const vm = new Vue({data:myData})``对data做了什么呢？
1. 代理：vm为实际内容的代理，返回一个有同名计算属性的对象
2. 监控：vm通过内部value存储值，并用同名计算属性覆盖原对象上的属性，对myData进行监控
3. 作用
   1. 让vm成为代理，增加约束，如数据范围，现实中比如卖房最低价格
   2. 对所有属性进行监控，防止属性改变，知道属性改变，就可以调用render(data)
   3. 主要目的还是data变化时，可以渲染

下面是实现监控+代理的代码
```js
let myData = {
  n: 0
}
let data = proxy({data: myData})
//通过代理，添加约束
//不满足约束无法更改
data.n = -1
console.log("data.n:", data.n)//0
//满足约束可以更改
data.n = 1
console.log("+data.n:", data.n)//1
//即使通过引用原数据，也无法越过监听
myData.n = -1
console.log("-data.n:", data.n)//1

function proxy({data}) {
  let value = data.n
  delete data.n
  Object.defineProperty(data, "n", {
    get() {
      return value
    },
    set(v) {
      //这里是对原始数据的改变进行监听
      if (v <= 0) return
      value = v
    },
  })
  const obj = {}
  Object.defineProperty(obj, "n", {
    get() {
      return data.n
    },
    set(v) {
      //这里是对数据常规的改变进行代理
      if (v <= 0) return
      value = v
    },
  })
  return obj
}
```
## 	使用中Vue data的"bug"
如果template中使用的变量没有在data里声明，Vue会发出警告，但Vue只进行“浅"检查
```js
//Vue警告，并且对undefined的值不会进行显示
const vm = new Vue({
	template: `
      <div>
        {{n}}
      </div>
    `})
//Vue没有警告，但同样不会显示
const vm = new Vue({
	data:{
		obj:{
			a: 0
		}
	},
	template: `
      <div>
        {{obj.b}}
      </div>
	`
})
```
那么很自然我们会想到，如果所有变量都需要注册，那么想要给对象增加属性，或给数组增加元素，要怎么办呢？ Vue提供了解决方案：
使用Vue.set/this.$set()函数
1. $是为了防止与vm的方法或属性同名，两个是同一个函数
2. eg：this.$set(this.obj,’xxx’,100)
3. 该方法会自动为变量创建代理和监听，在数据变化时触发UI更新（副作用，但不是立即更新，是异步更新）
4. 但作用于数组时，数组的元素改变不会触发UI更新，使用Vue重写的数组API和this.$set时，才会触发UI更新。

个人思考，可以通过如下思路实现：
```js
class VueArray extends Array{
	push(...args){
		const oldLength = this.length
		super.push(...args)
		for(let i=oldLength;i<this.length;i++){
			Vue.set(this, i, this[i])
			//将每个新增的key都告诉Vue
		}
	}
}
```

