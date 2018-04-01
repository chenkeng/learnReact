 React 学习(一) ---- React Element /组件/JSX

　　学习React的时候，你可能听到最多的就是要先学习webpack, babel，要先学会配置然后才能学react 等等，一堆的配置就把我们吓着了，根本就没有心情就学习react了。其实在最开始学习react， 想要了解React 是什么的时候，我们完全不用配置，直接用script标签 引入React就可以了， 不过要注意，这里需要引入两个库：React 和ReactDom。React 用来创建UI, ReactDom 负责把React 创建的UI 渲染到浏览器中. 初学react只需下面这个模版就可以了。
复制代码

<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello World</title>
    <!-- 引入React 和 ReactDOM -->
    <script src="https://cdn.bootcss.com/react/16.1.0/umd/react.development.js"></script>
    <script src="https://cdn.bootcss.com/react-dom/16.1.0/umd/react-dom.development.js
    "></script>
  </head>
  <body>
    <div id="root"></div>
    <script>
      // react 代码写在这里
    </script>
  </body>
</html>

复制代码

　　这里，页面中还增加了一个id 为root 的div, 它的作用是告诉ReactDom 要把UI 渲染到什么地方。注意，我们这里先不使用JSX语法，从而了解一下JSX的由来，或它底层的东西。好了，我们开始学习React 吧. 

　　React Element

　　在学习React的时候，遇到的第一个概念可能就是virtual DOM或React element, 它们其实对应的是浏览器DOM 或 HTML元素。在做JS 开发的时候， 大家都知道，DOM 操作很慢，我们都会尽量避免操作DOM, 那么怎么才能更快地操作DOM呢？React 提出了virtual DOM的概念， 因为这份DOM 不是在浏览器中，而是在内存中，浏览器中的DOM是真实的DOM，它能直接渲染出来，但内存中的DOM 又和浏览器的DOM 一致，所以叫virtual DOM， 同理，浏览器中的DOM是由于一个个HTML element 组成，那么作为对应，virtual DOM 就是由一个个的React element 组成。概念总是抽象的，我们写一下代码了解一下。

　　React element, 是由React 库提供的createElement 函数创建而来，我们创建一个React element,

let h1Elem = React.createElement('h1', {id: 'recipe', 'data-type': 'title'}, 'Hello World');

　　你可以看到 createElement 函数接受三个参数，第一个是type, 我们要创建哪种类型的React Element, 在这里，我们创建了h1 element. 第二个是properties, 这个element 有哪些属性，在这里有两个属性，一个是id, 一个是data-type. 第三个参数是children, 这个元素有哪些子元素。通过分析，我们也可以猜测出h1 大概长什么样子。 <h1 id=’recipe’  data-type=’title’ ></h1>

 　　这时我们打开浏览器，刷新一下页面，什么也没有，我们创建了react element, 到底发生了什么事情呢？这时我们 console.log(h1Elem)  一下

　　我们看到它只是一个Js 对象，拥有props，type等等属性， 和html element 没有任何关系, 但是通过它，我们却准确地描述了 html element 长什么样子，所以它是virtual, 用于指导构造真正的html element.

　　当React element 多了起来，层层嵌套，就成了一个DOM 树，这就是所谓virtual DOM. createElement 方法怎么才能形成层层嵌套关系呢？ 这主要在于这个函数可以接受任意个参数，从第三个参数及其以后的参数，都会当成创建的element 的children 来对待。我们创建一个ul--li 的结构
复制代码

let ulElem = React.createElement(
        'ul', 
        null ,
        React.createElement('li', null, 'JS'),
        React.createElement('li', null, 'React'),
        React.createElement('li', null, 'Redux')
      );

复制代码

　　　通过这个createElement 函数，我们也可以很清楚地知道它代表下面的ul-li

　　当我们用React 创建出虚拟Dom, 怎样才能渲染到html 页面中，形成真实的DOM呢？那就用到了react-dom 库了，它里面有一个render 方法， 可以把虚拟DOM 转化成真实的DOM,  用法如下

 ReactDOM.render(ulElem, document.getElementById('root'));

　　它接受两个参数，一个是要转化的Element, 一个是渲染到什么地方， 这时我们刷新浏览器，可以看到3个li, 和我们用createElement 

创建的元素一一对应。

　　React Component

　　当我们写大量的React element  的时候，你会发现有些代码可以共用，就像我们上面创建的ulElem，可以用到很多地方，这时我们就想把它们封装起来，这就形成了React component, 组件。在js 中代码封装有两种方法，一种是函数，一种是对象，相应的React 也提供了创建组件的两种方法，一种是类式，类式就是对象封装，一种是函数式。

　　类式，则是利用es6 class 语法, 我们所有的组件都继承自React.Component，在render 函数中返回React Element
复制代码

      class Content extends React.Component {

        render() {
          return React.createElement('ul', null ,
            React.createElement('li', null, 'JS'),
            React.createElement('li', null, 'React'),
            React.createElement('li', null, 'Redux')
          );
        }
      }

