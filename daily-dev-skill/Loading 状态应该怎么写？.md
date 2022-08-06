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

```tsx
export const SelectNextMarkItemButton = () => {

  const [loading, setLoading] = useState(false);

  return (
    <Button
      loading={loading}
      onClick={async () => {
          setLoading(true)
          // await 请求
		  // 一些操作
          setLoading(false)
        }
      }}
      type="primary"
    >
      xxx
    </Button>
  );
};
```

参考 [从 loading 的 9 种写法谈 React 业务开发](https://juejin.cn/post/6844903757642203144)