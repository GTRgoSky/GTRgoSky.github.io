---
title: 每周一段JavaScript权威指南-第四周
date: 2020-06-06
tags:
- JavaScript权威指南
header-img: https://img-blog.csdnimg.cn/2020050614115438.png
catalog: true
toc_nav_num: true
---

# 第四周

（第九章）
> finish 本章内容较多，且相对重要

------

## 1：第九章（类和模块）

> 每个对象都是一个属性集合，相互之间没有联系，在JS中可以定义对象的类，让每个对象共享某一类的属性。 类的实现是基于原型机制实现的。



### 1、类和原型
   类的所有实例对象都是从一同一个原型对象上继承属性。因此，原型对象是类的核心。

   实现一个基础的继承概念代码：
   ```javascript
    function inhert(p: Object | null) {
        if(Objcet.create) return Objcet.create(p);
        function F() {};
        F.prototype = p;
        return new F();
    }
    var prototypeObj = {
        a: function() {return true};
    }

    var vm = inhert(prototypeObj); // => vm.a() ->true
   ```


### 2、类和构造函数：
    1、构造函数是用来初始化新创建的对象的。 使用new来调用。
    他会自己创造出一个新对象，构造函数this指向该对象，构造函数初始化this，最后把这个对象返回。
    构造函数的prototype被新创建的实例引用为原型。
    2、
        1）原型对象是类的唯一标识。
        可以用 vm instanceof Cls 来判断vm是否继承自Cls.prototype
        2）constructor属性：
        任何函数都可以用做构造函数。并且，调用构造函数是需要用到一个prototype属性。因此，每个函数都拥有一个prototype属性（bind绑定生成的函数除外）。
        这个属性值是一个对象，包含一个不可枚举的属性constructor（它的值是一个函数对象指向构造函数自己）这个构造函数为vm的类。
        3）构造函数、原型、实例关系：
        function Cls() {};
        构造函数：Cls === Cls.prototype.constructor
        原型(其实相当于新生成了一个原型对象，指向了这里)：
        Cls.prototype === vm.__proto__
        实例：vm === new Cls() // 这里抽象概念，实例化的一个方法。
    
### 3、类的继承：
> 定义类的步骤：
    1、先定义一个构造函数，并设置初始化的新对象的属性 <br>
    2、给构造函数的prototype设置实例的公共方法 <br>
    3、给构造函数定义类字段和类属性。

    直接上继承例子：
```javascript
    function ClsChild() {}
    function ClsParent(x, y) {
        this.x = x;
        this.y = y;
    }
    // 声明一个原型对象。
    var prototypeObj = {
        con() {
            console.log(this.x, this.y);
        },
    };
    // 给父类绑定新的原型对象
    ClsParent.prototype = {
        constructor: b,
        ...obj,
    };
    // 寄生式继承
    /**
    *
    * @param {Objcet} Child //子类
    * @param {Object} Parent //父类
    */
    function extend(Child, Parent) {
        function content(obj) {
            // if(Object.create) return Object.create(obj);
            // 声明一个构造函数
            function F() {} 
            //将构造函数的原型对象指向obj,且构造函数指向自己
            F.prototype = obj; 
            F.prototype.constructor = F;
            return new F(); // 返回F的实例用于作为子类的原型对象
        }

        // 将父类的原型对象传入，得到指向父类原型对象的实例
        let p = content(Parent.prototype);

        // 如果传入的是实例，则会多一层__prop__。因为实例的__prop__才是真正的指向原型对象 
        // let p = content(new Parent()); 
        
        // 将子类指向实例p
        // 引出 实例的__proto__指向原型对象
        Child.prototype = p;
        // 构造函数的标识类指向自己
        Child.prototype.constructor = Child;

        return Child;
    }

    // 这里其实是一个函数式编程思维，将函数作为对象传入并操作函数。但是它改变了原函数，所以不严谨。
    extend(ClsChild, ClsParent);
    var c2 = new ClsChild(1, 2); // {}

    // 如果想初始化子类的时候同时初始化父类。
    function superFun2() {
        /** 第一位为一个子类构造函数的this，后面拼接参数 */
        let arr = Array.from(arguments);
        let selfObj = arr.shift();
        // 获取子类的原型对象
        // selfObj.__proto__ === ClsChild.prototype 在这里是一个实例对象
        let vm = Object.getPrototypeOf(selfObj);
        // 获取父类的构造函数
        // vm.__proto__.constructor === ClsParent
        let fun = Object.getPrototypeOf(vm).constructor;
        fun.apply(selfObj, arr);
    }

    function ClsChild2(x, y) {
        this.a = x + 1;
        this.b = y + 1;
        superFun2(this, x, y);
    }

    extend(ClsChild2, ClsParent);
    var c2 = new ClsChild2(1, 2); // {a: 2, b: 3, x:1, y: 2}
```

