这两个 `api` 很相似，我们经常会使用 `useEffect`，那他和 `useLayoutEffect` 有什么区别呢？

> 参考[官方文档](https://reactjs.org/docs/hooks-reference.html#uselayouteffect)
>
> The signature is identical to useEffect, but it fires synchronously after all DOM mutations.


首先要明确的是，这两个 `api` 的函数签名是完全一致的。

他俩的区别在于：
1. `useEffect` 在 `dom` 渲染完后，是延迟调用的。
2. `useLayoutEffect` 则是立即调用。

在 `mini-code` 中 实现 `antd form` 的 `Field` 组件中，使用了 `useLayoutEffect`