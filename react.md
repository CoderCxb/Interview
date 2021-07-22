##### 1. setState

- 不得直接修改state，需要使用setState修改state

  ```javascript
  // 情况一
  this.state.count++; 	// 不允许
  // 情况二 
  // 虽然看起来没什么问题，但是push的时候list已经发生改变了并且由于在setState时，list已经发生改变，所以setState前后的list是相等的，这样的话shouldComponentUpdate就无法进行优化。
  this.setState({
      list:this.state.list.push(1) // 不允许
  })
  // 等价于
  this.state.push(1);
  this.setState({
      list:this.state.list
  })
  ```

- 同步|异步

  ```javascript
  // count 默认设置为 0 
  // 1. 在合成事件中(如 onClick) setState是异步的
  this.setState({
      count:this.state.count+1
  },()=>{
      // 回调函数相等于nextTick，可以获取到最新的值
      console.log(this.state.count) // 1
  });
  console.log(this.state.count); // 0
  
  // 2. 在生命周期(钩子)中是异步的
  componentDidMount(){
      this.setState({
      count:this.state.count+1
  });
  console.log(this.state.count); // 0
  }
  
  // 3. setState使用函数时，后续代码拿不到最新的值(异步)，但是每一次setState都是有效的，所以页面可以拿到最新值。
  this.setState((preState)=>{
      return {
          count:preState.count+1
      }
  })
  this.setState((preState)=>{
      return {
          count:preState.count+1
      }
  })
  console.log(this.state.count) // 0，同步获取不到最新值,但是页面上显示的是最新值(2);
  
  // 大胆的推断，setState在宏任务中是同步的
  // 4. setTimeout中 setState是同步的
  setTimeout(()=>{
      this.setState({
          count:this.state.count+1
      })
      console.log(count) // 1
  })
  
  // 5. 自定义的DOM事件中为同步
  document.body.addEventListener('click',()=>{
      this.setState({
          count:this.state.count+1
      })
      console.log(this.state.count) // 1
  })
  
  // 6. 在requestAnimationFrame中也是同步的
  requestAnimationFrame(()=>{
      this.setState({
          count:this.state.count+1
      })
      console.log(this.state.count); // 1
  })
  ```

  

- 多次执行setState时，是否会被合并

  ```javascript
  // 与setState的同步与异步有关
  // 1. 在setTimeout和自定义DOM中，setState是同步的，因此后续的setState获取的值就是最新的，每次操作都是有效的。
  // 2. 在方法中，setState是异步的，后续的setState获取的state都是旧的，所以只会以最后一次的setState为准。
  ```




##### 2.  生命周期

![img](images/12.png)

```react
// 一、组件挂载阶段
// 1. constructor() 构造函数阶段
// 构造函数主要用于初始化state和为方法绑定this执行 如果不需要 可以不用写构造函数 并且不要调用setState方法 构造函数可以直接初始化state并且需要在其他语句前调用super(props)。

// 2. static getDerivedStateFromProps(props,state) 生命周期的名字翻译过来就是：从props中衍生出state
// 在初次渲染和后续更新中都会触发，返回一个对象来更新state，返回null则不更新。
// 可以用于将props映射到state上(组件不允许修改传入的props，所以根据需求，有时需要将props映射至state上)

// 3. render() 渲染函数 必须实现 纯函数
// 在渲染函数中不得使用setState，因为setState会不断触发render，会导致页面不断的更新，无法正常显示。
// 返回值:JSX、数组、fragments、Protals、字符串和数值、布尔和nul

// 4. UNSAFE_componentWillMount() 16版本会报警告,17版本报异常 不建议使用

// 5. componentDidMount() 组件挂载完毕时触发
// 和浏览器的交互需要在这个生命周期中定义，因为这是组件才挂载完毕，其他生命周期时，组件还没有挂载，也就无法操作DOM。
// 网络请求也可以放在这里请求。

// 二、组件更新阶段
// 1. static getDerivedStateFromProps(props, state) 同挂载阶段。

// 2. shouldComponentUpdate(nextProps, nextState)
// 重点：常用于性能优化，返回true时，组件更新，返回false，阻止组件更新。
// 通过对比props和state前后变化，决定返回true还是false来优化性能。
// 可以使用forceUpdate强制更新组件，但是子组件的更新照常。

// 3. UNSAFE_componentWillUpdate(nextProps, nextState) 16版本会报警告,17版本报异常 不建议使用

// 4. render()

// 5. getSnapshotBeforeUpdate(prevProps, prevState)
// 必须配合componentDidUpdate使用，否则会报错
// componentDidUpdate前调用，返回值作为componentDidUpdate的第三个参数

// 6. componentDidUpdate(prevProps, prevState, snapshot) 组件更新后触发，首次加载不会触发 
// 这个生命周期也不允许直接setState，需要附加条件，避免重复触发。
// 接收更新前的props和state，第三个参数为getSnapshotBeforeUpdate的返回值，如果没有，则为undefined

// 三、组件卸载时
// 1. componentWillUnmount()
// 组件卸载以及销毁时触发，主要做清理操作，如清除定时器、取消网络请求和清除订阅监听等。

// 四、异常边界
// 1. static getDerivedStateFromError(error)
// 此生命周期会在后代组件抛出错误后被调用。 它将抛出的错误作为参数，并返回一个值以更新 state(可以用来控制页面显示)
// 降级UI,即当发生异常时，显示别的内容，

// 2. componentDidCatch(error, info)
// error —— 抛出的错误。
// info  —— 带有 componentStack key 的对象，其中包含有关组件引发错误的栈信息。
// 可执行副作用，记录错误情况。
// 开发模式：错误会冒泡至window，即错误会被根错误处理机制捕获。
// 生产模式：只会没有被处理的错，才会被根错误处理机制捕获。
```



