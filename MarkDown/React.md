# React 
 本地引入
 ```
<!-- 支持 react的语法 -->
<script crossorigin src='https://unpkg.com/react@18/umd/react.production.min.js'></script>
<!-- 将react的 功能 引用到 dom上 -->
<script crossorigin src='https://unpkg.com/react-dom@18/umd/react-dom.production.min.js'></script>
<!-- 将 react的 jsx 语法 编译成 正常的js  是浏览器能够识别 -->
<script crossorigin src='https://unpkg.com/@babel/standalone/babel.min.js'></script>
 ```
## JSX
### JSX语言
- jsx语法就是把html和js混合到一起的写法,遇到<>解析html,遇到{}解析js
### 插值
```
<!-- 加上 type='text/babel' 为了页面加载的时候 代码 会被 babel 编译成正常的js -->
<script type='text/babel'>
    let arr= [1, 2, 3, [4, 5, [6, 7]]]
    var x = 10
    // 模板字符串
    var str = "<h1>123456</h1>"
    // 模板变量
    var y = <h1>789</h1>
    ReactDOM.render(
        <div>
            <h1>hello react {x}</h1>
            {
                // 添加注释
                // 所有的单标签 都要加上 / 收尾
                // 不会解析模板字符串
                // jsx 无法直接 解析 模板字符串
                // 可以通过 dangerouslySetInnerHTML={{ __html: 模板字符串 }} 实现
                // 参考 v-html理解
                // 不建议使用  (xss 攻击)
            }
            {str}
            <h1 dangerouslySetInnerHTML={{ __html: str }}></h1>
            <hr />
            {y}
            {
                // 数组 会 展平
            }
            {arr}
            <h1>
                {
                    // {
                    //     x: 10,
                    //     y: 20
                    // }
                }
            </h1>
        </div>,
        // 挂载
        document.getElementById("app")
    )
</script>
```
### 属性插值
```
let url = "http://www.baidu.com"
let msg = "百度一下"
let imgurl = "https://t7.baidu.com/it/u=2168645659,3174029352&fm=193&f=GIF"

let flag = false
let c = "red"

ReactDOM.render(
    <div>
        <a href={url} >{msg}</a>
        <img width="100" src={imgurl} alt="" />
        <h1 style={
            //  动态控制 行内样式
            {
                "color": flag ? c : "blue",
                fontSize: "100px"
            }
        } >hello react</h1>
        <h1 className="aaa">123456</h1>
    </div>,
    document.getElementById("app")
)
```
### 类名
```
let a = "aa"
let b = "bb"
let classarr = ["a", "b", "c", "d"]
ReactDOM.render(
    <div>
        <h1 className={1 ? a : b}>hello react</h1>

        <h1 className="a b">hello react</h1>

        <h1 className={(0 ? a : b) + " c"}>hello react</h1>

        <h1 className={classarr.join(" ")}>hello react</h1>
        
        <h1 className={[0 ? a : b, "b", "c", "d"].join(" ")}>hello react</h1>

    </div>,
    document.getElementById("app")
)
```
### wanFor:因为{ }不能直接写语句，所以可以使用高阶函数。
```
function wantFor(f) {
    let arr = []
    f(arr)
    return arr
}
{
    // 外部定义高阶函数，主要的思想是要返回一个数组，利用JSX解析数组的时候会展平
    wantFor(arr => {
        for (let i = 0; i < 6; i++) {
            arr.push(
                <div>
                    <h3>{i}</h3>
                </div>
            )
        }
    })
}
{
    // 等同 ↑ 只是不需要在外部定义高阶函数 
    (() => {
        let arr = []
        for (let i = 0; i < 6; i++) {
            arr.push(
                <div>
                    <h3>{i}</h3>
                </div>
            )
        }
        return arr
    })()
}
```
### 判断  wantIf参考wantfor (这里只是随便起的函数名)
```
{
    flag ? <h1>hello</h1> : <h1>未登录</h1>
}
{
    // { } 中间 数值 字符串 标签模板  数组 表达式
    // 表达式 返回的结果 也要是 值 字符串  数组 标签模板
    // 函数调用 返回的结果 也要是 值 字符串  数组 标签模板
}
```
## 组件(组件名称必须以大写字母开头)
### 函数式组件
```
// 函数式组件 也叫 无状态组件  不受控组件 不稳定组件  
let Mybtn = props => {
    console.log(props)
    let x = 10
    let add = () => {
        x++
        console.log(x)
    }
    return (
        <div>
            <h1 onClick={add}>我是 btn 组件-- {x}</h1>
        </div>
    )
}

ReactDOM.render(
    <div>
        {
            //方式1： 调用
            Mybtn("123")
        }
        <Mybtn x="10"></Mybtn>
    </div>,
    document.getElementById("app")
)
```
### 容器组件
- Box相当于工厂，经过的组件会被他进行'加工'
- Item这个组件中 props是个对象，可以访问原始的以及经过加工的自定义属性
```
let Item = props => {
    console.log(props, "-----------")
    return (
        <div>
            <h1>{ props.num }</h1>
        </div>
    )
}
let Box = props => {
    return (
        <div>
            {
                // props注入
            }
            <Item {...props} x={120} y="119" ></Item>
        </div>
    )
}
ReactDOM.render(
    <div>
        <Box num={110} msg="hello"></Box>
    </div>,
    document.getElementById("app")
)
```
### 插槽
```
let Box = props => {
return (
    <div>
        <h1>插槽</h1>
        {
            console.log(props)
        }
        {
            // 组件标签内的 标签 映射到 props.children 上面 多个的时候 会变成 数组
            props.children
        }
        <div>
            {
                props.children[0]
            }
        </div>
        <p>
            {
                props.children[1]
            }
        </p>
    </div>
)
}
ReactDOM.render(
    <div>
        <Box>
            <h1>hello</h1>
            <h2>word</h2>
        </Box>
    </div>,
    document.getElementById("app")
)
```
### 类组件
```
const { Component, Fragment } = React

class App extends Component {
    constructor(props) {
        super(props)
        // 定义组件状态
        this.state = {
            num: 20
        }
    }
    render() {
        return (
            // 组件 要求唯一顶层元素 
            //  Fragment 参考 template 理解
            <Fragment>
                <h1>{this.state.num}</h1>
                <h3>sdasdasd</h3>
            </Fragment>
            /*
                可以简写成
                <>
                    <h1>{this.state.num}</h1>
                    <h3>sdasdasd</h3>
                </>
            */
        )
    }
}

//挂载节点
ReactDOM.render(
    <App />,
    document.getElementById('app')
)
```
#### 类组件 最大的痛点 就是 this指向问题
#### 类组件 定义方法 跟render 同级
```
render() {
    return (
        <Fragment>
            <h1>{this.state.num}</h1>
            <button onClick={this.add.bind(this)}>add</button>
        </Fragment>
    )
}
add() {
    // 事件处理函数内部的this  不一定指向 组件实例
    console.log(this) //undefined

    // 不能通过 this.state.num  直接对 数据 进行修改  , 会失去响应性
    // this.state.num++

    //  必须通过 react  自身的  setState 方法 修改
    // this.setState({
    //     num: 66
    // })
    this.setState({
        // num:this.state.num++  错误写法  num++  ->  num = num +1
        num: this.state.num + 1
    })
    console.log(this.state.num)
}
```
#### 访问最新状态
```
add() {
    this.setState({
        num: 66
    }, () => {
        // this.setState 第二个参数 就是 更新之后的回调 参考 vue nextTick
        console.log(this.state.num, "----")
    })
    console.log(this.state.num, "+++++")
}
```
#### 事件绑定
```
render() {
    return (
        <Fragment>
            <h1>{this.state.num}</h1>
            {
                // 直接把 add 当成事件处理函数的时候 this指向 undefined
                // 不能直接 在 {} 中 调用函数 因为 { } 解析 js  , 会立即执行 该函数
            }
            <button onClick={this.add}>add</button>
            {
                //  在绑定事件的时候 对 this 指向 进行 纠正
            }
            <button onClick={this.add.bind(this, 123)}>add</button>
            {
                // 通过 箭头 函数 纠正this
                //使用 箭头函数 包裹之后 此时的事件处理函数 是箭头函数 所以 e 会在 箭头函数的 形参上, 需要倒一手 给 add 
            }
            <button onClick={() => { this.add() }}>add</button>
        </Fragment>
    )
}
add(e) {
    console.log(this, e)
}
```
## 生命周期
### componentWillMount  挂载前
### componentDidMount  挂载后
### componentWillUpdate  更新前
### componentDidUpdate  更新后
### componentWillUnmount  卸载前
### 注意点：当 数据发生变化的时候 (setState调用) , 当前组件 以及其所有的子组件 都会重新 render

