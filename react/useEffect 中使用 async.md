[stackoverflow 示例](https://stackoverflow.com/questions/56838392/how-to-call-an-async-function-inside-a-useeffect-in-react)
[devtrium 示例](https://devtrium.com/posts/async-functions-useeffect)


使用案例（不推荐直接将 包裹的函数写成 async ）
```ts
useEffect(() => {
  const fetchData = async () => {
     const data = await getData(1);
     setData(data);
  }

  fetchData();
}, []);
```

```ts
useEffect(() => {

  (async () => {
     const data = await getData(1);
     setData(data);
  })();

}, []);
```

不需要使用 `async` 的好办法

```ts
useEffect(() => {
	getData(1).then(setData);
}, []);
```

