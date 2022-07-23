# declare 的使用方法

## 进行类型判断

### 正常的方法

不使用 `declare` 的时候：需要给 `obj` 显式的声明值 `{ a: { b: [{ name: 'obj' }] } }`，否则会报错。如下所示：

```ts
const obj: {
  a?: {
    b?: {
      name?: string
    }[]
  }
} = { a: { b: [{ name: 'obj' }] } }

```

### 使用 declare

在**只是想要进行类型比较**的前提下，其实并没有必要真的去声明两个变量，即涉及了值空间的操作。

我们完全可以只在类型空间中（你可以理解为用于存放 `TypeScript` 类型信息的内存空间）比较这些类型，只需要使用 `declare` 关键字：

使用 `declare` 的时候：不需要给 `obj` 显式的声明值，直接可以进行类型的判断。如下所示：

```ts
declare const obj: {
  a?: {
    b?: {
      name?: string
    }[]
  }
}

console.log(obj.a?.b?.[0].name)
```

## 其他用法（在 .d.ts 文件中使用）

参考官方文档 [Declaration Reference](https://www.typescriptlang.org/docs/handbook/declaration-files/by-example.html)
