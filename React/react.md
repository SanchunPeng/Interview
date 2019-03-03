1、当你调用setState的时候，发生了什么事？
------------
  在调用setState以后，<br/>
1）react会将传入的参数对象跟当前的state合并，触发调和过程。<br/>
2）调和以后，react会高效的根据新的状态构建react元素树。<br/>
3）生成react元素树以后，通过diff算法可以得到新树和老树的节点差异。<br/>
4）根据这些差异，可以精确的实现按需更新，即可最小化 UI 的占用空间。

2、在 React 当中 Element 和 Component 有何区别？
------------
react Element是所见内容的数据结构，是对UI的描述，是通过jsx构建的声明式代码片段。<br/>
React Component 是接收参数输入返回某个 React Element 的函数或者类。

3、什么时候在功能组件（functional Component）上使用类组件（class component）？
------------

  如果您的组件具有状态（state）或生命周期方法，请使用class组件，否则就使用功能组件

4、什么是 React 的 refs ，为什么它们很重要？
------------
  refs就像一个逃生舱口，允许您直接访问DOM元素或组件实例。为了使用它们，您可以向组件添加一个ref属性，该属性的值是一个回调函数，它将
  接收底层的DOM元素或组件的已挂接实例，作为其第一个参数<br/>
  ```
      class UnControlledForm extends Component{
        handleSubmit = () => {
          console.log('Input Value:',this.input.value )
        }
        render() {
          return(
            <form onSubmit = {this.handleSubmit}>
              <input 
                type = 'text'
                ref = {(input) => this.input = input } />
              <button type = 'submit'>Submit</button>
             </form>
          )
        }
      }
  ```
  
  以上注意到我们的输入字段有一个ref属性，其值是一个函数。该函数接收我们然后放在实例上的实际的DOM元素，以便在handleSubmit函数内部访问他。<br/>
  经常误解的是，您需要使用类组件才能使用ref，但ref也可以通过利用JavaScript中的闭包与功能组件一起使用。<br/>
  
  ```
      function CustomForm({handleSubmit}) {
        let inputElement
        return (
          <form onSubmit = {() => handleSubmit(inputElement.value)} />
            <input 
              type = 'text'
              ref = {(input) => inputElement = input } />
             <button type='submit'>Submit<button/>
           </form>
        )
      }
  ```
  
 4、React中的keys是什么？为什么它们很重要？
------------
   ```
      return (
         <ul>
            {this.state.todoItems.map(({task, uid} => {
               return <li key = {uid}>{task}</li>
            }))
         </ul>
      )
   ```
  keys使react用于追踪哪些列表中元素被修改、被添加或者被移除的辅助标识<br/>
  在开发过程中，我们需要保证某个元素的key在其同级元素中具有唯一性<br/>
  1）React Diff算法中react会借助元素的key值来判断该元素是新近创建的还是被移动而来的元素，从而减少不必要的元素重渲染<br/>
  2）React还需要借助key值来判断元素与本地状态的关联关系，因此我们绝不忽视转换函数中key的重要性<br/>
  
 5、react中的回调渲染模式？
------------
   ```
      <Twitter username='tylermcginnis33'>
         {(user) => user === null
            ? <Loading />
            : <Badge info = {user} />}
      </Twitter>
   ```
   ```
      import React, { Component, PropTypes } from 'react'
      import fetchUser from 'twitter'
      //fetchUser接收用户名返回promise
      //当得到用户的数据的时候，返回resolve状态
      
      class Twitter extends Component{
         //在这里写下你的代码
         state = {
            user: null,
         }
         static propTypes = {
            username:PropTypes.string.isRequired,
         }
         componentDidMount() {
            fetchUser(this.props.username)
               .then((user) => this.setState({user})
         }
         render() {
            return this.props.children(this.state.user)
         }
      }
   ```
   渲染回调模式，在这种模式中，一个组件接收一个函数作为它的child。注意上面包含在标签内的内容。Twitter组件的child是一个函数，而不是一个组件。这意味着在实现的时候，我们需要将props.children作为一个函数来处理。<br/>
   这种模式的好处是我们已经将我们的父组件和子组件分离了。父组件可以直接访问子组件的内部状态而不需要再通过props传递。这样父组件能够更为方便地控制子组件展示的UI界面。<br/>
   为了演示这一点，我们假设在另一个文件中，我们要渲染一个 Profile 而不是一个 Badge, ，因为我们使用渲染回调模式，所以我们可以轻松地交换 UI ，而不用改变我们对父（Twitter）组件的实现。
   
      <Twitter username='tylermcginnis33'>
         {(user) => user === null
            ? <Loading />
            : <Profile  info = {user} />}
      </Twitter>
      
## 6、在生命周期中的哪一步应该发起AJAX请求？

在componentDidMount函数中发起AJAX请求
1）保证请求仅在组件挂载完毕后才会要求响应 <br/>
2）调和算法fiber会通过开始或停止渲染的方式优化应用性能，其会影响到componentWillMount的触发次数。React可能会多次频繁调用ComponentWillMount.
  
 5、React中的事件处理逻辑
------------

&nbsp;&nbsp;为了解决跨浏览器兼容性问题，React会将浏览器原生事件封装成合成事件传入设置的事件处理器中。这里的合成事件提供了与原生事件相同的接口，不过他们屏蔽了底层浏览器的细节差异，保证了行为的一致性。<br/>
&nbsp;&nbsp;另外有意思的是，React并没有直接将事件附着在子元素上，而是以单一事件监听器的方式将所有的时间发送到顶层进行处理。这样React在更新DOM的时候就不需要考虑如何去处理附着在DOM上的事件监听器，最终达到优化性能的目的。
