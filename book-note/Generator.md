# Generator-生成器函数

### 核心概念

generator函数实现了一个状态机，可以使用next方法改变函数内部状态，从而得到不同的值。



### 定义

在es6中，generator函数的表示方法。

```javascript
function * fn(){
	yield 'hello';
    yield 'world';
    return 'ck'
};
const res = fn(); // res不是函数的返回结果。
```

在普通函数声明中，`function`与函数名称直接增加一个`*`（星号），内部使用`yield`返回每一步的状态，这样的函数就是`Generator`函数。

### 返回值

```javascript
function * fn(){
	yield '1';
    yield '2';
    return '3';
};
const gen = fn();
// gen 是一个Iterator对象。可以使用for...of进行遍历。
```

`Generator`的返回值是一个`Iterator`对象。`for...of`可以自动执行每一步的`yield` 无需调用`next`。

### next

```javascript
gen.next(); // {value: '1', done: false};
gen.next(); // {value: '2', done: false};
gen.next(); // {value: '3', done: true};
gen.next(); // {value: undefined, done: true};
```

### yield 表达式

- 只能写在`Generator`函数内部
- 不能在`forEach`中使用







