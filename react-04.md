 React 学习(四) ---- 生命周期函数

　　现在我们能修改状态，页面可以进行交互了，但是还有一种状态改变没有解决，那就是倒计时效果，时间一直在变化，组件状态也一直在改变，但我们什么都没有做，如果要实现这样的效果，需要怎么处理？ 我们都知道，改变状态用的是setState,  上次讲的加减操作是在把它写到事件处理函数中来改变状态的，但现在没有什么事件供我们调用，因为我们没有做任何操作，它却一直在变化，现在要做的就是找一个机会或入口，来写setState 函数， 这个机会就是组件的生命周期函数。

　　生命周期也是来源于对我们对现实生活的思考, 对于一个有生命的事物来说，它都会经历从生到死的过程，在这个过程中又分不同的阶段，每一个阶段都有不同的事情要做。就拿我们自己来说，都会经历少年，中年，老年三个阶段，少年时，我们的重点，就是上学， 中年的重点是工作，老年可能就是帮助我们一下代了。对于组件来说，它是有状态的，也就是有生命的，就会分为不同的阶段，具体分为哪些阶段，React 已经给我们定义好了，就是提供了一系列的函数，每一个阶段(函数)都有不同的重点，要做什么事性。这就给我们提供了机会，我们可以做自己的事。 这些函数就叫生命周期函数， 生命周期函数是由React 提供的， 它就负责管理，到了哪个阶段，React 会自动调用这些函数， 我们要做的就是在这些函数中，定义要做的事性。这就给我们提供了机会，来改变状态。

　　组件的生命周期，它会经历三个阶段， 装载阶段，更新阶段，卸载阶段：

　　　　装载阶段： 就是组件初次渲染到页面上的阶段。首次加载页面时，页面是空白的，然后经过几秒时间，页面中显示内容。这一过程称之为装载。

　　　　更新阶段：用户在页面上进行交互，组件重新渲染的过程。比如用户点击加号， 页面中的数字由1变成2.

　　　　卸载阶段： 组件从页面中删除。点击按钮显示一个新的组件，原来的组件就删除了。

　　先来看一下挂载阶段，它又具体分为四个阶段，进入构造函数，将要挂载，进行渲染，挂载完成。

　　进入构造函数：就是每个组件中的构造函数。它的主要作用就是初始化状态，当然它还有一个props参数，它可以使用组件传递过来的props，因此也可以用props进行初始化。

　　将要挂载：React 提供了compontentWillMount 函数，不过一般不会在这时做什么。

　　进行渲染：就是我们每一个组件中render 函数，它是至关重要的，因为我们写一个组件的目的，就是要渲染到页面上一些东西，它只有返回一个虚拟DOM， ReactDOM 才能根据它构建出真实的DOM渲染到页面上。它会根据props 和state 渲染出react element, 指导react渲染出真正的dom. 但总存在特殊的情况，组件不需要渲染什么东西，如验证用户有没有登录的组件，它确实不需要返什么，我们只是根据它进行跳转到不同的页面，这时直接在render 函数中返回null.

　　挂载完成：React 提供了componentDidMounti函数： 当真实的DOM 渲染到页面上会触发它。 需要注意的是render 函数在调用完成后，componentDidMount 并不会立刻调用，而是在当所有的render函数都调用完华之后，组件的componentDidMount 才会调用。 写一下代码会看得比较清楚，这也是我第一次明白地认识了react的挂载生命周期。

　　在讲state时，我们写了一个Counter，现在再写一个组件CounterWrapper, 它包含Counter 组件，因为此时CounterWrapper 是包含Counter组件的，我们也可以称CounterWrapper 为父组件，Counter 为子组件，为了更能清楚地明白生命周期函数，我们父组件多包含几个Counter. 为了对每一个Counter 进行区分，我们给它们传一个caption 和initValue 参数。 在父组件CounterWrapper 中 由于没有state, 我只写了render函数和ComponentDidMount 函数，而在子组件Counter中，它有state， 有完整的生命周期函数，依次写了四个阶段，constructor, componentWillMount, render, componentDidMount, 当然它们只是console.log一下信息，看一下执行的过程。
复制代码

<script type="text/babel">

// 子组件counter
class Counter extends React.Component {