## 组件通信
### 父传子和vue一样
### 子传父需要 在向子组件 传递方法的时候 直接 纠正好 this的问题 否则 在子组件 调用的时候 this 会 指向 子组件自己的 props
- 可以用bind修改，不过更推荐使用箭头函数,比较直观的观察参数
  
## 类组件性能优化
- react 组件内部 只要调用setState 不管数据 是否发生变化 当前组件 以及其 所有子组件 都会重新 render , 挂载钩子 不会重新执行
### shouldComponentUpdate
- 根据组件 当前的 状态 (props,state) 是否发生变化 决定是否 重新 render
- ```
    //  根据组件 当前的 状态 (props,state) 是否发生变化 决定是否 重新 render
    shouldComponentUpdate(newprops, newstate) {
        console.log(newstate)
        console.log(this.state)
        if (newstate.num == this.state.num) {
            return false
        } else {
            return true
        }
    }
  ```
- 返回的是false不会进行更新，true就会进行更新
### PureComponent
- 把类组件的 继承 从component 变成 PureComponent 就能实现 只有 状态 发生变化 才会重新 render

## 操作DOM
### 遍历多个的问题
- ```
    {
        (() => {
            let list = []
            for (let i = 0; i < 6; i++) {
                list.push(
                    // <button ref={'btn' + i} key={i}>{i}</button>
                    <button ref="btn" key={i}>{i}</button>
                )
            }
            return list
        })()
    }
    <hr />
    <button onClick={
        () => {
            // 多个元素 ref 相同 通过 this.refs 访问到的是最后一个 dom
            //  单个dom 可以使用该方式
            console.log(this.refs)
        }
    }>访问DOM</button>
   ```
