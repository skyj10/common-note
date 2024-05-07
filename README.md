# 本文档用于记录开发学习过程中遇到的bug和解决

## vscode：已包含的文件名 “XXX.vue.js“ 仅大小写与文件名 “xxx.vue.js“ 不同
- 遇到import文件报错，一般是因为改了文件名大小写造成，而开发环境中可能存在文件名大小写不敏感的机制，如git，为避免出bug，因此报错提醒开发者注意，解决方法是把文件名拓展名更改后，再更改一次。凡是文件名大小写修改都要照此办理。

## prettier 的代码格式不生效，保存没有自动格式化
- vscode 的设置搜索 `Default Formatter` ,在下拉选项中选择prettier，如果旁边出现 `已在其他位置被修改`，要悬浮上面看提示，看看别的作用域是否也被配置正确

## Next.js 发生TypeError: Cannot read properties of undefined (reading 'clientModules')
- next.js在app目录下不能建立名字为index的文件夹，这样的路由不能被正确索引报错


## next App router 模式下引入组件库出问题
- react ui 的文档只支持page router模式，app router 模式没有_app.js这个根app，app router 模式下根节点在app/layout.js文件中，根组件插入方法
``` js
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <NextUIProvider>{children}</NextUIProvider>
      </body>
    </html>
  )
}

code
```
## react.js 在tailwind.config.js自定义主题不生效
- 自定义主题相关json参数需要在plugins: [nextui({})]条目中定义
```js
module.exports = {
  plugins: [
    nextui({
      themes: {
        "purple-dark": {
            //配置写在这
        }
      }
    })
  ]
}
code
```
## 组件化开发
- next中，approuter模式下，app下默认的页面文件都是服务器渲染ssr模式，因此需要客户端渲染并状态管理的要封装为组件，使用 `'use client'`标记为csr组件，嵌入app下的页面进行调用
- 页面一般用export default来暴露渲染方法，组件一般用export 暴露渲染方法
- import组件的时候要注意用{}来包裹export的具名组件名，如果用export default暴露的，不要加{},而且命名由引入者自定义
  
## commonjs(.js) 和 ECMAjs(.mjs)
- CommonJS 和 ECMAScript 是两种不同的 JavaScript 模块系统。
- CommonJS 是一种模块系统，最初是为服务器端 JavaScript 环境设计的，它使用 require 和 module.exports 来导入和导出模块。
- ECMAScript 模块是 JavaScript 的官方模块系统，它使用 import 和 export 语法来导入和导出模块。
- 主要的区别在于语法和加载时机。CommonJS 是动态加载模块，而 ECMAScript 模块是静态加载模块。这意味着在 CommonJS 中，模块的导入和导出是在运行时进行的，而在 ECMAScript 模块中，这些操作是在解析阶段进行的。
- 在实际应用中，如果你在浏览器环境中使用 JavaScript，通常会使用 ECMAScript 模块。而在 Node.js 等服务器端环境中，你可能会使用 CommonJS 模块。然而，随着现代 JavaScript 的发展，许多环境都已经开始支持 ECMAScript 模块。


在浏览器环境中，如果你想使用 ECMAScript 模块，你可以创建一个名为 "module.mjs" 的文件，其中包含以下内容：
```JavaScript
// module.mjs
const message = "Hello from ECMAScript module";
export { message };
```
然后，你可以在另一个 ECMAScript 模块文件中导入 "module.mjs" 文件中的内容：
```JavaScript
// main.mjs
import { message } from './module.mjs';
console.log(message);
```


在 Node.js 环境中，如果你想使用 CommonJS 模块，你可以创建一个名为 "module.js" 的文件，其中包含以下内容：
```JavaScript
// module.js
const message = "Hello from CommonJS module";
module.exports = { message };
```


然后，你可以在另一个 CommonJS 模块文件中导入 "module.js" 文件中的内容：
```JavaScript
// main.js
const { message } = require('./module.js');
console.log(message);
```





## next 国际化
- 截至2024年四月next-i18next国际化框架折腾了很久，都未能在approuter 模式下成功运用。
- 用next intl库实现国际化配置比较方便可行，可以在approuter模式下实现功能

## chromedriver依赖安装失败 解决办法
### vue 项目 npm install时 出现chromedriver无法安装的情况
- 这个文件时chrome驱动文件，安装不成功会导致npm run dev命令无法执行，报webpack错误
```js
'webpack-dev-server' 不是内部或外部命令，也不是可运行的程序
```
- npm install报错
```npm
npm ERR! ChromeDriver installation failed Error extracting archive: Error: end of central directory record signature not foundv
```
- 根据报错信息中的地址，手动下载zip文件到本地，不要解压
- 输入命令npm install chromedriver --chromedriver_filepath=下载的文件的地址
```js
 npm install chromedriver --chromedriver_filepath=D:\gitCode\chromedriver_win32.zip
```
- 安装成功后，才可运行npm run dev

## eslint
- eslint的强制校验有时候会比较困扰，每次run都会因为格式不规范报错，影响开发效率
- 关闭开启的方法是在eslintrc.js文件中的extend数组中注释standard，注释就不强制校验
- eslint默认校验函数和括号之间要加空格，取消此校验可以在eslintrc.js 的rule 中添加 `"space-before-function-paren": 0,`


## compress plugins 构建95%出错
- 因为node版本过高，要兼容旧版本openssl
- 要在package.json加"scripts": {
    "dev": "SET NODE_OPTIONS=--openssl-legacy-provider && vue-cli-service serve",

