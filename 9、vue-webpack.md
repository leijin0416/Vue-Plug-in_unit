# webpack

（1）、调整webpack配置最简单的方式就是`在vue.config.js中的 configureWebpack 选项提供一个对象`。该对象将会被webpack-merge合并如最终的webpack配置。在configureWebpack里可以配置webpack的loader和plugins等

（2）、Vue CLI 内部的 webpack 配置是通过` webpack-chain `维护的。这个库提供了一个 webpack 原始配置的上层抽象，使其可以定义具名的 loader 规则和具名插件，并有机会在后期进入这些规则并对它们的选项进行修改。

它允许我们更细粒度的控制其内部配置。

构建流程：https://fed.taobao.org/blog/2016/09/10/webpack-flow/

### 1、treemap sizes：

- stat：文件的“输入”大小，在任何转换（如缩小）之前，它被称为“stat-size”，因为它是从Webpack的stats对象获得的。

- parsed：这是文件的“输出”大小。如果使用的是像 uglify 这样的 Webpack插件，那么这个值将反映代码的小型化。

- gzipped：这是通过gzip压缩运行解析的包/模块的大小。

### 2、开启压缩gzip

```js
npm install -D compression-webpack-plugin

// 引入
const CompressionPlugin = require("compression-webpack-plugin")
```

### 3、代码优化 uglifyjs-webpack-plugin 压缩

减少打包体积

```js
npm install -D uglifyjs-webpack-plugin

const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
```

### 4、externals 配置选项

将指定的内容排除在构建的vendor中，但是，指定的内容需要出现在用户环境中。

### 5、Dllplugin & DllReferencePlugin 分包插件,提取公共库

预编译资源模块，加快打包速度

https://blog.csdn.net/qq_15253407/article/details/90077207

### 6、webpack-bundle-analyzer

BundleAnalyzerPlugin 是分析 Webpack 生成的包体组成并且以可视化的方式反馈给开发者的插件

### 7、webpack常用的loader

- `样式：`style-loader、css-loader、less-loader、sass-loader等

- `文件：`raw-loader、file-loader 、url-loader等

- `编译：`babel-loader、coffee-loader 、ts-loader等

- `校验测试：`mocha-loader、jshint-loader 、eslint-loader等

--- 

```js
const path = require('path')
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
// 开启压缩gzip
const CompressionPlugin = require("compression-webpack-plugin")
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

function resolve(dir) {
    return path.join(__dirname, dir)
}

const isDevBD = {
    '/api': {
        target: 'http://192.168.1.131:6100',
        changeOrigin: true,
        wx: true,
        pathRewrite: {
            "^/api": "/"
        }
    }
}
const isDevCS = {
    '/api': {
        target: 'http://114.****.165.42:6100',
        changeOrigin: true,
        wx: true,
        pathRewrite: {
            "^/api": "/"
        }
    }
}
// 正式
const isDevXQ = {
    '/api': {
        target: 'http://39.****.227.64:6100',
        changeOrigin: true,
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
    // 链式操作：允许对内部的webpack配置进行更细粒度的修改
    chainWebpack: (config) => {
        config.resolve.alias
            .set('@', resolve('src'))
            .set('aset', resolve('src/assets/img'))
            .set('cpts', resolve('src/components'))

        if (process.env.NODE_ENV === 'production') {
            return {
                plugins: [
                    // 压缩代码
                    new CompressionPlugin({
                        algorithm: 'gzip',
                        test: /\.(js|css|json|txt|html|ico|svg)(\?.*)?$/i,    // 匹配文件名
                        threshold: 10240,                // 对超过10k的数据压缩
                        minRatio: 0.8,
                        deleteOriginalAssets: false     // true 不删除源文件 false 删除源文件
                    })
                ],
            }
        }
    },
    // 简单配置 loader和plugins
    configureWebpack: config => {
        if (process.env.NODE_ENV === 'production') {
            // 生产环境打包分析体积
            config.mode = "production";
            config.plugins.push(
                new UglifyJsPlugin({
                    // 删除debugger和console
                    uglifyOptions: {
                        compress: {
                            drop_debugger: true,
                            drop_console: true,  //生产环境自动删除console
                            pure_funcs: ['console.log']
                        },
                        warnings: false,
                    },
                    sourceMap: false,
                    parallel: true, // 多进程并行来提高构建速度
                }),
                // 压缩提示
                new BundleAnalyzerPlugin()
            );
        } else {
            // 为开发环境修改配置...
            config.mode = "development";
        }
    },
    // webpack的css的一些loader
    css: {
        extract: true, //是否使用css分离插件
        loaderOptions: {
            // 支持的 loader:css-loader,postcss-loader,sass-loader,less-loader,stylus-loader
            sass: {
                data: `
                @import "@/style/mixin.scss";
                @import "@/style/_var.scss";
                `
            },
            // 这里的选项会传递给 postcss-loader
            postcss : {
            }
        }
    },
	// 第三方插件的选项
    pluginOptions: {}
};

```
