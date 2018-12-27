https://www.jianshu.com/p/80c10afd18eb

## 1. Object.freeze

a. 给对象设置了Object.preventExtension(obj1), **禁止更改原型，禁止添加属性**;

b. 为对象的每一个属性设置writable:false，**禁止更改属性值**,

c. 为对象的每个属性设置configurable:false, **禁止删除属性，禁止更改writable为true,禁止更改configurable为true**

注：禁止添加属性，是Object.preventExtensions控制的，而禁止删除属性，是configurable:false控制的

```
const obj1 = {
    a:1,
};

const obj2 = Object.freeze(obj1);

obj1 === obj2;
// 返回原来的对象

Object.isExtensible(obj2);  // false

Object.getOwnPropertyDescriptor(obj2,'a'); 
// {value: 1, writable: false, enumerable: true, configurable: false}

// 禁止更改原型
Object.setPrototypeOf(obj2,{c:3});  
// 非严格模式：Uncaught TypeError: #<Object> is not extensible
// 严格模式：Uncaught TypeError: #<Object> is not extensible

// 禁止添加属性
obj2.b = 2;  
// 非严格模式，静默失败，obj2还是{a:1}
// 严格模式：Uncaught TypeError: Cannot add property b, object is not extensible

// 禁止更改属性值
obj2.a = 2;  
// 非严格模式，静默失败，obj2还是{a:1}
// 严格模式：Uncaught TypeError: Cannot assign to read only property 'a' of object '#<Object>'

// 禁止删除已有属性
delete obj2.a;  
// 非严格模式，返回false
// 严格模式：Uncaught TypeError: Cannot delete property 'a' of #<Object>

/// 禁止更改writable为true
Object.defineProperty(obj2,'a',{
    writable:true,
});  
// 非严格模式：Uncaught TypeError: Cannot redefine property: a
// 严格模式：Uncaught TypeError: Cannot redefine property: a

// 禁止更改enumerable为false
Object.defineProperty(obj2,'a',{
    enumerable:false,
});  
// 非严格模式：Uncaught TypeError: Cannot redefine property: a
// 严格模式：Uncaught TypeError: Cannot redefine property: a

// 禁止更改configuable为true
Object.defineProperty(obj2,'a',{
    configuable:true,
});  
// 非严格模式，静默失败，configuable还是false
// 严格模式：静默失败，configuable还是false
```

## 2. Object.seal

a. 给对象设置Object.preventExtension(obj1), **禁止更改原型，禁止添加属性**

b. 为对象的每一个属性设置configurable:true，**禁止删除属性，禁止更改writable为true,禁止更改configurable为true**

与Object.freeze不同的是,Object.seal后的对象是可写的 writable:true

```
const obj1 = {
    a:1,
};

const obj2 = Object.seal(obj1);

obj1 === obj2;
// 返回原来的对象

Object.isExtensible(obj2);  // false

Object.getOwnPropertyDescriptor(obj2,'a'); 
// {value: 1, writable: true, enumerable: true, configurable: false}
```