### 4、类的扩充：
> 1、基于原型的继承机制是动态的：对象从原型继承属性， <br>
如果创建对象后原型发生改变，则所有的实例都会受到影响 <br>
这也是非基本类型在栈内存里存的是堆内存地址（指针）的原因。 <br>
2、可以使用Object.defineproperty生成不可枚举不可修改的继承方法。

### 5、类和类型：
三种用以检测任意对象的类的技术：
1、instanceof运算符：
c instanceof p 判断c的原型链上是否有p.prototype。
它的机制：先获取p.prototype然后看是否在c的原型链上。所以，这里的继承不一定是直接继承，也可以是子后代。
isPortotypeOf:
Object.prototype.isPrototypeOf(vm); 
在vm的原型链上搜索Object.prototype
2、constructor属性：
原型上.constructor === 原型的构造函数。
3、构造函数的名称：
暂无介绍。
4、拓展：
    鸭式辩型：对于检测对象的类，不是用相等的思维来解决。而是只关注对象能够做什么。

### 6、JS中面向对象技术 *（重要）
    1、类集合：
    集合（set）是一种数据结构，用于表示非重复值的无序集合。
    //eg: JS中的对象就是属性名和对应值的基本集合。
    // Set实现代码（添加，删除，查询）（P217页）：
```javascript
    function Set() {
        //一个构造函数 -- 让每一个不同的值都一个唯一字符串对应
        //集合数据保存在对象的属性里
        this.values = {}; 
        // 集合中值的个数
        this.n = 0; 
        //把所有参数添加到这个集合
        this.add.apply(this, arguments); 
    }

    //将每个参数都添加至集合中
    Set.prototype.add = function () {
        for (var i = 0; i < arguments.length; i++) {
            //遍历每个参数
            var val = arguments[i]; //待添加到集合中的值
            var str = Set._v2s(val); //把它转换为唯一字符串
            if (!this.values.hasOwnProperty(str)) {
                // 如果不在集合中
                this.values[str] = val; //将字符串和值对应起来
                this.n++; // 计数器加1
            }
        }
        return this;
    };

    // 从集合删除元素，这些元素由参数决定
    Set.prototype.remove = function () {
        for (var i = 0; i < arguments.length; i++) {
            //遍历每个参数
            var str = Set._v2s(arguments[i]); // 将字符串和值对应
            if (this.values.hasOwnProperty(str)) {
                //如果在集合中
                delete this.values[str]; // 删除
                this.n--; // 计数器减1
            }
        }
        return this;
    };

    // 如果集合包含这个值。返回true 否则返回 false
    Set.prototype.contains = function (value) {
        return this.values.hasOwnProperty(Set._v2s(value));
    };

    // 返回集合大小
    Set.prototype.size = function () {
        return this.n;
    };

    // 遍历集合中的所有元素，在指定的上下文中调用f
    Set.prototype.foreach = function (f, context) {
        for (var s in this.values) {
            // 遍历集合中的所有字符串
            if (this.values.hasOwnProperty(s)) {
                // 忽略继承属性
                f.call(context, this.values[s]); // 调用f，传入value，上下文指向context
            }
        }
    };

    //判断that是否和调用他的对象相等
    Set.prototype.equals = function (that) {
        if (this === that) return true;

        if (!(that instanceof Set)) return false;

        if (that.size() != this.size()) return false;

        try {
            this.foreach(function (v) {
                if (!that.contains(v)) throw false;
            });
            return true;
        } catch (e) {
            if (e == false) return false;
            throw e;
        }
    };

    // 这是一个内部函数，用来将任意的Javascript值和唯一的字符串对应起来
    Set._v2s = function (val) {
        switch (val) {
            case undefined: //特殊的原始值，值只返回一个字母
                return 'u';
            case null:
                return 'n';
            case true:
                return 't';
            case false:
                return 'f';
            default:
                switch (typeof val) {
                    case 'number':
                        return '#' + val;
                    case 'string':
                        return '"' + val;
                    default:
                        return '@' + objectId(val);
                }
        }

        /**
        * 对任意对象来说，都会返回一个字符串
        * 针对不同的对象，这个函数会返回不同的字符串
        * 对于同一个对象的多次调用，总是返回相同的字符串
        * 为了做到这一点，它给o创建了一个属性。在ES5中，这个属性是不可枚举且是只读的
        */
        function objectId(o) {
            var prop = '|**objectid**|'; //私有属性用于存放id
            if (!o.hasOwnProperty(prop)) {
                //如果对象没有id
                o[prop] = Set._v2s.next++; //将下一个值赋予它
            }
            return o[prop]; //返回id
        }
    };

    Set._v2s.next = 100; //设置初始的id值

    var b = function (v) {
        console.log(v);
    };
    var a = new Set(
        {},
        {},
        function a() {},
        function a() {},
        b,
        b,
        null,
        null,
        [1, 2, 2, 3]
    );

    /**
    * {
    *  n: 6,
    *  valuse: {
    *      @100:{},
    *      @101: {},
    *      @102: function a(){},
    *      @103: function a(){},
    *      @104: function() {},
    *      'n': null,
    *      @105: [1,2,2,3]
    *  }
    * }
    *
    * b['|**objectid**|'] = 104
    */
    console.log(a);

    var c = {
        a: 1,
        b: 2,
    };
    a.foreach(function (v) {
        if (typeof v == 'function' && v.name == 'b') {
            v(this.a);
        }
    }, c);

    console.log(this);

``` 
    2、枚举类型：它是值的有限集合。如果值定义为这个类型，则代表他是可以列出的。
    // 直接上代码例子(P219页）：
