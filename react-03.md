 React 学习(三) ---- state 和 setState

　　在上两节中，我们讲述了props, 组件使用props进行渲染，但是这是一次性的， props渲染完成之后就不做任何事情了，但是现实中却不是这样的，当我们点击购物车上的加减按钮时，数量会自动加1或减1，还有在就是倒计时效果，这就用了组件内部的状态， 如果一个组件有内部状态，那只能用类式组件，因为类中拥有构造函数，构造函数表示就是一个对象实例上的属性，对象实例上的属性都是特有的，所以也可以称之为对象实例的状态。

　　在组件的构造函数中声明组件的状态也是很简单，直接写this.state = {}，我们在这个对象中添加组件中使用到的状态。如我们写一个加减组件，它需要一个状态保存数字，我们可以写 this.state= {count: 0}，然后我们在render函数中就可以this.state.count获取组件中的状态进行渲染。现在我们写一个counter 组件。
复制代码

class Counter extends React.Component {

  constructor(props) {
    super(props);
    this.state = {
      count: 0
    }
  }

  render() {
    const buttonStyle = {
        margin: '10px'
    };
    return (
      <div>
        <button style={buttonStyle}>+</button>
        <button style={buttonStyle}>-</button>
        <span>count: {this.state.count}</span>
      </div>
    );
  }
}

复制代码

　　你可以注意到，调用构造函数时, 多了super(props) 语句，这是es6 的语法规定, 子类中没有this, 只能调用super生成子类的 this，如果在调用super 之前使用this, 就会报错。 这个地方其实是不会变化的，可以看成一个模式。每次给组件添加状态的时候，我们就按照这个模式书写就可以了。先写

constructor(props) {
    super(props);
}

　　要添加什么状态，直接在构造函数里面super下面写this.state = …. 就可以了。
复制代码

  constructor(props) {
    super(props);
    this.state = {
      count: 0
    }
  }

复制代码

　　页面效果如下，组件中获取到了state中定义的状态。

　　我们怎么才能更改状态呢，当点击加号的时候，count 加1， 点击减号, count 减1？ 首先要给加减button 添加click事件，然后再在事件中进行加减1的操作，状态的更改

　　React 中给元素添加事件，就像我们给DOM元素添加行内事件一样简单，直接在元素身上写事件属性就可以了。但这里也有两点不同，事件名要用驼峰命名法，如click事件要写成onClick，事件处理函数是一个函数，用{} 括起来， <button onClick={handleClick}>点击</button>。那么这又引出了另外一个问题，handleClick 函数写在什么地方？ 这里用到了ES6中类的语法，我们在一个类中写函数，该函数会自动绑定到类的原型上，对象实例通过 this 就能获取到。相对应地，在组件类中，我们直接写事件处理函数，然后在render函数中通过this 获取。现在我们给button 添加click 事件
复制代码

class Counter extends React.Component {

  constructor(props) {
    super(props);
    this.state = {
      count: 0
    }
  }
    
    // 加号事件处理函数
    handleClickIncrement() {
    　　console.log('加1')
  　}
    
  // 减号事件处理函数
  handleClickDecrement() {
    console.log('减1')
  }
  render() {
        const buttonStyle = {
            margin: '10px'
        };
    return (
      <div>
        {/*button 添加事件处理函数*/}
        <button style={buttonStyle} onClick={this.handleClickIncrement}>+</button>
        <button style={buttonStyle} onClick={this.handleClickDecrement}>-</button>
        <span>count: {this.state.count}</span>
      </div>
    );
  }
}

复制代码

　　现在点击加减按钮，控制台上可以看到加1或减1出现，表明我们绑定事件成功。现在就要改变状态了，对于状态的改变，React不允许直接更改状态, 或者说，我们不能给状态（如: count）进行赋值操作,  为此react 提供了一个 setState函数，必须调用它来更改状态。它接受一个对象或一个函数作为参数，当接受一个对象时，对象的键就是要改变的状态，对象的值就是状态改变之后的值。当接受一个函数时，react 自动注入两个参数，一个是prevState, 一个props，然后返回一个对象，返回的对象和它接受的对象是一致的，键是要改变的状态，值就是状态更改后的值。对于prevState, 它指的就是组件的上一次状态，构造函数中this.state对象。Props，很好理解，就是这个组件接受的属性。我们在handleClickIncrement 和handleClickDecrement 事件处理函数中调用this.setState方法来更改状态。
复制代码

    // 加号事件处理函数
    handleClickIncrement() {
    this.setState({
            count: this.state.count + 1
        })
  }
    
    // 减号事件处理函数
  handleClickDecrement() {
    this.setState({
            count: this.state.count - 1
        })
  }

复制代码

　　这时点击加或减，控制台竟然报错了，

　　我们只使用了this.setState, 难道 this 是undefined, 我们在handleClickIncrement 和handleClickDecrement 事件处理函数中console.log(this), 发现它确定是undefined
复制代码

// 加号事件处理函数
handleClickIncrement() {
    console.log(this);
    this.setState({
            count: this.state.count + 1
        })
  }

复制代码

　　也就是this 并没有指向React 组件实例，看来我们要修正this的指向问题，使它指向我们这个组件实例。改变this指向有以下几种方法:

　　一种是ES5提供的bind()方法，它的第一个参数就是指定函数中this的，且它返回 一个函数，可以知道，返回的这个函数中this已经写死了，在程序运行的时候也不会变化了。我们在什么位置上使用bind方法？ 在构造函数中，因为构造函数中的this, 就是具体创建的对象实例，在构造函数中把this 传递给bind作为第一个参数，bind 返回的参数中的this 就固定成了对象实例。构造函数中修改如下，现在点击加减没有问题了。
复制代码

constructor(props) {
    super(props);
    this.state = {
        count: 0
    }

    // bind方法绑定this
    this.handleClickIncrement = this.handleClickIncrement.bind(this); 
    this.handleClickDecrement = this.handleClickDecrement.bind(this);
}

复制代码

　　另一种是箭头函数，因为箭头函数里面的this继承于包围它的函数，那么我们直接把click 事件处理函数写成箭头函数，里面的this 就指向我们这个组件。
复制代码

// 事件处理函数赋值一个箭头函数
handleClickIncrement = () => {
    this.setState({
        count: this.state.count + 1
    })
}

handleClickDecrement = () => {
    this.setState({
        count: this.state.count - 1
    })
}

复制代码

　　注意这种写法还没有被浏览器支持，React 库是默认支持的，所以在这里是没有问题的。如果你以后使用wepback等构建工具时，要自己进行配置。

　　其实箭头函数还有一种写法，就是我们在绑定事件处理函数的时候，不直接写this. 而是写一个箭头函数，

 <button style={buttonStyle} onClick={() => this.handleClickIncrement()}>+</button>
 <button style={buttonStyle} onClick={() => this.handleClickDecrement()}>-</button>

　　这种方法不太常见，因为写起来比较麻烦，但是它有一个好处，因它是一个函数，我们可能把实例上的属性传递给事件处理函数。

　　刚才提到过setState还可以接受一个函数，React 为它注入prevState 参数和props 参数，我们这里，只用到prevState 参数。把加号事件处理函数用函数写一下。
复制代码

    // 加号事件处理函数
    handleClickIncrement() {
    this.setState(prevState => {
            console.log(prevState);
            return {
                count: prevState.count + 1
            }
        })
  }

复制代码

　　我顺便打印了一下prevState, 可以看到，页面上显示6，而prevState是5， 它就是更改之前的状态，我们只有获取到更改之前的状态，才能修改它，进而形成新的状态。

