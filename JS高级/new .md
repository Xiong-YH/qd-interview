## new

- 产生一个新的对象
- 将新对象原型指向构造类原型
- this绑定新对象
- 若没有返回非空对象，则返回该对象



手写实现

```javascript
function create() {
    let obj = {}
    //得到arguments第一项函
    let Con = [].shift.call(arguments)
    obj.__proto__ = Con.prototype
    let res = Con.apply(obj,arguments)
    return res instanceof Object ? res : obj
}
```

