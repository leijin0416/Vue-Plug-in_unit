# webpack

[参考文章](https://juejin.cn/post/6957597810938085384)

![images](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dbedc66bbafc46ecbf24566346569527~tplv-k3u1fbpfcp-zoom-1.image)

> webpack 会**自动递归解析**入口所需要加载的所有资源文件，是一个现代 javascript 应用程序的静态模块打包器，专注构建模块化项目。在 webpack 里一切文件皆模块。这样的好处是可以清楚的了解各模块之间的依赖关系，以便 webpack 进行组合与打包。在打包的过程中：通过 loader 使得 webpack 有能力调用外部的脚本或工具，实现对不同格式的文件的处理，然后再通过 plugins 进行功能的扩展，比如压缩文件、分割文件的处理等，然后进行打包。

（1）、调整webpack配置最简单的方式就是`在vue.config.js中的 configureWebpack 选项提供一个对象`。该对象将会被webpack-merge合并如最终的webpack配置。在configureWebpack里可以配置webpack的loader和plugins等

（2）、Vue CLI 内部的 webpack 配置是通过` webpack-chain `维护的。这个库提供了一个 webpack 原始配置的上层抽象，使其可以定义具名的 loader 规则和具名插件，并有机会在后期进入这些规则并对它们的选项进行修改。

它允许我们更细粒度的控制其内部配置。

[使用流程](https://fed.taobao.org/blog/2016/09/10/webpack-flow/)

## treemap sizes

- stat：文件的“输入”大小，在任何转换（如缩小）之前，它被称为“stat-size”，因为它是从Webpack的stats对象获得的。

- parsed：这是文件的“输出”大小。如果使用的是像 uglify 这样的 Webpack插件，那么这个值将反映代码的小型化。

- gzipped：这是通过gzip压缩运行解析的包/模块的大小。

---

## vue-cli3/4 直接引入terser-webpack-plugin

解决 uglifyjs-webpack-plugin 不兼容es6问题

```js
// 最新
module.exports = {
  configureWebpack: (config) => {
    if (process.env.NODE_ENV === 'production') {
      config.optimization.minimizer[0].options.terserOptions.compress.drop_console = true
      config.optimization.minimizer[0].options.terserOptions.compress.pure_funcs = ['console.log']
    }
  }
}

---

const TerserPlugin = require("terser-webpack-plugin");
module.exports = {
  // 加密压缩(terser)
  configureWebpack: config => {
    const optimization: {
      minimizer: [
        new TerserPlugin({
          terserOptions: {
            compress: {
              warnings: false,
              drop_console: true,
              drop_debugger: true,
              pure_funcs: ['console.log', 'console.error']
            },
          },
        }),
      ],
    }
    // 将optimization的所有属性合并到config里
    Object.assign(config, {
      optimization
    })
  }
}
```

## 【2】开启压缩 Gzip

npm install -D compression-webpack-plugin

```js
const CompressionWebpackPlugin = require("compression-webpack-plugin") // 引入
configureWebpack: config => {
  config.plugins.push(
    new CompressionWebpackPlugin({
      algorithm: "gzip",
      filename: "[path].gz[query]",
      test: productionGzips,
      threshold: 10240,             // 只有大小大于该值的资源会被处理
      minRatio: 0.8,                // 只有压缩率小于这个值的资源才会被处理
      deleteOriginalAssets: false,  // 删除原文件
    }),
  )
}
```

## 【2.1】开启 babel-polyfill

npm install -D babel-polyfill 解决ie11兼容ES6，但同时会增加打包体积

```js
// 引入
chainWebpack: config => {
  // ============解决ie11兼容ES6 start==========
  config.entry('main').add('babel-polyfill')
  // ============解决ie11兼容ES6 end============
}
```

## 【3】ts-import-plugin 按需加载UI组件

```js
const tsImportPluginFactory = require("ts-import-plugin")
{
  chainWebpack: config => {
    config.module
      .rule("ts")
      .use("ts-loader")
      .tap(options => {
        options = merge(options, {
          transpileOnly: true,
          getCustomTransformers: () => ({
            before: [
              tsImportPluginFactory({
                libraryName: "vant",
                libraryDirectory: "es",
                style: true,
              })
            ]
          }),
          compilerOptions: {
            module: "es2015",
          }
        })
        return options
      })
  }
}
  
```

## 【4/1】CND 优化加速

 [【CDN案例】](https://github.com/leijin0416/Vue-Plug-in_unit/blob/master/9-01%E3%80%81webpack-CDN.md)

（1）、[参考 1](https://juejin.im/post/5ddc8a6be51d4523275838db#heading-9)

（2）、[参考 2](https://segmentfault.com/a/1190000016178566?utm_source=tag-newest)

## 【4/2】webpack-bundle-analyzer 分析webapck构建打包后的文件

BundleAnalyzerPlugin 是分析 Webpack 生成的包体组成并且以可视化的方式反馈给开发者的插件

## 【4/3】hard-source-webpack-plugin 在启动项目时会针对项目生成缓存

若是项目无package或其他变化，下次就不用花费时间重新构建，直接复用缓存。

## 【5】externals 配置选项

将指定的内容排除在构建的vendor中，但是，指定的内容需要出现在用户环境中。

## 【6】Dllplugin & DllReferencePlugin 分包插件,提取公共库

预编译资源模块，加快打包速度

[地址](https://blog.csdn.net/qq_15253407/article/details/90077207)

## 【7】webpack常用的loader

- `样式：`style-loader、css-loader、less-loader、sass-loader等

- `文件：`raw-loader、file-loader 、url-loader等

- `编译：`babel-loader、coffee-loader 、ts-loader等

- `校验测试：`mocha-loader、jshint-loader 、eslint-loader等

## 【10】ParallelUglifyPlugin 优化压缩，加快构建速度

ParallelUglifyPlugin 插件则会开启多个子进程，把对多个文件压缩的工作分别给多个子进程去完成，但是每个子进程还是通过UglifyJS去压缩代码。无非就是变成了并行处理该压缩了，并行处理多个子任务，效率会更加的提高。

- 把在 HappyPack 中的多进程并行处理的思想也引入到代码压缩中；

- 使用 ParallelUglifyPlugin 也非常简单，把原来Webpack配置文件中内置的 UglifyJsPlugin 去掉后，再替换成 ParallelUglifyPlugin；

[构建优化 -ParallelUglifyPlugin](https://www.cnblogs.com/tugenhua0707/p/9569762.html) --|-- [构建优化 -使用 HappyPack](https://www.jianshu.com/p/2487e3c5421e)

```js
// 引入 ParallelUglifyPlugin 插件
const ParallelUglifyPlugin = require('webpack-parallel-uglify-plugin');
{
  configureWebpack: config => {
    // 生产环境打包分析体积
    if (isDev === 'production') {
      config.plugins.push(
        new ParallelUglifyPlugin({
          // 传递给 UglifyJS 的参数
          uglifyJS: {
            output: {
              // 最紧凑的输出
              beautify: false,
              // 删除所有的注释
              comments: false
            },
            warnings: false,
            compress: {
              reduce_vars: true,
              drop_debugger: true,
              drop_console: true
            }
          },
          test: /.js$/g,
          sourceMap: false // 是否为压缩后的代码生成对应的Source Map
        })
      )
    }
  },
}
```

---

## 实际构建

```js
const path = require('path')
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
const CompressionPlugin = require("compression-webpack-plugin")  // 开启压缩gzip
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

// 定义getAliasPath方法，把相对路径转换成绝对路径
function resolve(dir) { return path.join(__dirname, dir)}

const isDev = process.env.NODE_ENV;  //当前的环境
const productionGzipExtensions = /\.(js|css|json|txt|html|ico|svg)(\?.*)?$/i;

const isDevCS = {
  '/api': {
    target: 'http://114.5200.165.42:6100',
    changeOrigin: isDev === 'development' ? true : false,
    wx: true,
    pathRewrite: {
        "^/api": "/"
    }
  }
}

module.exports = {
  runtimeCompiler: true,
  productionSourceMap: false,
  lintOnSave: false,
  devServer: {
    open: false,
    https: false,
    proxy: isDevCS
  },
  transpileDependencies: [],
  /*
   * 链式操作：
   * - 允许对内部的webpack配置进行更细粒度的修改
   */
  chainWebpack: (config) => {
    // 缩小文件检索解析范围
    config.resolve.alias
      .set('@', resolve('src'))
      .set('aset', resolve('src/assets/img'))
      .set('cpts', resolve('src/components'))

    if (isDev === 'production') {
      return {
        plugins: [
          // 移除 prefetch 插件
          config.plugins.delete('prefetch')
          // 移除 preload 插件
          config.plugins.delete('preload');
        ]
      }
    }
  },
  // 生产环境禁用eslint
  lintOnSave: !process.env.NODE_ENV !== 'production',
  /*
   * 简单配置 loader和plugins
   * - 生产环境打包分析体积
   */
  configureWebpack: config => {
    if (isDev === 'production') {
      config.mode = "production";
      config.plugins.push(
        new UglifyJsPlugin({
          // 删除debugger和console
          uglifyOptions: {
              compress: {
                drop_debugger: true,
                drop_console: true,
                pure_funcs: ['console.log']
            },
            warnings: false,
          },
          sourceMap: false,
          parallel: true, // 多进程并行来提高构建速度
        }),
        // 压缩代码
        new CompressionPlugin({
          algorithm: 'gzip',
          filename: '[path].gz[query]',
          test: productionGzipExtensions,
          threshold: 10240,
          minRatio: 0.8,
          deleteOriginalAssets: false   // 是否删除源文件
        }),
        // 压缩分析提示
        new BundleAnalyzerPlugin()
      )
    } else {
      // 为开发环境修改配置...
      config.mode = "development";
    }
  },
  /**
   *  webpack的css的一些loader
   *  - 支持的 loader:
   *  css-loader, postcss-loader, sass-loader, less-loader, stylus-loader
   */
  css: {
    extract: false, //是否使用css分离插件
    modules: false,
    sourceMap: process.env.NODE_ENV === "production" ? false : true,
    loaderOptions: {
      sass: {
        data: `
        @import "@/style/mixin.scss";
        @import "@/style/_var.scss";
        `
      },
      // 传递给 postcss-loader
      postcss : {
      }
    }
  },
  // 第三方插件的选项
  pluginOptions: {}
}
```