```javascript
    function enumeration(nTV) {
        // 返回一个构造函数。并且它的name属性指向他的实例
        var enu = function () {};

        var proto = (enu.prototype = {
            constructor: enu,
            toString: function () {
                return this.name;
            },
            valueOf: function () {
                return this.value;
            },
            toJSON: function (k) {
                return this.name;
            },
        });

        enu.values = [];

        for (name in nTV) {
            var e = Object.create(proto);
            e.name = name;
            e.value = nTV[name];
            enu[name] = e;
            enu.values.push(e);
        }

        enu.foreach = function (f, c) {
            for (var i = 0; i < this.values.length; i++) {
                f.call(c, this.values[i]);
            }
        };

        return enu;
    }

    function Card(suit, rank) {
        this.suit = suit;
        this.rank = rank;
    }

    Card.Suit = enumeration({ Culbs: 2, Diamonds: 1, Hearts: 3, Spades: 4 }); // 梅花，方块，红心，黑桃牌
    Card.Rank = enumeration({
        Two: 2,
        Three: 3,
        Four: 4,
        Five: 5,
        Six: 6,
        Seven: 7,
        Eight: 8,
        Nine: 9,
        Ten: 10,
        Jack: 11,
        Queen: 12,
        King: 13,
        Ace: 15,
    });

    Card.prototype.toString = function () {
        return this.rank.toString() + 'of' + this.suit.toString();
    };

    Card.prototype.compareTo = function (that) {
        if (this.rank < that.rank) return -1;
        if (this.rank > that.rank) return 1;
        return 0;
    };

    Card.orderByRank = function (a, b) {
        return a.compareTo(b);
    };

    Card.orderBySuit = function (a, b) {
        // a = {name: xxx, value: xxx},在比较时会调取valueOf的值
        var c = JSON.stringify(a);
        console.log(c);
        console.log(JSON.parse(c));
        if (a.suit < b.suit) return -1;
        if (a.suit > b.suit) return 1;
        if (a.rank < b.rank) return -1;
        if (a.rank > b.rank) return 1;
        return 0;
    };

    function Deck() {
        var cards = (this.cards = []);
        Card.Suit.foreach(function (s) {
            Card.Rank.foreach(function (r) {
                cards.push(new Card(s, r));
            });
        });
    }

    Deck.prototype.shuffle = function () {
        var deck = this.cards,
            len = deck.length;
        for (var i = len - 1; i > 0; i--) {
            var r = Math.floor(Math.random() * (i + 1)),
                temp;
            (temp = deck[i]), (deck[i] = deck[r]), (deck[r] = temp);
        }
        return this;
    };

    Deck.prototype.deal = function (n) {
        if (this.cards.length < n) throw 'out';
        return this.cards.splice(this.cards.length - n, n);
    };

    var deck = new Deck().shuffle();
    var hand = deck.deal(13).sort(Card.orderBySuit);
    console.log(hand);

```
    3、标准转换方法：
        1）toString():在希望使用字符串的时候会使用它。
        2）valueOf（）：当数字运算符（除了+）和关系运算符作用于数字文本标识的对象时，会自动调用。
        3）toJson（）：在调用JSON.stringify时；会调用这个方法。key作为参数传入。并接受其返回值作为序列化对象的value。举例：
