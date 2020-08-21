---
title: "深入 Web 开发： Code snippets for JavaScript"
date: "2017-08-30"
toc: true
---

<!--more-->

### Array

#### 返回指定范围内的随机整数

```javascript
const randomIntegerInRange = (min, max) => Math.floor(Math.random() * (max - min + 1)) + min;

// EXAMPLES
randomIntegerInRange(0, 5); // 2
```

1. *30 seconds of code, https://www.30secondsofcode.org/js/s/random-integer-in-range, Apr 17 , 2020*

#### 返回指定范围内的随机数

```javascript
const randomNumberInRange = (min, max) => Math.random() * (max - min) + min;

// EXAMPLES
randomNumberInRange(2, 10); // 6.0211363285087005
```

1. *30 seconds of code, https://www.30secondsofcode.org/js/s/random-number-in-range, Apr 17 , 2020*

### Object

#### 浅拷贝（Shallow Clone）

使用 `Object.assign()` 和一个空对象（`{}`）创建一个浅拷贝对象。

```javascript
const shallowClone = obj => Object.assign({}, obj);

// EXAMPLES
const a = { x: true, y: 1 };
const b = shallowClone(a); // a !== b
```

1. *30 seconds of code, https://www.30secondsofcode.org/js/s/shallow-clone, Apr 17 , 2020*

#### 深拷贝（Deep Clone）

最简单的方法是使用 `JSON.stringify()` 和 `JSON.parse()` 来进行深拷贝。

```javascript
let obj = { a: 1, b: { c: 2 } };
let clone = JSON.parseJSON.stringify(obj));
clone.b.c = 4;
// obj = { a: 1, b: { c: 2 }}, clone = { a: 1, b: { c: 4 } }
```

由于此方法必须序列化和反序列化整个对象，因此要处理的数据量很大或者对性能有严格要求时，可以使用递归（Recursion）来进行深拷贝。

```javascript
const deepClone = obj => {
  if (obj === null) return null;
  let clone = Object.assign({}, obj);
  Object.keys(clone).forEach(
    key => (clone[key] = typeof obj[key] === 'object' ? deepClone(obj[key]) : obj[key])
  );
  return Array.isArray(obj) && obj.length
    ? (clone.length = obj.length) && Array.from(clone)
    : Array.isArray(obj)
    ? Array.from(obj)
    : clone;
};

// EXAMPLES
const a = { foo: 'bar', obj: { a: 1, b: 2 } };
const b = deepClone(a); // a !== b, a.obj !== b.obj
```

1. *Angelos Chalaris, https://www.30secondsofcode.org/blog/s/javascript-shallow-deep-clone, Apr 14, 2020*
2. *30 seconds of code, https://www.30secondsofcode.org/js/s/deep-clone, Apr 17, 2020*

### Browser

#### 平滑滚动到页面顶部

```javascript
const scrollToTop = () => {
  const c = document.documentElement.scrollTop || document.body.scrollTop;
  if (c > 0) {
    window.requestAnimationFrame(scrollToTop);
    window.scrollTo(0, c - c / 8);
  }
};

// EXAMPLES
scrollToTop();
```

[*https://www.30secondsofcode.org/js/s/scroll-to-top*](https://www.30secondsofcode.org/js/s/scroll-to-top)

#### 查询 URL 字符串参数

```javascript
const getURLParameters = url =>
  (url.match(/([^?=&]+)(=([^&]*))/g) || []).reduce(
    (a, v) => ((a[v.slice(0, v.indexOf('='))] = v.slice(v.indexOf('=') + 1)), a),
    {}
  );

// EXAMPLES
getURLParameters('http://url.com/page?name=Adam&surname=Smith');
// {name: "Adam", surname: "Smith"}
```

[*https://www.30secondsofcode.org/js/s/get-url-parameters*](https://www.30secondsofcode.org/js/s/get-url-parameters)

#### 检测 iOS 设备和版本（Detecting iOS and Detecting iOS version）

这是 stackoverflow 的一个最佳回答。

```javascript
function iOS() {
  return [
    'iPad Simulator',
    'iPhone Simulator',
    'iPod Simulator',
    'iPad',
    'iPhone',
    'iPod'
  ].includes(navigator.platform)
  // iPad on iOS 13 detection
  || (navigator.userAgent.includes("Mac") && "ontouchend" in document)
}
```

回答者还提供了检测 iOS 版本的方法。

```javascript
function iOSversion() {

  if (iOS) { // <-- Use the one here above
    if (window.indexedDB) { return 'iOS 8 and up'; }
    if (window.SpeechSynthesisUtterance) { return 'iOS 7'; }
    if (window.webkitAudioContext) { return 'iOS 6'; }
    if (window.matchMedia) { return 'iOS 5'; }
    if (window.history && 'pushState' in window.history) { return 'iOS 4'; }
    return 'iOS 3 or earlier';
  }

  return 'Not an iOS device';
}
```

1. *Paul Rumkin, https://stackoverflow.com/a/9039885, Jul 17, 2020*