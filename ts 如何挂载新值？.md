## 如果在函数上挂载一个值不报错？

```ts
interface fn {
  (val: number): number;
  a: string;
  b: number;
}

const func: fn = () => {
  console.log("test");
  return 1;
};
func.a = "a";
func.b = 1;
```

## 如何在函数组件上挂载一个值不报错？

```tsx
const _Component = () => {
  return <div>这是一个tsx组件</div>;
};

type _ComponentType = typeof _Component;
interface ComponentType extends _ComponentType {
  a: string;
  b: number;
}

const Component = _Component as ComponentType;
Component.a = "a";
Component.b = 1;
```
