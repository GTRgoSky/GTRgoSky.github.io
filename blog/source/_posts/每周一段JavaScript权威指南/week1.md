---
title: 每周一段JavaScript权威指南-第一周
date: 2020-04-25
tags:
- JavaScript权威指南
header-img: http://note.youdao.com/yws/res/9100/WEBRESOURCE17201741f985d3792054e718efa766af
catalog: true
toc_nav_num: true
---
# 第一周

（第三章---第五章）

> 一些日常开发中应该了解到的技术总结

------

## 1：第三章（值类型；变量）

### 1、JavaScript的类型分为两类：
    - 原始类型 --可以理解为存在了栈中
        null、undefiend、数字类型、字符串、布尔值，Symbol（ES6）
        原始值是不可更改的。如果值切换，其实是销毁Old，创建一个新的值并保存他。
    - 对象类型 --可以理解为存在了堆中
        对象（function函数；Object对象；Array数组）
        存在堆中，定义函数指向他后，其实在对应的栈中存的是指向堆内存的地址。
        当切换对象的值时，只是地址的切换，而堆内存的值并未被删除（若还有其他引用存在）
        所以，当修改对象类型的值时，其余的指向相同地址的定义对象都会被修改（得到的值）。
### 2、数字范围：
    数字大小在-2^53 ～ 2^53 的范围
    
### 3、判断NaN：
    用x!=x来判断NaN，只有为true时则可判读这个值为NaN

### 4、null 和 undefined
     1. 对Null执行typeOf 得到的是object字符串
     2. Null是不占用内存的
     3. undefiend是预定义的全局变量，占用内存空间

### 5、类型转换
| 值         | 转换为字符串    |  数字   |  布尔Boolean()   |  对象   | 
| --------   | -----:  | ----:  | ----:  | ----:  |
| undefiend<br>null| String() => 'undefiend'<br>String() => 'null'|Number() = > NaN<br> Number() = > NaN|Boolean() => false<br>false|Error<br>Error|
| true<br>false|String() => 'true'<br>String() => 'false' |  Number() = > 1 <br>  Number() = > 0 ||new Boolean(true)<br>new Boolean(false)|
| ""(空字符串)<br>'1.2'(数字)<br>'o'(非数字)||   Number() = > 0  <br>1.2<br> NaN  |false<br>true<br>true|new String('')<br>('1.2')<br>('o')|
| 0<br>-0<br>NaN<br>Infinity无穷<br>-Infinity<br>1（非0）|String() => '0'<br>'0'<br>'NaN'<br>'Infinity'<br>'-Infinity'<br>'1'||false<br>false<br>false<br>true<br>true<br>true|new Number(0)<br>(-0)<br>(NaN)<br>(Infinity)<br>(-Infinity)<br>(1)|
| []<br>[9]<br>['a']<br>function|String([])=>''<br>String([9])=>'9'<br>['a'].join('')=>'a'<br>同对象的转换规则|Number([])=>0<br>Number([9])=>9<br>Number(['a'])=>NaN<br>Number(function)=>NaN|true<br>true<br>true<br>true||
| {}<br>| 若有toString()方法<br>则执行该方法，<br>如果他返回的是原始值<br>则将该值转换为字符串。<br>若无toStong()方法<br>或返回的不是原始值<br>那么调用ValueOf()<br>如果存在这个值<br>并返回的是原始类型<br>则转换为字符串。<br>若都没有则报错|如果有ValueOf则调用<br>若返回原始值则转为数字<br>否则调用toString()<br>若都没有则报错|Boolean() =>true||
>* 日期默认调取valueOf，所以日期做运算时也是默认使用valueof进行运算

### 6、变量声明
    var sum 即声明了一个sum的变量。并且默认sum为undefiend；提前至顶部

### 7、变量作用域
1、在一个函数中声明一个变量。该变量的值为该函数私有
2、若未声明变量就使用，则该变量为全局变量（被自动声明在全局）
3、若全局变量和私有变量重名，则私有变量优先（作用域链）
4、函数作用域链：Global->function内的变量->嵌套函数的变量，变量值根据这个链条去寻找使用的值。并且每个函数的作用域链都是独立的（闭包）。
5、局部变量在其函数体内和他的子嵌套函数中都是有定义的。
6、作用域链：类似一个链表（或对象列表），他连接着从函数到全局的所有变量。当一个函数定义时，就会生成一个这个作用域链。调用函数时，会生成一个新的对象保存函数中的局部变量。并且这个对象将会被保存在作用域链上（可以想象成我们的Global对象其实就是我们全局函数中的局部变量。所有全局变量都被保存在Global上，Global又保存在作用域链的最顶端）。同时这个函数会创建一个更长的调用作用域链（因为调用时可能作用域链和声明时有所不同）。
>* 更长的调用作用域链---->用内部函数来理解。当外部函数调用内部函数时。每次调用外部函数，内部函数都会生成一个新的作用域链（比如外部函数的变量改变，则内部函数的作用域链肯定是不一样的）。

