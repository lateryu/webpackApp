
1. 理解webpack

```js
  module.exports = {
  entry: "./src/main.js",
  output: {
    filename: "build.js",
    path: __dirname + '/assets/',
    publicPath: "/assets/"
  },
  module: {
    loaders: [
      {test: /.css$/, loader: 'style!css'},
      {test: /.(png|jpg)$/, loader: 'url-loader?limit=8192'}
    ]
  }
  resolve: {
    extensions: ['', '.js', '.jsx'],
    //模块别名定义，方便后续直接引用别名，无须多写长长的地址
    alias: {
        a : 'js/assets/a.js',  // 后面直接引用 require(“a”)即可引用到模块
        b : 'js/assets/b.js',
        c : 'js/assets/c.js'
    }
  },
  plugins: [commonsPlugin, new ExtractTextPlugin("[name].css")]
}
```

  1. module.loader: 其中test是正则表达式，对符合的文件名使用相应的加载器./.css$/会匹配 xx.css文件，但是并不适用于xx.sass或者xx.css.zip文件.   

  2. url-loader: 它会将样式中引用到的图片转为模块来处理; 配置信息的参数“?limit=8192”表示将所有小于8kb的图片都转为base64形式。

  3. entry: 模块的入口文件。依赖项数组中所有的文件会按顺序打包，每个文件进行依赖的递归查找，直到所有模块都被打成包；

  4. output：模块的输出文件，其中有如下参数：

  5. filename: 打包后的文件名

  6. path: 打包文件存放的绝对路径。

  7. publicPath: 网站运行时的访问路径。
  
  8. relolve.extensions: 自动扩展文件的后缀名，比如我们在require模块的时候，可以不用写后缀名的。

  9. relolve.alias: 模块别名定义，方便后续直接引用别名，无须多写长长的地址

  10. plugins 是插件项;

  11. module.filename 开发期间，该行代码所在的文件(包含文件名)
  12. __dirname 始终等于 module.filename 
  13. __dirname 开发期间，该行代码所在的目录
  14. process.cwd()：运行node的工作目录，可以使用  cd /d 修改工作目录。
  15. require.main.filename：用node命令启动的module的filename, 如 node xxx，这里的filename就是这个xxx。
http://www.cnblogs.com/tugenhua0707/p/5576262.html



## 2. html-webpack-plugin学习
* 进入html-webpack-plugin文件运行 npm install 加载webpack.json的plugin
* 加载后执行webpack就能生成buid文件夹了，里面会自动生成 两个文件 index.html和index.js文件

html-webpack-plugin还支持一下配置

1. title: 用于生成的HTML文件的标题。   
2. filename: 用于生成的HTML文件的名称，默认是index.html。你可以在这里指定子目录。  
3. template: 模板文件路径，支持加载器，比如 html!./index.html  
4. inject: true | 'head' | 'body' | false ,注入所有的资源到特定的 template 或者 templateContent 中，如果设置为 true 或者 body，所有的 javascript 资源将被放置到 body 元素的底部，'head' 将放置到 head 元素中。
5. favicon: 添加特定的 favicon 路径到输出的 HTML 文件中。
6. minify:{ //压缩HTML文件
removeComments:true, //移除HTML中的注释
collapseWhitespace:true //删除空白符与换行符
}
7. hash: true | false, 如果为 true, 将添加一个唯一的 webpack 编译 hash 到所有包含的脚本和 CSS 文件，对于解除 cache 很有用。
8. cache: true | false，如果为 true, 这是默认值，仅仅在文件修改之后才会发布文件。
9. showErrors: true | false, 如果为 true, 这是默认值，错误信息会写入到 HTML 页面中
10. chunks: 允许只添加某些块 (比如，仅仅 unit test 块)
11. chunksSortMode: 允许控制块在添加到页面之前的排序方式，支持的值：'none' | 'default' | {function}-default:'auto'
12. excludeChunks: 允许跳过某些块，(比如，跳过单元测试的块)

比如我现在webpack.config.js配置改为如下：

