 React 学习(二) ---- props验证与默认属性

　　在上一节中, 我们提到了props, 组件之间数据的传递使用props. 我们调用组件时可以设置props, 组件内部通过props获取. 为了props 使用更加友好, React 提供了简单的验证机制，就是我们可以标注我们的组件内部想要使用什么样的数据, 如果使用组件时传递过来的数据不符合要求, React就会抛出warnig, 有利于我们排错。

　　看一下我们的content 组件, 它内部用到了四个属性, name, item, num, bool, 并且类型也不一致. 我们肯定希望传递过来参数和组件内的要求是一一对应的，从而避免组件报错。这时我们可以在组件内作出一定的要求, 就是写一些验证条件. React的验证也很简单，给组件提供一个propTypes属性，它是一个对象，键就是组件内的属性，值就是验证条件。对于Content 组件来说，name 是一个字符串，item 是一个数据，num 是一个数字，bool是一个布尔值，所以我们就可以这样写
复制代码

Content.propTypes = {
  item: PropTypes.array,
  name: PropTypes.string,
  bool: PropTypes.bool,
  num: PropTypes.number
}

复制代码

　　 这时用到了PropTypes库，array, string, bool, number 就是PropTypes 库提供的简单验证条件，只要我们写上number, React 就会帮我们验证它是不是number. 我们用script 引入PropTypes 库

<script src="https://unpkg.com/prop-types@15.6/prop-types.js"></script>

　　有时有些属性非常重要，我们要求必传，这时要提供一个isRequired进行表示，比如item属性是必传的
复制代码

Content.propTypes = {
  item: PropTypes.array.isRequired,
  name: PropTypes.string,
  bool: PropTypes.bool,
  num: PropTypes.number
}

复制代码

　　如果简单的验证不能满足我们的要求时，React 也提供了自定义验证。自定义验证，它是一个函数，有两个参数，props, 和propName,  它返回null 或 Erorr,  如果

通过验证，则返回null ,如果验证失败， 则返回一个错误。 props 和propName, 是React 自动注入的，我们不用管。我们对name提供一个自定义验证。
复制代码

Content.propTypes = {
  item: PropTypes.array.isRequired,
  bool: PropTypes.bool,
  num: PropTypes.number,
  name: (props, propName) => {  // 自定义验证
    console.log(props, propName);
    if (typeof props[propName] !== 'string') {
      return new Error('必须传递一个字符串');
    } else if (props[propName].length >= 5) {
      return new Error('字符串长度必须小于5')
    } else {
      return null;
    }
  }
}

复制代码

　　我打印了一下两个参数props 和propName, 可以看到props 就是我们整个组件接受到的所有属性，propName 就是当前验证的属性name.  这里的验证规则就是字符串小于5. 

　　除了props验证之外，React 还提供了默认属性defaultProps, 给我们组件内部的属性提供默认值。它的语法与验证一样，也是一个对象，不过它的值是默认值，而不验证规则。

Content.defaultProps = {
  name: 'sam'
}

　　以上props验证和defaulProps 语法，对类式组件和函数式组件都适用。对于函数式组件来说，defaultProps 还有一种更简单的方式，用ES6 的解构赋值。 
复制代码

const Content = ({item=[], name='', bool=false, num = 0}) => {
  return  
      (<section>
          <ul>
            {
              this.props.item.map((item, index) => 
                <li key={index}>{item}</li>
              )
            }
          </ul>  
          <p>name 的值是{this.props.name}，类型是 {typeof this.props.name}</p>
          <p>bool 的值是{this.props.bool}，类型是 {typeof this.props.bool}</p>
          <p>num 的值是{this.props.num}，类型是 {typeof this.props.num}</p>
        </section>
      )
}