### createRef
- ```
    constructor(props) {
        super(props)
        this.state = {
        }
        this.btn = createRef()
    }
    render() {
        return (
            <div>
                {
                    (() => {
                        let list = []
                        for (let i = 0; i < 6; i++) {
                            list.push(
                                <button ref={this.btn} key={i}>{i}</button>
                            )
                        }
                        return list
                    })()
                }
                <button onClick={
                    () => {
                        //  createRef 如果帮定 多个 DOm 访问到的 也是 最后一个 
                        //  适合 操作 单独DOM
                        console.log(this.btn.current)
                    }
                }>访问DOM</button>
            </div>
        )
    }
   ```
### 批量操作DOM
- ```
    constructor(props) {
        super(props)
        this.state = {
            btns: []
        }
    }
    render() {
        return (
            <div>
                {
                    (() => {
                        let list = []
                        for (let i = 0; i < 6; i++) {
                            list.push(
                                <button ref={el => {
                                    this.state.btns.push(el)
                                }} key={i}>{i}</button>
                            )
                        }
                        return list
                    })()
                }
                <hr />
                <button onClick={
                    () => {
                        console.log(this.state.btns)
                    }
                }>访问DOM</button>
            </div>
        )
    }
   ```
## 路由
- ```
    const {
        HashRouter, //包裹路由区域 内部定义 路由切换
        Route, //包裹路由组件 以及 匹配规则
        Link,// 路由切换 组件 a
        Switch, //路由匹配成功之后 不在 接着向下 匹配
    } = ReactRouterDOM
  ```
### 路由组件
- ```
    let Index = () => <h1>首页</h1>
  ```
### 路由匹配问题
- ```
    render() {
        return (
            <div>
                {
                    // <h1>网页头部--公共区域</h1>
                }
                <HashRouter>
                    <div>
                        <Link to="/">首页</Link>
                        <Link to="/list">列表页</Link>
                    </div>
                    {
                        //路由匹配成功之后 不在 接着向下 匹配
                    }
                    <Switch>
                        <Route path="/">
                            <Index></Index>
                        </Route>
                        <Route path="/list">
                            <List />
                        </Route>
                    </Switch>
                </HashRouter>
                {
                    // <h1>网页尾部--公共区域</h1>
                }
            </div>
        )
    }
  ```
- 更换顺序
- 使用精准匹配 exact
- ```
    <Switch>
        {
            // exact 精准匹配 要求 地址 完全 一致
        }
        <Route path="/" exact component={Index} />
        <Route path="/list" component={List} />
    </Switch>
    ```

  

