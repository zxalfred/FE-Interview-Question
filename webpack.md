# Webpack  🕸

> **0. 有哪些常见的 Loader?**

* `row-loader` : 加在原始文件原始内容 (utf-8)
* `file-loader` : 把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件
* `url-loader` : 与 file-loader 类似，区别是用户可以设置一个阈值，大于阈值会交给 file-loader 处理，小于阈值时返回文件 base64 形式编码 (处理图片和字体)
* `source-map-loader`: 加载额外的 Source Map 文件，以方便断点调试
* `svg-inline-loader`: 将压缩后的 SVG 内容注入代码中
* `image-loader`: 加载并且压缩图片文件
* `json-loader`: 加载 JSON 文件（默认包含）
* `handlebars-loader`: 将 Handlebars 模版编译成函数并返回
* `babel-loader`: 把 ES6 转换成 ES5
* `ts-loader`: 将 TypeScript 转换成 JavaScript
* `awesome-typescript-loader`: 将 TypeScript 转换成 JavaScript，性能优于 ts-loader
* `sass-loader`: 将SCSS/SASS代码转换成CSS
* `css-loader`: 加载 CSS，支持模块化、压缩、文件导入等特性
* `style-loader`: 通过\<style\>标签把 css 加载到 DOM 中
* `postcss-loader`: 扩展 CSS 语法，使用下一代 CSS，可以配合 autoprefixer 插件自动补齐 CSS3 前缀
* `eslint-loader`: 通过 ESLint 检查 JavaScript 代码
* `tslint-loader`: 通过 TSLint检查 TypeScript 代码
* `mocha-loader`: 加载 Mocha 测试用例的代码
* `coverjs-loader`: 计算测试的覆盖率
* `vue-loader`: 加载 Vue 单文件组件
* `i18n-loader`: 国际化
* `cache-loader`: 可以在一些性能开销较大的 Loader 之前添加，目的是将结果缓存到磁盘里

> **1. 有哪些常见的 Plugin?**
* `speed-measure-webpack-plugin`: 可以看到每个 Loader 和 Plugin 执行耗时 (整个打包耗时、每个 Plugin 和 Loader 耗时)
* `SplitChunksPlugin`: 将公共依赖项提取到现有的 entry chunk 或全新的代码块中
* `webpack-bundle-analyzer`: 可视化 Webpack 输出文件的体积 (业务组件、依赖第三方模块)
* `ignore-plugin`: 从 bundle 中排除某些模块
* `ModuleConcatenationPlugin`: 提升所有模块到一个闭包中，提升你的代码在浏览器中的执行速度。
* `html-webpack-plugin`: 创建 HTML 文件
* `web-webpack-plugin`: 可方便地为单页应用输出 HTML，比 html-webpack-plugin 好用
* `terser-webpack-plugin`: 压缩 JS，支持 ES6
* `mini-css-extract-plugin`: 分离样式文件，CSS 提取为独立文件，支持按需加载 (替代extract-text-webpack-plugin)
* `serviceworker-webpack-plugin`: 为网页应用增加离线缓存功能
* `clean-webpack-plugin`: 目录清理

> **2. Loader和Plugin的区别是什么**

`Loader` 本质是一个函数，在该函数中对接收到的内容进行转换，返回转换后的结果。因为 Webpack 只认识 JavaScript，所以 Loader 就成了翻译官，对其他类型的资源进行转译的预处理工作。

`Plugin` 就是插件，基于事件流框架 `Tapable`, 插件可以扩展 Webpack 功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

`Loader` 在 module.rules 中配置，作为模块解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options(参数) 等属性。

`Plugin` 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。

> **3. Webpack构建流程**

Webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：

- `初始化参数`：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数
- `开始编译`：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译
- `确定入口`：根据配置中的 entry 找出所有的入口文件
- `编译模块`：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理
- `完成模块编译`：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系
- `输出资源`：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会
- `输出完成`：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统

在以上过程中，Webpack 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。

简单说

`初始化`：启动构建，读取与合并配置参数，加载 Plugin，实例化 Compiler

`编译`：从 Entry 出发，针对每个 Module 串行调用对应的 Loader 去翻译文件的内容，再找到该 Module 依赖的 Module，递归地进行编译处理

`输出`：将编译后的 Module 组合成 Chunk，将 Chunk 转换成文件，输出到文件系统中

> **4. 使用webpack开发时，你用过哪些可以提高效率的插件？**
- `webpack-dashboard`: 可以更友好地展示相关打包信息
- `webpack-merge`: 提取公共配置，减少重复配置代码
- `speed-measure-webpack-plugin`: 分析出打包过程中 Loader 和 Plugin 的耗时，有助于找到构建过程中的性能瓶颈。
- `size-plugin`: 监控资源体积变化，尽早发现问题
- `HotModuleReplaceMentPlugin`: 模块热替换

