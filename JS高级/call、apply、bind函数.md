## **call()、apply()、bind()**

- **call()函数**

  - 语法： `function.call(thisArg, arg1, arg2, ...)`。  其中thisArg是要设置为函数执行上下文的对象，也就是this要指向的对象，从第二个参数开始，arg1, arg2, ... 是传递给函数的参数。通过使用call方法，可以将一个对象的方法应用到另一个对象上。

  - ```javascript
    const person = {
        name:"alice",
        getperson:function() {
            console.log(this); //{name: 'lina', age: 20}
        }
    }
    
    const person2 = {
        name:"lina",
        age:20
    }
    
    person.getperson.call(person2)
    ```


  

  - 手写call()方法

  - ```javascript
    Object.prototype.mycall = function(thisObject,...Arys) {
        //先判定传入是不是对象
        //如果是一个null或者undefined，赋值window
        thisObject = (thisObject === null || thisObject === undefined) ? window:Object(thisObject)
    
        Object.defineProperty(thisObject,"fn",{
            enumerable:false,
            configurable:true,
            writable:false,
            value:this
        })
    
        thisObject.fn(...Arys)
        //避免污染环境变量
        delete thisObject.fn
    }
    
    person.getperson.mycall(person2)
    ```


  

- **apply()函数**

  - 语法：`function.apply(thisArg, [argsArray])`。 其中thisArg是要设置为函数执行上下文的对象，也就是this要指向的对象，argsArray是一个包含参数的数组。通过使用apply方法，可以将一个对象的方法应用到另一个对象上，并使用数组作为参数。

  - ```javascript
    const person = {
        name:"alice",
        getperson:function(name,age) {
            
            console.log(name,age); //why 20
        }
    }
    
    const person2 = {
    }
    
    person.getperson.apply(person2,["why",20])
    ```


  

  - 手写apply()方法

  - ```javascript
    Object.prototype.myapply = function(thisObject,Arys) {
        //先判定传入是不是对象
        //如果是一个null或者undefined，赋值window
        thisObject = (thisObject === null || thisObject === undefined) ? window:Object(thisObject)
    
        Object.defineProperty(thisObject,"fn",{
            enumerable:false,
            configurable:true,
            writable:false,
            value:this
        })
    
        thisObject.fn(...Arys)
        //避免污染环境变量
        delete thisObject.fn
    }
    
    person.getperson.myapply(person2,["why",20])
    ```


  

- **bind()函数**

  - 语法：`function.bind(thisArg, arg1, arg2, ...)`。 其中thisArg是要绑定到函数执行上下文的对象，也就是this要指向的对象，从第二个参数开始，arg1, arg2, ...是传递给函数的参数。与call和apply方法不同，bind方法并不会立即执行函数，而是返回一个新函数，可以稍后调用。这对于事件处理程序和setTimeout函数等场景非常有用。

  - ```javascript
    const person = {
        name:"alice",
        getperson:function(name,age) {
            
            console.log(name,age);
        }
    }
    
    const person2 = {
    }
    
    let p1 = person.getperson.bind(person2,"lihua",21)
    setTimeout(p1,2000) //2秒后出现 lihua 21
    ```


  

  - 手写bind()

  - ```javascript
    Object.prototype.mybind = function(thisObject,...Arys) {
    
        //先判定传入是不是对象
        //如果是一个null或者undefined，赋值window
        thisObject = (thisObject === null || thisObject === undefined) ? window:Object(thisObject)
    
        Object.defineProperty(thisObject,"fn",{
            enumerable:false,
            configurable:true,
            writable:false,
            value:this
        })
                
        return  (...newArgs) => {
            // var allArgs = otherArgs.concat(newArgs)
            var allArgs = [...Arys, ...newArgs]
            thisObject.fn(...allArgs)
          }
    }
    
    let p1 = person.getperson.mybind(person2,"lihua",21)
    setTimeout(p1,2000)///2秒后出现 lihua 21
    ```


  

  
