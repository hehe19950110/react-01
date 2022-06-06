### 一、unpkg 
> 1、unpkg是一个内容源自npm的全球快速CDN。
它支持 h/2 和很多新特性，如果不考虑网络延迟的原因，性能优化较为出色。在国内一些互联网公司也有镜像，例如知乎和饿了么。
它能以快速而简单的方式提供任意包、任意文件，通过类似这样的URL：unpkg.com/:package@:version/:file

> 2、使用 unpkg:
>> 1. 使用固定的版本号：
unpkg.com/react@16.0.0/umd/react.production.min.js
unpkg.com/react-dom@16.0.0/umd/react-dom.production.min.js
>> #### react先， react-dom后，且先后顺序不能改变。

>> 2. 可使用语义化版本范围，或标签来代替固定版本号，亦可忽略版本和标签，直接使用最新的版本。
unpkg.com/react@^16/umd/react.production.min.js
unpkg.com/react/umd/react.production.min.js

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>React——hello world</title>
    <script src="http://unpkg.com/react@16/umd/react.development.js"></script>
    <script src="http://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
    <script src="http://unpkg.com/@babel/standalone/babel.min.js"></script>
  </head>
  <body>
    <div id="root"></div>
    <script type="text/babel">
    ReactDOM.render(
    <h1>hello,world</h1>,
    document.getElementById('root')
    );
    </script>
  </body>
</html>
```
>> http://js.jirengu.com/repunurise/2/
不推荐使用，把JS源码 经过babel在线的变成 JavaScript ，消耗浏览器性能。

> 3、unpkg上的发布流程
如果你是 npm 包作者，只要发布到 npm 仓库，unpkg 替你减轻了发布到CDN的麻烦。
仅需 npm 包中包含UMD构建即可（并非在代码仓库里包含，两者不同！）。

通过以下步骤：
>>  添加umd（或 dist） 目录到 .gitignore 文件中
>>  添加umd目录到package.json文件数组(files)中
>>  发布的时候，使用脚本构建 UMD打包文件到umd目录
>>  就是这样了，当npm发布时，在unpkg上也会拥有一个有效的文件版本。
>>  一旦发布到npm后即可被访问到，如果按以上说明操作，将具有更好的效果。

### 二、JSX与环境搭建
JSX：JavaScript 语法扩展，⽤于形象描述⼀段DOM结构。
JSX与转换后的代码:
预定义 React.createElement 的功能：

```
    const React = { }
    React.createElement = function () { }
```

### 三、如何设置React环境，Webpack和Babel
https://github.com/jirengu/OneReact
> 1、设置项目:
>> 1. 首先，为项目创建目录：
`mkdir webpack-react-tutorial && cd webpack-react-tutorial`
>> 2. 创建用于保存代码的最小目录结构：
`mkdir -p src`
>> 3. 通过运行以下内容来初始化项目：
`npm init -y`

> 2、设置Webpack:
>> 1. 让我们通过运行以下命令安装webpack和webpack-cli：
`npm i webpack webpack-cli --save-dev`

>> 2. 现在在里面添加webpack命令package.json：
` "scripts": { "build": "webpack --mode production"} `
此时，无需为webpack定义配置文件。较旧的webpack版本会自动查找配置文件。从版本4开始，情况不再如此。

> 3、设置 Babel 解释 JSX:
>> 1. 设置 Babel 解释 JSX： Babel 是一个转换工具，可以将代码转换。如 将最新的JS的写法转换为旧的 等。
Webpack本身不知道如何转换JavaScript；它依赖于loader作为转换工具。 React组件主要是用现代JavaScript语法编写的。以class关键字为例。有状态的React组件可以声明为类或箭头（或常规函数）。但是旧版浏览器无法理解ECMAScript 2015，因此我们需要某种转换。 

>> 2. 一个webpack loader 将某些东西作为输入并产生一个输出，称为bundle。 babel-loader是负责与Babel对话的 webpack loader。 
同时 Babel必须配置预设（preset，预先配置好的一组插件）：babel-loader是负责与Babel对话的 webpack loader。
同时 Babel必须配置预设（preset，预先配置好的一组插件）：
 * @babel/preset-env 用于将现代JavaScript编译为ES5
 * @babel/preset-react 可将JSX和其他内容编译为JavaScript

>> 3. 安装依赖项：
`npm i @babel/core babel-loader @babel/preset-env @babel/preset-react --save-dev`
以上工具的作用是：
webpack项目里当 import 一个.jsx文件时，使用 babel-loader 来处理这个文件， babel-loader 使用 @babel/core 来执行转换， 在转换的过程中使用了babel的 @babel/preset-env插件用于把最新的ES转换为ES5，使用 @babel/preset-react把 JSX转换为正常的JavaScript。
在项目根目录创建 .babelrc 文件，该文件的作用是 告诉 babel-core 在执行转换的时候使用如下插件：

```
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
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
>> plugins 插件: 如果插件在 npm 中，你可以传入插件的名字，Babel 会检查它是否安装在 node_modules 中。这将被添加到 plugins 配置项，该选项接受一个数组。

