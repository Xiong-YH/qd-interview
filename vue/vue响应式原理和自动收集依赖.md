# 响应式和自动收集依赖实现

```javascript
let reactuveFn = null
function watchFn(fn) {
    reactuveFn = fn
    fn()
}

class Dep {
    constructor() {
        this.reactiveData = []
    }

    add(fn) {
        this.reactiveData.push(fn)
    }

    notify() {
        this.reactiveData.forEach((fn)=>{
            fn()
        })
    }
}



const obj = {
    name:'kobe',
    age:20
}

//先进行数据劫持
Object.keys(obj).forEach((index)=>{
    let value = obj[index]
    Object.defineProperty(obj,index,{
        set:function(newValue) {

            value = newValue
            const dep = getDeepend(obj,index)
            dep.notify()
            
        },
        get:function() {
            const dep = getDeepend(obj,index)
            dep.add(reactuveFn)
            return value
        }
    })
})


//如何做到数据改变时，和该数据相关的依赖能够自动触发
//如何做到自动收集依赖，对每个属性都有自己的依赖,
//能获取什么？ obj + 属性名
const wekObj = new WeakMap()
function getDeepend(obj,key) {
    //根据对象obj，找到对应的map
    let map = wekObj.get(obj)
    if(!map) {
        map = new Map ()
        wekObj.set(obj,map)
    }
    //根据对应的key，找到对应的dep
    let dep = map.get(key)
    if(!dep) {
        dep = new Dep()
        map.set(key,dep)
    }
    return dep
}

watchFn(()=>{
    console.log(obj.name);
})


watchFn(()=>{
    console.log(obj.age);
})
console.log('-----------------------------------------');


obj.name = 'james'
```

