参考 [JS 函数柯里化（curry）和函数合成（compose）](http://c.biancheng.net/view/5744.html)

## compose

```js
const f1 = (arg) => {
  console.log("f1", arg);
  return arg;
};
const f2 = (arg) => {
  console.log("f2", arg);
  return arg;
};
const f3 = (arg) => {
  console.log("f3", arg);
  return arg;
};

f3(f2(f1("omg"))); // f1 f2 f3
console.log("------");
// compose()('omg')
// compose(f1)('omg')
const fn = compose(f1, f2, f3); // f1 f2 f3
fn("omg");

// compose 返回一个函数
function compose(...args) {
  if (args.length === 0) {
    return (args) => {
      return args;
    };
  }
  if (args.length === 1) {
    return args[0];
  }

  return args.reduce((fnPre, fnCur) => {
    return (arg) => {
      return fnCur(fnPre(arg));
    };
  });
}
```

## curry

```js
function currying(fn) {
  return function curried() {
    var args = [].slice.call(arguments),
      context = this;

    return args.length >= fn.length
      ? fn.apply(context, args)
      : function () {
          var rest = [].slice.call(arguments);
          return curried.apply(context, args.concat(rest));
        };
  };
}
```
