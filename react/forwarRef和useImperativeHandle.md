## 作用

### forwardRef

参考[简书文章](https://www.jianshu.com/p/bf9f66ac3f9c)

`forwardRef` 的作用是：函数组件不能直接接受 `ref`，所以要用 `forwardRef` 进行处理。
> 参考 [forwardRef](https://reactjs.org/docs/react-api.html#reactforwardref) 官方文档
>
> `React.forwardRef` creates a React component that forwards the ref attribute it receives to another component below in the tree. 

### useImperativeHandle

`useImperativeHandle` 的作用是：把函数签名的第二个 `createHandle` 的返回值，传给 `ref.current`
> 参考 [useImperativeHandle](https://reactjs.org/docs/hooks-reference.html#useimperativehandle) 官方文档
>
> `useImperativeHandle(ref, createHandle, [deps])`
>
> `useImperativeHandle` customizes the instance value that is exposed to parent components when using ref

## 示例

### 官方示例

示例一：
```tsx
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// You can now get a ref directly to the DOM button:
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```

示例二：
```tsx
function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} {...props} />;
}
FancyInput = forwardRef(FancyInput);
```

### MiniForm

> [`index.tsx` 源码`](https://github.com/october-rain/mini-code/blob/master/mini-antd-form/src/component/mini-rc-form/index.tsx)

```tsx
// index.tsx (通常也会直接在 Form.tsx 里进行处理)
// forwardRef 的处理方式，使得 Form 组件可以接收 ref 了
const Form = React.forwardRef(_Form) as (props: any) => React.ReactElement;
```

接收 `ref`，并将实例反弹给祖先组件。
> [`Form.tsx` 源码](https://github.com/october-rain/mini-code/blob/master/mini-antd-form/src/component/mini-rc-form/Form.tsx)

```tsx
// Form.tsx
const Form = (props, ref) => {
  ...
  // 把 formInstance 传给祖先组件
  React.useImperativeHandle(ref, () => formInstance)
  ...
  return <></>
}

export default Form;
```