```js
var path = require('path');
var HtmlwebpackPlugin = require('html-webpack-plugin');

//定义了一些文件夹的路径
var ROOT_PATH = path.resolve(__dirname);

var SRC_PATH = path.resolve(ROOT_PATH, 'src');

var BUILD_PATH = path.resolve(ROOT_PATH, 'build');
console.log(SRC_PATH)
module.exports = {
  
  entry: SRC_PATH + "/js/index.js",
  //输出的文件名 合并以后的js会命名为index.js
  output: {
    path: BUILD_PATH,
    filename: 'index.js'
  },
  //添加我们的插件 会自动生成一个html文件
  plugins: [
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      filename: '1.0.0/home.html',
      inject: true,
      hash: true
    })
  ]
};

```

通过在配置文件中添加多次这个插件，来生成多个 HTML 文件。
webpack.config.js代码如下：
```js
var path = require('path');
var HtmlwebpackPlugin = require('html-webpack-plugin');

//定义了一些文件夹的路径
var ROOT_PATH = path.resolve(__dirname);

var SRC_PATH = path.resolve(ROOT_PATH, 'src');

var BUILD_PATH = path.resolve(ROOT_PATH, 'build');
console.log(SRC_PATH)
module.exports = {
  
  entry: SRC_PATH + "/js/index.js",
  //输出的文件名 合并以后的js会命名为index.js
  output: {
    path: BUILD_PATH,
    filename: 'index.js'
  },
  //添加我们的插件 会自动生成一个html文件
  plugins: [
    new HtmlwebpackPlugin(),
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      filename: 'app1/home.html',
      template: 'src/html/index.html',
      inject: true,
      hash: true
    })
  ]
};

```

也可以在配置项 加上minify选项 压缩HTML文件；代码如下：

```js
var path = require('path');
var HtmlwebpackPlugin = require('html-webpack-plugin');
//定义了一些文件夹的路径
var ROOT_PATH = path.resolve(__dirname);

var SRC_PATH = path.resolve(ROOT_PATH, 'src');

var BUILD_PATH = path.resolve(ROOT_PATH, 'build');
console.log(SRC_PATH)
module.exports = {
  
  entry: SRC_PATH + "/js/index.js",
  //输出的文件名 合并以后的js会命名为index.js
  output: {
    path: BUILD_PATH,
    filename: 'index.js'
  },
  //添加我们的插件 会自动生成一个html文件
  plugins: [
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      minify:{ //压缩HTML文件
        removeComments:true,    //移除HTML中的注释
        collapseWhitespace:true    //删除空白符与换行符
      }
    })
  ]
};

```

## 3. 压缩js与css

* webpack已经内嵌了uglifyJS来完成对JS与CSS的压缩混淆，无需引用额外的插件。

```js
new webpack.optimize.UglifyJsPlugin({    //压缩代码
   compress: {
       warnings: false
   },
   except: ['$super', '$', 'exports', 'require']    //排除关键字
})
```

## 4. 理解less-loader加载器的使用
  less-loader加载器是把css代码转化到style标签内
## 5.理解babel-loader加载器

babel-loader加载器能将ES6的代码转换成ES5代码，我们需要安装babel-loader
执行命令：npm install babel-loader --save-dev 
```js 
var path = require('path');
var HtmlwebpackPlugin = require('html-webpack-plugin');
//定义了一些文件夹的路径
var ROOT_PATH = path.resolve(__dirname);

var SRC_PATH = path.resolve(ROOT_PATH, 'src');

var BUILD_PATH = path.resolve(ROOT_PATH, 'build');

module.exports = {
  entry: SRC_PATH + "/js/index.js",
  output: {
    filename: "build.js",
    path: BUILD_PATH
  },
  module: {
    loaders: [
      //.css 文件使用 style-loader 和 css-loader 来处理
      {
        test: /\.less$/,
        loader: "style!css!less"
      },
      {
        test: /\.js$/, 
        loader: 'babel'
      }
    ]
  },
  resolve: {
    extensions: ['', '.js', '.jsx']
  },
  plugins: [
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      filename: 'index.html',
      template: 'src/html/index.html',
      inject: true,
      hash: true
    })
  ]
};

```

## 6.理解 extract-text-webpack-plugin(独立打包样式文件)

