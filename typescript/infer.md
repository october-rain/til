# ts 中 infer 的使用技巧

## 含义

`infer` 表示待推断的类型。它**只能在条件类型中使用**。

## examples

还是得看实际的例子学习。

### 推断函数的返回值

```ts
type Func = (...args: any[]) => any;
type FunctionReturnType<T extends Func> = T extends (
  ...args: any[]
) => infer R
  ? R
  : never;

type StringResult = FunctionConditionType<() => string>; // string
type NonStringResult1 = FunctionConditionType<() => boolean>; // boolean
type NonStringResult2 = FunctionConditionType<() => number>; // number
```

### Swap

```ts
type Swap<T extends any[]> = T extends [infer A, infer B] ? [B, A] : T;

type SwapResult1 = Swap<[1, 2]>; // 符合元组结构，首尾元素替换[2, 1]
type SwapResult2 = Swap<[1, 2, 3]>; // 不符合结构，没有发生替换，仍是 [1, 2, 3]
```

下面又几个特别的例子。

```ts
// 提取首尾两个
type ExtractStartAndEnd<T extends any[]> = T extends [
  infer Start,
  ...any[],
  infer End
]
  ? [Start, End]
  : T;

// 调换首尾两个
type SwapStartAndEnd<T extends any[]> = T extends [
  infer Start,
  ...infer Left,
  infer End
]
  ? [End, ...Left, Start]
  : T;

// 调换开头两个
type SwapFirstTwo<T extends any[]> = T extends [
  infer Start1,
  infer Start2,
  ...infer Left
]
  ? [Start2, Start1, ...Left]
  : T;
```

除了数组，`infer` 结构也可以是接口：

```ts
// 提取对象的属性类型
type PropType<T, K extends keyof T> = T extends { [Key in K]: infer R }
  ? R
  : never;

type PropTypeResult1 = PropType<{ name: string }, 'name'>; // string
type PropTypeResult2 = PropType<{ name: string; age: number }, 'name' | 'age'>; // string | number

// 反转键名与键值
type ReverseKeyValue<T extends Record<string, unknown>> = T extends Record<infer K, infer V> ? Record<V & string, K> : never

type ReverseKeyValueResult1 = ReverseKeyValue<{ "key": "value" }>; // { "value": "key" }
```

递归的提取多层的 `promise`

```ts
type PromiseValue<T> = T extends Promise<infer V> ? PromiseValue<V> : T;

type PromiseValueResult = PromiseValue<Promise<Promise<boolean>>>; // boolean
```
