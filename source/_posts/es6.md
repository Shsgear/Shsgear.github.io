---
title: es6
date: 2018-08-03 17:26:36
tags: 
      - javascript
      - es6
---

<!-- more -->

# Destructuring

``` javascript
let [x, y, ...z] = [1, 2, 3, 4, 5];
console.log(x, y, z);

let [l, m, ...n] = ['a'];
// 结构不成功 输出undefined
// 解构的右边必须是具有iterator接口的结构
console.log(l, m, n);
// a
// undefined
// []

// 默认值生效的条件是，对象的属性值严格等于undefined
let [b = true] = [];
console.log(b); // true
let [c = 1, d = 3] = [2];
console.log(c, d); // 2 3

function f () {
  console.log('YYY');
}

let [a = f()] = [1]; //惰性求值
// 等价于
// let a;
// if ([1][0] === undefined) {
//   a = f();
// } else {
//   x = [1][0]
// }


let { foo, bar } = { foo: "foo", baz: "baz" };

console.log(foo, bar); // foo  undefined


let obj = { first: "A", last: "B" };
let { first: fir, last: last } = obj;
console.log(fir, last);


let objA = {
  p: ['Hello', { y: 'World' }]
}
let { p: [o, { y: k }] } = objA;
console.log(o, k); //p是匹配模式，此处未赋值。 m: Hello   n: World




// 用途
// 交换变量
let nA = 1;
let nB = 2;
[nA, nB] = [nB, nA];
console.log(nA, nB);


// 从函数返回多个值
function example() {
  return { eA: 1, eB: 2 };
}
let { eA, eB } = example();
console.log(eA, eB);



// 函数参数定义
function f1([x, y, z]) {
  /* ... */
}
f1([1, 2, 3]);

function f2({ x, y, z }) {
  /* ... */
}
f2({ z: 1, x: 2, y: 1 });

// 提取接口返回值
let jsonData = {
  status: 'OK',
  id: 11,
  data: [122, 122]
}
let {status, id, data: number} = jsonData;
console.log(status, number);

// 函数参数值的默认值
function defaultParam({ delay = 1000, str = 'Hello World!' } = {}) {
  setTimeout(() => {
    console.log(str);
  }, delay);
}
defaultParam();
defaultParam({str: 'Fxxk off'});


// 加载模块的指定方法
// import { Button } from 'element-ui';

```


# Symbol

``` javascript
const mySymbol = Symbol();
console.log('mySymbol === Symbol()', mySymbol === Symbol());
let a = {};

// method 1
a = {
  [mySymbol]: 'Hello mySymbol'
}

// method 2
a[mySymbol] = 'Hello anotherSymbol!'; // 为对象赋予Symbol属性

// method 3
Object.defineProperty(a, mySymbol, { value: 'Hello thirdSymbol!' });

a.mySymbol = 'Hello stringMySymbol'; // .操作符为对象赋予的是字符串属性

console.log(a[mySymbol]) // Hello Symbol
console.log(a['mySymbol']) // "Hello stringMySymbol"

// 常规遍历对象不输出Symbol属性
// 只输出字符串属性
for (let i in a) {
  console.log('props: ', i); 
}
// 只输出字符串属性
console.log(Object.getOwnPropertyNames(a));
// 此方法可以获取
console.log(Object.getOwnPropertySymbols(a));
// 或者此方法输出所有类型的键名
console.log(Reflect.ownKeys(a));



/* Symbol的用处 */

//  stage1 '魔法字符串' 耦合性太强。不变维护
let getArea = (shape, option) => {
  let area = 0;
  switch (shape) {
    case 'Triangle':
      area = (option.width * option.height) * .5;
      break;
    /* ... */
    default:
      break;
  }
  return area;
}

getArea('Triangle', { width: 100, height: 100 });

// stage2 将字符串变成变量。易于维护
let areaShape = {
  triangle: 'Triangle'
}
getArea = (shape, option) => {
  let area = 0;
  switch (shape) {
    case areaShape.triangle:
      area = (option.width * option.height) * .5;
      break;
    /* ... */
    default:
      break;
  }
  return area;
}

getArea(areaShape.triangle, { width: 100, height: 100 });

// stage3 变量并不需要指定一个值。只需要和其他变量区别开来，Symbol很适合
areaShape = {
  triangle: Symbol(),
  rectAngle: Symbol()
}
getArea = (shape, option) => {
  let area = 0;
  switch (shape) {
    case areaShape.triangle:
      area = (option.width * option.height) * .5;
      break;
    /* ... */
    default:
      break;
  }
  return area;
}

getArea(areaShape.triangle, { width: 100, height: 100 });


```

# Set Map

``` javascript
const s = new Set();
[2, 3, 4, 5, 5, 2, 3, 6].forEach(x => s.add(x));

//  Set的add會將重复的值舍弃

// 用法
// 数组去重
let a = [...new Set([1, 1, 2, 2, 3, 3, '3'])];
console.log(a);

// 实现并集
let arrA = new Set([1, 2, 3]);
let arrB = new Set([2, 3, 4]);
let union = [...new Set([...arrA, ...arrB])];
console.log('union', union);

// 实现交集
let intersect = new Set([...arrA].filter(x => arrB.has(x)));
console.log('intersect', intersect);

```