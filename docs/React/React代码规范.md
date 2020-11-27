/**
* @name: React代码规范
* @author: LIULIU
* @date: 2020-11-27 11:24
* @description：React代码规范
* @update: 2020-11-27 11:24
*/
## React 代码规范
https://github.com/airbnb/javascript（网友整理Javascript规范）
https://github.com/airbnb/javascript/tree/master/react（网友整理React规范）
https://github.com/airbnb/css#css（网友整理CSS规范）
团队中每个开发人员的水平不同，技术关注点不同，如果没有一份代码规范的参照和约束，那么项目中的代码将会风格迥异，难以维护，为保证代码质量和风格统一，特此拟定一份《团队React 代码规范》，这样整个团队的开发人员可以参照这份代码规范进行编码，从而让团队的代码风格统一，利于维护。如果你的团队还没有这么一份 React 代码规范，也许这正是你需要的；如果你的团队已经有了 React 代码规范，这份规范也许能起到锦上添花的效果。
### 1、基础规则
1. 一个文件声明一个组件： 尽管可以在一个文件中声明多个 React 组件，但是最好不要这样做；推荐一个文件声明一个 React 组件，并只导出一个组件；
2. 使用 JSX 表达式： 不要使用 React.createElement 的写法；
3. 函数组件和 class 类组件的使用场景： 如果定义的组件不需要 props 和 state ，建议将组件定义成函数组件，否则定义成 class 类组件。
### 2、组件声明
（1）组件名称和定义该组件的文件名称建议要保持一致；
````javascript 1.8
推荐：
import Footer from './Footer';
````
````javascript 1.8
不推荐：
import Footer from './Footer/index';
````
（2）不要使用 displayName 属性来定义组件的名称，应该在 class 或者 function 关键字后面直接声明组件的名称。
````javascript 1.8
推荐：
export default class MyComponent extends React.Component {
}
````
````javascript 1.8
不推荐：
export default React.Component({
 displayName: 'MyComponent',
});
````
### 3、React 中的命名
• 组件名称： 推荐使用大驼峰命名;
• 属性名称： React DOM 使用小驼峰命令来定义属性的名称，而不使用 HTML 属性名称的命名约定；
• style 样式属性： 采用小驼峰命名属性的 JavaScript 对象；

推荐：
````javascript 1.8
// 组件名称
MyComponent
// 属性名称
onClick
// 样式属性
backgroundColor
````
### 4、JSX 写法注意
````javascript 1.8
4.1、标签
（1）当标签没有子元素的时候，始终使用自闭合的标签 。
推荐：
// Good
<Component />
不推荐：
<Component></Component>
（2）如果标签有多行属性，关闭标签要另起一行 。
推荐：
<Component
 bar="bar"
 baz="baz"
/>
不推荐：
<Component
 bar="bar"
 baz="baz" />
（3）在自闭标签之前留一个空格。
推荐：
<Foo />
不推荐:
<Foo/>
<Foo />
<Foo
 />
（4）当组件跨行时，要用括号包裹 JSX 标签。
推荐：
render() {
    return (
        <MyComponent className="long body" foo="bar">
        <MyChild />
        </MyComponent>
    );
}
不推荐：
render() {
    return <MyComponent className="long body" foo="bar">
        <MyChild />
    </MyComponent>;
}
````
4.2、对齐
````javascript 1.8
JSX 语法使用下列的对齐方式 ：
// 推荐
<Foo
 superLongParam="bar"
 anotherSuperLongParam="baz"
/>
// 如果组件的属性可以放在一行（一个属性时）就保持在当前一行中
<Foo bar="bar" />
// 多行属性采用缩进
<Foo
 superLongParam="bar"
 anotherSuperLongParam="baz"
>
 <Quux />
</Foo>
// 不推荐
<Foo superLongParam="bar"
 anotherSuperLongParam="baz" />
4.3、引号
JSX 的属性都采用双引号，其他的 JS 都使用单引号 ，因为 JSX 属性 不能包含转义的引号, 所以当输入 "don't" 这类的缩写的时候用双引号会更方便。
推荐：
<Foo bar="bar" />
<Foo style={{ left: '20px' }} />
不推荐：
<Foo bar='bar' />
 
