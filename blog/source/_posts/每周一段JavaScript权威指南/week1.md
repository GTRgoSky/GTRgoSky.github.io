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

### （第三章---第八章）

> 一些日常开发中应该了解到的技术总结

------

#### 1：第三章（值类型；变量）

###### 1、JavaScript的类型分为两类：
    - 原始类型 --可以理解为存在了栈中
        null、undefiend、数字类型、字符串、布尔值，Symbol（ES6）
        原始值是不可更改的。如果值切换，其实是销毁Old，创建一个新的值并保存他。
    - 对象类型 --可以理解为存在了堆中
        对象（function函数；Object对象；Array数组）
        存在堆中，定义函数指向他后，其实在对应的栈中存的是指向堆内存的地址。
        当切换对象的值时，只是地址的切换，而堆内存的值并未被删除（若还有其他引用存在）
        所以，当修改对象类型的值时，其余的指向相同地址的定义对象都会被修改（得到的值）。
###### 2、数字范围：
    数字大小在-2^53 ～ 2^53 的范围
    
###### 3、判断NaN：
    用x!=x来判断NaN，只有为true时则可判读这个值为NaN

###### 4、null 和 undefined
     1. 对Null执行typeOf 得到的是object字符串
     2. Null是不占用内存的
     3. undefiend是预定义的全局变量，占用内存空间

###### 5、类型转换
| 值         | 转换为字符串    |  数字   |  布尔Boolean()   |  对象   | 
| --------   | -----:  | ----:  | ----:  | ----:  |
| undefiend<br>null| String() => 'undefiend'<br>String() => 'null'|Number() = > NaN<br> Number() = > NaN|Boolean() => false<br>false|Error<br>Error|
| true<br>false|String() => 'true'<br>String() => 'false' |  Number() = > 1 <br>  Number() = > 0 ||new Boolean(true)<br>new Boolean(false)|
| ""(空字符串)<br>'1.2'(非空数字)<br>'one'(非数字)||   Number() = > 0  <br>1.2<br> NaN  |false<br>true<br>true|new String('')<br>('1.2')<br>('one')|
| 0<br>-0<br>NaN<br>Infinity无穷大<br>-Infinity<br>1（非0）|String() => '0'<br>'0'<br>'NaN'<br>'Infinity'<br>'-Infinity'<br>'1'||false<br>false<br>false<br>true<br>true<br>true|new Number(0)<br>(-0)<br>(NaN)<br>(Infinity)<br>(-Infinity)<br>(1)|
| []<br>[9]<br>['a']<br>function|String([])=>''<br>String([9])=>'9'<br>['a'].join('')=>'a'<br>同对象的转换规则|Number([])=>0<br>Number([9])=>9<br>Number(['a'])=>NaN<br>Number(function)=>NaN|true<br>true<br>true<br>true||
| {}<br>| 若有toString()方法<br>则执行该方法，<br>如果他返回的是原始值<br>则将该值转换为字符串。<br>若无toStong()方法<br>或返回的不是原始值<br>那么调用ValueOf()<br>如果存在这个值<br>并返回的是原始类型<br>则转换为字符串。<br>若都没有则报错|如果有ValueOf则调用<br>若返回原始值则转为数字<br>否则调用toString()<br>若都没有则报错|Boolean() =>true||
>* 日期默认调取valueOf，所以日期做运算时也是默认使用valueof进行运算

###### 6、变量声明
    var sum 即声明了一个sum的变量。并且默认sum为undefiend；提前至顶部

###### 7、变量作用域
    1、在一个函数中声明一个变量。该变量的值为该函数私有
    2、若未声明变量就使用，则该变量为全局变量（被自动声明在全局）
    3、若全局变量和私有变量重名，则私有变量优先（作用域链）
    4、函数作用域链：Global->function内的变量->嵌套函数的变量，变量值根据这个链条去寻找使用的值。并且每个函数的作用域链都是独立的（闭包）。
    5、局部变量在其函数体内和他的子嵌套函数中都是有定义的。
    6、作用域链：类似一个链表（或对象列表），他连接着从函数到全局的所有变量。当一个函数定义时，就会生成一个这个作用域链。调用函数时，会生成一个新的对象保存函数中的局部变量。并且这个对象将会被保存在作用域链上（可以想象成我们的Global对象其实就是我们全局函数中的局部变量。所有全局变量都被保存在Global上，Global又保存在作用域链的最顶端）。同时这个函数会创建一个更长的调用作用域链（因为调用时可能作用域链和声明时有所不同）。
>* 更长的调用作用域链---->用内部函数来理解。当外部函数调用内部函数时。每次调用外部函数，内部函数都会生成一个新的作用域链（比如外部函数的变量改变，则内部函数的作用域链肯定是不一样的）。