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

console.log(sorted([1, 2, 3]))// => true
console.log(sorted([3, 1, 2]))// => false
console.log(sorted([3, 2, 1], function (a, b) { return b - a }))// => true
```

**（2）代码**

```js
module.exports = const sorted = (array, comparator) => {
  // 如果传入不是数组，则返回
  if (!Array.isArray(array)) throw new TypeError('Expected Array, got ' + (typeof array))

  // 比较函数，不传入则默认为升序
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

获取数组的前k个元素

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
  // 传入不是数组则报错
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

获取数组的后k个元素

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
  // 传入不是数组则报错
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

## arr-flatten
**（1）功能与示例**

扁平化数组

```js
var flatten = require('arr-flatten');
flatten(['a', ['b', ['c']], 'd', ['e']]); //=> ['a', 'b', 'c', 'd', 'e']
```

**（2）代码**

```js
module.exports = function (arr) {
  return flat(arr, []);
};

function flat(arr, res) {
  var i = 0, cur;
  var len = arr.length;
  for (; i < len; i++) {
    cur = arr[i];
    // 这里采用递归形式
    Array.isArray(cur) ? flat(cur, res) : res.push(cur);
  }
  return res;
}
```

【数组扁平化的常用方法汇总】

**方法1：使用flat函数**

flat(depth) 方法中的参数depth，代表展开嵌套数组的深度，默认是1。如果我们想把任意维数组扁平为1维数组，可以使用`arr.flat(dimension-1)`或`arr.flat(Infinity)`操作。

```js
let a = [1,[2,3,[4,[5]]]];  
console.log(a.flat())
console.log(a.flat(1))
console.log(a.flat(4-1))
console.log(a.flat(Infinity))

// [ 1, 2, 3, [ 4, [ 5 ] ] ]
// [ 1, 2, 3, [ 4, [ 5 ] ] ]
// [ 1, 2, 3, 4, 5 ]
// [ 1, 2, 3, 4, 5 ]
```

**方法2：for循环**

```js
var arr1 = [1, 2, 3, [1, 2, 3, 4, [2, 3, 4]]];
const flatten = arr => {
  var res = [];
  for (let i = 0, length = arr.length; i < length; i++) {
    if (Array.isArray(arr[i])) {
      res = res.concat(flatten(arr[i])); //concat 并不会改变原数组
      //res.push(...flatten(arr[i])); //扩展运算符  
    } else {
      res.push(arr[i]);
    }
  }
  return res;
}
console.log(flatten(arr1)); //[1, 2, 3, 1, 2, 3, 4, 2, 3, 4]
```

**方法3：while循环**

```js
var arr1 = [1, 2, [3], [1, 2, 3, [4, [2, 3, 4]]]];
const flatten = arr => {
  // 只有arr中有元素为数组，则展开此元素
  // 再判断arr中是否有元素为数组，有则再展开……
  // 循环往复，直到arr中再也没有数组
  while (arr.some(item => Array.isArray(item))) {
    arr = [].concat(...arr);
    //arr = Array.prototype.concat.apply([],arr);
  }
  return arr;
}
console.log(flatten(arr1)); //[1, 2, 3, 1, 2, 3, 4, 2, 3, 4]
```

**方法4：**

```js
var arr1 = [1, 2, [3], [1, 2, 3, [4, [2, 3, 4]]]];
const flatten = arr => {
  return arr.reduce((pre, cur) => {
    return pre.concat(Array.isArray(cur) ? flatten(cur) : cur);
  }, []);
}
console.log(flatten(arr1)); //[1, 2, 3, 1, 2, 3, 4, 2, 3, 4]
```

**方法5：转化为字符串**

```js
function flatten(input) {
  return input.toString().split(',').map(item => +item);
  // return input.join().split(',').map(item => +item);
  // return input.join(',').split(',').map(item => +item);
}
console.log(flatten(arr1)); //[1, 2, 3, 1, 2, 3, 4, 2, 3, 4]
```

## dedupe
**（1）功能与示例**

数组去重，数组元素可以为基本类型，也可以为引用类型

```js
var dedupe = require('dedupe')

var a = [1, 2, 2, 3]
console.log(dedupe(a)) // [1, 2, 3]

var aa = [{a: 2}, {a: 1}, {a: 1}, {a: 1}]
console.log(dedupe(aa)) // [{a: 2}, {a: 1}]

var aaa = [{a: 2, b: 1}, {a: 1, b: 2}, {a: 1, b: 3}, {a: 1, b: 4}]
console.log(dedupe(aaa, value => value.a)) // [{a: 2, b: 1}, {a: 1,b: 2}]
```

**（2）代码**

```js
function dedupe (arr, hash) {
  hash = hash || JSON.stringify

  const clone = []
  const map = {} // 构建一个map，存储hash过的值是否重复

  for (let i = 0; i < arr.length; i++) {
      let ele = arr[i]
      let hashEle = hash(ele)

      if (!map[hashEle]) {
          clone.push(ele)
          map[hashEle] = true
      }
  }

  return clone
}

module.exports = dedupe
```

## array-range
**（1）功能与示例**

给定一个范围，构建一个数组

```js
var range = require('array-range')
range(3)       // -> [ 0, 1, 2 ]
range(1, 4)    // -> [ 1, 2, 3 ]
range(5).map( x => x*x )  // -> [ 0, 1, 4, 9, 16 ]
range(2, 10).filter( x => x%2===0 )  // -> [ 2, 4, 6, 8 ]
```

**（2）代码**

```js
module.exports = function newArray(start, end) {
  var n0 = typeof start === 'number',
    n1 = typeof end === 'number'

  // start是数字，end不是数字，则传入的数字是end，start默认从0开始
  if (n0 && !n1) {
    end = start
    start = 0
    // start、end都不是数字，则start和end都是0
  } else if (!n0 && !n1) {
    start = 0
    end = 0
  }

  start = start | 0
  end = end | 0

  // 保障数组长度是合法的，即end>=start
  var len = end - start
  if (len < 0)
    throw new Error('array length must be positive')

  // 填充数组元素
  var a = new Array(len)
  for (var i = 0, c = start; i < len; i++, c++)
    a[i] = c

  return a
}
```

## arr-diff
**（1）功能与示例**

返回两个数组不一样的元素

```js
var diff = require('arr-diff');

var a = ['a', 'b', 'c', 'd'];
var b = ['b', 'c'];

console.log(diff(a, b))
//=> ['a', 'd']
```

**（2）代码**

```js
'use strict';

module.exports = function diff(arr) {
  const len = arguments.length;
  let idx = 0;
  while (++idx < len) {
    arr = diffArray(arr, arguments[idx]);
  }
  return arr;
};

// 比较两个数组的差异
function diffArray(one, two) {
  if (!Array.isArray(two)) {
    return one.slice();
  }

  const tlen = two.length
  const olen = one.length;
  let idx = -1;
  let arr = [];

  while (++idx < olen) {
    let ele = one[idx];

    let hasEle = false;
    for (let i = 0; i < tlen; i++) {
      let val = two[i];

      if (ele === val) {
        hasEle = true;
        break;
      }
    }

    if (hasEle === false) {
      arr.push(ele);
    }
  }
  return arr;
}
```

找两个数组不同元素的方法：

**方法1：**
```js
function getArrDifference(arr1, arr2) {
  // 1. 先将两个数组合并
  // 2. 再找出满足indexOf、lastIndexOf相等的元素（即代表只出现过1次，该元素唯一）
  return arr1.concat(arr2).filter(function(v, i, arr) {
      return arr.indexOf(v) === arr.lastIndexOf(v);
  });
}
```

**方法2：**

```js
// 求交集
let intersection = a.filter(v => b.includes(v))

// 求差集
let difference = a.concat(b).filter(v => !a.includes(v) || !b.includes(v))

// 求并集
let union = [...new Set(a,b)]
```

## filled-array
**（1）功能与示例**

生成填充某固定值的数组

```js
const filledArray = require('filled-array');

filledArray('x', 3);//=> ['x', 'x', 'x']
filledArray(0, 3);//=> [0, 0, 0]
filledArray(i => {
	return (++i % 3 ? '' : 'Fizz') + (i % 5 ? '' : 'Buzz') || i;
}, 15);//=> [1, 2, 'Fizz', 4, 'Buzz', 'Fizz', 7, 8, 'Fizz', 'Buzz', 11, 'Fizz', 13, 14, 'FizzBuzz']
```

**（2）代码**

```js
'use strict';
module.exports = function (item, n) {
	var ret = new Array(n);
	var isFn = typeof item === 'function';

  // 如果传入的不是函数，是个固定值，那么直接使用fill函数填充数组
	if (!isFn && typeof ret.fill === 'function') {
		return ret.fill(item);
	}

  // 如果没有fill函数，则依次遍历填充数组
  // 这里考虑传入的形式：
  // 1. 函数
  // 2. 固定值
	for (var i = 0; i < n; i++) {
		ret[i] = isFn ? item(i, n, ret) : item;
	}

	return ret;
};
```

## map-array
**（1）功能与示例**

```js
  const mapArray = require('map-array');
  const obj = {
    giorgio: 'Bianchi',
    gino: 'Rossi'
  };
  console.log(mapArray(obj, (key, value) => key + ' ' + value)); // ['giorgio Bianchi', 'gino Rossi']
```

**（2）代码**

```js
'use strict';
const map = require('map-obj');

function mapToArray(obj, fn) {
	let idx = 0;
	const result = map(obj, (key, value) =>
		[idx++, fn(key, value)]
	);
	result.length = idx;
	return Array.from(result);
}

module.exports = mapToArray;
```

## in-array
**（1）功能与示例**

判断某个值是否在数组中

```js
var inArray = require('in-array');
console.log(inArray(['a', 'b', 'c'], 'a')); //=> true
console.log(inArray(null, 'a')); //=> false
console.log(inArray(null)); //=> false
```

**（2）代码**

```js
'use strict';

module.exports = function inArray (arr, val) {
  arr = arr || [];
  var len = arr.length;
  var i;

  // 直接遍历，判断arr中是否有元素等于val值
  for (i = 0; i < len; i++) {
    if (arr[i] === val) {
      return true;
    }
  }
  return false;
};

```

## unordered-array-remove
**（1）功能与示例**

从数组中移除某个位置的元素

```js
var remove = require('unordered-array-remove')

var list = ['a', 'b', 'c', 'd', 'e']
remove(list, 2) // remove 'c'
console.log(list) // returns ['a', 'b', 'e', 'd'] (no 'c')
```

**（2）代码**

```js
module.exports = remove

function remove (arr, i) {
  if (i >= arr.length || i < 0) return

  // last为数组最后一个元素
  var last = arr.pop()

  // 直接把arr第i位元素替换为last即可
  if (i < arr.length) {
    var tmp = arr[i]
    arr[i] = last
    return tmp // 函数的返回值为被替换的元素
  }
  
  return last
}
```

## swap-array
**（1）功能与示例**

交换数组中两个位置的元素

```js
import SwapArray from 'swap-array';
let arr = ['thats','cool','dude'];
SwapArray(arr, 0, 2);
// ['dude','thats','cool'];
```

**（2）代码**

```js
export default (Arr, Caller, Target) => {
  // Caller是第1个index
  // Target是第2个index

  let Instance = Arr.constructor(); // 如果Arr是数组，则Arr.constructor()是[]
  let Stash = Arr;

  // 如果传入的不是数组，则抛错
  let InstanceType = Array.isArray(Instance) ? 'array' : typeof Instance;
  if(InstanceType !== 'array') throw '[ERR] SwapArray expects a array as first param';

  // 复制Arr到Instance中
  Stash.map((s, i) => Instance[i] = s);

  // 1. 第2个index的元素后插入第1个index元素
  // 2. 第1个index元素位赋值为第2个index元素
  Instance[Caller] = Instance.splice(Target, 1, Instance[Caller])[0];

  return Instance;
}
```

## mirrarray
**（1）功能与示例**

将数组变为pair object，数组中的每个元素成为key and value

```js
import mirrarray from 'mirrarray';
const keymirror = mirrarray(['this', 'that', 'another']);
console.log(keymirror)

// {
//   this: 'this',
//   that: 'that',
//   another: 'another'
// }
```

**（2）代码**

[代码](https://github.com/johnwquarles/mirrarray/blob/master/index.js)

## array.chunk
**（1）功能与示例**

按照给定的size去切分数组

```js
const chunks = require('array.chunk');
console.log(chunks([1, 2, 3, 4, 5], 2)); // [[1, 2], [3, 4], [5]]
```

**（2）代码**

```js
function getType(val) {
  // `[object Array]`.slice(8, -1) ==> Array
  return Object.prototype.toString.call(val).slice(8, -1); // 取 [object Array] 中的 Array
}

// 判断是否是array类型
function isArray(arr) {
  var type = getType(arr);
  var arrTypes = [
    'Array',
    'Int8Array',
    'Uint8Array',
    'Uint8ClampedArray',
    'Int16Array',
    'Uint16Array',
    'Int32Array',
    'Uint32Array',
    'Float32Array',
    'Float64Array'
  ];

  return arrTypes.indexOf(type) !== -1;
}

module.exports = function chunks(arr, size) {
  // 错误处理

  if (!isArray(arr)) {
    throw new TypeError('Input should be Array or TypedArray');
  }

  if (typeof size !== 'number') {
    throw new TypeError('Size should be a Number');
  }

  var result = [];

  // 依次去切割数组，push到新数组中
  for (var i = 0; i < arr.length; i += size) {
    if (typeof arr.slice === 'function') {
      result.push(arr.slice(i, size + i));
    } else {
      result.push(arr.subarray(i, size + i));
    }
  }

  return result;
};
```

## group-array
**（1）功能与示例**

按照给定的object key值，去对arr中的元素做分类

```js
var arr = [
  {tag: 'one', content: 'A'},
  {tag: 'one', content: 'B'},
  {tag: 'two', content: 'C'},
  {tag: 'two', content: 'D'},
  {tag: 'three', content: 'E'},
  {tag: 'three', content: 'F'}
];

groupArray(arr, 'tag');

// {
//   one: [
//     {tag: 'one', content: 'A'},
//     {tag: 'one', content: 'B'}
//   ],
//   two: [
//     {tag: 'two', content: 'C'},
//     {tag: 'two', content: 'D'}
//   ],
//   three: [
//     {tag: 'three', content: 'E'},
//     {tag: 'three', content: 'F'}
//   ]
// }
```

**（2）代码**

[代码](https://github.com/doowb/group-array/blob/master/index.js)

## decamelize
**（1）功能与示例**

将驼峰转为下划线

```js
const decamelize = require('decamelize');

decamelize('unicornRainbow'); //=> 'unicorn_rainbow'
decamelize('unicornRainbow', {separator: '-'}); //=> 'unicorn-rainbow'
decamelize('testGUILabel', {preserveConsecutiveUppercase: true}); //=> 'test_GUI_label'
decamelize('testGUILabel', {preserveConsecutiveUppercase: false}); //=> 'test_gui_label'
```

**（2）代码**

[代码](https://github.com/sindresorhus/decamelize/blob/main/index.js)
## pad-left
**（1）功能与示例**

左侧补0

```js
var pad = require('pad-left');
pad(  '4', 4, '0') // 0004
pad( '35', 4, '0') // 0035
pad('459', 4, '0') // 0459
```

**（2）代码**

```js
'use strict';

var repeat = require('repeat-string');

module.exports = function padLeft(str, num, ch) {
  str = str.toString();

  if (typeof num === 'undefined') {
    return str;
  }

  if (ch === 0) {
    ch = '0';
  } else if (ch) {
    ch = ch.toString();
  } else {
    ch = ' ';
  }

  // 1. 将str的其他位补齐
  // 2. 加上原始str后返回
  return repeat(ch, num - str.length) + str;
};
```

## to-camel-case
**（1）功能与示例**

其他形式（下划线、点、括号、空格等）转驼峰

```js
var toCamelCase = require('to-camel-case')

toCamelCase('space case')  // "spaceCase"
toCamelCase('snake_case')  // "snakeCase"
toCamelCase('dot.case')    // "dotCase"
toCamelCase('weird[case')  // "weirdCase"
```

**（2）代码**

```js
var space = require('to-space-case') // 将字符串变为空格分隔形式（如下划线、点等）

// var clean = require('to-no-case')
// function toSpaceCase(string) {
//   return clean(string).replace(/[\W_]+(.|$)/g, function (matches, match) {
//     return match ? ' ' + match : ''
//   }).trim()
// }
// module.exports = toSpaceCase

function toCamelCase(string) {
  // 正则匹配字符串的空格，将空格后的字母变为大写
  return space(string).replace(/\s(\w)/g, function (matches, letter) {
    return letter.toUpperCase()
  })
}
module.exports = toCamelCase
```

## to-capital-case
**（1）功能与示例**

字符串改为大写单词开头的形式

```js
var toCapitalCase = require('to-capital-case')

toCapitalCase('camelCase')        // "Camel Case"
toCapitalCase('space case')       // "Space Case"
toCapitalCase('snake_case')       // "Snake Case"
toCapitalCase('dot.case')         // "Dot Case"
toCapitalCase('some*weird[case')  // "Some Weird Case"
```

**（2）代码**

```js
var space = require('to-space-case')

// toCapitalCase('camelCase')        // "Camel Case"
function toCapitalCase(string) {
  return space(string).replace(/(^|\s)(\w)/g, function (matches, previous, letter) {
    return previous + letter.toUpperCase()
  })
}
```

## to-constant-case
**（1）功能与示例**

变成下划线连接全部大写的常量形式

```js
var toConstantCase = require('to-constant-case')

toConstantCase('camelCase')   // "CAMEL_CASE"
toConstantCase('snake_case')  // "SNAKE_CASE"
toConstantCase('dot.case')    // "DOT_CASE"
toConstantCase('weird[case')  // "WEIRD_CASE"
```

**（2）代码**

```js
var snake = require('to-snake-case') // 转为下划线连接的形式

// var toSpace = require('to-space-case')
// module.exports = toSnakeCase
// function toSnakeCase(string) {
//   return toSpace(string).replace(/\s/g, '_')
// }

function toConstantCase(string) {
  return snake(string).toUpperCase()
}
module.exports = toConstantCase
```

## to-dot-case
**（1）功能与示例**

```js
var toDotCase = require('to-dot-case')

toDotCase("snake_case")  // "snake.case"
toDotCase('camelCase')   // "camel.case"
toDotCase('space case')  // "space.case"
toDotCase('slug-case')   // "slug.case"
toDotCase("PascalCase")  // "pascal.case"
toDotCase('weird[case')  // "weird.case"
```

**（2）代码**

```js
var space = require('to-space-case') // 转化为空格分隔的形式

// var clean = require('to-no-case')
// function toSpaceCase(string) {
//   return clean(string).replace(/[\W_]+(.|$)/g, function (matches, match) {
//     return match ? ' ' + match : ''
//   }).trim()
// }
// module.exports = toSpaceCase

function toDotCase(string) {
  return space(string).replace(/\s/g, '.')
}
module.exports = toDotCase
```

## to-no-case
**（1）功能与示例**

去除全部case，转化为纯小写、空格分隔的形式

```js
var toNoCase = require('to-no-case')

toNoCase('camelCase')            // "camel case"
toNoCase('snake_case')           // "snake case"
toNoCase('slug-case')            // "slug case"
toNoCase('Title of Case')        // "title of case"
toNoCase('Sentence case.')       // "sentence case."
toNoCase('RAnDom -jUNk$__loL!')  // "random -junk$__lol!"
```

**（2）代码**

```js
var hasSpace = /\s/ // 是否有空格
var hasSeparator = /(_|-|\.|:)/ // 是否有分隔符（下划线_、中连接线-、点.、冒号:）
var hasCamel = /([a-z][A-Z]|[A-Z][a-z])/ // 是否存在驼峰形式（Ab、aB等）

function toNoCase(string) {
  // 如果含义空格，则直接转化为小写
  if (hasSpace.test(string)) return string.toLowerCase()

  // 如果含有分隔符，则反分隔符，转化为小写
  if (hasSeparator.test(string)) return (unseparate(string) || string).toLowerCase()

  // 如果含有驼峰形式，则反驼峰，转化为小写
  if (hasCamel.test(string)) return uncamelize(string).toLowerCase()

  // 否则，直接转化为小写
  return string.toLowerCase()
}

// 反分隔符——将分隔符转化为空格
function unseparate(string) {
  const separatorSplitter = /[\W_]+(.|$)/g
  return string.replace(separatorSplitter, function (m, next) {
    return next ? ' ' + next : ''
  })
}

// 反驼峰——将大写全部转化为小写
function uncamelize(string) {
  const camelSplitter = /(.)([A-Z]+)/g
  return string.replace(camelSplitter, function (m, previous, uppers) {
    return previous + ' ' + uppers.toLowerCase().split('').join(' ')
  })
}

module.exports = toNoCase
```

## to-pascal-case
**（1）功能与示例**

每个单词首字母大写，无任何分隔符

```js
var toPascalCase = require('to-pascal-case')

toPascalCase('space case')  // "SpaceCase"
toPascalCase('snake_case')  // "SnakeCase"
toPascalCase('dot.case')    // "DotCase"
toPascalCase('weird[case')  // "WeirdCase"
```

**（2）代码**

```js
var space = require('to-space-case') // 将字符串转化为空格分隔的形式

function toPascalCase(string) {
  // 删除空格，并将后面紧接的字母转化为大写形式
  return space(string).replace(/(?:^|\s)(\w)/g, function (matches, letter) {
    return letter.toUpperCase()
  })
}
module.exports = toPascalCase
```

## to-sentence-case
**（1）功能与示例**

将句子的首字母大写

```js
var toSentenceCase = require('to-sentence-case')
toSentenceCase('the catcher, in the rye.')  // "The catcher, in the rye."
```

**（2）代码**

```js
var clean = require('to-no-case') // 转化为无任何case的形式，全部小写，空格分隔

function toSentenceCase(string) {
  // 将第一个字母变为大写，注意这里不是/g全局匹配了，所以只会匹配到第一个字母
  return clean(string).replace(/[a-z]/i, function (letter) {
    return letter.toUpperCase()
  }).trim()
}

module.exports = toSentenceCase
```

## to-snake-case
**（1）功能与示例**

转化为下划线分隔的形式

```js
var toSnakeCase = require('to-snake-case')

toSnakeCase('camelCase')   // "camel_case"
toSnakeCase('space case')  // "space_case"
toSnakeCase('dot.case')    // "dot_case"
toSnakeCase('weird[case')  // "weird_case"
```

**（2）代码**

```js
var toSpace = require('to-space-case') // 转化为空格分隔的形式

function toSnakeCase(string) {
  // 将空格全部转化为下划线
  return toSpace(string).replace(/\s/g, '_')
}
module.exports = toSnakeCase
```

## to-space-case
**（1）功能与示例**

将其他case形式转化为空格space分隔的形式

```js
var toSpaceCase = require('to-space-case')

toSpaceCase('camelCase')             // "camel case"
toSpaceCase('snake_case')            // "snake case"
toSpaceCase('dot.case')              // "dot case"
toSpaceCase('-RAnDom -jUNk$__loL!')  // "random junk lol"
```

**（2）代码**

```js
var clean = require('to-no-case')

function toSpaceCase(string) {
  return clean(string).replace(/[\W_]+(.|$)/g, function (matches, match) {
    return match ? ' ' + match : ''
  }).trim()
}
module.exports = toSpaceCase
```

## to-title-case
**（1）功能与示例**

```js
var toTitleCase = require('to-title-case')
toTitleCase('the catcher in the rye')  // "The Catcher in the Rye"
```

**（2）代码**

```js
var sentence = require('to-sentence-case')
var escape = require('escape-regexp-component')
var minors = require('title-case-minors')

var escaped = minors.map(escape)
var minorMatcher = new RegExp('[^^]\\b(' + escaped.join('|') + ')\\b', 'ig')
var punctuationMatcher = /:\s*(\w)/g

function toTitleCase(string) {
  return sentence(string)
    .replace(/(^|\s)(\w)/g, function (matches, previous, letter) {
      return previous + letter.toUpperCase()
    })
    .replace(minorMatcher, function (minor) {
      return minor.toLowerCase()
    })
    .replace(punctuationMatcher, function (letter) {
      return letter.toUpperCase()
    })
}
module.exports = toTitleCase
```