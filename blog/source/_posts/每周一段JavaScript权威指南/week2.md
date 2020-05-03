---
title: 每周一段JavaScript权威指南-第二周
date: 2020-05-03
tags:
- JavaScript权威指南
header-img: http://note.youdao.com/yws/res/9100/WEBRESOURCE17201741f985d3792054e718efa766af
catalog: true
toc_nav_num: true
---

# 第二周

（第六章---第七章）
> 目前只更新到第六章

------

## 1：第六章（对象）

> 对象是一种复合值。可以看作是属性的无序集合。
对象类型：内置对象[Date,Array],宿主对象[window,documnet],自定义对象[new Object()...]
两类属性：自有属性；继承属性。

属性特性：
```javascript
{
    writable: true|false, //是否可以设置该属性的值。
    enumerable: true|false, //该属性是否可以枚举。
    configurable: true|false,// 是否可以删除或者修改该属性。
    value: any, // 该属性的值
}
```

### 1、创建对象：
    1.对象直接量：var obj = {};
    每次运算都创建一个新的对象。
    2.通过new创建： var obj = new Object();
    用构造函数创建一个新的对象。每个原始类型都有一个内置的构造函数。
    3.通过create创建： var obj = Objcet.create(Object.prototype);
    Object.create创建一个新对象。其中第一个是对象原型对象的指向。第二个是可选参数，用于对对象的属性进行描述【属性特性】。示例如下：
```javascript
var o = Object.create(Object.prototype, {
  // foo会成为所创建对象的数据属性
  foo: { 
    writable:true,
    configurable:true,
    value: "hello" 
  },
  // bar会成为所创建对象的访问器属性
  bar: {
    configurable: false,
    get: function() { return 10 },
    set: function(value) {
      console.log("Setting `o.bar` to", value);
    }
  }
});

/**
o => {
    foo:"hello",
    bar: 10, 
    get bar:function(){}, 
    set bar:function(){},
    __proto__:Object.prototype
}
*/
```
    1、普通对象都具有原型。
    2、没有原型的对象位数不多。Object.prototype就是其中之一。它不继承任何属性。
    *即：Object.prototype.__proto__ = null;*
    3、所有的内置函数都具有一个继承自Object.prototype的原型。
    *即：Date.prototype的属性继承自Object.prototype。*
    4、由new Date创建的Date对象同时继承自Date.prototype && Object.prototype。
    构成的原型链： new Date() -> Date.prototype -> Object.prototype
    *即：new Date().__proto__ = Date.prototype; Date.prototype.__proto__ = Object.prototype;*

> 实现一个--通过原型链继承创建一个新对象：
```javascript
    // inherit返回一个继承自原型对象p的新对象
    function inherit(p) {
        if(p == null) throw TypeError(); //p不能是null
        if(Object.create) return Objcet.create(p);
        var t = typeof p;
        if(t !== "objcet" && t !== "function") throw TypeError();
        function F() {};
        F.prototype = p;
        return new F();
    }
```

### 2、属性的查询设置：
    1、obj.property || obj['property'];
    2、javascript所有对象都是关联数组。
    3、内置函数的构造函数都是只读的。
    4、几种赋值失败的场景：
        1）【o = {readonly p:any}】若p是只读的，不能给p赋值。不过
        通过使用Object.definePropery设置writeable或添加set函数可以修改只读。
        设置只读：只给p设置get函数，或者writeable设置fasle。
        2）【o = {__proto__: {readonly p:any}}】p是继承属性。且只读。
        3）o不可扩展【Object.preventExtensions(o);不可逆，不能定义新属性】,
        且o中不存在p。且没有setter方法调用。
    
### 3、删除属性：
    1、只是断开属性和宿主的联系，不会处理属性中的属性。    
> eg: var a = {p:{x:1}}; var b = a.p; delete a.p; {x:1}依然存在因为b在引用。

    2、delete不能删除可配置为false的属性[configurable: false]
    3、在严格模式中删除失败会报错。非严格模式返回false

### 4、检查属性：
    1、in 左侧属性名，右侧对象名，可以检查到继承属性。 'p' in o => true|false;
    2、hasOwnProperty检测对象自有属性[非继承属性]。o.hasOwnProperty('p') => true|false;
    3、propertyIsEnumerable()检测自有属性的可枚举属性.=> true|false;

### 5、枚举属性：
    1、如何设置：
```javascript
    Object.definePropery({},"key", {
        value: any,
        enumerable: Boolean
    })
```
    2、 for...in：循环对象中所有可枚举的属性（包括原型上的）
    3、Object.getOwnPropertyNames() //返回自身属性，包括不可枚举
    4、Object.keys（） // 返回自身可枚举属性

