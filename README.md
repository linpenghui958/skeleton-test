# vue-skeleton-test(demo)

> page-skeleton-webapack-plugin demo
> vue骨架屏demo（基于PSWP）

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev
```
``` bash
# enter togglerBar in console and 
```
> In the development page, use CtrlOrCmd + enter to call out the plugin interactive interface, or enter the toggleBar callout interface in the browser's JavaScript console.
![](http://pi82b6lei.bkt.clouddn.com/skeleton11-15-5.png)
Click the button in the interactive interface to preview the skeleton page. This process may take about 20s. After the plugin is ready for the skeleton page, it will automatically open the preview page through the browser, as shown below.
![](http://pi82b6lei.bkt.clouddn.com/111.png)

```bash
# build for production with minification
npm run build
```


### how to use page-skeleton-webpack-plugin
如何使用PSWP插件进行自动化生产多路由骨架片

第一步，新建一个项目，并安装相关依赖
```
	vue init webpack skeleton-test
	cd skeleton-test
	npm install
	npm install --save-dev page-skeleton-webpack-plugin
	npm install --save-dev html-webpack-plugin
```

第二步，然后在build/webpack.base.conf.js中
```js
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { SkeletonPlugin } = require('page-skeleton-webpack-plugin')
const path = require('path')
const webpackConfig = {
  entry: 'index.js',
  output: {
    path: __dirname + '/dist',
    filename: 'index.bundle.js'
  },
  plugin: [
    new HtmlWebpackPlugin({
       // 本身的配置项
    }),
    new SkeletonPlugin({
      pathname: path.resolve(__dirname, '../shell'), // customPath为来存储 shell 文件的地址
      staticDir: path.resolve(__dirname, '../dist'), // 最好和 `output.path` 相同
      routes: ['/', '/test'], // 将需要生成骨架屏的路由添加到数组中，测试根路径和/test路径
    })
  ]
}
```
第三步，运行项目，生产skeleton-page
- 还是在控制台输入toggleBar后点击页面上方的control bar唤醒
![](http://pi82b6lei.bkt.clouddn.com/111.png)
右上角依次为，预览不同路由生产的骨架屏，手机预览，写入本地文件
可以看到现在更新了许多功能，在预览无误后
点击右上方写入本地文件即可进行最后的打包预览
![](http://pi82b6lei.bkt.clouddn.com/222.png)
可以看到项目目录多了一个shell路径，这跟之前设置的保存路径一致

第四步，打包预览效果

```js
	npm run build
	cd dist
	http-server
```
![](http://pi82b6lei.bkt.clouddn.com/skeleton11-15-3.png)
然后进入浏览器预览
看一下根路径
![](http://pi82b6lei.bkt.clouddn.com/screen12.gif)
看一下/test路径
![](http://pi82b6lei.bkt.clouddn.com/skeleton-screen13.gif)

ps！！！前方高能预警
这里是以vue-cli为基础进行的测试
在初始化的模板中，webpack分为base、dev、 prod三个文件
这里因为PSWP为dev和prod都需要的插件，所以放在base文件中
但是在第一次打包后预览，笔者发现prod环境并没有生效
原因是在webpack.prod.conf.js中的HtmlWebpackPlugin配置中
```js
plugins: [
	new HtmlWebpackPlugin({
	  filename: config.build.index,
	  template: 'index.html',
	  inject: true,
	  minify: {
	    // removeComments: true,  移除注释
	    collapseWhitespace: true,
	    removeAttributeQuotes: true
	    // more options:
	    // https://github.com/kangax/html-minifier#options-quick-reference
	  },
	  // necessary to consistently work with multiple chunks via CommonsChunkPlugin
	  chunksSortMode: 'dependency'
	}),
]
```
这里有一项minify的配置为移除注释，查看minify的[文档](https://github.com/kangax/html-minifier#options-quick-reference)
![](http://pi82b6lei.bkt.clouddn.com/skeleton11-15-4.png)
笔者觉得很有可能是这个配置，移除了index.html中的<!-- shell -->给移除了，导致打包后未能生效。
在注释掉这一选项后，dev和prod环境均正常

