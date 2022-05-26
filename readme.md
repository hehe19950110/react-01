一、JSX与环境搭建

1. JSX：JavaScript 语法扩展，⽤于形象描述⼀段DOM结构。

2. JSX与转换后的代码:

1)预定义 React.createElement 的功能：
```    
    const React = { }
    React.createElement = function () { }
```
2)配置环境实现JSX转换：

> 1、设置项目：

    1. 首先，为项目创建目录：` mkdir teach-one-react `
    
    2. 创建用于保存代码的最小目录结构：` mkdir src `
    
    3. 通过运行以下内容来初始化项目：` npm init -y `

> 2、设置Webpack

    1. 让我们通过运行以下命令安装webpack和webpack-cli：` npm install webpack webpack-cli --save-dev `

    2. 现在在package.json里面添加webpack命令：` "scripts": {"build": "webpack --mode production"} `
    
> 此时，无需为webpack定义配置文件。较旧的webpack版本会自动查找配置文件。从版本4开始，情况不再如此。

> 3、设置 Babel 解释 JSX：

> Babel 是一个转换工具，可以将代码转换。如 将最新的JS的写法转换为旧的 等。Webpack本身不知道如何转换JavaScript；它依赖于loader作为转换工具。
> React组件主要是用现代JavaScript语法编写的。以class关键字为例。有状态的React组件可以声明为类或箭头（或常规函数）。但是旧版浏览器无法理解ECMAScript 2015，因此我们需要某种转换。
> 一个webpack loader 将某些东西作为输入并产生一个输出，称为bundle。 babel-loader是负责与Babel对话的 webpack loader。
> 同时 Babel必须配置预设（preset，预先配置好的一组插件）：
* @babel/preset-env 用于将现代JavaScript编译为ES5
* @babel/preset-react 可将JSX和其他内容编译为JavaScript

安装依赖项：` npm i @babel/core babel-loader @babel/preset-env @babel/preset-react --save-dev `

1. @babel/core，webpack项目里当 import 一个.jsx文件时，使用 babel-loader 来处理这个文件，babel-loader使用 @babel/core 来执行转换。

2. @babel/preset-env 可以根据开发者的配置，按需加载插件。
配置项大致包括：需要支持的平台：比如node、浏览器等；需要支持的平台的版本：比如支持比较新的语法，ES6、ES7、ES8等最新的ES转变为ES5的语法。

3. @babel/preset-react 代码里把 JSX转换为正常的JavaScript。
在项目根目录创建 .babelrc 文件，该文件的作用是 告诉 babel-core 在执行转换的时候使用如下插件：
```
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}    //babel/preset-react里面包含了''transform-react-jsx''插件
//或者
{
  "presets": [
    "@babel/preset-env"
  ],
  "plugins": [
    ["transform-react-jsx", {
        "pragma": "React.createElement"
    }]
  ]
}
```
> 创建一个名为的文件webpack.config.js，内容如下：
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  entry: {
    index: './src/index.js'
  },
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader'
        }
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin()
  ],
  output: {
    filename: '[name].[hash:5].bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  devtool: 'inline-source-map',
  devServer: {
    static: {
      directory: path.join(__dirname, 'dist'),
    },
  }
}
> 为了使环境能正常启动，需要安装 html-webpack-plugin 和 webpack-dev-server：
`npm i --save-dev html-webpack-plugin webpack-dev-server`
 修改 package.json:
{
  ...
  "scripts": {
    "start": "webpack-dev-server",
    "build": "webpack"
  },
  ...
}
> 4、测试JSX：
创建 src/index.js 文件:
const React = {
  createElement() {
    console.log(arguments)
  }
};
let div = <div>hello </div>;
console.log(div);

二、Babel：
Babel 是一个工具链，可以在线将ES6代码转为ES5代码。转换后的代码，可以直接作为ES5代码插入网页运行。

以下是 Babel 可以做的主要事情：
1. 转换语法
2. Polyfill 目标环境中缺少的功能（通过如 core-js 的第三方 polyfill）
3. 源代码转换(codemods)...
> 1、安装:
`npm install --save-dev @babel/preset-react`
> 2、配置banel：
// 在项目的根目录（package.json 所在的位置）中，创建一个名为 babel.config.json 的文件，其中包含以下内容。
{
  "presets": [...],
  "plugins": [...]
}
//javaScript:
module.exports = function (api) {
  api.cache(true);

  const presets = [ ... ];
  const plugins = [ ... ];

  return {
    presets,
    plugins
  };
}
> 3、plugins 插件:
如果插件在 npm 中，你可以传入插件的名字，Babel 会检查它是否安装在 node_modules 中。这将被添加到 plugins 配置项，该选项接受一个数组。
{
  "plugins": ["babel-plugin-myPlugin", "@babel/plugin-transform-runtime"]
}
//你还可以指定插件的 相对/绝对 路径。
{
  "plugins": ["./node_modules/asdf/plugin"]
}
插件排序：
1. 插件在预设之前运行。
2. 插件排序是从第一个到最后一个。
3. 预设顺序是颠倒的（最后一个到第一个）。
例如：
`{ "presets": ["@babel/preset-env", "@babel/preset-react"] }`；将按以下顺序运行：先 @babel/preset-react 再 @babel/preset-env。
> 4、preset 预设
Babel 预设可以作为 Babel 插件和配置 选项 的共享集。
常见环境组合了几个预设：
1. @babel/preset-env 用于编译 ES2015+ 语法
2. @babel/preset-typescript 用于 TypeScript
3. @babel/preset-react 用于 React
4. @babel/preset-flow 用于 Flow

三、虚拟DOM
JSX映射为对象:
const React = {
  createElement(tag, attrs, ...children) {
    return {
      tag,
       attrs,
       children
     }
  }
};

渲染标签:
function render(vdom, container) {
  let node;
  if(typeof vdom === 'string') {
    node = document.createTextNode(vdom);
  }
  if(typeof vdom === 'object') {
    node = document.createElement(vdom.tag);
    vdom.children.forEach(childVdom => render(childVdom, node));
  }
  container.appendChild(node);
}
const ReactDom = {
  render(vdom, container) {
  container.innerHTML = '';
  render(vdom, container);
  }
};

渲染属性：
function setAttribute(node, attrs) {
   if(!attrs) return;
   for(let key in attrs) {
     if(key.startsWith('on')) {
       node[key.toLocaleLowerCase()] = attrs[key];
    } else if(key === 'style') {
    Object.assign(node.style, attrs[key]);  //Object.assign 方法，合并对象，合并具有相同属性的对象，拷贝 Symbol 类型属性等。
     } else {
    node[key] = attrs[key];
    }
  }
}

es6中export和export default的区别：
1. export与export default均可用于导出常量、函数、文件、模块。
2. 你可以在其它文件或模块中通过import+(常量 | 函数 | 文件 | 模块)名的方式，将其导入，以便能够对其进行使用。
3. 在一个文件或模块中，export、import可以有多个，export default仅有一个。
4. 通过export方式导出，在导入时要加{ }，export default则不需要,使用export default命令，为模块指定默认输出，这样就不需要知道所要加载模块的变量名。

参考：https://github.com/jirengu/OneReact.git
