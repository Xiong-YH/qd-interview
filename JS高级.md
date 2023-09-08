# JS高级

### 原型相关

- **原型**

prototype：每个函数都会创建一个prototype属性，这个属性指向原型对象，该对象包含应该由特定引用类型的实例共享的属性和方法。实际上，这个对象就是通过调用构造函数创建的对象的原型。
proto： 每次调用构造函数创建一个新实例，这个实例内部[[prototype]]指针就会指向该构造函数的原型对象，脚本中没有访问[[prototype]]特性的标准方式，但是在部分浏览器中会在每个对象上暴露__proto__属性来访问原型对象。
constructor：所有原型对象会自动获得名为constructor的属性，指回与之关联的构造函数

- **原型链**

每个实例对象都有__proto__属性指向它构造函数的原型对象，而原型对象也有自己的原型对象，一层层向上，最终直到顶级原型对象null，这样就形成一个原型链。
当访问对象的一个属性或方法时，该对象身上不存在该属性方法，就会沿着原型链向上找，直到找到该属性方法。
原型链顶层是Object.prototype,如果没有了就指向null。
![8896d2c94eb06236b1522bd5db3f835.jpg](https://cdn.nlark.com/yuque/0/2023/jpeg/38776445/1693925522090-b58a5c60-e1cb-4d05-a9df-8f0fec150356.jpeg#averageHue=%23f9f9f6&clientId=uc79508fb-7a8c-4&from=paste&height=896&id=ua3cd3740&originHeight=1344&originWidth=1080&originalType=binary&ratio=1.5&rotation=0&showTitle=false&size=323440&status=done&style=none&taskId=ua08d2467-4ed4-4c88-90ac-f21517e10b7&title=&width=720)
图中证明每一个函数的默认原型都是Object的一个实例对象，其__proto__指向Object原型，Object函数的prototype指向Object原型，__proto__指向Function函数原型。

- 继承

  - 组合继承

    ```javascript
    function Person (name,age) {
        this.age = age
        this.name = name
    }
    
    Person.prototype.fn1 = function() {
        console.log('fn1');
    }
    
    function Child(name,age,height) {
        Person.call(this,name,age)
        this.height = height
    }
    
    Child.prototype = new Person()
    
    let c1 = new Child("李华",18,190)
    console.log(c1);
    ```

    组合式继承可以做到继承父类的属性和方法，但是要调用两次父类，因此会产生性能损耗。

  - 寄生组合继承

    ```javascript
    function createObject(o) {
        function Foo() {}
        Foo.prototype = o
        return new Foo()
    }
    
    
    function inherit(subType,superType) {
        subType.prototype = Object.create(superType.prototype)    //也可以使用createObject()
        Object.defineProperty(subType.prototype,"constructor",{
            enumerable:false,
            configurable:true,
            writable:true,
            value:subType
        })
    }
    
    function Person (name,age) {
        this.age = age
        this.name = name
    }
    
    Person.prototype.fn1 = function() {
        console.log('fn1');
    }
    
    function Child(name,age,height) {
        Person.call(this,name,age)
        this.height = height
    }
    
    Child.prototype.studying = function() {
        console.log("studying~");
    }
    
    inherit(Child,Person)
    
    let c2 = new Child("why", 18, 111)
    
    ```

    这里只调用了一次SuperType构造函数，减少了性能开销，也解决了subType.prototype上不必要用到的的属性。

  - ES6中的extend关键字实现

  ```javascript
  class Person {
      constructor(name) {
          this.name = name
      }
  
      getName = function() {
          console.log("我的名字是"+this.name);
      }
      static fn1 = function() {
              console.log('fn1');
      }
  }
  
  class Child extends Person {
      constructor(name,age) {
          super(name)
          this.age = age
      }
  }
  
  const c1 = new Child("小明",20)
  c1.getName()
  console.log(c1);
  Person.fn1()
  Child.fn1()
  console.log(Child);、
  ```



- **call()、apply()、bind()**

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
    Object.prototype._call = function(thisObject,...Arys) {
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
    
    person.getperson._call(person2)
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
    Object.prototype._apply = function(thisObject,Arys) {
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
    
    person.getperson._apply(person2,["why",20])
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
    Object.prototype._bind = function(thisObject,...Arys) {
    
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
    
    let p1 = person.getperson._bind(person2,"lihua",21)
    setTimeout(p1,2000)///2秒后出现 lihua 21
    ```

    

  - 

    

