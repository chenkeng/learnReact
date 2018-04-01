 React 学习(七) ---- create-react-app

　　现在react 基础知识已经算是学完了，知道了React是做什么的，以及怎么使用，是时候学习一个webpack, babel 等现代化前端开发了，真正做项目的时候，我们不可能再使用babel 的线上编译，一是很慢，二是我们写代码没有提示，因为我们在script标签中加入了type= 'text/babel', 影响开发效率。如果实在不学也没有关系，react的提供了create-react-app脚手架工具,  和vue-cli 一样，它帮你配置好了webpack/babel 等，我们直接书写代码就可以了。 打开命令行，全局安装npm install create-react-app -g 然后创建项目时，用create-react-app  项目名就可以了。

　　当我们使用create-react-app来创建应用时，发现它非常慢，上网搜了一下，看能不能能快一点？ 网上提供了以下说法，可以试一下。

　　因为create-react-app是从npm 官网下载依赖，而我们平时使用cnpm 来安装依赖，它比较快，是因为从淘宝镜像来安装依赖，我们能不能也让create-react-app从淘宝镜像下载依赖，那是可以的，就是把npm的register给永久设置成淘宝镜像的地址，打开cmd 窗口， 输入命令npm config set registry https://registry.npm.taobao.org即可，可以用以下命令查看成一下是否成功。npm config get registry。 这时我们再用create-react-app 创建项目时，可以发现它是从淘宝镜像下载依赖，速度相对快一些.

　　我在这里碰到一个小问题： 把源变成了淘宝镜像，以后直接可以使用npm install 安装依赖了，但是有一天当我npm install node-sass ,sass-loader时，它下载不下来node-sass, 不知道 为什么，当我们改用cnpm 去安装时，它却很快。

　　用create-react-app 创建项目成功后，当我们打开package.json文件，发现它并没有我们平时所安装的webpack, babel 等依赖，而是多了一个react-scripts 依赖。原来React 把所有的webpack babel配置都放到了react-scripts中，这样我们想修改一下webpack的配置文件就很困难了。还好 React给我们提供了一个命令eject，可把react-scripts打开，看到它封装的配置文件， eject命令就在scripts 字段的下面，我们执行npm run eject 就可以了。 命令执行完成后，再看一下package.json, 和我们平时经常见的一致了。而且多了config和scripts目录，它里面就是webpack的配置文件。 但是这个命令是单向，不可逆的，我们打开react-scripts以后，就不可能再回去了。

　　现在我们修改一下配置， 当我们开发项目的时候，有时会用到第三方UI, 比如：antd-mobile。 由于组件库通常非常大，我们需要按需要加载组件。按需加载，官方推荐使用  babel-plugin-import 插件，然后在 .babelrc or  webapck的配置文件中的babel-loader 中的opiton 中引入

"plugins": [ ["import", { libraryName: "antd-mobile", style: "css" }] // `style: true` 会加载 less 文件 ]

　　首先安装babel-plugin-import 插件,  npm install babel-plugin-import  --save-dev， 但这时并没有发现.babelrc 文件，当再次打开package.json文件时，它有一个babel字段，这就相当于.babelrc 文件，原来我们在.babelrc文件中怎么配置，在这里就怎么配置。我们直接把plugins语句放到它里面，但要注意一点，在json 文件中，所有的字段都要用双引号

　　这时就实现了按需加载，我们在js文件中，直接使用 import {Button} from ‘antd-mobile’ 就可以了，css都不用引入了，并且，打包到生产环境中的代码只包括Button组件， 其它没有用到的组件不会加载。

　　对于其它的babel 插件安装也是同样的道理，直接在package.json的babel字段下直接写就可以了。比如有可能用到 es6 的decorator装饰器语法, 需要用到babel-plugin-transform-decorators-legacy插件，npm install babel-plugin-transform-decorators-legacy --save-dev 安装，如下配置

　　还有一点，有利于我们开发，在package.json 中设置proxy代理，支持浏览器的跨域请求。直接在配置文件的最下面写 “proxy”: “http://localhost: 3000”,  我们所有的ajax 请求，都会代理本地服务器3000 端口下。如我们用axios 发送一个post 请求, axios.post(‘user/login’, {name: ‘sam’}) , 它真实的请求地址是’http://localhost:3000/user/login’ , 相当于axios.post(‘http://localhost:3000/user/login’, {name: ‘sam’}) 

　　除了webpack、babel构建工具以外，我们还要学习react 的状态管理---Redux或Mobx， 因为如果应用比较大时，每一个组件都有一个状态，不太好管理。当用React 作单页应用时，还要用到React-router.