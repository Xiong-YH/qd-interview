# connect函数

> 调用时返回值为高阶组件，参数是两个函数，分别为mapStateToProps和mapDispatchToProps

```javascript
import { PureComponent } from "react";
import store from '../redux练习2/store/index'

function myConnect (mapStateToProps,mapDispatchToProps) {
    return function (WrapperComponent) {
        class NewComponent extends PureComponent {
            constructor(props) {
                super(props)
                    this.state = mapStateToProps(store.getState())
            }

            componentDidMount() {
                this.unsubscribe = store.subscribe(()=>{
                    //当数据变化时，页面强制刷新
                    // this.forceUpdate()
                    this.setState = (mapStateToProps(store.getState())) 
                })
            }

            componentWillUnmount() {
                this.unsubscribe()
            }

            render() {
                const state = mapStateToProps(store.getState())
                const dispatch = mapDispatchToProps(store.dispatch)
                return <WrapperComponent {...this.props} {...state} {...dispatch}/>
            }
        }
        return NewComponent
    }
}

export default myConnect
```

**因为调用时要使用store，耦合度较高，可以利用context对store进行共享**

![image-20231012222731321](C:\Users\听风\AppData\Roaming\Typora\typora-user-images\image-20231012222731321.png)

**最后放入index.js入口文件**

![image-20231012222822167](C:\Users\听风\AppData\Roaming\Typora\typora-user-images\image-20231012222822167.png)