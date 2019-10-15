## 入口开始，解读Vue源码（三）—— initMixin 上篇

这边文章我们主要去解读 ```initMixin```后续的执行过程，上篇我们已经可以看到，接下来主要会发生这几个操作过程：
```js
    initLifecycle(vm)
    initEvents(vm)
    initRender(vm)
    callHook(vm, 'beforeCreate')
    initInjections(vm)
    initState(vm)
    initProvide(vm)
    callHook(vm, 'created')
```

在我们一个个了解之前，首选我们需要了解一下响应式数据原理，也就是我们常说的：订阅-发布 模式。

![](http://img.souche.com/f2e/8a9c80357cb84baae5285de47f4aeaeb.png)

先从 Observer 开始谈起


#### Observer
```
export class Observer {
  value: any;
  dep: Dep;
  vmCount: number; // number of vms that has this object as root $data

  constructor (value: any) {
    this.value = value
    this.dep = new Dep()
    this.vmCount = 0
    def(value, '__ob__', this)
    if (Array.isArray(value)) {
      const augment = hasProto
        ? protoAugment
        : copyAugment
      augment(value, arrayMethods, arrayKeys)
      this.observeArray(value)
    } else {
      this.walk(value)
    }
  }

  /**
   * Walk through each property and convert them into
   * getter/setters. This method should only be called when
   * value type is Object.
   */
  walk (obj: Object) {
    const keys = Object.keys(obj)
    for (let i = 0; i < keys.length; i++) {
      defineReactive(obj, keys[i], obj[keys[i]])
    }
  }

  /**
   * Observe a list of Array items.
   */
  observeArray (items: Array<any>) {
    for (let i = 0, l = items.length; i < l; i++) {
      observe(items[i])
    }
  }
}

```
当我们调用```new Observer(value)```的时候，会去执行```this.walk(value)```这个方法，其功能主要是遍历value的属性，通过```Object.defineProperty```方法来定义响应式数据。那么是如何实现响应通知的呢？来看一下他的具体代码：

#### defineReactive
```js
export function defineReactive (
 obj: Object,
 key: string,
 val: any,
 customSetter?: ?Function,
 shallow?: boolean
) {
  const dep = new Dep()
  ...
  Object.defineProperty(obj, key, {
      ...
      get: function reactiveGetter () {
        ...
        dep.depend()
        ...
        return value
      },
      set: function reactiveSetter (newVal) {
        ...
        dep.notify()
      }
    })
}
```
这里我们省略了部分代码，主要注意```Dep``` 这个东西，也就是我们常说的<strong>订阅器</strong>，这里主要做了2件事情：
1. ```dep.depend()```
2. ```dep.notify()```

#### Dep
通过上面的图，我们知道，Dom 上通过指令或者双大括号绑定的数据，会为数据进行添加观察者```watcher```，当实例化```Watcher```的时候
会触发属性的```getter```方法，此时会调用```dep.depend()```。我们看一下```depend```方法：
```js
depend () {
  if (Dep.target) {
    Dep.target.addDep(this)
  }
}
```
```Dep.target``` 是什么东西呢？其实在进行```Watcher```实例化的时候，会调用内部的```get```函数，开始为他初始化
```js
get () {
  pushTarget(this)
  ...
}
```

#### Watcher
其中```pushTarget``` 方法就是为```Dep.target```绑定此```watcher```实例，所以```Dep.target.addDep(this)```也就是执行此实例中的```addDep```方法
```js
addDep (dep: Dep) {
  ...
  dep.addSub(this)
}
```
这样便为我们的```dep```实例添加了一个```watcher```实例。



接着当我们更新```data```的时候，会触发他的```set```方法，执行```dep.notify()```函数：
```js
notify () {
  // stabilize the subscriber list first
  const subs = this.subs.slice()
  for (let i = 0, l = subs.length; i < l; i++) {
    subs[i].update()
  }
}
```
这里也就是遍历```dep```中收集到的```watcher```实例，进行```update()```。也就是进行数据更新操作。这也就是简单的数据响应式，其实还需要注意的是：
当数据的getter触发后，会收集依赖，但也不是所有的触发方式都会收集依赖，只有通过watcher触发的getter会收集依赖：```if (Dep.target) { dep.depend() }```，而所谓的被收集的依赖就是当前watcher，DOM中的数据必须通过watcher来绑定，只通过watcher来读取。


接下来进入[下篇](https://github.com/monkeyWangs/blogs/blob/master/src/Vue/4.md)






