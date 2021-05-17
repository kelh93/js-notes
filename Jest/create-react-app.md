# `@testing-library`

### [官方文档](https://testing-library.com/docs/)

## 指导原则

- 如果它涉及到呈现组件，那么它应该处理DOM节点而不是组件实例，而且它不应该鼓励处理组件实例
- 通常，以用户使用它的方式测试应用程序组件将非常有用。 我们在这里做出一些取舍，因为我们使用的是计算机，通常是模拟的浏览器环境，但总的来说，实用程序应鼓励按预期使用组件的方式进行测试
- 实用程序实现和api应该简单而灵活

> @testing-library 无法获取到源代码中的组件内部方法和实例，这是它有意为之的，因为它的中心思想是测试用户使用的页面，也就是已经渲染完成的页面。



`@testing-library`是一个以用户为中心的测试框架，它以我们使用程序的方式进行测试。可以很方便的对测试页面进行操作。

### 常用的npm包

`@testing-library/react` react组件测试库

`@testing-library/jest-dom` 可以自定义jest matcher的库

`@testing-library/user-event` 页面元素点击操作的库

使用示例

```javascript
import {render, fireEvent, screen} from '@testing-library/react';

```

```javascript
import userEvent from '@testing-library/user-event';

test('can render with redux with custom initial state', () => {
  render(<ConnectedCounter />, {initialState: {count: 3}})
	// 页面元素点击
  userEvent.click(screen.getByText('-'))
	//screen页面dom节点查找
  expect(screen.getByTestId('count-value')).toHaveTextContent('2')
})

```