```javascript
    var a = {aa: 1};
    a.toJSON = function(T) {return 6};
    JSON.stringify(a);
    // 此时会调用toJSON -》 参数T为：aa。
    // 最终得到的值为 "{'aa': 6}";
    // 当有多个对象时，循环调用toJSON
```
    4、比较方法：对象比较时，比较的是引用而不是值。
    如果将对象用于关系运算符进行比较，则会先调用对象的ValueOf；如果其返回的是一个原始值，则直接比较原始值。
    5、方法借用：
    举例：
    Array.prototype.funName.call(类数组)
    6、私有状态：
    我们可以通过将变量（或参数）闭包在一个构造函数内来模拟实现私有实例字段，调用构造函数会创建一个实例。为了做到这一点，需要在函数内部定义一个函数（闭包，这个函数可以访问外部函数的作用域对象）并将这个函数赋值给新创建的对象的属性。（注意：使用闭包函数会影响一定性能，且持续占有内存）。
    7、构造函数的重载与工厂方法：
    重载：通过传入不同的参数，执行不同的初始化方法。（类似方法重写）
    工厂方法：給一个定义一个方法，该方法返回自身的实例。（根据传入的参数不同，实例也会不同）

### 7、子类：
> 面向对象编程中：类B可以继承自类A。我们称A为父类，B为子类。<br>
B的实例从A继承了所有实例方法。类B可以定义自己的实例方法，有些方法可以重载A的方法。<br>
并且B的重载方法调用了A的重载方法。这种做法称为方法链。<br>
同理，B的构造函数可能需要调用A的构造函数，这种做法称为“构造函数链”。<br>
当子类层次太深时，需要定义抽象类。抽象类中没有定义方法实现，抽象方法是在抽象类的具体子类中实现的。

1、定义子类：
在ES6中可以直接用extends实现。
在Es5中可以将子类的constructor和prototype指向（继承）父类。并且遍历父类的类属性和类方法并拷贝。

2、构造函数和方法链：
```javascript
//举例：
function Set() {...};
// NoNullSet是Set的子类，NoNullSet extends Set.
function NoNullSet() {
    // 作为普通函数调用父类的构造函数来初始化通过该构造函数调用创建对象。
    // 构造函数链。
    Set.apply(this, arguments)
}
// 重载
NoNullSet.prototype.add = function newFun() {
    // 方法链
    return Set.prototype.add.apply(this, arguments);
};
```

3、组合VS子类：

组合：将父类的原型对象绑定在自己的实例上。并用于操作父类原型。生成的多个子类其实共用的是一个类。而不在是非别的类。

4、类的层次结构和抽象类：
    
    1）抽象类：
    定义一个类，该类定义了该类型所需要的方法集合（只有名称），但并不具备具体的方法实现。子类继承抽象类，并重载自己对应的方法实现（不需要的不重载）。
    含有抽象方法，无法被实例化（因为该方法并非完整函数无法被使用）。
    2）抽象方法：在子类中才被实现，父类中只存在声明而为实现。
    3）【免责，自己理解非书中定义】：JS中无真正的抽象类，只是讲一些共有的方法抽离，并用函数先声明但不实现，并在子类中具体实现所需要的公共方法。

### 8、ECMAScript5中的类：

1、让属性不可枚举：
Object.defineproperty(Obj, key, {enumerable: false})

2、定义不可变的类：
Object.defineproperty(Obj, key, {configurable: false})

Tip：Object.getOwnPropertyDescriptor(Obj, key)// 获取对应key的描述

### 模块：
让代码可以在很多不同场景中重用。

