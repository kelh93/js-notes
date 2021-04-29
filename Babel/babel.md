babel的一些库

## @babel/core

babel核心库



## babel-plugin-macros

可以创建宏

babelPluginMacros.createMacro(handler: MacroHandler, options?: Options) : any;



## @babel/types

babel中的lodash,为方便AST操作提供了很多便捷的api, 参见[@babel/types官方文档](https://babel.docschina.org/docs/en/6.26.3/babel-types/) 



## @babel/parser

Babel解析器(以前为Babylon)是Babel中使用的JavaScript解析器。 参见[@babel/parser官方文档](https://babeljs.io/docs/en/babel-parser)

`babelParser.parse(code, [options])`

`babelParser.parseExpression(code, [options])`

`parse()`解析整个ESMAScript程序代码,而`parseExpression()`试图在保证性能的情况下解析单个表达式.如果有疑惑则直接使用`parse()`









## @babel/traverse

@babel/traverse会自动遍历抽象语法树,



## @babel/template

可以将字符串代码转换成AST节点.

```javascript
import template from '@babel/template';
const ast = template.ast(`
	const axios = require('axios');
`)

```



## faster.js

它将常用的Javascript编译成更快、微优化的Javascript。