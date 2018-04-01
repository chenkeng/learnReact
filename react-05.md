 React 学习(五) ---- 条件和列表渲染

　　条件渲染

　　React中的条件渲染和我们平常写的js 代码一样，都是用的if else， 只不过在if else 中它的返回值是jsx, 根据不同的条件渲染不同的UI. 先写两个组件
复制代码

 　　  //登录的用户显示欢迎
      function UserWelcome(){
        return <h1>Welcome to user</h1>
      }
      //为登录的用户显示请登录
      function GusterWelcome(){
        return <h1>please sign up</h1>
      }

复制代码

　　再写一个组件进行条件判断，从而显示不同的组件
复制代码

function Greeting(props){
        const isLogged  = props.isLogged;

        // 根据条件进行判断，渲染不同的组件。
        if(isLogged){
          return <UserWelcome />  // 返回jsx
        }else {
          return <GusterWelcome/>
        }
      }

复制代码

　　渲染Greeting 组件，给他一个isLogged 参数，控制显示和隐藏， isLogged参数是true  or false， 可以看到页面显示不同的内容

 ReactDOM.render(
    <Greeting isLogged={true} />,
    document.getElementById('root')
 );

　　如果是简单的条件渲染，也可以在jsx中使用条件表达式，直接用{} 把表达式包起来，因为在jsx 中，{} 中的内容都可以进行计算。如果只有条件成立的情况下执行的内容，可以使用 && 运算符， 如果条件成立和条件不成立都要执行，那就要用 三目运算符。

　　比如我们只想在isLogged 为true的情况下渲染组件，false 则不显示。那么我们就要用&& 。Greeting 组件修改如下
复制代码

function Greeting(props){
        const isLogged  = props.isLogged;
        return (
          <div>
            {isLogged && <UserWelcome />}
          </div>
        )
      }

复制代码

　　注意，这时要用div 标签把条件表达式包括起来，否则会报错。再用三元运算符对于Greeting 进行修改
复制代码

function Greeting(props){
        const isLogged  = props.isLogged;
        return (
          <div>
            {isLogged? <UserWelcome />: <GusterWelcome />}
          </div>
        )
      }

复制代码

 　　列表渲染

　　React 中的列表渲染，用的是数组的map 方法，将数组中的每一项映射成一个jsx, 然后进行返回。数组就是我们要渲染到页面中的数据, 那么经过map 方法之后返回的新数组，每一项都成了包含渲染数据的jsx， 我们再对新数组进行渲染就可以把数据渲染到页面中.
复制代码

    // 要渲染到页面中的数据
    const numbers = [1, 2, 3, 4, 5]; 

    // listItems 经过map方法返回的新数组，它的每一项都是jsx
    const listItems = numbers.map((number) =>
      <li>{number}</li>
    );
    ReactDOM.render(
      <ul>{listItems}</ul>,  // 对新数组进行渲染，就可以在页面中显示数据
      document.getElementById('root')
    );

复制代码

　　我们这个list 组件封装为一个组件
复制代码

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

复制代码

　　列表组件的调用

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);

 　　在进行列表渲染的时候，一定要给它一个key，表示每一个列表项都是唯一的，这有利于是react 优化。Key是一个字符串，给li增加一个key

<li key={number.toString()}>
    {number}
  </li>

　　由于每一项的key值是独一无二的，所以最好的key 就是每一项上的id, 那么把我们的数组改成每一项都是对象，同时对listItems 进行修改
复制代码

const numbers = [
      {
        id:"first",
        num: 1
      },
      {
        id: "second",
        num: 2
      }
    ]

复制代码

 const listItems = numbers.map((number) =>
    <li key={number.id}>{number.num}</li>
  );

　　但有的时候每一个li内容都很多，我们需要把 li 单独封装成一个组件

function ListItem(props) {
  const value = props.value;
  return<li>{value}</li>
}

　　那这时key怎么处理，key 永远跟随map方法中的函数，只有map 才能产生循环， 只有在循环内确保唯一性。
复制代码

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    
    <ListItem key={number.id} value={number.num} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

复制代码

 　　表单操作

　　表单是特殊的html 元素，因为它有自己的状态，最典型的就是输入框，用户输入，输入框中直接显示用户输入的内容，它们状态都是自保持的， 这与react 所有状态是放到组件中且只能通过setState改变状态是不相符的。这时react 通过value 和onchange 事件组合解决。表单元素的value 属性从 state 中获取，用户输入时触发onChange 事件，onChange 事件中通过setState() 来动态改变状态，使输入框中的内容实时显示用户输入的内容，input, textarea, select都是这么操作的，这里要注意的是 value 和onChange 事件要写到select 标签中， textarea 也是一样。
复制代码

class FormSelection extends React.Component {
        constructor(props){
          super(props);
          this.state = {value: 'lime'}   // 设置初始状态，就是下拉列表默认选择的一项
        }

        handleChange = (e) => {
          this.setState({value: e.target.value})  // 改变状态
        }

        render(){
          return (
            <form>
              <label>
                Pick your favorite La Croix flavor:
                <select value={this.state.value} onChange={this.handleChange}>  // value 和onChange 事件写到select标签中
                  <option value="grapefruit">Grapefruit</option>
                  <option value="lime">Lime</option>
                  <option value="coconut">Coconut</option>
                  <option value="mango">Mango</option>
                </select>
              </label>
            </form>
          )
        }
      }

复制代码

　　对于一个组件中有多个input，怎样设置状态? 无论是哪一个input， 只要是一输入，它就会调用onChange 事件，setState 方法，setState 方法第一个参数，就是要设置的属性，第二个参数就是得到的用记输入的值，它不会变化，都是event.target.value.  所以这里的核心就是怎么处理要设置的属性，就是我们怎么确定是哪一个input. 这要给每一个input设置一个标识, 可以用input 的name 属性，并且它的name 属性等于它的状态， 这时用户输入的时候，我们能获取到是哪个input（event.target.name），且name 值正好等于它的状态，我们调用setState() 的时候，直接把获取到的用户的值(event.target.value)  赋值给name，就可以改变input 的状态， 和用户的输入保持同步。

　
复制代码

   class FormSelection extends React.Component {
        constructor(props){
          super(props);
          this.state = {
            isGoing: true,
            numberOfGuests:2
          }
        }

        handleInputChange = (event) => {
          const target = event.target;
          // 获取用户输入的值
          const value = target.type === 'checkbox'? target.checked: target.value;  
          // 通过name 获取到用于点击的输入框，由于name的取值是状态值，同时获取到input 当前状态
          const name = target.name;
          // 用户输入的值赋值给 name， 同时改变了input的状态
          this.setState({[name]: value})
        }

        render(){
          // 对于有两个input，给每一个input name值，唯一标识，且取值为状态值，同时保存当前状态
          return (
            <form>
              <label>
                Is going: <input name='isGoing' type="checkbox" checked= {this.state.isGoing}
                  onChange= {this.handleInputChange} />  
              </label>
              <br />
              <label>
                Number of Guests: 
                <input name='numberOfGuests' type='number' value={this.state.numberOfGuests}
                  onChange={this.handleInputChange} />
              </label>
            </form>
          )
        }
      }
      
      ReactDOM.render(
        <FormSelection />,
        document.getElementById('root')
      );