##### 3. Portals 传送门

```react
// 1. 基本使用 将元素传送到指定的DOM元素下 类似vue的teleport
ReactDOM.createPortal(
    <button>按钮</button>,
    document.getElementById('box'))

// 2. 注意点
// 2.1 指定的DOM元素必须已经挂载了,不然获取不到,在render函数中拿不到DOM元素(除了body和document)，所以挂载到别的DOM得用别的方式使用。
// 2.2 注意portal的事件冒泡，portal的事件冒泡并不会传递给传送后的父级DOM元素，而是按照代码中的父级进行冒泡。

// 3. 使用方式
// 3.1 方式一
// 通过自己创建一个DOM元素，然后将创建的portal挂载到该DOM上，然后再将这个DOM元素挂载到指定的DOM元素上。
// 3.2 方式二
// 可以将portal设置为一个变量，然后在componentDidMount生命周期中进行赋值，然后在render函数中进行渲染。
```

##### 4. context

```react
// 1. 创建一个需要共享的context
const MyContext = createContext({
    theme:'dark'
})

// 2. 使用 value发生改变时，其中所有消费(Consumer)组件都会重新渲染
<MyContext.Provider value={{theme:'light'}}>
    {/* Context的用法一 */}
    <MyContext.Consumer>
        {/* 通过props传入,通过this.props访问*/}
        {(value) => <UseContext {...value}></UseContext>}
    </MyContext.Consumer>
</MyContext.Provider>

// 类组件
// static contextType=MyContext;
// 通过this.context访问
```

##### 5. 异步组件

```react
// 1. import ();
// 2. React.lazy(import());
// 3. Suspense
```

##### 6. 性能优化

```react
// 前情提要:
// 在react中，父组件更新时，所有子组件都会触发更新
// 1. shouldComponentUpdate
shouldComponentUpdate(nextProps, nextState) {
    // 在此处可以比较props和nextProps，state和nextState 
    // 当返回false的时候 组件不更新 返回true 组件更新
    return true;
}

// 2. PureComponent
class MyComponent extends React.PureComponent{}
// 3. memo
function MyComponent(props) {
  /* 使用 props 渲染 */
}
function areEqual(prevProps, nextProps) {
  /*
  如果把 nextProps 传入 render 方法的返回结果与
  将 prevProps 传入 render 方法的返回结果一致则返回 true，
  否则返回 false
  */
}
// 当不传入areEqual函数时，默认进行浅层比较，即只比较props第一层属性
export default React.memo(MyComponent, areEqual);
```



##### 7. 公共代码抽离

本质上都是将需要包装的组件传递到一个函数中

- #####  High Order Component (HOC) 高阶组件

```react
const hoc= (Component)=>{
    class updateComponent extends React.Component{
        constructor(props){}
        render(){
			// 可以传递props
            return <div>
            	<Component></Component>
            </div>
        }
    }
    return updateComponent;
}
// 使用
hoc()
```

