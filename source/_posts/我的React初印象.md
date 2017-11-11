---
title: 我的React初印象
date: 2017-11-11 17:36:24
tags:
- React
categories: 
- React
---
# 我的React初印象
前两天刚把Vue给看了下，做了个小项目练手，这两天趁着把如日中天的React官网文档刷了一半，跟着它的**Thinking in React**的思路也做了一个[小demo](https://github.com/flyinglove/learning-react),顺道把React给我的印象总结了一下，总体来说就是React中并没有给我们封装很多的指令，很多Vue中有的指令在React中都需要我们自己去写js，我把Vue看作是对React的一个封装（当然只是概念上的，Vue中很多内容我还没有挖掘到，不过就指令上来说Vue确实做了很多方便使用的操作），所以Vue更加简单，不多说，放上我的笔记：
1. React内使用JSX来写html
2. JSX中，每个标签都必须闭合
3. React中的组件就类似于一个函数，它接收传递过来的props参数（代表从父级传递过来的状态对象，类似于Vue中的props）,然后根据这些状态生成一个html,就像：
```
function DemoComponent(props) {
	return (
		<div>{props.name}</div>
	)
}
```
4. 组件还可以使用类的写法，使用这种方法可以创建有状态(state)的组件,换句话说，创建无状态的组件使用`函数式组件`, 创建有状态的组件使用`类组件`,*类组件*加像下面：
```
class DemoComponent extends React.Component {
	//如果组件有内部状态，可以使用constructor构造函数
	constructor(props) {
		super(props)
		this.state = {
			name: 'Sia'
		}
	}
	render(
		<div>
			{this.state.name}
		</div>
	)
}
```
5. 如果想要改变组件的状态，需要使用`setState`函数， 给个完整的demo
```
	function FuncComponent(props) {
		return (
			<div>{props.title}</div>
		)
	}

	class ClassComponent extends React.Component {
		constructor(props) {
			super(props)
			this.state = {
				text: '我是不是一个类组件?'
			}
		}
		answer = () => {
			this.setState({text: '我是一个类组件'})
		}
		render() {
			return (
				<div onClick={this.answer}>
					{this.state.text}					
				</div>
			)
		}
	}

	const element = (<div>
						<FuncComponent title="Question & Answer" />
						<ClassComponent />
					</div>)
	ReactDOM.render(element, document.getElementById('root'))
```
6. 在组件内部写方法的时候要注意this的绑定问题，确保this是绑定到这个组件的，不会出现this的绑定丢失, 常规的写法是在`constructor`内部进行this的绑定，eg: `this.onhandleChange=this.onhandleChange.bind(this)`,官方推荐的写法是在组件内部，`constructor`的外部这样写：
```
	onhandleChange = () => {

					}
```
还有第三种写法，是在组件的返回函数里面，利用箭头函数：

```
return (
	<button onClick={(e) => this.onhandleChange(e)}></button>
)
```
7. 类似于Vue中的v-if/v-show,在React中也有条件渲染， 不过React中的条件渲染没有特殊的指令，它利用的是在JSX中利用Javascript写if...else..., 以及其他的判断逻辑，另外需要注意的是如果一个组件渲染函数返回的是`null`并不影响这个组件的生命周期钩子函数的执行，也就是说它类似于v-show了
8. React中列表渲染，说白了，个人感觉就是： 给一个数据数组，经过数组函数的处理返回了一组html元素，就像下面：
```
function NumberList(props) {
	const numbers = props.numbers
	const listItem = numbers.map((number) => {
		return <li>{number}</li>
	})
	return (
		<ul>
			{listItem}
		</ul>
	)
}
const numbers = [1, 2, 3, 4, 5]
ReactDOM.render(<NumberList numbers={numbers} />, document.getElemebtById('root'))

```
但如果只是这样写会报警告，说需要key值：key值的作用是给渲染出来的这组元素的每个元素加一个唯一的标志， 这样如果这个元素被移除了或者是新加入的，React立即就可以知道
还有就是key值的指定应该是灵活的，由外部根据使用的上下文指定好key值，eg：
```
function ListItem(props) {
	return (
		<li>{props.value}</li>
	)
}
function NumberList(props) {
	const numbers = props.numbers
	const listItems = numbers.map((number) => {
		<ListItem key={number.toString()} value={number}></ListItem>
	})
	return (
		<ul>
			{listItems}
		</ul>
	)
}
const numbers = [1, 2, 3, 4, 5, 6]
ReactDOM.render(<NumberList numbers={numbers} />)
```
还有就是key值是不能通过props传递到子组件中去的

9. React中没有计算属性，它的所有需要计算的内容都在render()函数中自己维护，也算是有吧,
	相应的，关于组件之间的通信，Vue中兄弟组件之间的通信需要在使用一个Vue实例做个中转，React中更加直接，直接将共享的数据放在了父组件中，然后在渲染函数中将其作为props传入子组件
10. Vue中有插槽，React中没有，但是React中可以直接通过传props的方式将对应的dom传入子组件，然后子组件中就可以使用`props.left`等来进行展示，eg:
```
function SplitPane(props) {
	return (
		<div className="SplitPane">
			<div className="SplitPane-left">
				{props.left}
			</div>
			<div className="SplitPane-right">
				{props.right}
			</div>
		</div>
	)
}
function App() {
	return (
		<SplitPane left={<Contact />} right={<Chat />} />
	)
}
```