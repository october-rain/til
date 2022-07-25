# window.open 踩坑

## 使用方法

就是用来打开一个新的页面，第一个参数是链接，第二个参数是打开方式（_blank、_self等）

**注意**：如果调用失败，返回值会是 null。

## 问题

`window.open` 在调用的时候，如果在点击事件中先进行一些异步请求再去 `window.open` 则会被一些浏览器拦截。解决办法有很多，下面的掘金文章里都有写。

但是我试了试，发现本质上还是得把调用的位置移到异步函数之外，但是在项目中很难实现。

最后就使用 `Antd` 中的 `modal` 函数，进行弹窗提醒。并在 `okButtonProps` 中传入参数。

```tsx
import { Modal } from 'antd';
export const showNewTabMsg = (url: string, type: '_blank' | '_self' = '_blank') => {
  Modal.confirm({
    title: `xxx`,
    content: 'xxx',
    okText: '打开',
    okButtonProps: {
      href: url,
      target: type,
    },
  });
};
```

## 参考

[MDN window.open](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/open)
[掘金文章](https://juejin.cn/post/6918728715924930574)
