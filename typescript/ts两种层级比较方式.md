# ts 两种层级比较方式————类型信息的层面、结构化类型系统层面

```ts
type Result1 = {} extends object ? 1 : 2; // 1 
type Result2 = object extends {} ? 1 : 2; // 1 

type Result3 = object extends Object ? 1 : 2; // 1 
type Result4 = Object extends object ? 1 : 2; // 1 

type Result5 = Object extends {} ? 1 : 2; // 1 
type Result6 = {} extends Object ? 1 : 2; // 1 
```

12 和 56 这两对，难道说明 `{}` 和 `object` 类型相等，也和 `Object` 类型一致？当然不，这里的 `{} extends` 和 `extends {}` 实际上是两种完全不同的比较方式。

`{} extends object` 和 `{} extends Object` 意味着， `{}` 是 `object` 和 `Object` 的字面量类型，是从**类型信息的层面**出发的，即字面量类型在基础类型之上提供了更详细的类型信息。`object extends {}` 和 `Object extends {}` 则是从**结构化类型系统**的比较出发的，即 `{}` 作为一个一无所有的空对象，几乎可以被视作是所有类型的基类，万物的起源。如果混淆了这两种类型比较的方式，就可能会得到 `string extends object` 这样的错误结论。

而 `object extends Object` 和 `Object extends object` 这两者的情况就要特殊一些，它们是因为“系统设定”的问题，`Object` 包含了所有除 `Top Type` 以外的类型（基础类型、函数类型等），`object` 包含了所有非原始类型的类型，即数组、对象与函数类型，这就导致了你中有我、我中有你的神奇现象。

类似的还有 `any`：

```ts
type Result1 = Object extends any ? 1 : 2; // 1
type Result2 = Object extends unknown ? 1 : 2; // 1

type Result3 = any extends Object ? 1 : 2; // 1 | 2
type Result4 = unknown extends Object ? 1 : 2; // 2
```

前两条符合预期，而后面两条却很奇怪。

这气势还是因为“系统设定”的原因。`any` 代表了任何可能的类型，当我们使用 `any extends` 时，它包含了“让条件成立的一部分”，以及“让条件不成立的一部分”。而从实现上说，在 `TypeScript` 内部代码的条件类型处理中，如果接受判断的是 `any`，那么会直接返回条件类型结果组成的联合类型。
