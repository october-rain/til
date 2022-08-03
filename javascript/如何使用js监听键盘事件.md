# 如何使用js监听键盘事件

## KeyboardEvent 键盘事件
[MDN 详解 KeyboardEvent](https://developer.mozilla.org/zh-CN/docs/Web/API/KeyboardEvent)

监听 `keydown` 事件，在回掉函数里，就可以拿到 `KeyboardEvent` 事件，使用 `event.key` 来判断，当前按的什么键。

```ts
window.addEventListener("keydown", function (event: KeyboardEvent) {
  if (event.defaultPrevented) {
    return; // 如果事件已经在进行中，则不做任何事。
  }

  switch (event.key) {
    case "ArrowUp":
      // 按“↑”方向键时要做的事。
      break;
    case "ArrowDown":
      // 按“↓”方向键时要做的事。
      break;
    case "ArrowLeft":
      // 按“←”方向键时要做的事。
      break;
    case "ArrowRight":
      // 按“→”方向键时要做的事。
      break;
    case "Enter":
      // 按“回车”键时要做的事。
      break;
    case "Escape":
      // 按“ESC”键时要做的事。
      break;
    default:
      return; // 什么都没按就退出吧。
  }

  // 取消默认动作，从而避免处理两次。
  event.preventDefault();
}, true);
```

## 常见的一些 key

查看 [所有key列表](https://developer.mozilla.org/zh-CN/docs/Web/API/UI_Events/Keyboard_event_key_values)

值得注意的是：空格键对应的 `key` 是 `' '` 。