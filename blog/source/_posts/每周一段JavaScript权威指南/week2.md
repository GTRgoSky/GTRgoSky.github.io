---
title: 每周一段JavaScript权威指南-第二周
date: 2020-05-03
tags:
- JavaScript权威指南
header-img: https://img-blog.csdnimg.cn/2020050614115438.png
catalog: true
toc_nav_num: true
---

# 第二周

（第六章---第七章）
> finish

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


## 1：第七章（数组）

> 数组是值的有序集合。每一个值叫做一个元素，而每个元素在数组中有一个位置，以数字表示，称为索引。
数组的第一个元素索引是0，最大能容纳2^32 - 2 个单位的元素。
数组是对象的特殊形式。索引对应是属性名称。访问常规对象时，数组是要更快一些的。

### 1、创建数组：
    1、var arr = [];
    2、new Array(10) = [empty * 10]; =>empty = undefined;
        new Array(2, 'a'. 'b') = ['a', 'b'];
    3、var arr = [,,,] => [undefined,undefined,undefined]

### 2、数组元素的读和写：
    1、arr = [1,2,3]; a[1] = 2;
        a[3] = 4; ==> arr = [1,2,3,4];
    2、所有索引都是属性名,但只有[0,2^32-2]范围内的整数才是索引

### 3、稀疏数组：
    1、var arr = [1,empty,2];
    2、稀疏数组在实现上比稠密数组更慢，内存利用率更高。

### 4、数组长度：
    1、每个数组都有一个length长度。稠密数组中这个长度决定元素的个数。
    2、用Object.defineproperty将Array的length设置成只读的
    [writable：false]。则数组的长度无法改变。无法新增或者删除数组。只能修改当前数组。

### 5、数组元素的添加和删除：
    1、尾部增加：[d].push(a,b,c) => [d,a,b,c]; return length;
    2、头部添加：[a].unshift(b,c,d) => [b,c,d,a]; return d;
    3、尾部删除：[a,b,c].pop() => [a,b]; return c;
    4、头部删除：[a,b,c].shift() => [b,c]; return a;
    5、splice：用于删除、添加、替换数组。

### 6、数组遍历：
    for循环。
    用for...in循环只会找到数组的下标。【相当于遍历对象】
    forEach。

### 7、数组方法：
    1、join：
    将数组转换为字符串并拼接在一起。默认使用","。
    [a,b,c].join(':') => "a:b:c";
    2、reverse：
    将数组元素颠倒。
    [a,b,c].reverse() => [c,b,a];
    3、sort：
    将数组排序。排序的方法是：一般使用快速排序，特殊使用了并归排序。

```javascript
    arr.sort(function(a,b) {
        //a,b代表数组元素。
        若a应该在前则 return -1；
        若a应该在后则 return 1；
        若return 0 则a, b不换位。
    })
```
    4、concat：拼接数组。
    [a,b].concat(c,d) => [a,b,c,d];
    [a,b].concat([c,d]) => [a,b,c,d];
    5、slice：
    返回指定数组的片段。它的两个参数分别指代开始和结束位置。
    [开始index，结束index）；包含前边，不包含后边。
    如果参数出现复数，则代表从后面开始计算。
    它不会修改调用它的数组。
        var arr = [1,2,3,4];
        arr.slice(0,2) => return [1,2];
        arr.slice(2) => return [3,4]; 只天蝎一个就直接到结尾。
        arr.slice(1,-1) =>return [2,3] //从1开始不包含最后一位
        arr.slice(-2,-1) => return [3] //从倒数第二个开始。
    6、splice：
    在数组中插入，删除的通用方法。会修改调用他的数组。
    第一个参数指定起始位置。第二个参数表示应该删除(替换)的个数。
    返回值为删除元素组成的数组。如果没有则为[]；
    [1，2，3，4，5，6，7，8].splice(4) => arr = [1,2,3,4]; return [5,6,7,8];
    [1,2,3,4].splice(1,2) => arr = [1,4]; return [2,3];
    [1,2,3,4].splice(1,2,a,b)=> arr = [1,a,b,4]; return [2,3];
    [1,2,3].splice(1,0,a,b) => arr = [1,a,b,2,3];return [];
    7、push和pop：
    允许数组当作栈来使用[先进后出]；
    都会修改调用他们的数组。
    8、unshift和shift：
    从头部操作，和push，pop一样会影响调用数组。
    9、toString：
    他与空参数的join()一样结果。

### 8、ES5中的数组方法：
    1、forEach(item, index, selfArr)：
    从头到位遍历数组。三个回调参数依次是：数组元素，数组下标，
    数组自身。
    若想终止forEach，可以把循环写在try...catch语句中。然后在循环中throw 错误。
    2、map()：
    要有返回值。与forEach类似。不修改调用数组。
    3、filter()：
    根据返回值的true|false判断返回的数组中是否含有当前数组元素。不会修改调用数组。
    返回的永远是稠密数组。会直接跳过稀疏数组中的缺失元素。【不会跳过null和undefined】；
    4、every和some：
    是个判断方法，返回true|false。
    every中有一个返回的是false则最终的得到的是false。
    some中有一个返回的是true则最终返回的是true。
    一旦有一个值可以确定最终返回值，则循环会停止。
    5、reduce[从左开始]和reduceRight[从右开始]：
    将每个数组元素循环进行操作。并接受上一个元素的返回值。
    在空数组中调用无参数的reduce会报错。eg：
    
```javascript
    var a = [1,2,3,4];
    a.reduce(function(x,y) {
        // 1，2 return x+y
        // 3，3
        // 6，4
    }, 初始值，不写的话默认取数组第0个元素)；
    a.reduceRight(function (x, y) {
        //4，3
        //7，2
        //9，1
    },初始值，不写的话默认取数组最后一个元素)
```

    6、indexOf[从左侧开始搜索]和lastIndexOf[从右侧开始搜索]：
    搜多整个数组的指定元素，如果有则返回下标。若没有则返回-1；
    第二个参数指定开始搜索的位置。负数则代表从右侧开始数。

### 9、数组类型：
    检测数组类型：Array.isArray();
    Object.property.toString.call(arr).slice(8, -1)
    
### 10、类数组对象：
    拥有length等一些数组特征。却不具备数组的特性。[个人理解：原型上不含Array.property]
    常见的是函数的参数：arguments，dom元素的List。
    可以通过：Array.prototype.数组方法.call(类数组对象)或
    [].slice().数组方法.call（类数组对象）；对它执行数组方法

### 11、总结：
    1、new Array(2,1,2) => [1,2];
        new Array(2) => [empty * 2] => arr[0] == undefined;
    2、索引都是属性名，但是范围在[0,2^32-2]内；数组都是对象。
    3、数组查找快于对象；若数组下标非连续，则为稀疏数组。
    4、数组长度控制数组大小。若length被锁死，则数组无法增删。但可变更。若用了Object.freeze则不可变更。
    5、数组的各个方法。
    6、instanceof 不可靠，不同窗体相同构造函数会由差别。
    7、类数组对象是由length的objcet。下标是key值而非索引。