执行安装命令：
sudo npm install extract-text-webpack-plugin 
然后再webpack.config.js 加入加载器配置项如下代码：
```js
var path = require('path');
var HtmlwebpackPlugin = require('html-webpack-plugin');
var ExtractTextPlugin = require('extract-text-webpack-plugin');

//定义了一些文件夹的路径
var ROOT_PATH = path.resolve(__dirname);

var SRC_PATH = path.resolve(ROOT_PATH, 'src');

var BUILD_PATH = path.resolve(ROOT_PATH, 'build');

module.exports = {
  entry: SRC_PATH + "/js/index.js",
  output: {
    filename: "build.js",
    path: BUILD_PATH
  },
  module: {
    loaders: [
      //.css 文件使用 style-loader 和 css-loader 来处理
      {
        test: /\.less$/,
        loader: ExtractTextPlugin.extract(
            'css?sourceMap!' +
            'less?sourceMap'
        )
      },

      {
        test: /\.js$/, 
        loader: 'babel'
      }
    ]
  },
  resolve: {
    extensions: ['', '.js', '.jsx']
  },
  plugins: [
    // 内联css提取到单独的styles的css
    new ExtractTextPlugin("index.css"),
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      filename: 'index.html',
      template: 'src/html/index.html',
      inject: true,
      hash: true
    })
  ]
};

```

## 7. webpack打包多个资源文件

 我们在开发页面的时候，有时候需要有多个入口文件，做到文件是按需加载，这样就可以使用缓存提升性能；

只需要像如下编码即可：
```js
module.exports = {
  entry: {
     "main": "./src/a.js",
     "index": "./src/index.js"
  },
  output: {
    filename: "[name].js"
  }
};
```

webpack.config.js代码如下：

```js
var path = require('path');
var HtmlwebpackPlugin = require('html-webpack-plugin');
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var webpack = require("webpack");
//定义了一些文件夹的路径
var ROOT_PATH = path.resolve(__dirname);

var SRC_PATH = path.resolve(ROOT_PATH, 'src');

var BUILD_PATH = path.resolve(ROOT_PATH, 'build');

module.exports = {
  entry: {
     "a": SRC_PATH + "/js/a.js",
     "index": SRC_PATH + "/js/index.js",
  },
  output: {
    filename: "[name].js",
    path: BUILD_PATH
  },
  module: {
    loaders: [
      //.css 文件使用 style-loader 和 css-loader 来处理
      {
        test: /\.less$/,
        loader: ExtractTextPlugin.extract(
            'css?sourceMap!' +
            'less?sourceMap'
        )
      },

      {
        test: /\.js$/, 
        loader: 'babel'
      }
    ]
  },
  resolve: {
    extensions: ['', '.js', '.jsx']
  },
  plugins: [
    // 内联css提取到单独的styles的css
    new ExtractTextPlugin("index.css"),
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      filename: 'index.html',
      template: 'src/html/index.html',
      inject: true,
      hash: true
    }),
    new webpack.optimize.UglifyJsPlugin({    //压缩代码
       compress: {
           warnings: false
       },
       except: ['$super', '$', 'exports', 'require']    //排除关键字
    })
  ]
};
```

8. webpack对图片打包

首先先安装 url-loader插件；
sudo npm install --save-dev url-loader
在webpack.config.js代码配置加入如下：
{
test: /.(png|jpg)$/, 
loader: 'url?limit=8192'
}
webpack.config.js所有代码如下：

```js
var path = require('path');
var HtmlwebpackPlugin = require('html-webpack-plugin');
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var webpack = require("webpack");
//定义了一些文件夹的路径
var ROOT_PATH = path.resolve(__dirname);

var SRC_PATH = path.resolve(ROOT_PATH, 'src');

var BUILD_PATH = path.resolve(ROOT_PATH, 'build');

module.exports = {
  
  entry: {
     "a": SRC_PATH + "/js/a.js",
     "index": SRC_PATH + "/js/index.js",
  },
  output: {
    filename: "[name].js",
    path: BUILD_PATH
  },
  module: {
    loaders: [
      //.css 文件使用 style-loader 和 css-loader 来处理
      {
        test: /\.less$/,
        loader: ExtractTextPlugin.extract(
            'css?sourceMap!' +
            'less?sourceMap'
        )
      },

      {
        test: /\.js$/, 
        loader: 'babel'
      },
      {
        test: /.(png|jpg)$/, 
        loader: 'url?limit=8192&name=img/[hash:8].[name].[ext]'
      }
    ]
  },
  resolve: {
    extensions: ['', '.js', '.jsx']
  },
  plugins: [
    // 内联css提取到单独的styles的css
    new ExtractTextPlugin("index.css"),
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      filename: 'index.html',
      template: 'src/html/index.html',
      inject: true,
      hash: true
    }),
    new webpack.optimize.UglifyJsPlugin({    //压缩代码
       compress: {
           warnings: false
       },
       except: ['$super', '$', 'exports', 'require']    //排除关键字
    })
  ]
};
```

