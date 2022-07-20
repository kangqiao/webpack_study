### css

应保证 loader 的先后顺序：'style-loader' 在前，而 'css-loader' 在后。如果不遵守此约定，webpack 可能会抛出错误。

### 加载 images 图像

假如，现在我们正在下载 CSS，但是像 background 和 icon 这样的图像，要如何处理呢？在 webpack 5 中，可以使用内置的 Asset Modules，我们可以轻松地将这些内容混入我们的系统中：

### 加载 fonts 字体

那么，像字体这样的其他资源如何处理呢？使用 Asset Modules 可以接收并加载任何文件，然后将其输出到构建目录。这就是说，我们可以将它们用于任何类型的文件，也包括字体。让我们更新 webpack.config.js 来处理字体文件：

配置好 loader 并将字体文件放在合适的位置后，你可以通过一个 `@font-face` 声明将其混合。本地的 url(...) 指令会被 webpack 获取处理，就像它处理图片一样：

### 加载数据

此外，可以加载的有用资源还有数据，如 JSON 文件，CSV、TSV 和 XML。类似于 NodeJS，JSON 支持实际上是内置的，也就是说 import Data from './data.json' 默认将正常运行。要导入 CSV、TSV 和 XML，你可以使用 csv-loader 和 xml-loader。让我们处理加载这三类文件：

### HtmlWebpackPlugin

如果在代码编辑器中打开 index.html，你会看到 HtmlWebpackPlugin 创建了一个全新的文件，所有的 bundle 会自动添加到 html 中。

### 使用 source map

为了更容易地追踪 error 和 warning，JavaScript 提供了 source maps 功能，可以将编译后的代码映射回原始源代码。如果一个错误来自于 b.js，source map 就会明确的告诉你

```javascript
module.exports = {
  mode: 'development',
  devtool: 'inline-source-map',
  ...
}

运行后:
Uncaught ReferenceError: cosnole is not defined
at HTMLButtonElement.printMe (print.js:2)
```

### 使用 watch mode(观察模式)

你可以指示 webpack "watch" 依赖图中所有文件的更改。如果其中一个文件被更新，代码将被重新编译，所以你不必再去手动运行整个构建。
package.json

```json
{
  "name": "webpack-demo",
  "version": "1.0.0",
  "description": "",
  "private": true,
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "watch": "webpack --watch",
    "build": "webpack"
  },
  ...
}
```

### 使用 webpack-dev-server

webpack-dev-server 为你提供了一个基本的 web server，并且具有 live reloading(实时重新加载) 功能。设置如下：

`npm install --save-dev webpack-dev-server`

webpack.config.js

```json
module.exports = {
  ...
  devServer: {
    static: './dist'
  },
  ...
  optimization: {
    runtimeChunk: 'single'
  }
}
```

以上配置告知 webpack-dev-server，将 dist 目录下的文件 serve 到 localhost:8080 下。（译注：serve，将资源作为 server 的可访问文件）
webpack-dev-server 在编译之后不会写入到任何输出文件。而是将 bundle 文件保留在内存中，然后将它们 serve 到 server 中，就好像它们是挂载在 server 根路径上的真实文件一样。如果你的页面希望在其他不同路径中找到 bundle 文件，则可以通过 dev server 配置中的 devMiddleware.publicPath 选项进行修改。

### 使用 webpack-dev-middleware

webpack-dev-middleware 是一个封装器(wrapper)，它可以把 webpack 处理过的文件发送到一个 server。webpack-dev-server 在内部使用了它，然而它也可以作为一个单独的 package 来使用，以便根据需求进行更多自定义设置。下面是一个 webpack-dev-middleware 配合 express server 的示例。
`npm install --save-dev express webpack-dev-middleware`

server.js

```javascript
const express = require("express");
const webpack = require("webpack");
const webpackDevMiddleware = require("webpack-dev-middleware");

const app = express();
const config = require("./webpack.config.js");
const compiler = webpack(config);

// 告知 express 使用 webpack-dev-middleware，
// 以及将 webpack.config.js 配置文件作为基础配置。
app.use(
  webpackDevMiddleware(compiler, {
    publicPath: config.output.publicPath,
  })
);

// 将文件 serve 到 port 3000。
app.listen(3000, function () {
  console.log("Example app listening on port 3000!\n");
});
```