![作用域链自己的理解](https://note.youdao.com/yws/res/9103/WEBRESOURCEc4eec7caee931527f377072cfc48607b)

-----

## 2：第四章（表达式；运算符）

### 1、表达式

1、原始表达式：常量 | 直接量 ('string')、关键字(保留字this)、变量(var i 的 i)；
2、对象和数组表达式： {}; []
3、函数定义表达式： var square = function() {};
4、函数声明表达式 function square() {};
5、属性访问表达式  x.a | x[a] <= x = {a:1};当使用访问表达式时，若是基本类型（非undefined、null）则会用构造函数生成Object再去取值。如果是undefined、null则抛出错误。
6、调用表达式： a() <= function a() {};如果是 x = {a:function(){}}, x.a() , 则称之为方法调用。
7、对象创建表达式： new Object()；此时是用this指向了一个{}，并且在生成过程中为this添加属性。
8、赋值表达式： a = 1 ； b = c = 1; 赋值表达式的执行顺序是从右向左执行的。

### 2、运算符

1、弄清左值（表达式只出现在运算符左侧）、运算符优先级；
2、在比较值时，“==” 比较时：若两边类型不同则会进行类型转换，然后比较。
“===”时：不进行转换。
对于对象或者数组的比较，其实是比较两边的引用地址是否相同。
3、in 运算符： in希望他的左值是个字符串或者可以转成字符串的值。如果右侧的对象拥有左侧值的属性名，则返回true
4、instanceof运算符： 希望左操作数是一个object。右侧是对象的类。如果左侧是右侧的实例，则返回true。 比如 o in f 。系统会先获取f.prototype原型链，然后在该链上找是否存在o，如果找到则返回true。
5、delete运算符： 用于删除对象。但是不能删除全局变量（除非手动写的this.x)。
在严格模式中，如果del失败则会抛出错误。在非严格模式中则返回一个false
6、void：直接返回undefiend，忽略函数的真实返回值。
7、typeof运算符：
| x          | typeof x   |
| --------   | -----:  |
| undefiend  | 'undefiend' |
| null       | 'object' |
| true/false | 'boolean' |
| 数字/NaN    | 'number' |
| String     | 'string' |
| 函数function  | 'function' |
| object/array  | 'object' |
| 内置对象  | 'object' |
| 宿主对象（window）  | 由浏览器确定 |

### 3、表达式的计算（eval)

1、eval 赋予了js在执行过程中创建并执行函数的能力。它的环境变量取决于当前的执行作用域。而一般的函数执行，其作用域一般在全局（即使是内置函数，this指向一般也是Global）。

-----

## 3：第五章（语句）

### 1、表达式语句
> 将表达式当作语句执行。
eg: a = 1;  
fun(); 
b = c + 1;

### 2、声明语句
> var i; 
var j = 0; 
var f = function(){}[函数定义语句]; 
function fun() {}[函数声明语句，若是嵌套声明,则只能声明在顶部，不能声明在if、while等语句中];
函数声明语句中，函数名是一个变量名，变量指向函数对象。该函数被显示的提前。所以即使不写在顶部也可以在任意地方调用。但是在函数定义语句中，提前的只有声明的对象，只有当赋值语句执行后才能执行函数。

### 3、条件语句
1、if...else if...else;
2、switch:
```javascript
//expression是按照 === 来计算的
switch(expression) {
    // statements
    case 1:
        break;
    default:
        break;
}```
3、循环：

    - while
        while(expression) {}; //先判断在执行第一次
        do{}while(expression); // 至少执行一次
    - for(x in (obj)) {};
        for...in遍历对象的所有可枚举的属性。并且包括原型链上的可枚举属性。
        若遍历的是数组，x为key值。

### 4、跳转    
    1、 标签语句：
        label：加入label后循环中，break或者continue中断并跟label则循环直接从label声明的循环开始执行。
```javascript
labelname：while(expression) {
   while() {
        braek labelname;//直接从外部while开始执行
   }
}```

    2、break：直接中断语句块，跳出循环继续向下执行代码。
    3、continue：
        在while中执行continue，while的判断会立即执行一次，然后在从头执行一次。
        在do...while中会直接跳到结尾。然后判断一次。
        在for中，会执行一次自增表达式，然后在执行判断表达式。
    4、return：在循环中类似break；直接结束函数的执行，并将其返回值作为函数的返回值。
    5、throw：抛出错误，停止执行函数。并跳转至就近的异常处理程序中。
    6、try...catch...finally:
        try：执行正常代码逻辑。若有错误或主动throw则进入catch。若return 则直接进入finally
        catch：接受错误逻辑，并且有e参数捕获错误信息。
        finally：无论前两个代码块怎么执行，这里都是最终被执行的代码块。
        在循环语句中的try...catch...finally，若终止循环，则必进入finally后在执行下一次循环。
        
### 5、其他语句
    1、with(obj){}[严格模式禁用]: 将obj添加到作用域链头部。执行代码块，然后恢复作用域链
    2、use strict:
        1）严格模式禁用with
        2）严格模式所有变量要先声明。否则报错。【非严格模式时绑定给全局对象】
        3）调用函数时，this是undefined。【非严格是global】
        4）apply，call的第一个参数指向自己。【非严格中null和undefined指向global】；
        5）给只读属性或不可扩展属性赋值报错【非严格不报错。只会失败】
        6）argument在严格模式中无法修改实参。即：
            function(a = [1,2,3]) {
                argument[1] = 4;
                a[1] === 2;
            }
        7)声明多个同一名称参数会报错。
    