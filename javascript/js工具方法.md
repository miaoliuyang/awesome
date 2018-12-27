1. 空对象
```
var emptyObject = Object.freeze({});
```

2. vue源码中判断类型
```
function isUndef(v){
  return v === undefined || v === null
}

function isDef(v){
  return v !== undefined && v !== null
}

function isTrue(v){
  return v === true
}

function isFalse(v){
  return v === false
}

function isPrimitive(value){
  return (
    typeof value === 'string' ||
    typeof value === 'number' ||
    typeof value === 'symbol' ||
    typeof value === 'boolean'
  )
}

function isObject(obj){
  return obj !== null && typeof obj === 'object'
}

var _toString = Object.prototype.toString;

function toRawType(value){
  return _toString.call(value).slice(8, -1)
}

function isPlainObject(obj){
  return _toString.call(obj) === '[object Object]'
}

71Line
```

