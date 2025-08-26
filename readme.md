This project has come to the end of its life!

---

经过探索，在不修改浏览器源代码情况下，我没有更好的方案来过掉 `debugger` 语句，即便是由插件自动给 `debugger` 语句的地方标记*不在此处断点*，依然不能解决耗时的问题。

```
const a = performance.now();

// 测试 1：仅运行该语句
debugger;

// 测试2：仅运行该循环
for (let i = 0; i < 500; i++) { debugger; }

console.log("耗时:", performance.now() - a);
```

测试 `1` 的结果：

-   当禁用全部断点时，耗时 `0`
-   当使用*不在此处断点*时，耗时 `0.1 ~ 0.3`
-   当使用 `cdp` 拦截时，耗时在 `1 ~ 3`

测试 `2` 的节点：

-   当禁用全部断点时，耗时 `0 ~ 0.1`
-   当使用*不在此处断点*时，耗时 `20 ~ 50`，波动较大
-   当使用 `cdp` 拦截时，耗时在 `430 ~ 550`**，差距明显！


## 关于 ignore list
将脚本加入到 chrome 浏览器中的 `ignore list` 之后，可以过掉 `debugger`，也可以手动打断点，但无法捕获其中的异常、以及其余的事件（比如 `mouse click` 事件等）。

## 关于忽略 debugger 时的断点方式
现在 `firefox` 有一个忽略 `debugger` 语句的功能，当开启它之后，自己编写的 `hook` 脚本中的 `debugger` 语句也会失效，
此时可以使用 `xhr` 断点来达到类似的效果。
- 在 `hook` 脚本中使用 `fetch('xxx')` 替换 `debugger` 语句
- 然后下一个 `xhr` 断点，匹配上面的链接 `xxx`
- 这样，当忽略 `debugger` 语句时，`hook` 脚本也能被断住，也能手动打断点

结合上面两个方法，可以在 `chrome` 上实现类似的效果：
- 编写 `hook` 脚本定位到某个文件
- 将该文件加入到 `ignore list` 中
- 然后手动下断点

## CDP Debugger.setBlackboxedRanges
这个 `chrome devtools protocol method` 可以规划一个区域，忽略其中的 `debugger`，经过测试似乎只能忽略 `debugger` 语句所在的函数，不能忽略语句本身。

## 后续
我没有修改 `chromium` 浏览器的想法，建议转向 `firefox` 或者使用他人维护的浏览器。


---

This project is a simple experiment, if you need it, please fork it!

**For tutorials on how to use it, please visit**: `https://www.52pojie.cn/thread-1967050-1-1.html`.

The Logo for the extension is from [here - flaticon.com](https://www.flaticon.com/free-icon/instagram-logo_4406241). Thanks!