## 9.学习web-dev-server 创建服务器及动态监听css及js文件的改变；
在webpack中，我们经常使用webpack-dev-server作为开发服务器，用于实时监听和打包编译静态资源，这样每当我们修改js、css等等文件时，客户端（如浏览器等）能够自动刷新页面，展示实时的页面效果。

webpack-dev-server只监听webpack.config.js中entry入口下文件（如js、css等等）的变动，

只有这些文件的变动才会触发实时编译打包与页面刷新，但是html文件更改后保存不能监听新内容到，但是对于开发影响不大，我们在编写css文件
或者js文件的时候保存后，会自动刷新页面，所以html页面也会自动更新到；

首先需要进入我们的项目的根目录下需要安装webpack-dev-server 安装命令如下：
sudo npm install --save-dev webpack-dev-server

安装完成后，需要在webpack.config.js文件配置下；如下代码：

```js
var path = require('path');
var HtmlwebpackPlugin = require('html-webpack-plugin');
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var webpack = require("webpack");

//定义了一些文件夹的路径
var ROOT_PATH = path.resolve(__dirname);

var SRC_PATH = path.resolve(ROOT_PATH, 'src');

var BUILD_PATH = path.resolve(ROOT_PATH, 'build');

module.exports = {

  entry: {
     "a": SRC_PATH + "/js/a.js",
     "index": SRC_PATH + "/js/index.js"
  },
 
  output: {
    filename: "/js/[name].js",
    path: BUILD_PATH
  },
  module: {
    loaders: [
      //.css 文件使用 style-loader 和 css-loader 来处理
      {
        test: /\.less$/,
        loader: ExtractTextPlugin.extract(
            'css?sourceMap!' +
            'less?sourceMap'
        )
      },

      {
        test: /\.js$/, 
        loader: 'babel'
      },
      {
        test: /.(png|jpg)$/, 
        loader: 'url?limit=8192&name=images/[hash:8].[name].[ext]'
      }
    ]
  },
  resolve: {
    extensions: ['', '.js', '.jsx']
  },
  plugins: [
    // 内联css提取到单独的styles的css
    new ExtractTextPlugin("/css/index.css"),
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      filename: 'html/index.html',
      template: 'src/html/index.html',
      inject: true,
      hash: true
    }),
    new webpack.optimize.UglifyJsPlugin({    //压缩代码
       compress: {
           warnings: false
       },
       except: ['$super', '$', 'exports', 'require']    //排除关键字
    })
  ]
};
```
接着需要创建一个webpack-config-dev.js文件，该文件的作用是创建本地服务器，及实时监听css及js文件的改变；代码如下：
```js
var path = require('path')
var webpack = require('webpack');
var HtmlwebpackPlugin = require('html-webpack-plugin');
var ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
  devtool: 'cheap-eval-source-map',
  entry: [
    'webpack-dev-server/client?http://127.0.0.1:8080',
    'webpack/hot/dev-server',
    './src/js/index',
    './src/js/a',
    './src/less/main.less'
  ],
  output: {
    path: path.join(__dirname, 'build'),
    filename: '/js/[name].js'
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      filename: 'html/index.html',
      template: 'src/html/index.html',
      inject: true,
      hash: true
    }),
    new ExtractTextPlugin("index.css")
  ],
  module: {
    loaders: [
      //.css 文件使用 style-loader 和 css-loader 来处理
      {
        test: /\.less$/,
        loader: ExtractTextPlugin.extract(
            'css?sourceMap!' +
            'less?sourceMap'
        )
      },

      {
        test: /\.js$/, 
        loader: 'babel'
      },
      {
        test: /.(png|jpg)$/, 
        loader: 'url?limit=8192&name=images/[hash:8].[name].[ext]'
      }
    ]
  },
  devServer: {
    contentBase: './dist',
    hot: true
  }
}
```

