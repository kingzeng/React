# React

React Lifecycle

Mount =>初始化阶段
Update => 运行中阶段
UnMount => 销毁阶段

1 componentWillMount()
   note: 该方法是唯一一个在render()之前调用的生命周期方法。因此是在服务端渲染中唯一被调用的方法。
   explain: 1.setState不会发生重新渲染(re-render);
            2.通常情况下，推荐用constructor()方法代替
            3.可以进行开启定时器、向服务器发送请求等操作
            
2 render()
   note:组件渲染
   
3 componentDidMount()
   note:组件已经被渲染到页面中后触发 
   explain: 1.React 已经渲染了组件并且将组件插入 DOM。因此如有有任何依赖于 DOM 的初始化，应该放在这里
            2.这里可以加载服务器数据，并且如果使用了redux之类的数据服务，这里可以出发加载服务器数据的action
            3.这里可以使用setState()方法触发重新渲染(re-render)
  
4 componentWillReceiveProps(nextProps) =>将更新为 static getDerivedStateFromProps（新增）
   note: 当组件接收到新的props，该方法会首先被调用，但是需要注意一点，即使props并没有发生改变，该方法也会被调用，所以使用该方法的时候要确保比较               this.props和nextProps，避免不必要的渲染
   
5 shouldComponentUpdate(nextProps, nextState)
   note:有些时候需要避免不必要的渲染，可以使用该方法。返回false意味着 React 不执行componentWillUpdate()，render()，componentDidUpdate()。
        组件首次渲染时并不会触发
   explain:一般我们通过该函数来优化性能：
   example: React项目需要更新一个小组件时，很可能需要父组件更新自己的状态。而一个父组件的重新更新会造成它旗下所有的子组件重新执行render()方法，形成新的虚拟DOM，再用diff算法对新旧虚拟DOM进行结构和属性的比较，决定组件是否需要重新渲染
无疑这样的操作会造成很多的性能浪费，所以我们开发者可以根据项目的业务逻辑，在shouldComponentUpdate()中加入条件判断，从而优化性能
例如React中的就提供了一个PureComponent的类，当我们的组件继承于它时，组件更新时就会默认先比较新旧属性和状态，从而决定组件是否更新。值得注意的是，PureComponent进行的是浅比较，所以组件状态或属性改变时，都需要返回一个新的对象或数组
 
6 componentWillUpdate(nextProps, nextState)()即将移除
    note:该方法在被渲染前调用。shouldComponentUpdate在新的props进入组件或者state改变的时候调用。组件首次渲染不会触发

7 componentDidUpdate(prevProps, prevState)
    note:组件被更新完成后触发。页面中产生了新的DOM的元素，可以进行DOM操作
    
8 componentWillUnmount()
    note:组件被销毁时触发。这里我们可以进行一些清理操作，例如清理定时器，取消Redux的订阅事件等等。
    
    
 // code
import React from 'react'
import ReactDOM from 'react-dom';

class SubCounter extends React.Component {
    componentWillReceiveProps() {
        console.log('9、子组件将要接收到新属性');
    }

    shouldComponentUpdate(newProps, newState) {
        console.log('10、子组件是否需要更新');
        if (newProps.number < 5) return true;
        return false
    }

    componentWillUpdate() {
        console.log('11、子组件将要更新');
    }

    componentDidUpdate() {
        console.log('13、子组件更新完成');
    }

    componentWillUnmount() {
        console.log('14、子组件将卸载');
    }

    render() {
        console.log('12、子组件挂载中');
        return (
                <p>{this.props.number}</p>
        )
    }
}

class Counter extends React.Component {
    static defaultProps = {
        //1、加载默认属性
        name: 'sls',
        age:23
    };

    constructor() {
        super();
        //2、加载默认状态
        this.state = {number: 0}
    }

    componentWillMount() {
        console.log('3、父组件挂载之前');
    }

    componentDidMount() {
        console.log('5、父组件挂载完成');
    }

    shouldComponentUpdate(newProps, newState) {
        console.log('6、父组件是否需要更新');
        if (newState.number<15) return true;
        return false
    }

    componentWillUpdate() {
        console.log('7、父组件将要更新');
    }

    componentDidUpdate() {
        console.log('8、父组件更新完成');
    }

    handleClick = () => {
        this.setState({
            number: this.state.number + 1
        })
    };

    render() {
        console.log('4、render(父组件挂载)');
        return (
            <div>
                <p>{this.state.number}</p>
                <button onClick={this.handleClick}>+</button>
                {this.state.number<10?<SubCounter number={this.state.number}/>:null}
            </div>
        )
    }
}
ReactDOM.render(<Counter/>, document.getElementById('root'));
    
    