> **5. source map 模式**

**开发环境推荐：**

`cheap-module-eval-source-map`

**生产环境推荐：**

`cheap-module-source-map `

（这也是下版本 webpack 使用-d命令启动 debug 模式时的默认选项）

**原因如下：**

1. 使用 cheap 模式可以大幅提高 souremap 生成的效率。大部分情况我们调试并不关心列信息，而且就算 sourcemap 没有列，有些浏览器引擎（例如 v8） 也会给出列信息。
2. 使用 eval 方式可大幅提高持续构建效率。参考官方文档提供的速度对比表格可以看到 eval 模式的编译速度很快。
3. 使用 module 可支持 babel 这种预编译工具（在 webpack 里做为 loader 使用）。
4. 使用 eval-source-map 模式可以减少网络请求。这种模式开启 DataUrl 本身包含完整 sourcemap 信息，并不需要像 sourceURL 那样，浏览器需要发送一个完整请求去获取 sourcemap 文件，这会略微提高点效率。而生产环境中则不宜用 eval，这样会让文件变得极大。

**SourceMap 模式效率对比图**
![SourceMap 模式效率对比图](https://lc-api-gold-cdn.xitu.io/a2e245898b08cdc389a2.jpeg?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

> **6. source map 模块打包原理知道吗？**

Webpack 实际上为每个模块创造了一个可以导出和导入的环境，本质上并没有修改代码的执行逻辑，代码执行顺序与模块加载顺序也完全一致。

>  **7. 文件监听原理**

发现源码发生变化时，自动重新构建出新的输出文件

Webpack 开启监听模式，有两种方式：
- 启动 webpack 命令时，带上 --watch 参数
- 在配置 webpack.config.js 中设置 watch: true

缺点：每次需要手动刷新浏览器

原理：轮询判断文件的最后编辑时间是否变化，如果某个文件发生了变化，并不会立刻告诉监听者，而是先缓存起来，等 `aggregateTimeout` 后再执行

```javascript
module.export = {
  // 默认false,也就是不开启
  watch: true,
  // 只有开启监听模式时，watchOptions才有意义
  watchOptions: {
    // 默认为空，不监听的文件或者文件夹，支持正则匹配
    ignore: /node_modules/,
    // 监听到变化发生后会等300ms再去执行，默认300ms
    aggregateTimeout: 300,
    // 判断文件是否发生变化是通过不停询问系统指定文件有没有变化实现的，默认每秒问1000次
    poll: 1000
  }
}
```

> **8. 说一下 Webpack 的热更新原理吧**

`Webpack` 的热更新又称热替换(```Hot Module Replacement```)，缩写为 HMR。这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块。

HMR 的核心就是客户端从服务端拉取更新后的文件，准确的说是 chunk diff(chunk 需要更新的部分)，实际上 WDS 与浏览器之间维护了一个 `WebSocket`，当本地资源发生变化时，WDS 会向浏览器推送更新，并带上构建时的 hash，让客户端与上一次资源进行对比。客户端对比差异后会向 WDS 发起 `Ajax` 请求获取更改内容（文件列表、hash），这样客户端就再借助这些信息继续向 WDS 发起 `jsonp` 请求获取该 chunk 增量更新。

后续的部分(拿到增量更新之后如何处理？哪些状态该保留？哪些又需要更新？)由 `HotModulePlugin` 来完成，提供了相关 API 以供开发者针对自身场景进行处理，像`react-hot-loader` 和 `vue-loader` 都是借助这些 API 实现 HMR。

> **9. 如何对bundle体积进行监控和分析？**

VSCode 中有一个插件 `Import Cost` 可以帮助我们对引入模块的大小进行实时监测，还可以使用 `webpack-bundle-analyzer` 生成 bundle 的模块组成图，显示所占体积。

`bundlesize` 工具包可以进行自动化资源体积监控。

> **10. 文件指纹是什么？怎么用？**

文件指纹是打包后输出的文件名后缀。

- `Hash`: 每个构建过程生成的唯一 hash
- `Chunkhash`: 基于每个 chunk 内容生成的 hash
- `Contenthash`: 根据文件内容生成的 hash

占位符名称及含义

- `ext` 资源后缀名
- `name` 文件名称
- `path` 文件的相对路径
- `folder` 文件所在的文件夹
- `emoji` 一个随机的指代文件内容的emoji

<center style="font-weight:bold;text-decoration:underline">JS 文件指纹设置</center>
设置 output 的 filenanme，用 chunkhash

```
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js
  },
  output: {
    filename: '[name][chunkhash:8].js',
    path
  }
}
```

<center style="font-weight:bold;text-decoration:underline">CSS 文件指纹设置</center>
设置 MiniCssExtractPlugin 的 filename，使用 contenthash。

```
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js
  },
  output: {
    filename: '[name][chunkhash:8].js',
    path
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: `[name][contenthash:8].css`
    })
  ]
}
```
<center style="font-weight:bold;text-decoration:underline">图片文件指纹设置</center>
设置file-loader的name，使用hash。

```
module.exports = {
  entry: {
    app: './src/app.js',
    search: './src/search.js
  },
  output: {
    filename: '[name][chunkhash:8].js',
    path
  },
  module: {
    rules: [{
      test: /\.(png|svg|jpg|gif)$/,
      use: [{
        loader: 'file-loader',
        options: {
          name: 'img/[name][hash:8].[ext]'
        }
      }]
    }]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: `[name][contenthash:8].css`
    })
  ]
}
```

> **11.在实际工程中，配置文件上百行乃是常事，如何保证各个loader按照预想方式工作？**

可以使用 `enforce` 强制执行 `loader` 的作用顺序，`pre` 代表在所有正常 `loader` 之前执行，`post` 是所有 `loader` 之后执行。

> **12.如何优化 Webpack 的构建速度？**

- `多进程压缩`:
  - terser-webpack-plugin 开启 parallel
  - happypack 插件
- `提取页面公共资源`:
  - 使用 html-webpack-externals-plugin，将基础包通过CDN引入，不打入 bundle 中
  - 使用 SplitChunksPlugin 进行（公共脚本、基础包、页面公共文件）分离
- `DLL` : 使用 DllPlugins 进行分包，使用 DllReferencePlugin（索引链接）对 manifest.json 引用，让一些基本不会改动的代码先打包成静态资源，避免反复编译浪费时间
- `利用缓存提升二次构建速度`:
  - babel-loader 开启缓存
  - terser-webpack-plugin 开启缓存
  - 使用 cache-loader
- `noParse`:
  - 如果一些第三方模块没有AMD/CommonJS规范版本，可以使用 noParse 来标识这个模块，这样 Webpack 会引入这些模块，但是不进行转化和解析，从而提升 Webpack 的构建性能 ，例如：jquery 、lodash
  ```javascript
  //webpack.config.js
  module.exports = {
      //...
      module: {
          noParse: /jquery|lodash/
      }
  }
  ```
- `IgnorePlugin`
  webpack 的内置插件，作用是忽略第三方包指定目录。
  例如: moment 会将所有本地化内容和核心功能一起打包，我们就可以使用 IgnorePlugin 在打包时忽略本地化内容。
  ```javascript
  //webpack.config.js
  module.exports = {
      //...
      plugins: [
          //忽略 moment 下的 ./locale 目录
          new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/)
      ]
  }
  ```
  在使用的时候，如果我们需要指定语言，那么需要我们手动的去引入语言包，例如，引入中文语言包:
  ```javascript
  import moment from 'moment';
  import 'moment/locale/zh-cn';// 手动引入
  ```
- `externals`:
  我们可以将一些JS文件存储在 CDN 上(减少 Webpack打包出来的 js 体积)，在 index.html 中通过 &lt;script&gt; 标签引入，如:
  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <title>Document</title>
  </head>
  <body>
      <div id="root">root</div>
      <script src="http://libs.baidu.com/jquery/2.0.0/jquery.min.js"></script>
  </body>
  </html>
  ```
  我们希望在使用时，仍然可以通过 import 的方式去引用(如 import $ from 'jquery')，并且希望 webpack 不会对其进行打包，此时就可以配置 externals。
  ```javascript
  //webpack.config.js
  module.exports = {
      //...
      externals: {
          //jquery通过script引入之后，全局中即有了 jQuery 变量
          'jquery': 'jQuery'
      }
  }
  ```
- `tree-shaking`:
  - 打包过程中检测工程中没有引用过的模块并进行标记，在资源压缩时将它们从最终的bundle中去掉(只能对ES6 Modlue生效) 开发中尽可能使用ES6 Module的模块，提高tree shaking效率
  - 禁用 babel-loader 的模块依赖解析，否则 Webpack 接收到的就都是转换过的 CommonJS 形式的模块，无法进行 tree-shaking
- `scope hoisting`:
  - 构建后的代码会存在大量闭包，造成体积增大，运行代码时创建的函数作用域变多，内存开销变大。Scope hoisting 将所有模块的代码按照引用顺序放在一个函数作用域里，然后适当的重命名一些变量以防止变量名冲突
  - 必须是ES6的语法，因为有很多第三方库仍采用 CommonJS 语法，为了充分发挥 Scope hoisting 的作用，需要配置 mainFields 对第三方模块优先采用 jsnext:main 中指向的ES6模块化语法
- `动态Polyfill`:
  - 建议采用 polyfill-service 只给用户返回需要的polyfill，社区维护。 (部分国内奇葩浏览器UA可能无法识别，但可以降级返回所需全部polyfill)


## 参考
1. https://juejin.cn/post/6844904093463347208