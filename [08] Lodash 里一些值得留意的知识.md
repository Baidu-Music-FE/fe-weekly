# Lodash 里一些值得留意的知识

在看本文的时候请确定你将 lodash checkout 到版本 v2.4.1。版本 v3.0 之后 lodash 的 api 有了诸多变化。

前端开发几乎每天都能碰到使用 underscore 或者 lodash 的时候。
运行下 `_.functions(_).length`，我当前版本的 lodash 加上 alias 一共提供了约 140 个函数。虽然有这么多，实际经常被使用到的大概也就 30 个左右。虽然文档里每一个函数的说明和用例都已经非常详细了，但浏览全部文档的时候，很容易漏掉一些有趣的函数，这里就着重介绍下那些容易被忽视却很有用的函数们。

## `_.identity()` 和 `_.createCallback()`

这个函数几乎是所有 collection 的默认 callback，要用好 lodash 你不得不了解一些它背后的机制。
lodash 里默认 callback 基本就两种，`1` 和 `_.identity`。而这个函数的源代码只有一行：

```javascript

function foo (int) {
    map(identity, ()->)
}

map = function (fn) {
 fn()asdfjalskdfj
}

function identity(value) {
    return value;
}
```

单纯的把传入的值返回，感觉像是将一个数值转换成函数，因为函数式编程是将函数作为对象进行传递，为了保证表达的一致性，这个操作是有其价值的。

接下来看看 `_.createCallback()` 这个函数。先看个示例：

```javascript
var arr = [
    { name: 'Jack', age: 27 },
    { name: 'Andy', age: 21 },
    { name: 'Lisa', age: 23, color: 'white' }
];

_.map(arr, 'name');
// → ['Jack', 'Andy', 'Lisa']
```

这里 `_.map(arr, 'name')` 的第二个参数实际上应该是一个 callback，虽然这里用的字符串，但它确实帮我们快速获取了全部的名字。这个例子看上去很好理解，那么下面几个呢？

```javascript
_.map(arr, { age: 21 });
// → [false, true, false]

_.find(arr, { age: 23 });
// → {name: "Lisa", age: 23, color: "white"}

_.find(arr, 'color')
// → {name: "Lisa", age: 23, color: "white"}
```

要理解上面三个例子我们需要深入了解下 `_.createCallback()` 这个方法。如果你看过它的源代码就会知道，这个函数还是比较脏的，通过判断传入的参数类型，`_.createCallback()` 会自动生成对应的 callback。比如传入 `{ age: 21 }` 对象时，它判断了这个对象的键值只有一对，于是 `_.createCallback()` 大概长这个样子：

```javascript
// 这里为了解释是不完全的实现，单纯为了表达思想，你可以去阅读完整的源代码。
// 当然 lodash 的源代码为了覆盖一些极端情况，实现比较 dirty。这里是精简后的效果。
createCallback = function (func) {
    switch (typeof func) {
        case 'string':
            return function(object) {
                return object[func];
            }

        case 'object':
            return function(object) {
                return _(func).keys().any(function(key) {
                    return func[key] === object[key];
                });
            }

        default:
            return func;
    }
}

var cb = createCallback({ age: 21 });
_.map(arr, cb);
// → [false, true, false]

var cb = createCallback('color');
_.find(arr, cb);
// → {name: "Lisa", age: 23, color: "white"}
```

除了 hack 的用法，官方还给出了 hack 这个函数的实例，让其支持类似 mongodb
的语法： `_.filter(arr, 'age__gt22');`

## Map


## Reduce

分组化简，比如
```javascript
reduce(arr, function(s, a, b) {
    return s * a * b;
})
```

未完待续...