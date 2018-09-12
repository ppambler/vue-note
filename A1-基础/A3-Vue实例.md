---
typora-copy-images-to: p
---

# Vue实例

## ★创建一个 Vue 实例

### ◇创建一个Vue实例的姿势

每个 Vue 应用都是通过用 `Vue` 函数创建一个新的 **Vue 实例**开始的：

```js
var vm = new Vue({
  // 选项
})
```

`vm` (ViewModel 的缩写) 这个变量名表示 Vue 实例，之所以用到这个名字是受到 [MVVM 模型](https://zh.wikipedia.org/wiki/MVVM)的启发！

### ◇传入一个参数

这个参数叫做「选项对象」，话说你叫对象不叫好了吗？为啥还有加个「选项」二字？难道说这里的选项指的是，对象里的key是可选的？

根据 [API 文档](https://cn.vuejs.org/v2/api/#%E9%80%89%E9%A1%B9-%E6%95%B0%E6%8D%AE)中的描述，你会发现那些就是所谓的选项列表！

总之，这篇教程**主要描述的就是如何使用这些选项来创建你想要的行为**。

话说我有什么行为想要创建的？而且需要使用这些选项来创建？

### ◇一个Vue应用的组成？

> 一个 Vue 应用由一个通过 `new Vue` 创建的**根 Vue 实例**，以及可选的嵌套的、可复用的**组件树**组成。

**✎：**

一个 todo 应用的组件树可以是这样的：

```
根实例
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

想要深入了解一下？那就去看[组件系统](https://cn.vuejs.org/v2/guide/components.html)章节。

不过目前，还是建议不要！

> 此刻的你只需要明白所有的 Vue 组件都是 Vue 实例，并且**接受相同的选项对象** (一些根实例特有的选项除外)。

为啥Vue组件都是Vue实例啊？不是说一个Vue应用只有一个根Vue实例吗？还是说可以把一个Vue应用当作是个Vue组件呢？又或者是一个Vue应用可以嵌套很多个组件，这些组件本身就类似于根实例？

总而言之，就是把Vue组件看作是Vue实例好了！既然Vue组件它是Vue实例，那么它的能力应该等价于根实例才对啊！？？？

或者我认为这些组件虽然叫Vue实例，但是同根Vue实例还是有点区别的！

## ★数据与方法

### ◇data与视图？

1. 创建了个 Vue实例，传入了一个data对象进来

2. 你data对象搞了几个属性，并在视图中的使用了这些属性，那么一旦这些属性值变化了，那么视图将会自动更新这些值

   ```js
   // 我们的数据对象
   var data = { a: 1 }
   
   // 该对象被加入到一个 Vue 实例中
   var vm = new Vue({
     data: data
   })
   
   // 获得这个实例上的属性
   // 返回源数据中对应的字段
   vm.a == data.a // => true
   
   // 设置属性也会影响到原始数据
   vm.a = 2
   data.a // => 2
   
   // ……反之亦然
   data.a = 3
   vm.a // => 3
   ```

3. 如果你的data中的某个属性是后引入的，那么不好意思，即便你修改了值，视图也不会响应式的发生变化，所以如果你在后面会用到这些数据，那么最好就给写上，并赋上初始值，如空字符串、undefined、null等等……

   后来添加的属性，**✎：**

   ```js
   vm.b = 'hi'
   ```

   搞点初始值，**✎：**

   ```js
   data: {
     newTodoText: '',
     visitCount: 0,
     hideCompletedTodos: false,
     todos: [],
     error: null
   }
   ```

### ◇如何让响应系统无法再「追踪」变化？

使用个`Object.freeze()`就好，这会阻止修改现有的属性，简单来说就是Duang一下，把这个对象给冻住了！**✎：**

```js
var obj = {
  foo: '我是个被冻结的字符串'
}

Object.freeze(obj)

new Vue({
  el: '#app',
  data: obj
})
```

视图的效果，**✎：**

```html
<div id="app">
  <p>{{ foo }}</p>
  <!-- <p>{{ xxx }}</p> -->
  <!-- 这里的 `foo` 不会更新！ -->
  <button v-on:click="foo = '你肯定看不见这个值吧'">Change it</button>
</div>
```

如果你在视图中添加这个 `<p>{{ xxx }}</p>`，那么会报错，因为这个`xxx`未定义，谁叫你用了模板语法呢！

还有就是别以为只有一个属性被冻结，这个obj的所有属性都被冻结了！你想更新data就能更新视图？我劝你还是死了这条心吧！

### ◇区分用户定义的属性

> 除了数据属性，Vue 实例还暴露了一些有用的实例属性与方法。它们都有前缀 `$`，以便与用户定义的属性区分开来。

视图，**✎：**

```html
<div id="app">
    <p>{{ a }}</p>
    <button v-on:click="a = '改变a的值'">Change it</button>
</div>
```

JavaScript，**✎：**

```js
var data = { a: 1 }
var vm = new Vue({
  el: '#app',
  data: data
})

console.log(vm.$data === data) // => true
console.log(vm.$el === document.getElementById('app')) // => true

// $watch 是一个实例方法
vm.$watch('a', function (newValue, oldValue) {
  // 这个回调将在 `vm.a` 改变后调用
  alert('你能看到这个，证明vm.a发生了变化！')
})
```

以后你可以在 [API 参考](https://cn.vuejs.org/v2/api/#%E5%AE%9E%E4%BE%8B%E5%B1%9E%E6%80%A7)中查阅到完整的实例属性和方法的列表。

## ★实例生命周期钩子



## ★小结

## ★Q&A

### ①选项对象？

这是jQuery的，**✎：**

**➹：**[jquery - In Javascript, what is an options object? - Stack Overflow](https://stackoverflow.com/questions/3089561/in-javascript-what-is-an-options-object)

我对选项对象的理解是，在这里应该强调的是选项二字吧！在创建Vue实例时，所传入的对象，Vue提供了许多的选项，即所谓的key，来让开发者来填充这些选项所对应的值。

在之前学习MVC的时候，这种赋值就是所谓的解析赋值吧！

### ②钩子？

摘录这个答案的回答，**✎：**

**➹：**[Vue当中钩子函数的具体意思？ - Ruphi的回答 - 知乎](https://www.zhihu.com/question/60256230/answer/191762066)

> 按我个人理解，就是在生命周期执行流程中预留的一个能让我们执行自己代码的地方。叫钩子函数，很形象的，钩子钩子，挂载我们自己的东西。
>
> 而钩子函数的实现，基本原理就是callback。

先就这么理解吧！

似乎也可以理解成在某种条件下，就执行这个钩子函数！

当然这是Vue的钩子概念……

而JavaScript中的钩子呢？

> 提供一个可以影响默认的(或原有的)流程(机制)的时机。
>
> 通常就是：一个库、一个框架、一个系统或一种语言，提供一个对外公开的接口，通过这个接口，用户能够影响库、框架、系统或程序的行为。
>
> 至于具体实现，那是千差万别的。举个例子就是有同学提到了：Java中的模板方法。

所以什么是钩子呢？

套用轮子哥的话，**✎：**

> 大概跟很多人要买什么东西，要先跟老婆问候一下，的感觉是差不多的吧。这就是钩子

钩子函数与回调函数有啥关系呢？

按照我的理解就是，钩子函数可以说也是一种回调函数，不过它的这种回调更狭窄一点吧！如事件触发后才执行这钩子函数！



**➹：**[钩子编程 - 维基百科，自由的百科全书](https://zh.wikipedia.org/zh-hans/%E9%92%A9%E5%AD%90%E7%BC%96%E7%A8%8B)

**➹：**[计算机英语中总出现的"hooks" 是什么意思? "钩子" ? 这个钩子应该怎么理解? 是回调的意思? - 知乎](https://www.zhihu.com/question/20610442)

**➹：**[JavaScript：理解事件、事件处理函数、钩子函数、回调函数 - 简书](https://www.jianshu.com/p/a0c580ed3432)

**➹：**[javascript - 麻烦帮解释一下，什么叫“钩子”？ - SegmentFault 思否](https://segmentfault.com/q/1010000004335505)

**➹：**[vue 中钩子 是怎样的一个概念? - 知乎](https://www.zhihu.com/question/50880439)

**➹：**[Vue当中钩子函数的具体意思？ - 知乎](https://www.zhihu.com/question/60256230)

**➹：**[钩子函数与回调函数 — 3Lweb](http://www.3lweb.cn/2017/08/10/%E9%92%A9%E5%AD%90%E5%87%BD%E6%95%B0%E4%B8%8E%E5%9B%9E%E8%B0%83%E5%87%BD%E6%95%B0/)

**➹：**[什么是HOOK技术 - 简书](https://www.jianshu.com/p/150c284c0f3b)

**➹：**[钩子函数，回调函数？ - 知乎](https://www.zhihu.com/question/24675608)

**➹：**[钩子（hook）是啥 - 简书](https://www.jianshu.com/p/27374378baca)

**➹：**[什么是钩子函数？ - 知乎](https://www.zhihu.com/question/21200927)



