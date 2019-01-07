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

function isRegExp (v) {
    return _toString.call(v) === '[object RegExp]'
}
```

3. 判断是否是有效的array index
```
function isValidArrayIndex (val) {
    var n = parseFloat(String(val));
    return n >= 0 && Math.floor(n) === n && isFinite(val)
}
```

4.  Convert a value to a string that is actually rendered.
```
function toString(val) {
  return val == null ? '' : typeof val === 'object' ? JSON.stringify(val,null,2): String(val)
}
```

5. Convert an input value to a number for persistence.If the conversion fails, return original string.
```
function toNumber(val) {
  val n = parseFloat(val);
  return isNaN(n) ? val : n
}
```

6. Make a map and return a function for checking if a key is in that map.
```
function makeMap(str, expectLowerCase) {
  var map = Object.create(null);
  var list = str.split(',');
  for(var i = 0; i < list.length; i++){
    map[list[i]] = true;
  }
  return expectLowerCase ? function(val) { return map[val.toLowerCase()]; } : function(val) { return map[val];}
}
```

7. Remove an item from an array
```
function remove(arr, item) {
  if(arr.length){
    var index = arr.indexOf(item);
    if (index > -1){
      return arr.splice(index, 1)
    }
  }
}
```

8. Check whether an object has the property
```
var hasOwnProperty = Object.prototype.hasOwnProperty;
function hasOwn(obj ,key){
   return hasOwnProperty.call(obj, key)
}
```

9.  Create a cached version of a pure function
```
function cached(fn) {
  var cache = Object.create(null);
  return (function cachedFn(str) {
    var hit = cache[str];
    return hit || (cache[str] = fn(str))
  })
}

var capitalize = cached(function(str){
  return str.charAt(0).toUpperCase() + str.slice(1)
});
```
