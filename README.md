**npm包发布流程：**

1. git clone ...下载仓库
2. npm init 初始化:
3. 构建目录结构

```
├── node_modules // 依赖
├── package.json // 配置信息
├── .gitignore
├── LISENCE
├── README.md // 包的介绍
├── index.js // 导出的文件，使用module.exports导出即可
└── lib // 可选
    ├── config.js
    └── index.js
└── src // 可选
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

## array-first
**（1）功能与示例**

```js
var first = require('array-first');

first(['a', 'b', 'c', 'd', 'e', 'f']); //=> 'a'
first(['a', 'b', 'c', 'd', 'e', 'f'], 1); //=> 'a'
first(['a', 'b', 'c', 'd', 'e', 'f'], 3); //=> ['a', 'b', 'c']
```

**（2）代码**

```js
var isNumber = require('is-number');
var slice = require('array-slice');

// 【is-number】

// module.exports = function(num) {
//   // 这里考虑了NaN，NaN-NaN=NaN
//   if (typeof num === 'number') {
//     return num - num === 0;
//   }
//   // 这里考虑了无穷大
//   if (typeof num === 'string' && num.trim() !== '') {
//     return Number.isFinite ? Number.isFinite(+num) : isFinite(+num);
//   }
//   return false;
// };


// 【array-slice】

// module.exports = function slice(arr, start, end) {
//   var len = arr.length;
//   var range = [];

//   start = idx(len, start);
//   end = idx(len, end, len);

//   while (start < end) {
//     range.push(arr[start++]);
//   }
//   return range;
// };

// function idx(len, pos, end) {
//   if (pos == null) {
//     pos = end || 0;
//   } else if (pos < 0) {
//     pos = Math.max(len + pos, 0);
//   } else {
//     pos = Math.min(pos, len);
//   }

//   return pos;
// }

module.exports = function arrayFirst(arr, num) {
  if (!Array.isArray(arr)) {
    throw new Error('array-first expects an array as the first argument.');
  }

  if (arr.length === 0) {
    return null;
  }

  var first = slice(arr, 0, isNumber(num) ? +num : 1);
  if (+num === 1 || num == null) {
    return first[0];
  }
  return first;
};
```

## array-last
**（1）功能与示例**

```js
var last = require('array-last');
last(['a', 'b', 'c', 'd', 'e', 'f']);//=> 'f'
last(['a', 'b', 'c', 'd', 'e', 'f'], 1);//=> 'f'
last(['a', 'b', 'c', 'd', 'e', 'f'], 3);//=> ['d', 'e', 'f']
```

**（2）代码**

```js
var isNumber = require('is-number'); // is-number的函数在上面已经展示了

module.exports = function last(arr, n) {
  if (!Array.isArray(arr)) {
    throw new Error('expected the first argument to be an array');
  }

  var len = arr.length;
  if (len === 0) {
    return null;
  }

  n = isNumber(n) ? +n : 1;
  if (n === 1) {
    return arr[len - 1];
  }

  var res = new Array(n);
  while (n--) {
    res[n] = arr[--len];
  }
  return res;
};

```