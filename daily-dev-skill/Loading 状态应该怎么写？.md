这两天写业务的时候，遇到了一点小问题。

`Loading` 的状态，在 `react` 中如何实现呢？

简单的来说，就是使用 `useState` 去实现。

```tsx
const [loading, setLoading] = useState(true)

useEffect(() => {
	setLoading(true)
	// 请求, 也可以用 Promise 的写法
	await xxx;
	setLoading(false)
}, [])
```

再举个例子：



参考 [从 loading 的 9 种写法谈 React 业务开发](https://juejin.cn/post/6844903757642203144)