    constructor(props) {
        super(props);
        console.log(`进入子组件Counter的构造函数constructor ----- ${props.caption}`);
        this.state = {
            count: props.initValue || 0
        }
    }
    // 生命周期函数
    componentWillMount() {
        console.log(`进入子组件Counter的componentWillMount ----- ${this.props.caption}`);
    }
    componentDidMount() {
        console.log(`进入子组件Counter的componentDidMount ----- ${this.props.caption}`);
    }
  　render() {
        console.log(`进入子组件Counter的render ----- ${this.props.caption}`);
        const buttonStyle = {
            margin: '10px'
        };
        
    return (
      <div><button style={buttonStyle} >+</button>
        <button style={buttonStyle} value = {5}>-</button>
        <span>{this.props.caption}count: {this.state.count}</span>
      </div>
    );
  }
}

// 父组件CounterWrapper
class CounterWrapper extends React.Component {
    
    render(){
        console.log('进入父组件CounterWrapper的render 函数');
        return (<div>
            <Counter caption="第一个" initValue={3}></Counter>
            <Counter caption="第二个" initValue={6}></Counter>
            <Counter caption="第三个" initValue={9}></Counter>
        </div>)
    }
    
    componentDidMount() {
        console.log('进入父组件CounterWrapper的 componentDidMount 函数');
    }
}

   ReactDOM.render(<CounterWrapper />, document.getElementById('root'));
</script>

复制代码

　　控制台输出如下信息

　　

　　可以看到它先进入的是父组件CounterWrapper 的render 函数，然后再进入第一个子组件的constructor, componentWillMount和 render, 再进入第二个子组件的constructor, componentWillMount和 render函数，再进入第三个子组件的constructor, componentWillMount和 render函数， 最后才是每一个子组件的componentDidMount, 第二个子组件的ccomponentDidMount函数， 第三个子组件的ccomponentDidMount函数，最后的最后是父组件的CompoentDidMount.

　　我们惊奇地发现，组件的挂载生命周期并不是依次调用的，render 函数后面并不是紧紧根随着componentDidMount 函数。只有当三个组件的render函数都调用完成后，三个组件的componentDidMount 才连在一起被调用。 只有当所有的子组件的componentDidMount都调用了，父组件的componetDidMount 才会被调用。在componentDidMount 函数的调用上，我一直存在误解，以为render 函数调用后，立即执行它，并有父组件的componentDidMount优于子组件的componentDidMount 执行，真是太想当然了。

　　现在再来想一下为什么会有这样的设计，当使用componentDidMount 的时候，我们听得最多的一句话可能就是，在这里，你可以操作真实的DOM了，换句话说，当 组件compoentDidMount的时候，真实的DOM 已经渲染完毕了，整个页面都已经渲染完成了。整个页面就意味着整个DOM树都已经构建完成了，注意这里是整个DOM树。当React进入到组件的render 函数时，它只知道当前组件长什么样子，而不会知道整个DOM树长什么样子，所以它只能接着找到第二个组件render， 因为后面有第三个子组件，它还是不知道整个DOM树长什么样子，所以它还要找到第三个组件render,  也就是说它会把所有的组件都循环一遍，直到能够构建整个DOM树，它才会渲染真实的DOM, 这也是最为省事的办法，如果它找到一个渲染一个，后面的组件再对前面的组件有影响，它要把前面的做法再来一次，效率低下。一次渲染就OK了。 渲染之后，肯定是最深层的组件先完成，只有小的完成了才能组成大的，所以最深层的组件的componentDidMount 先执行， 最外层的最后执行。这让我们想成了JS事件处理阶段，当构建DOM树的过程中，它是捕获阶段，从外面找到最里面，而在渲染真实的DOM的时候，componentDidMount的时候，它是冒泡阶段， 从最里面到最外面。

　　现在再来看更新阶段，当挂载阶段完成后，用户看到页面了。但如果要让页面有更好的交互性，那还要能更改状态或属性，重新渲染了组件，这就是更新阶段了。更新阶段又依次分为下面几个阶段：

　　1, 将要接收属性 --> componentWillRecieveProps

　　2, 组件应不应该更新 ---> shouldCompoentUpdate

　　3, 组件将要更新 ---> componentWillUpdate

　　4, 组件进行渲染 --> render