```
{
  "plugins": ["babel-plugin-myPlugin", "@babel/plugin-transform-runtime"]
}
//你还可以指定插件的 相对/绝对 路径。
{
  "plugins": ["./node_modules/asdf/plugin"]
}
```
>> 插件排序：
 * 插件在预设之前运行。
 * 插件排序是从第一个到最后一个。
 * 预设顺序是颠倒的（最后一个到第一个）。 
 例如：`{ "presets": ["@babel/preset-env", "@babel/preset-react"] }； `将按以下顺序运行：先 @babel/preset-react 再 @babel/preset-env。

>> 4.(preset) 预设: Babel 预设可以作为 Babel 插件和配置选项的共享集。 常见环境组合了几个预设：
  * @babel/preset-env 用于编译 ES2015+ 语法
  * @babel/preset-typescript 用于 TypeScript
  * @babel/preset-react 用于 React
  * @babel/preset-flow 用于 Flow

>> 5.创建一个名为的文件webpack.config.js，内容如下：

```
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
    contentBase: './dist'
  }
}
```
>> 该配置非常少。对于每个带有js或jsx扩展名的文件，Webpack都会通过babel-loader处理代码。 以上配置不只能用于本章练习JSX，也适合正式的React开发环境。

>> 6.为了使环境能正常启动，需要安装 html-webpack-plugin 和 wepack-dev-server
`npm i --save-dev html-webpack-plugin webpack-dev-server`

>> 7. 修改 package.json

```
{
  ...
  "scripts": {
    "start": "webpack-dev-server",
    "build": "webpack"
  },
  ...
}
```

> 4、测试JSX:
>> 1.创建 src/index.js 文件

```
const React = {
  createElement(...args) {
    console.log(args)
  }
};

let div = <div>hello </div>;
console.log(div);
```
>> 2.执行，启动测试
`npm run start `

### 四、虚拟DOM
> 1、JSX映射为对象:

```
const React = {
  createElement(tag, attrs, ...children) {
    return {
      tag,
       attrs,
       children
     }
  }
};
```
> 2、渲染标签:

```
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
```
> 3、渲染属性：

```
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
```
>> es6中export和export default的区别：
export与export default均可用于导出常量、函数、文件、模块。
你可以在其它文件或模块中通过import+(常量 | 函数 | 文件 | 模块)名的方式，将其导入，以便能够对其进行使用。
在一个文件或模块中，export、import可以有多个，export default仅有一个。
通过export方式导出，在导入时要加{ }，export default则不需要,使用export default命令，为模块指定默认输出，这样就不需要知道所要加载模块的变量名。
参考：https://github.com/jirengu/OneReact.git