- ##### Render Props

```react
function Test(props){
  let {render}=props;
  let title='Render Props';
  return <div>
    {render(title)}
  </div>
}
// 通过传递函数组件(名字任意,此处叫render),然后子组件通过props获取到这个函数组件并传参使用。(此处的感觉有点像插槽,并且和普通复用组件逻辑相似)
export default function RenderProps(){
  return <div>
    <Test render={(value)=><h1>{value}</h1>}></Test>
  </div>
}
```

#####  8. Hooks

```react
// 1. useState
// useState返回一个数组 [0]是值 [1]是设置值的函数
// const [count,setCount]=useState(0);

// 2. useEffect
// 监听state改变,在DOM更新完毕后异步执行，不会阻塞DOM
// 第一个参数回调,第二个参数是依赖(依赖改变,触发回调)
// 2.1 模拟componentDidUpdate 不接收依赖
// 2.2 模拟componentDidMount 接收依赖为[]
// 2.3 回调函数返回一个函数 该函数在组件卸载时触发

// 3. useLayoutEffect
// 和 useEffect相同,但是在DOM更新完毕后是同步执行，会阻塞DOM
// 相当于生命周期 componentDidMount 和 componentDidUpdate 

// 4. useContext  共享上下文
// 先创建一个全局的context
// const MyContext = createContext({
//    theme:'dark'
// })
// 然后通过 const context=useContext(MyContext);获取

// 5. useMemo  类似于计算属性 
// 接收回调函数和依赖 依赖发生改变时重新计算值(有缓存)
// 不提供依赖数组时，每次渲染都会重新获取值
// let countStr=useMemo(()=>{
// 		return `计数:${count}`;
// },[count])

// 5. useRef 返回一个可变的ref对象 其.current被传入参数初始化
// 返回的ref对象在组件生命周期保持不变 还可以用来获取DOM元素
// let child=useRef(); // 需要在didMount才能拿到
// <Child ref=child></Child>

// useRef和createRef不同
// useRef的值不会发生改变，而createRef每次返回新的引用

// useRef和useState异同 
// useRef和useState都会声明一个数据并在在render中不重置
// useRef的current改变不会触发render,但是useState会

// 6. useReducer  类似于redux
// const initialState={count:0};   // 初始值
// 纯函数 根据action 改变state
// function reducer(state, action) {
//   switch (action.type) {
//     case 'increment':
//       return {count: state.count + 1};
//     case 'decrement':
//       return {count: state.count - 1};
//     default:
//       throw new Error();
//   }
// }
// const [state,dispatch]=useReducer(reducer,initialState);
// 通过 dispatch({type:'increment'})使用

// 7. useCallback 返回缓存过的回调函数 这个函数只有在依赖发生改变时才会重新创建
// 因为默认情况下,函数组件重新渲染的时候 其中的函数都会重新创建
// 可以传递一个空的数组,该函数就会被缓存 而非重新创建
// 应用场景/解决的问题: 父组件是一个函数组件时,其中定义的方法在每次重新渲染的时候都会重新创建方法，尤其是当这个方法会传递给子组件的时候,会导致子组件的更新
// const memoizedCallback=useCallback(()=>{
// 		doSomething(count)
// },[count])
	
// 8. useDebugValue(value)
// useDebugValue用于在React开发者工具中显示自定义hook
```



##### 9. React中的方法为什么需要绑定this?

```react
// onClick={this.click} 
// 由上述代码可见，this.click并没有加(),所以只是将这个方法的引用给了onClick，并没有马上调用，而是将方法赋值给了onClick,下次onClick调用时直接执行方法，这是的this指向就是全局this。

// 实例代码
// let obj={
// 		run(){console.log(this)}
// }
// obj.run();  // obj
// let run=obj.run();
// run();      // 浏览器window,node中globalThis
```



##### 10. 受控组件和非受控组件

```javascript

```





##### 11. 什么是React Fiber 



##### 12. HOC(高阶组件)、Render Props以及自定义Hook的区别





##### 13. 为什么不能在render中setState



##### 14. React组件通信方式

- props以及传递回调函数
- context
- 事件总线
- redux 、 mobx 第三方库

##### 15.  Redux相关概念



##### 16. React的事件机制