　　5, 组件渲染完成 --> componentDidUpdate

　　componentWillRecieveProps() : 字面理解的意思是组件将要接受到新的props. 那么只要接受到props，该函数就会被调用。其实不是这样的，只要是父组件的render函数被调用，在render函数里面被渲染的子组件就会经历更新过程，不管父组件传给子组件的props 有没有改变，都会触发子组件的componentWillREciveProps函数。我们把父组件重新渲染一下，看是不是这个样子。因为我们的父组件是没有状态，无法调用setState函数，这时我们可以调用forceUpdate方法来强制更新。在父组件中加入一个button，调用forceUpdate函数，强制更新父组件。
复制代码

<script type="text/babel">

// 子组件counter
class Counter extends React.Component {

    constructor(props) {
        super(props);
        this.state = {
            count: props.initValue || 0
        }
    }
    componentWillReceiveProps() {
        console.log(`进入子组件Counter的componentWillReceiveProps ----- ${this.props.caption}`);
    }
  render() {
        console.log(`进入子组件Counter的render ----- ${this.props.caption}`);
        const buttonStyle = {
            margin: '10px'
        };
        
    return (
      <div>
        <button style={buttonStyle} >+</button>
        <button style={buttonStyle} value = {5}>-</button>
        <span>{this.props.caption}count: {this.state.count}</span>
      </div>
    );
  }
}

// 父组件CounterWrapper
class CounterWrapper extends React.Component {
    
    render(){
        return (<div>
            <Counter caption="第一个" initValue={3}></Counter>
            <Counter caption="第二个" initValue={6}></Counter>
            <Counter caption="第三个" initValue={9}></Counter>
            {/*更新父组件*/}
            <button onClick={()=> {this.forceUpdate()}}>更新父组件</button>
        </div>)
    }
}

  ReactDOM.render(<CounterWrapper />, document.getElementById('root'));
</script>

复制代码

　　这时点击父组件的更新父组件按钮，可以看到如下输出

　　可以看到，即使父组件传递过来的参数没有发生变化，子组件依然调用了componentWillReceiveProps 函数。

　　shouldComponentUpdate() : 组件应不应该更新，它决定了一个组件是不是需要重新渲染， 如果它返回fasle, 就表示该组件不需要更新，也就不会重新渲染，它后面的生命周期函数就不会被执行。如果返回true，表示该组件需要更新， 它后面的生命周期函数才会被执行，重新渲染。那它是根据什么返回true 或 false 的呢？我们知道，执行渲染，调用的是render 函数，而render函数，则是根据props和state来返回jsx， 所以我们只要判断下一次的props和state,是不是和当前的props 和state 一致，如果一致，就是返回true，如果不一致，则返回false. 那么componentShouldUpdate 就要接受两个函数，一个是nextProps, 一个是nextState.  现在来写一下componentShouldUpdate

    // 如果父组件传递过来的caption， 或 状态中的count 发生变化，才会返回true, 组件才需要演染。
    shouldComponentUpdate(nextProps, nextState) {
        return (nextProps.caption !== this.props.caption) || (nextState.count !== this.state.count);
    }

　　这时点击更新父组件按钮，可以看到只调用了componentWillReceiveProps 生命周期函数，因为它没有新的props和state, shouldComponentUpdate 返回了fasle, 所以后面的生命周期函数render 并没有执行。

　　现在再点击一下加和减，当我们点击哪个组件，哪个组件的render函数才会调用，因为点击时，组件状态改变了，shouldComponentUpdate 返回了true,  它后面的生命周期函数才会执行。而其它组件的shouldComponentUpdate 返回false, 它后面的生命周期函数没有执行。

　　

　　如果组件shouldComponentUpdate 返回true, React 会依次调用它后面的生命周期函数，componentWillUpdate，render , componentDidUpdate。 componentWillUpdate ---> componentDidUpdate 和 componentWillmount ---> componentDidmount 的用法是一样的，这里就不说了。

　　最后是卸载过程，在这里, React 只提供了一个componentwillUnmount 函数，当React 组件要从DOM树上删掉之前，对应的componentWillUnmount函数就会被调用，它适合做一些清理性的工作，就是删除这个组件之前有没有什么要清理的。注意它没有对应的did函数，因为组件删除以后，没有什么事情可以做了。