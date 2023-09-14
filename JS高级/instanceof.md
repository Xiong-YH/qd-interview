## instanceof原理

​	__instanceof__ 能判断对象原型，因为是通过对象原型链进星查找

​	手写实现

​	

```javascript
	function myInstanceof(obj,originObj) {
        //先得到类的原型
        let prototype = originObj.prototype
        //获取实例的原型
        	obj = obj.__proto__
        //循环判断直到对
        while(true) {
            if(obj === null || obj === undefined) return false
             if(obj === prototype) return true
            obj = obj.__proto__
        }
    }
```