<Foo style={{ left: "20px" }} />
````
### 5、样式写法
React 中样式可以使用 style 行内样式，也可以使用 className 属性来引用外部 CSS 样式表中定义的 CSS 类，我们推荐使用 className 来定义样式。并且推荐使用 SCSS 来替换传统的 CSS 写法，具体 SCSS 提高效率的写法可以参照先前总结的文章。
### 6、defaultProps 使用静态属性定义
defaultProps 推荐使用静态属性定义，不推荐在 class 外进行定义。
````javascript 1.8
推荐：
class Example extends React.Component {
    static defaultProps = {
        name: 'stranger'
    }
    render() {
        // ...
    }
}
不推荐：
class Example extends React.Component {
    render() {
        // ...
    }
}
Example.propTypes = {
    name: PropTypes.string
};
````
### 7、key 属性设置
key 帮助 React 识别哪些元素改变了，比如被添加或删除。因此你应当给数组中的每一个元素赋予一个确定的标识。当元素没有确定 id 的时候，万不得已你可以使用元素索引 index 作为 key，但是要主要如果列表项目的顺序可能会变化，如果使用索引来用作 key 值，因为这样做会导致性能变差，还可能引起组件状态的问题。
````javascript 1.8
推荐：
{todos.map(todo => (
 <Todo
 {...todo}
 key={todo.id}
 />
))}
不推荐：
{todos.map((todo, index) =>
 <Todo
 {...todo}
 key={index}
 />
)}
````
### 8、为组件绑定事件处理器
React 为组件绑定事件处理器提供 4 种方法，有 public class fields 语法、构造函数中进行绑定、在回调中使用箭头函数、使用 Function.prototype.bind 进行绑定，我们推荐使用 public class fields 语法，在不满足需求情况下使用箭头函数的写法（传递参数给事件处理器）。
````javascript 1.8
推荐：
handleClick = () => {
 console.log('this is:', this);
 }
 <button onClick={this.handleClick}> Click me </button>
不推荐：
constructor(props) {
 super(props);
 this.handleClick = this.handleClick.bind(this);
 }
 handleClick(){
 console.log('this is:', this);
 }
 <button onClick={this.handleClick}> Click me </button>
````
### 9、State
````javascript 1.8
9.1、不要直接修改 state
除了 state 初始化外，其它地方修改 state，需要使用 setState() 方法，否则如果直接赋值，则不会重新渲染组件。
推荐：
this.setState({comment: 'Hello'});
不推荐：
this.state.comment = 'hello';
9.2、State 的更新可能是异步的
出于性能考虑，React 可能会把多个 setState() 调用合并成一个调用；因为 this.props 和 this.state 可能会异步更新，所以这种场景下需要让 setState() 接收一个函数而不是一个对象 。
推荐：
this.setState((state, props) => ({
    counter: state.counter + props.increment
}));
不推荐：
this.setState({
    counter: this.state.counter + this.props.increment,
});
````
### 10、组件的代码顺序
组件应该有严格的代码顺序，这样有利于代码维护，我们推荐每个组件中的代码顺序一致性。
````javascript 1.8
class Example extends Component {
    // 静态属性
    static defaultProps = {}
    // 构造函数
    constructor(props) {
        super(props);
        this.state={}
    }
    // 声明周期钩子函数
    // 按照它们执行的顺序
    // 1. componentWillMount
    // 2. componentWillReceiveProps
    // 3. shouldComponentUpdate
    // 4. componentDidMount
    // 5. componentDidUpdate
    // 6. componentWillUnmount
    componentDidMount() { ... }
    // 事件函数/普通函数
    handleClick = (e) => { ... }
    // 最后，render 方法
    render() { ... }
}
````
### 11、使用高阶组件
使用高阶组件解决横切关注点问题，而不是使用 mixins ，mixins 导致的相关问题可以参照文档；
### 12、避免不必要 render 的写法
shouldComponentUpdate 钩子函数和 React.PureComponent 类都是用来当 state 和 props 变化时，避免不必要的 render 的方法。shouldComponentUpdate 钩子函数需要自己手动实现浅比较的逻辑，React.PureComponent 类则默认对 props 和 state 进行浅层比较，并减少了跳过必要更新的可能性。我们推荐使用React.PureComponent 避免不要的 render。
### 13、状态提升
如果多个组件需要反映相同的变化数据，建议将共享状态提升到最近的共同父组件中去；从而依靠自上而下的数据流，而不是尝试在不同组件间同步 state。
### 14、推荐使用 Context
如果某个属性在组件树的不同层级的组件之间需要用到，我们应该使用 Context 提供在组件之间共享此属性的方式，而不不是显式地通过组件树的逐层传递 props。
### 15、Refs 写法
Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素 。我们推荐使用 createRef API 的方式 或者 回调函数的方式使用 Refs ，而不是使用this.refs.textInput 这种过时的方式访问 refs ，因为它存在一些 问题。
### 16、路由加载
建议使用路由懒加载当前用户所需要的内容，这样能够显著地提高你的应用性能。尽管并没有减少应用整体的代码体积，但你可以避免加载用户永远不需要的代码，并在初始加载的时候减少所需加载的代码量。
````javascript 1.8
推荐：
const OtherComponent = React.lazy(() => import('./OtherComponent'));
不推荐：
import OtherComponent from './OtherComponent';
````
### 17、AJAX 发起请求的时机
推荐在 componentDidMount这个生命周期函数中发起 AJAX 请求。这样做你可以拿到 AJAX 请求返回的数据并通过 setState 来更新组件。