模块文件可以包含一个类定义，一组相关类，一个实用函数库或者一些可执行、待执行代码。

目标是支持大规模的程序开发，处理分散源中的代码的组装，并且能让代码正确运行。不同模块应该避免修改全局执行的上下文。不应当定义超过一个的全局标识。

### 总结：
    1、类的实现是基于原型继承机制的。如果两个实例都从同一个原型对象上继承属性，我们说他们是同一个类的实例。
    2、
        1）实用new调用构造函数会自动创建一个新对象，构造函数本身只需要初始化这个新的对象状态。
        2）特征：构造函数的prototype属性被用作新对象的原型：vm.__proto === Cls.prototype;
        3)在调用构造函数之前就已经创建了新对象（作用域对象），并通过构造函数中的this可以获取。构造函数只负责初始化this。
        4）原型对象时构造函数的唯一标识。
        5）除了bind方法外，任何函数都可以做构造函数（都具有prototype），这个属性值是一个对象，对象包含唯一一个不可枚举的属性constructor。它的属性值是一个函数对象，指代着这个原型对象的构造函数。
        6)
        ![构造函数关系图](https://img-blog.csdnimg.cn/20200606172833622.png)
    3、原型上的方法被所有实例继承，修改原型的方法会使所有的实例被影响，每个实例都是一个独立的对象，给实例定义的属性，不黑呗所有实例共享。
    4、
        1）instanceof，检测左侧的原型链上是否有右侧.prototype
        2）proto.isPrototypeOf(vm)检测proto方法（对象）是否在vm的原型链上
        3）用constructor检测， vm.constructor === 构造函数。
        4）鸭式辩类：检查一个对象是否实现了所有给定的参数表达式方法；只关注对象可以做什么，而不关注对象的类是什么。
        5）Objcet.getOwnPropertyNames()获取自身属性（包括不可枚举，不包括原型）。
    5、
        1）集合类Set标识非重复值的无序集合。集合类必须给集合中的每一个对象或者函数定义一个唯一的属性标识。
        2）枚举类型：值的有限集合，为这个类型的值是可枚举的。
        3）在希望使用字符串的地方用到对象的话会自动调用toString。
        4）当对象用在数学运算符和关系运算符时，会自动调用ValueOf。
        5）toJson。当对象调用JSON.stringify时，会调用这个值，并使用它的返回值。
        6）对象比较比的是引用，类实例的比较：简单的比较是用constructor来保证相同类型；复杂的比较可以采用鸭式辩型比较，比较两个实例的方法或者属性是否全部相等。
        7）方法借用。
        8）私有状态：通过将变量或者参数闭包在一个构造函数内部，来模拟实现私有字段，要在构造函数内定义一个内部函数，并将函数赋值给新创建的对象属性。
```javascript
function Range(from, to) {
    this.from = function() {return from};
    this.to = function() {return to};
}
```
        9）构造函数重载，即通过一些条件，使用不同的方法；重写原方法；覆盖愿方法。
    6、
        1）原型链：ChildVm中的方法重载了ParentVm中的方法，并且ChildVm的方法中还调用的ParentVm的方法。P233
        2）构造函数链：子类构造函数有时需要调用父类构造函数。
        3）抽象类：定义子类的共有方法，但是未有具体实现，具体实现在子类的构造函数原型上。（重载的方式）。P237
        4）组合优于继承：在继承中，每个组合需要创建一个新类P234；而组合将父类的实例用初始化属性保存。并在子类实例中直接调用父类原型方法。（通过父类实例）。（注意context）
        5）抽象方法：在子类中才被实现，父类只存在声明而为实现。
        6）抽象类：含有抽象方法且无法被正常实例化（因为方法未定义）；
        7）多态：一个父类面对不同子类，有多种实现方式（通过重载）。创建多种对象的特征。
    7、
        1）若直接在原型上做defineProperty的get和set劫持，则在子类上使用属性时，若该属性是继承来的，则被劫持。否则不会被劫持。
        2）在一个函数中可以用this instanceof Cls 来判断当前函数是否被作为用作构造函数，若是普通函数则this为undefined||global。P241
        3）用Object.defineProperty设置属性时，若是为存在过的属性，则其配置属性均为false。
        4）defineProperties（对象，{key2:{配置},key3:{配置...}}）；
        5）Object.getOwnPropertyDescriptor(对象，key)获取对象该key的配置