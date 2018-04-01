 React 学习(六) ---- 父子组件之间的通信

　　当有多个组件需要共享状态的时候，这就需要把状态放到这些组件共有的父组件中，相应地，这些组件就变成了子组件，从而涉及到父子组件之间的通信。父组件通过props 给子组件传递数据，子组件则是通过调用父组件传给它的函数给父组件传递数据。

　　很简单的一个例子，我们在输入框中输入温度，输入框下面显示冷和热。这里就有两个组件，输入框和它下面的显示内容，且它们共享一个状态，就是温度。所以我们要把温度这个状态放到这两个组件的父组件中。这里就有三个组件，一个输入框TemperatureInput,  一个是显示内容TemperatureShow,  父组件TemperatureContainer. 由于 TemperatuerInput  和TemperatureShow 是无状态的，可以用函数式声明，接受父组件传来的props. TempeartureContainer 是有状态的，要用class 声明。

　　TemperatureShow 组件非常简单，就是条件渲染。根据父组件传递过来的温度，渲染出不同的内容。
复制代码

  　　// temperatureShow
      function TemperatureShow(props) {
        if(props.temperature >38) {
          return <p>天气热</p>
        }else if(props.temperature<=38 && props.temperature >=0){
          return <p>天气正合适</p>
        }else {
          return <p>天气冷</p>
        }
      }

复制代码

　　TemperatureInput 则包含一个input输入框，在react, 输入框主要通过 value和 onChange 事件进行控制，状态是自保持的。但是这里我们把状态放到父组件TempeartureContainer 中，输入框是无状态的，所以它的value值也只能从父组件中获取，onChange 事件也只把用户输入的值传递给父组件，这里就是父子之间相互通信。 这里先简单写一个它的表现，onChange 稍后再说。
复制代码

 //temperatureInput 
      function TemperatureInput(props) {
        return (
          <p>
            <label htmlFor="tempInput">请输入天气温度</label>
            <input type="text" name="tempInput" value={props.temperature} onChange={} />
          </p>
        )
      }

复制代码

　　还剩下TempeartureContainer 父组件，它是一个容器，把所有的组件包起来，当然，它还要定义状态temperature,传递给子组件。
复制代码

class TemperatureContainer extends React.Component {
        constructor(props) {
          super(props);
          this.state = {
            temperature: ''
          };
        }

        render() {
          let temperature = this.state.temperature
          return (
            <div>
                <TemperatureInput temperature={temperature} />
                <TemperatureShow  temperature={parseFloat(temperature) } />
            </div>
          ) 
      }
    }

复制代码

　　现在通过ReactDOM.render 把TempeartureContainer 渲染到页面上。

ReactDOM.render(
        <TemperatureContainer />,
        document.getElementById('root')
);

　　最后我们要处理一下input 输入框中的输入问题，当我们进行输入的时候，会触发onChange事件，这时要改变temperature 的值，但是input自身是无法做到的，因为temperature存在于 父组件中，只能在父组件中改变。父组件中也很容易做到，声明一个函数，调用setState 方法，在setState中我们把新值赋值给temperature, 但是这个新值，也就是用户输入的值，有点问题，它存在于子组件input 中， 其实也很好解决，函数可以进行传参，函数通过传参可以获得它想要的值。所以在父组件中声明函数，在子组件调用，子组件通过函数传参，把获得的新值传递给这个函数，那么父组件 也就获得了子组件的值，当然父组件中函数要有一个参数用于接收值。父组件中声明的函数，子组件怎么调用? 这更简单了，父组件可以通过props 传递给子组件。

　　在父组件中声明一个函数，它要有一个参数，同时通过props 传递给子组件
复制代码

class TemperatureContainer extends React.Component {
        constructor(props) {
          super(props);
          this.state = {
            temperature: ''
          };
          this.handleTemp = this.handleTemp.bind(this); // 不要忘记 this 绑定
        }
        // 父组件中的函数,接受一个参数
        handleTemp(temperature) {
            this.setState({
                temperature:temperature
            })
        }
        render() {
          let temperature = this.state.temperature
          return (
            <div>
                {/* 传递给子组件*/}
                <TemperatureInput temperature={temperature} onTemperateChange={this.handleTemp}/>
                <TemperatureShow  temperature={parseFloat(temperature) } />
            </div>
          ) 
      }
    }

复制代码

　　子组件调用父组个传递过来的参数，并进行传值
复制代码

function TemperatureInput(props) {
        function handleTemp(e) {
            // 接受父组件传递过来的函数，调用并传值
            props.onTemperateChange(e.target.value)
        }
        return (
          <p>
            <label htmlFor="tempInput">请输入天气温度</label>
             {/* onChange 事件调用函数*/}
            <input type="text" name="tempInput" value={props.temperature} onChange={handleTemp}/>
          </p>
        )
      }