## new

- 产生一个新的对象
- 将新对象原型指向构造类原型
- this绑定新对象
- 若没有返回非空对象，则返回该对象



手写实现

```javascript
function create(fn , ...args) {
    //判定是否为函数
    if(typeof fn !== "function") {
        throw new Error(`${fn}不为函数`)
    }
    
    const obj = {}
    
    obj.__proto__ = Object.create(fn.prototype)
    
    let res = fn.apply(obj,args)
    
    r(typeof res === "object" && res !== null) || (typeof res === "function") ? res : obj
}
```

