---
title: Array.prototype.flat and Array.prototype.flatMap
date: '2018-12-20T23:50:00.121Z'
---

A Short love story of the experimental new addition to the Array methods - the Flat and FlatMap. We can **level flat** any irregular array (an array that contains array or arrays) irrespective of the depth level plus the one opportunity to flatten and map at the same time is a very cool feature coming to JavaScript after the whole `Array.smoosh` episode.

### A little Story

Have you ever heard about `#SmooshGate`? TC39 the working group that works on JavaScript(ECMAScript) released ``Array.prototype.flatten` to flatten array recursively up to a specified depth. And it broke the web 😭😭😭.

They had to find a quick fix to the bug and save the world. When fixing a bug, why? is very important. They found that MooTools was the cause since it implemented a flatten method that overwrite the default `Array.prototype.flatten`. The proposal author suggested to change the name from flatten to **smoosh**. Strange suggestion? Read more [here](https://developers.google.com/web/updates/2018/03/smooshgate).

It was renamed to `flat` after TC39 met in May 2018 and `Array.prototype.flatMap` was included.

---

### How do we flatten an Array Before ?

```js{1,4}
const flatten = (arr, depth = 1) =>
  arr.reduce(
    (a, v) =>
      a.concat(depth > 1 && Array.isArray(v) ? flatten(v, depth - 1) : v),
    []
  )
const flattened = arr => [].concat(...arr)
```

```js
function flattenArray(arr) {
  const flattened = [].concat(...arr)
  return flattened.some(item =>
    Array.isArray(item) ? flattenArray(flattened) : flattened
  )
}
```

Use recursion, decrementing depth by 1 for each level of depth. Use Array.prototype.reduce() and Array.prototype.concat() to merge elements or arrays. Base case, for depth equal to 1 stops recursion. Omit the second argument, depth to flatten only to a depth of 1 (single flatten).

Lodash comes in handy here with several utilities like `flatten` that flattens array a single level deep. `flattenDeep` recursively flattens an array.`flattenDepth` also recursively flatten an array up to depth that defaults to 1.

```js
import _ from 'lodash';

_.flatten([1, [2, [3, [4]], 5]]);
// => [1, 2, [3, [4]], 5]

_.flattenDeep([1, [2, [3, [4]], 5]]);
// => [1, 2, 3, 4, 5]

var array = [1, [2, [3, [4]], 5]];

_.flattenDepth(array, 1);
// => [1, 2, [3, [4]], 5]

_.flattenDepth(array, 2);
// => [1, 2, 3, [4], 5]
```

---

### Deep flattens an array

Use recursion. Use Array.prototype.concat() with an empty array ([]) and the spread operator (...) to flatten an array. Recursively flatten each element that is an array.

```js
const deepFlatten = arr =>
  [].concat(...arr.map(v => (Array.isArray(v) ? deepFlatten(v) : v)))
```

### Now Behold your Future

The syntax for the flat and flatMap

```js
var newArray = arr.flat([depth]); //defaults to one
```

```js
[1, 2, [3, [4, 5]], 5, 6].flatMap(
  (v, i) => (Array.isArray(v) ? v.flat() : v)
  )
```

The future is here not Just evenly distributed. With the two methods, we can flatten an array easily. `flatMap` combines the power of the regular map and flat together.

```js
// Infinity can be used to flatten recursively
[1, 2, [3, [4, 5]], 5, 6].flat(Infinity);
// [1, 2, 3, 4, 5, 5, 6]

[1, 2, [3, [4, 5]], 5, 6].flat() // flats 1 depth level - default
// [1, 2, 3, [4, 5], 5, 6]

[1, 2, [3, [4, 5]], 5, 6].flat(2) // flats 2 depth level
// [1, 2, 3, [4, 5], 5, 6]

```

The flat method removes empty slots in arrays and it will be very useful in cleaning up arrays.

```js
const arr = [1, 2, ,4, 5];
arr.flat(); // [1, 2, 4, 5]
```

### Specification and Support

It is still in the [candidate recommendation stage 3](https://tc39.github.io/proposal-flatMap/#sec-Array.prototype.flat) in the TC39 Proposals meaning only very few browser will have implemented it. Google Chrome supports start from version 69 and Firefox from version 62 with Opera and Safari supporting from 56 and 12 respectively.

[Nodejs does not have support for it at all](https://node.green/).