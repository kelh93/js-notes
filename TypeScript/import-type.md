# TypeScript基础知识

## import type(V3.8)

> 仅仅导入/导出声明

例子:

```typescript
import type { Something } from './some-module.js';
export type { Something };
```

### importsNotUsedAsValues

​	TS新增一个了配置,用了处理import type的编译结果,

- remove 丢弃这些导入语句. 默认行为
- preserve 保留所有的语句,即使是从来没有被使用. 它可以保留副作用
- error 保留所有的语句,但是当一个值的导入仅仅用于类型时将会抛出错误.