复制代码

　　函数式，就是一个函数，返回React element
复制代码

      const Content = () => {
        return  React.createElement('ul', null, 
          React.createElement('li', null, 'JS'),
          React.createElement('li', null, 'React'),
          React.createElement('li', null, 'Redux')
        )
      }

复制代码

　　我们声明了组件以后，怎么使用这个组件呢？ 这时要注意，我们的组件名称(Content)，只是相当于createElement 函数中的第一个参数type, 它不是一个React element, 它只是返回一个React element, 我们仍然需要调用React.createElement 来创建一个React element

      // 我们创建的组件名Content, 只是相当于createElement函数中的第一个参数type, 它是相当于h1的type, 而不是一个React Elemnet. 利用这个type，仍需要创建组件。
      let content = React.createElement(Content, null, null);
      
      ReactDOM.render(content, document.getElementById('root'));

　　这时页面中同样显示三个li, 表示成功。

　　总结： 创建组件，只是把一段可以重用的React element 进行封装，从而创建一个自定义的type, 然后再利用该type， 随处都可以创建element 元素，进而达到重用的目的。

　　React 组件数据

　　这时，你会发现我们组件所有的数据都是硬绑定的，能不能把数据和 UI的创建分开，从而使我们的组件更具有通用性，不同的数据渲染出不同的内容？ 这是可以的，当我们利用组件创建react element 的时候，第二个参数是null, 可以再回顾一下代码

let content = React.createElement(Content, null, null);

　　我们可以利用这个参数向组件内传递数据，因为这个参数就是表示这个组件的属性，它的形式也是一个键值对的形式，比如我们把JS, React ,Redux 数据提出来，形成一个数组，

const item = ['JS', 'React', 'Redux'];
// 向组件中传递一个数组数据 item 
let content = React.createElement(Content, {item: item}, null);

　　那么我们组件中怎么获取到这个数据呢？这要分两种情况，类式的组件则是通过this.props获取的，而函数式组件，则是通过传参的形式获取

　　先看类式的组件， 在组件中通过this.props 来获取， 我们可以把this.props 找印出来看一下
复制代码

      class Content extends React.Component {
        render() {
          // 打印props;
          console.log(this.props);  // {item: Array(3), children: null}
          return React.createElement('ul', null ,
            React.createElement('li', null, 'JS'),
            React.createElement('li', null, 'React'),
            React.createElement('li', null, 'Redux')
          );
        }
      }

复制代码

　　可以看到this.props 获取到了我们传递的item 数组，那我们就可以直接使用了数组数据了，这时通过数组的map 渲染li
复制代码

class Content extends React.Component {
  render() {
    return React.createElement('ul', null ,
      // this.props.item 获取到传递过来的数据
      this.props.item.map((item, index) => 
        React.createElement('li', {key:index}, item)
      )
    );
  }
}

复制代码

　　函数式的组件，则是它自动会获取props作为参数，组件中直接使用props.items 获取到数据
复制代码

// 自动获取props作为参数。
const Content = (props) => {
  return  React.createElement('ul', null, 
    props.item.map((item, index) => 
      React.createElement('li', {key:index}, item)
    )
  )
}

复制代码

　　JSX

　　在上面的代码中，我们每创建一个React Element 都要调用一次React.createElement 函数，非常繁琐，并且它想表达式的意思只是一个类html的元素，再来看一下我们创建的h1Elem element 

let h1Elem = React.createElement('h1', {id: 'recipe', 'data-type': 'title'}, 'Hello World');

　　它实际上表达的意思就是 <h1 id=’recipe’  data-type=’title’ ></h1>， 如果我能在代码中直接写h1 就好了。这就是可以了，它就是JSX 语法， 可以直接在js 代码中写类html 的语法。React 把createElement 函数作了进一步的封装，提供了JSX语法。为什么能直接写呢？其实createElement 函数，和html 元素有一一对应的关系。

　　在createElement 函数中，它的第一个参数是type，表示创建什么类型，而在html中，表示什么类型直接用html 标签，<h1></h1>  它就表示h1 类型， 第二个参数表示属性，元素有哪些属性，而在html标签中，有什么属性，就直接写在它的标签中，有多少，写多少, 如 <h1 id='recipe' class='title'></h1>. 第三个参数是children, 在html中表示children更简单，直接写在两个标签内部的内容都是children. 这样一一对应以后，就可以理解JSX 写法的用意了，在心理上写起来就比较舒服了，因为明白了。

　　对于组件来说，它也是一样的，因为组件名称，只是一个type, 仍然需要调用createElement 函数来 创建React Element 元素， 只要使用createElement 的地方，我们都可以使用类html 语法，如Content组件，<Content></Content>  就表示创建了一个element了。它的属性和children和上面的h1 用法一致。对于Content 组件，如果没有 chilren 属性，可以直接写单标签<Content />.   现在用JSX的语法来书写Content 组件。
复制代码

