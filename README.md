**npm包发布流程：**

1. git clone ...下载仓库
2. npm init 初始化
3. 构建目录结构

```
├── node_modules // 依赖
├── package.json // 配置信息
├── .gitignore
├── LISENCE
├── README.md // 包的介绍
├── index.js // 导出的文件
└── lib
    ├── config.js
    └── index.js
└── src
    ├── config.js
    └── index.js
```

4. 发布

```sh
npm login // 提示输入用户名，密码，公共邮箱，填好登录成功
npm whoami  // 查看当前登录状态 
npm publish // 发布
```

当发布成功之后如果进行修改，需要重新发布，需要将package.json中的version更改为比上一次的版本号更高。

[TOC]

## is-sorted
**（1）功能与示例**
判断数组是否已经排好序，排序函数可以自定义（如不定义，默认是升序排序）

```js
const sorted = require('is-sorted')

console.log(sorted([1, 2, 3]))
// => true

console.log(sorted([3, 1, 2]))
// => false

// supports custom comparators
console.log(sorted([3, 2, 1], function (a, b) { return b - a }))
// => true
```

**（2）代码**

```js
module.exports = const sorted = (array, comparator) => {
  // 如果传入不是数组，则返回
  if (!Array.isArray(array)) throw new TypeError('Expected Array, got ' + (typeof array))

  // 比较函数
  const defaultComparator=(a, b)=> a - b
  comparator = comparator || defaultComparator

  for (let i = 1, len = array.length; i < len; i++) {
    // 从前往后两两相比，如果不符合比较函数，则直接返回false
    if (comparator(array[i - 1], array[i]) > 0) return false
  }

  // 比较完了都符合比较函数，则返回true
  return true
}
```


