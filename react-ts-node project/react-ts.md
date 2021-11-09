## babel
### @babel/core
>babel-core 的作用是把 js 代码分析成 ast ，方便各个插件分析语法进行相应的处理。有些新语法在低版本 js 中是不存在的，如箭头函数，rest 参数，函数默认值等，这种语言层面的不兼容只能通过将代码转为 ast，分析其语法后再转为低版本 js。

### @babel/preset-env｜@babel/preset-react｜@babel/preset-typescript
>babel预设的一些转码规则集，这3个分别用来转码es、react、ts

### @babel/plugin-proposal-class-properties
>编译class

### @babel/plugin-proposal-object-rest-spread
>解构赋值

### @babel/plugin-syntax-dynamic-import"
>动态引用模块，实现了懒加载，用到那个模块才会加载那个模块，可以提高SPA应用程序的首屏加载速度

### @babel/runtime
>辅助函数

### @babel/plugin-transform-runtime
>辅助函数按需加载

### @babel/plugin-proposal-decorators
>让react项目支持"decorator"装饰器语法

### babel-plugin-import
>按需引用babel, antd, lodash等模块, 自动引入对应样式和JS文件


## eslint, prettier
### eslint及prettier
`npm install -D eslint prettier eslint-plugin-prettier eslint-config-prettier eslint-plugin-react`

### 添加ts的支持
`npm i -D @typescript-eslint/parser @typescript-eslint/eslint-plugin`

### husky和commitlint
`npm i -S -D husky pretty-quick @commitlint/cli @commitlint/config-conventional`