class Content extends React.Component {
  render() {
    return (
      <ul>
        {
          this.props.item.map((item, index) => 
            <li key={index}>{item}</li>
          )
        }
      </ul>  
    )
  }
}

// 向组件中传递一个数组数据 item 
let content = <Content item ={item}></Content>

复制代码

　　无论是在组件属性，还是元素属性中，我们都使用了{}， 如key={index}. 在jsx 中，｛｝里面的所有东西都当作js表达式进行解析， React 会把里面的内容进行求值计算。只要写表达式，我们都要用{} 括起来。 在JSX 中还有另外一种属性，就是字符串，它可以直接写如name="sam"，除此之外，所有的属性都要用{} 括起来。比如我们向组件中传递一个数字1，我们就要写 num = {1}， 传递一个布尔值，就要写 bool={false},  我们传递一个字符串，一个布尔值， 一个数字体验一下。
复制代码

// 组件添加了三个p, 有来接受数据
class Content extends React.Component {
  render() {
    return (
      <section>
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
}
// 向组件中另外传递 字符串name ，布尔值bool， 一个数字num。
let content = <Content item ={item} name="sam" bool={false} num={1}></Content>

复制代码

　　这时刷新浏览器，可以看到报错了，首先，JSX语法，浏览器是不支持的，我们要把它转换成JS， 所以引入babel库，在head 标签中引入

    <script src="https://unpkg.com/babel-standalone@6.15.0/babel.min.js"></script>

　　其次我们要告诉babel， 我们的代码需要转译，所以在写react 代码的script 标签上添加一个type 属性

<script type="text/babel">

　　这时再刷新浏览器，没有问题了，我们也获得了属性，并且它的类型也是对的，num 是Number, 字符串是String。

　　但是这里有一个问题，就是如果我们要传递很多属性，这么 一个一个列出来，非常麻烦，怎么办呢？ 这时可以使用对象，但如果用对象进行传值，又不符合 属性名=属性值的写法，这时要用到es6中的扩展运算符..., React 对es6 中的扩展运算符（…）进行扩展，它能运用到对象上，对对象进行分割。｛…obj｝； var obj = {name:”sam”, num: 1}  , …obj  => name=”sam” , num= 1, 注意，...obj 是一个表达式，仍需要把它用{} 括起来
复制代码

<script type="text/babel">

  // 组件添加了三个p, 有来接受数据
  class Content extends React.Component {
    render() {
      return (
        <section>
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
  }

  // 要把传递的属性写到一个对象中，
  const obj = {
    item : ['JS', 'React', 'Redux'],
    name: 'sam', 
    bool: false,
    num: 1
  }

  // 把对象进行分割
  let content = <Content {...obj}></Content>
  ReactDOM.render(content, document.getElementById('root'));
</script>

复制代码

　　在Content 组件中，你可能发现外层包了一个section, 这是因为所有的组件都返回一个单一的根节点，主要还是createElemet 函数第一个属性type 是一个值， 不能接受多个值。

你可能还发现所有的组件名是大写，还是因为createElement 的第一个参数type, type 有两种类型，一种是html原有的类型如h1, 一种是自定义的类型，就是component, 当我们传入时，React  无法区分这两种类型，所以它用大小写进行区分。如果是小写，它就以为是html原有的类型，如果是大写，就是自定义类型。如果我们组件使用了小写，React 按html原有的类型进行渲染，但是它又找不到这个类型，所以什么都不会渲染，组件名必须大写。

 　　在JSX语法中， 我们还要注意以下两点：

　　html 属性关键字： 如 我们可以给html元素添加属性<h1 class="book">, 但class 在js 中是关键字， 所以class 要变成 className. 由于JSX 最终会转换成原生js 函数，所以js中的一些关键字在JSX中是不能用的，如class, for. 但在JSX 的类html 模版中，html 元素属性中又有class 和for, 这就冲突了。React 对html 元素中有冲突的属性进行了重新命名，for 变成了htmlFor, class 变成了className.  所有变量的命名都要用 驼峰命名法。如label 元素 <label htmlFor=”input” className=”text”></label>

　　样式：在JSX 中，给一个html 元素添加样式有两种方法，一种是上面提到的className,  它的取值是一个样式名字符串，一种是内联样式style, 它的取值必须是一个对象。 <div className=”col-md-3” style ={style}></div> ，style 是组件内部定义的一个对象变量。 因为render 是一个函数，里面可以声明变量
复制代码

  class Content extends React.Component {
    render() {
    // 定义样式变量
　　var inlineStyle = {
      color: 'green' ,
　　　// css3 一些属性有些需要带浏览器厂商前缀，这时厂商前缀首字母必须大写, 所有的样式都是字符串
　　　WebkitFilter: blur('5px')
　　} 

    return (
      <section>
        <p style={inlineStyle}>name 的值是{this.props.name}，类型是 {typeof this.props.name}</p>
      </section>
    )
    }
  }