### 6、属性的特性
    1、设置Object.definePropery：
```javascript
    Object.definePropery({},"key", {
        writable: true|false, //是否可以设置该属性的值。
        enumerable: true|false, //该属性是否可以枚举。
        configurable: true|false,// 是否可以删除或者修改该属性。
        value: any, // 该属性的值
        set key() {}, //设置劫持
        get key() {} // 获取劫持
    })
```
    2、Object.getOwnProperyDescriptor() //获取自身某个属性的描述。
```javascript
    Object.getOwnProperyDescriptor(o,'p') =>
    {
        writable: true|false, //是否可以设置该属性的值。
        enumerable: true|false, //该属性是否可以枚举。
        configurable: true|false,// 是否可以删除或者修改该属性。
        value: any // 该属性的值
    }
```
    3、Object.defineProperies //批量设置属性
```javascript
    Object.defineProperies({}, {
        x:{
            writable: true|false, //是否可以设置该属性的值。
            enumerable: true|false, //该属性是否可以枚举。
            configurable: true|false,// 是否可以删除或者修改该属性。
            value: any // 该属性的值
        },
        y:{
            writable: true|false, //是否可以设置该属性的值。
            enumerable: true|false, //该属性是否可以枚举。
            configurable: true|false,// 是否可以删除或者修改该属性。
            value: any // 该属性的值
        }
    })
```
    4、Object.definePropery && Object.defineProperies 规则：
        1）如果对象不可扩展[Object.preventExtensions]，则只可以编辑自有属性，不能新增。
        2）如果属性不可配置，则不能修改可配置和可枚举的属性。
        3）如果属性不可配置，不能修改getter和setter，也不能转换为数据属性[同理不能反转]
        4）数据不可配置时，可以将writable设置为true但是不能设置为false

###  7、对象的是那个属性
    1、哪三个？ =》 property原型 、 class类 、 Extensibleattribute 可扩展性
        1）原型属性
            1- Object.getPrototypeOf()// 获取对象的原型，只获取一层
            2-isPrototypeOf() 检查一个对象是否是另一个对象的原型。[o.isPrototypeOf(p)]
            instanceof 检查左侧是否是右侧实例。 p instanceof o
        2）类属性
            1- 用Object.property.toString.call(对象实例).slice(8, -1); => Array/Date...
        3）可扩展性
            1- Object.preventExtensions将对象设置为不可扩展。
                只影响本身。不可逆。 Object.esExtensible()检测 =》true|false
            2- Object.seal() //将自有属性设置成不可扩展的同时设置成不可配置。
                Object.isSealed()检测 =》 Boolean。
            3- Object.freeze() // 除了seal实现之上将所有值设置成只读。【可以通过存取器设置值】。
                Object.isFrozen（）检测 =》 Boolean。
                
### 8、序列化对象
    1、 JSON.stringify() // 序列化对象可枚举属性。
    
### 9、总结：
    1、创建对象后其实是新实例的原型指向【构造函数的property[原型对象]】
        var a = new Object(); a.__proto__ = Object.property[Object的原型对象]
    2、所有函数的最终原型都指向Object.property
    3、对象是一个类字典的关联数组
    4、如果是只读属性，则不能重新赋值，用definePropery可以改变。
        继承来的只读属性无法被修改和创建。
    5、检测属性：
```javascript
    in： p in o => 可以查到原型上。看p是否在o的自身或原型上
    hasOwnProperty：o.hasOwnProperty(p) =>o是否由属性p。[只检查自身属性]
    for...in：遍历所有可枚举属性[包括原型]
    Object.getOwnPropertyNames() =>返回自身所有属性[包括不可枚举]
    Object.keys() => 返回自身可枚举属性[不包括原型]
    isPrototypeOf() => o.isPrototypeOf(p) => p是否继承了o[o.property是否有p]
    Object.getPrototypeOf() =》 获取当前对象的原型
    Object.preventExtensions（）将对象设置为不可扩展。【不可新增属性】
    Object.seal() 将对象设置为不可扩展，设置不可配置
    Object.freeze() 将对象设置为不可扩展，设置不可配置，设置只读
```
    6、通过存取器设置只读只写get；set；
    7、Object.definePropery：
```javascript
    Object.definePropery({},"key", {
        writable: true|false, //是否可以设置该属性的值。
        enumerable: true|false, //该属性是否可以枚举。
        configurable: true|false,// 是否可以删除或者修改该属性。
        value: any, // 该属性的值
        set key() {}, //设置劫持
        get key() {} // 获取劫持
    })
```