上面只是在开发环境配置的；我们还需要一个线上环境，进行打包，我们还需要使用一个线上环境打包的配置；我们可以新建一个叫webpack.config.prod.js文件； 该文件的配置用于在生产环境的打包；配置代码如下：
```js
var path = require('path')
var webpack = require('webpack');
var HtmlwebpackPlugin = require('html-webpack-plugin');
var ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = {
  devtool: 'source-map',
  entry: ['./src/js/index','./src/js/a'],
  output: {
    path: path.join(__dirname, 'build'),
    filename: '/js/[name].js'
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      filename: 'html/index.html',
      template: 'src/html/index.html',
      inject: true,
      hash: true
    }),
    new ExtractTextPlugin("index.css")
  ],
  module: {
    loaders: [
      //.css 文件使用 style-loader 和 css-loader 来处理
      {
        test: /\.less$/,
        loader: ExtractTextPlugin.extract(
            'css?sourceMap!' +
            'less?sourceMap'
        )
      },

      {
        test: /\.js$/, 
        loader: 'babel'
      },
      {
        test: /.(png|jpg)$/, 
        loader: 'url?limit=8192&name=images/[hash:8].[name].[ext]'
      }
    ]
  }
}
```

package.json文件

```js
{
  "name": "html-webpack-plugin",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "build": "webpack --config webpack.config.prod.js",
    "dev": "webpack-dev-server --config webpack.config.dev.js"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "babel-core": "^6.9.1",
    "babel-loader": "^6.2.4",
    "css-loader": "^0.23.1",
    "file-loader": "^0.8.5",
    "html-webpack-plugin": "^2.19.0",
    "http-server": "^0.9.0",
    "less": "^2.7.1",
    "less-loader": "^2.2.3",
    "react-hot-loader": "^1.3.0",
    "style-loader": "^0.13.1",
    "url-loader": "^0.5.7",
    "webpack": "^1.13.1",
    "webpack-dev-server": "^1.14.1"
  }
  "scripts": {
     "build": "webpack --config webpack.config.prod.js",
     "dev": "webpack-dev-server --config webpack.config.dev.js"
}
}
```

## 10.assets-webpack-plugin插件解决html文件的版本号的问题；
我们可以通过webpack的 assets-webpack-plugin 插件生成一个记录了版本号的文件；详细的可以看官网地址是： https://www.npmjs.com/package/assets-webpack-plugin;
首先我们需要在我们的项目下安装该插件：安装命令如下：
npm install assets-webpack-plugin --save-dev
只需要在webpack.config.json添加如下代码：
```js
var AssetsPlugin = require('assets-webpack-plugin');
new AssetsPlugin({
    filename: 'build/webpack.assets.js',
    processOutput: function (assets) {
        return 'window.WEBPACK_ASSETS = ' + JSON.stringify(assets);
    }
})
```


我们现在来在webpack.config.js配置项如下
```js
var path = require('path');
var HtmlwebpackPlugin = require('html-webpack-plugin');
var ExtractTextPlugin = require('extract-text-webpack-plugin');

//定义了一些文件夹的路径
var ROOT_PATH = path.resolve(__dirname);

var SRC_PATH = path.resolve(ROOT_PATH, 'src');

var BUILD_PATH = path.resolve(ROOT_PATH, 'build');

var AssetsPlugin = require('assets-webpack-plugin');

module.exports = {
  entry: {
     "a": SRC_PATH + "/js/a.js",
     "index": SRC_PATH + "/js/index.js"
  },
  output: {
    path: path.join(__dirname, "build"),
    filename: "js/[name]-[chunkhash:8].js",
  },
  module: {
    loaders: [
      //.css 文件使用 style-loader 和 css-loader 来处理
      {
        test: /\.less$/,
        loader: ExtractTextPlugin.extract(
            'css?sourceMap!' +
            'less?sourceMap'
        )
      },

      {
        test: /\.js$/, 
        loader: 'babel'
      }
    ]
  },
  resolve: {
    extensions: ['', '.js', '.jsx']
  },
  plugins: [
    // 内联css提取到单独的styles的css
    new ExtractTextPlugin("css/index.css"),
    new HtmlwebpackPlugin({
      title: 'Hello World app',
      filename: 'html/index.html',
      template: 'src/html/index.html',
      inject: true
    }),
    new AssetsPlugin({
        filename: 'build/webpack.assets.js',
        processOutput: function (assets) {
            return 'window.WEBPACK_ASSETS = ' + JSON.stringify(assets);
        }
    })
  ]
};

```


## 11.webpack关于同步加载和异步加载的问题
