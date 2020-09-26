## 你不知道的JavaScript中卷 - [美]KYLE SIMPSON著 /赵望野 梁杰译

读完时间: 2020年9月9日

出版时间: 2015年8月

# 第一部分: 类型和语法

## 第1章: 类型

### 1.1: 类型

全面掌握 JavaScript 的类型之后，我们旨在改变对强制类型转换的成见，看到它的好处并且意识到它的缺点被过分夸大了。

### 1.2: 内置类型

JavaScript 有七种内置类型：
• 空值（null）
• 未定义（undefined）
• 布尔值（ boolean）
• 数字（number）
• 字符串（string）
• 对象（object）
• 符号（symbol，ES6 中新增）

### 1.3: 值和类型

JavaScript 中的变量是没有类型的，只有值才有。变量可以随时持有任何类型的值。

#### 1.3.1: undefined 和 undeclared

已在作用域中声明但还没有赋值的变量，是 undefined 的。相反，还没有在作用域中声明
过的变量，是 undeclared 的。

#### 1.3.2: typeof Undeclared

该安全防范机制对在浏览器中运行的 JavaScript 代码来说还是很有帮助的，因为多个脚本
文件会在共享的全局命名空间中加载变量。

### 1.4: 小结

变量没有类型，但它们持有的值有类型。类型定义了值的行为特征。

## 第2张: 值

### 2.1: 数组

和其他强类型语言不同，在 JavaScript 中，数组可以容纳任何类型的值，可以是字符串、
数字、对象（object），甚至是其他数组（多维数组就是通过这种方式来实现的）：

```javascript
var a = [ 1, "2", [3] ];
a.length; // 3
a[0] === 1; // true
a[2][0] === 3; // true
```

> 使用 delete 运算符可以将单元从数组中删除，但是请注意，单元删除后，数组的 length 属性并不会发生变化。第 5 章将详细介绍 delete 运算符。

### 2.2: 字符串

JavaScript 中字符串是不可变的，而数组是可变的。并且 a[1] 在 JavaScript 中并非总是合法语法，在老版本的 IE中就不被允许（现在可以了）。正确的方法应该是 a.charAt(1)。
字符串不可变是指字符串的成员函数不会改变其原始值，而是创建并返回一个新的字符
串。而数组的成员函数都是在其原始值上进行操作。

```javascript
var c = a
 // 将a的值转换为字符数组
 .split( "" )
 // 将数组中的字符进行倒转
 .reverse()
 // 将数组中的字符拼接回字符串
 .join( "" );
c; // "oof"
```

如果需要经常以字符数组的方式来处理字符串的话，倒不如直接使用数组。这样就不用在字符串和数组之间来回折腾。可以在需要时使用 join("") 将字符数组转换为字符串。

### 2.3: 数字

JavaScript 中的“整数”就是没有小数的十进制数。所以 42.0 即等同于“整数”42。(number类型)
与大部分现代编程语言（包括几乎所有的脚本语言）一样，JavaScript 中的数字类型是基于 IEEE 754 标准来实现的，该标准通常也被称为“浮点数”。JavaScript 使用的是“双精度”格式（即 64 位二进制）。

```javascript
var a = 5E10;
a; // 50000000000
a.toExponential(); // "5e+10"
var b = a * a;
b; // 2.5e+21
var c = 1 / a;
c; // 2e-11
```

#### 2.3.2: 较小的数值

```javascript
0.1 + 0.2 === 0.3; // false
```

简单来说，二进制浮点数中的 0.1 和 0.2 并不是十分精确，它们相加的结果并非刚好等于0.3，而是一个比较接近的数字 0.30000000000000004，所以条件判断结果为 false。

```javascript
function numbersCloseEnoughToEqual(n1,n2) {
 return Math.abs( n1 - n2 ) < Number.EPSILON;
}
var a = 0.1 + 0.2;
var b = 0.3;
numbersCloseEnoughToEqual( a, b ); // true
numbersCloseEnoughToEqual( 0.0000001, 0.0000002 ); // false
```

能够呈现的最大浮点数大约是 1.798e+308（这是一个相当大的数字），它定义在 Number.MAX_VALUE 中。最小浮点数定义在 Number.MIN_VALUE 中，大约是 5e-324，它不是负数，但无限接近于 0 ！

#### 2.3.3: 整数安全范围

能够被“安全”呈现的最大整数是 2^53 - 1，即 9007199254740991，在 ES6 中被定义为Number.MAX_SAFE_INTEGER。最小整数是 -9007199254740991，在 ES6 中被定义为 Number.MIN_SAFE_INTEGER。

####2.3.4: 整数检测

```javascript
1.也可以为 ES6 之前的版本 polyfill Number.isInteger(..) 方法：
if (!Number.isInteger) {
 Number.isInteger = function(num) {
 return typeof num == "number" && num % 1 == 0;
 };
}
Number.isSafeInteger( Number.MAX_SAFE_INTEGER ); // true
Number.isSafeInteger( Math.pow( 2, 53 ) ); // false
Number.isSafeInteger( Math.pow( 2, 53 ) - 1 ); // true

2.可以为 ES6 之前的版本 polyfill Number.isSafeInteger(..) 方法：
if (!Number.isSafeInteger) {
 Number.isSafeInteger = function(num) {
 return Number.isInteger( num ) &&
 Math.abs( num ) <= Number.MAX_SAFE_INTEGER;
 };
}
```

#### 2.3.5: 32位有符号整数

虽然整数最大能够达到 53 位，但是有些数字操作（如数位操作）只适用于 32 位数字，所以这些操作中数字的全范围就要小很多，变成从 Math.pow(-2,31)（-2147483648，约－21 亿）到 Math.pow(2,31) - 1（2147483647，约 21 亿）。

### 2.4: 特殊数值

undefined 类型只有一个值，即 undefined。null 类型也只有一个值，即 null。它们的名称既是类型也是值。
undefined 和 null 常被用来表示“空的”值或“不是值”的值。二者之间有一些细微的差别。
   1.undefined 指从未赋值
   2.null 指曾赋过值，但是目前没有值
null 是一个特殊关键字，不是标识符，我们不能将其当作变量来使用和赋值。然而undefined 却是一个标识符，可以被当作变量来使用和赋值。

#### 2.4.3: 特殊数字

1.不是数字的数字

NaN 意指“不是一个数字”（not a number），这个名字容易引起误会，后面将会提到。将它理解为“无效数值”“失败数值”或者“坏数值”可能更准确些。

```JavaScript
var a = 2 / "foo"; // NaN
typeof a === "number"; // true
```

2.无穷数

熟悉传统编译型语言（如 C）的开发人员可能都遇到过编译错误（compiler error）或者运行时错误（runtime exception），例如“除以 0”：

```JavaScript
var a = Number.MAX_VALUE; // 1.7976931348623157e+308
a + a; // Infinity
a + Math.pow( 2, 970 ); // Infinity
a + Math.pow( 2, 969 ); // 1.7976931348623157e+308
```

3.零值

```JavaScript
var a = 0 / -3; // -0
var b = 0 * -3; // -0
```

>JSON.stringify(-0) 返回 "0"，而 JSON.parse("-0") 返回 -0。

#### 2.4.4: 特殊等式

ES6 中新加入了一个工具方法 Object.is(..) 来判断两个值是否绝对相等，可以用来处理上述所有的特殊情况：

```JavaScript
var a = 2 / "foo";
var b = -3 * 0;
Object.is( a, NaN ); // true
Object.is( b, -0 ); // true
Object.is( b, 0 ); // false
```

### 2.5: 值和引用

```JavaScript
function foo(x) {
 x.push( 4 );
 x; // [1,2,3,4]
 // 然后
 x.length = 0; // 清空数组
 x.push( 4, 5, 6, 7 );
 x; // [4,5,6,7]
}
var a = [1,2,3];
foo( a );
a; // 是[4,5,6,7]，不是[1,2,3,4]
```

记住: 我们无法自行决定使用值复制还是引用复制，一切由值的类型来决定。

### 2.6: 小结

简单标量基本类型值（字符串和数字等）通过值复制来赋值 / 传递，而复合值（对象等）通过引用复制来赋值 / 传递。JavaScript 中的引用和其他语言中的引用 / 指针不同，它们不能指向别的变量 / 引用，只能指向值。

## 第3章: 原生函数

常用的原生函数有：
• String()
• Number()
• Boolean()
• Array()
• Object()
• Function()
• RegExp()
• Date()
• Error()
• Symbol()——ES6 中新加入的！

### 3.1: 内部属性[[class]]

所有 typeof 返回值为 "object" 的对象（如数组）都包含一个内部属性 [[Class]]（我们可
以把它看作一个内部的分类，而非传统的面向对象意义上的类）。这个属性无法直接访问，
一般通过 Object.prototype.toString(..) 来查看。例如：

```javascript
Object.prototype.toString.call( [1,2,3] );
// "[object Array]"
Object.prototype.toString.call( /regex-literal/i );
// "[object RegExp]"
Object.prototype.toString.call( "abc" );
// "[object String]"
Object.prototype.toString.call( 42 );
// "[object Number]"
Object.prototype.toString.call( true );
// "[object Boolean]"
Object.prototype.toString.call( null );
// "[object Null]"
Object.prototype.toString.call( undefined );
// "[object Undefined]" 然后正则匹配一下,获取类型
```

### 3.2: 封装对象包装

封 装 对 象（object wrapper） 扮 演 着 十 分 重 要 的 角 色。 由 于 基 本 类 型 值 没 有 .length和 .toString() 这样的属性和方法，需要通过封装对象才能访问，此时 JavaScript 会自动为基本类型值包装（box 或者 wrap）一个封装对象：

```javascript
var a = "abc";
a.length; // 3
a.toUpperCase(); // "ABC"
```

如果想要自行封装基本类型值，可以使用 Object(..) 函数（不带 new 关键字）：

```javascript
var a = "abc";
var b = new String( a );
var c = Object( a );
typeof a; // "string"
typeof b; // "object"
typeof c; // "object"
b instanceof String; // true
c instanceof String; // true
Object.prototype.toString.call( b ); // "[object String]"
Object.prototype.toString.call( c ); // "[object String]"
```

再次强调，一般不推荐直接使用封装对象（如上例中的 b 和 c），但它们偶尔也会派上用场。

### 3.3: 拆封

如果想要得到封装对象中的基本类型值，可以使用 valueOf() 函数：

```JavaScript
var a = new String( "abc" );
var b = new Number( 42 );
var c = new Boolean( true );
a.valueOf(); // "abc"
b.valueOf(); // 42
c.valueOf(); // true
```

### 3.4: 原生函数作为构造函数

```javascript
function fakeJoin(arr,connector) {
 var str = "";
 for (var i = 0; i < arr.length; i++) {
 if (i > 0) {
 str += connector;
 }
 if (arr[i] !== undefined) {
 str += arr[i];
 }
 }
 return str;
}
var a = new Array( 3 );
fakeJoin( a, "-" ); // "--"
```

从中可以看出，join(..) 首先假定数组不为空，然后通过 length 属性值来遍历其中的元素。而 map(..) 并不做这样的假定，因此结果也往往在预期之外，并可能导致失败。

#### 3.4.2: Object(..)、Function(..) 和 RegExp(..)

```JavaScript
var c = new Object();
c.foo = "bar";
c; // { foo: "bar" }
var d = { foo: "bar" };
d; // { foo: "bar" }
var e = new Function( "a", "return a * 2;" );
var f = function(a) { return a * 2; }
function g(a) { return a * 2; }
var h = new RegExp( "^a*b+", "g" );
var i = /^a*b+/g;
```

#### 3.4.3: Date(..) 和 Error(..)

> 如果调用 Date() 时不带 new 关键字，则会得到当前日期的字符串值。其具体格式规范没有规定，浏览器使用 "Fri Jul 18 2014 00:31:02 GMT-0500 (CDT)"这样的格式来显示。

#### 3.4.4: Symbol(..)

我们可以使用 Symbol(..) 原生构造函数来自定义符号。但它比较特殊，不能带 new 关键字，否则会出错：

```JavaScript
var mysym = Symbol( "my own symbol" );
mysym; // Symbol(my own symbol)
mysym.toString(); // "Symbol(my own symbol)"
typeof mysym; // "symbol"
var a = { };
a[mysym] = "foobar";
Object.getOwnPropertySymbols( a );
// [ Symbol(my own symbol) ]
```

#### 3.4.5: 原生原型

• String#indexOf(..) 在字符串中找到指定子字符串的位置。
• String#charAt(..) 获得字符串指定位置上的字符。
• String#substr(..)、String#substring(..) 和 String#slice(..) 获得字符串的指定部分。
• String#toUpperCase() 和 String#toLowerCase() 将字符串转换为大写或小写。
• String#trim() 去掉字符串前后的空格，返回新的字符串。

以上方法并不改变原字符串的值，而是返回一个新字符串。

### 3.5: 小结

JavaScript 为基本数据类型值提供了封装对象，称为原生函数（如 String、Number、Boolean等）。它们为基本数据类型值提供了该子类型所特有的方法和属性（如：String#trim() 和Array#concat(..)）。

## 第4章: 强制类型转换

### 4.1: 值类型转换

将值从一种类型转换为另一种类型通常称为类型转换（type casting），这是显式的情况；隐式的情况称为强制类型转换（coercion）。

```javascript
var a = 42;
var b = a + ""; // 隐式强制类型转换
var c = String( a ); // 显式强制类型转换
```

### 4.2: 抽象值操作

ToString: 负责处理非字符串到字符串的强制类型转换。

```javascript
var a = [1,2,3];
a.toString(); // "1,2,3"
// json字符串转换
JSON.stringify( 42 ); // "42"
JSON.stringify( "42" ); // ""42"" （含有双引号的字符串）
JSON.stringify( null ); // "null"
JSON.stringify( true ); // "true"
// JSON.stringify(..) 在对象中遇到 undefined、function 和 symbol 时会自动将其忽略，在
数组中则会返回 null（以保证单元位置不变）。
JSON.stringify( undefined ); // undefined
JSON.stringify( function(){} ); // undefined
JSON.stringify(
 [1,undefined,function(){},4]
); // "[1,null,null,4]"
JSON.stringify(
 { a:2, b:function(){} }
); // "{"a":2}"
```

ToNumber: 有时我们需要将非数字值当作数字来使用，比如数学运算。

true 转换为 1，false 转换为 0。undefined 转换为 NaN，null 转换为 0

```JavaScript
var a = {
 valueOf: function(){
 return "42";
 }
};
var b = {
 toString: function(){
 return "42";
 }
};
var c = [4,2];
c.toString = function(){
 return this.join( "" ); // "42"
};
Number( a ); // 42
Number( b ); // 42
Number( c ); // 42
Number( "" ); // 0
Number( [] ); // 0
Number( [ "abc" ] ); // NaN
```

ToBoolean: 我们常误以为数值 1 和 0 分别等同于 true 和 false。在有些语言中可能是这样，但在 JavaScript 中布尔值和数字是不一样的。虽然我们可以将 1 强制类型转换为 true，将 0 强制类型转换为 false，反之亦然，但它们并不是一回事。

1.假值的布尔强制类型转换结果为 false。

• undefined
• null
• false
• +0、-0 和 NaN
• "" 

2.上例的字符串看似假值，但所有字符串都是真值。不过 "" 除外，因为它是假值列表中唯一的字符串。

```JavaScript
var a = "false";

var b = "0";

var c = "''";

var d = Boolean( a && b && c );

d; // true
```

### 4.3: 显式强制类型转换

显示强制装换

```JavaScript
var a = 42;
var b = String( a );
var c = "3.14";
var d = Number( c );
b; // "42"
d; // 3.14
```

1. 日期显式转换为数字

```JavaScript
var timestamp = new Date().getTime();
// var timestamp = (new Date()).getTime();
// var timestamp = (new Date).getTime();
```

2. 奇特的 ~ 运算符

```JavaScript
0 | -0; // 0
0 | NaN; // 0
0 | Infinity; // 0
0 | -Infinity; // 0
```

3. 字位截除

```JavaScript
~~1E20 / 10; // 166199296
1E20 | 0 / 10; // 1661992960
(1E20 | 0) / 10; // 166199296
```

#### 4.3.2: 显示解析数字字符串

```JavaScript
var a = "42";
var b = "42px";
Number( a ); // 42
parseInt( a ); // 42
Number( b ); // NaN
parseInt( b ); // 42
```

#### 4.3.3: 显式转换为布尔值

```javascript
var a = "0";
var b = [];
var c = {};
var d = "";
var e = 0;
var f = null;
var g;
Boolean( a ); // true
Boolean( b ); // true
Boolean( c ); // true
Boolean( d ); // false
Boolean( e ); // false
Boolean( f ); // false
Boolean( g ); // false
```

### 4.4: 隐式强制类型转换

隐式强制类型转换指的是那些隐蔽的强制类型转换，副作用也不是很明显。换句话说，你
自己觉得不够明显的强制类型转换都可以算作隐式强制类型转换。
显式强制类型转换旨在让代码更加清晰易读，而隐式强制类型转换看起来就像是它的对立
面，会让代码变得晦涩难懂。

1.隐式的简化

2.字符串和数字之间的隐式强制类型转换

```javascript
var a = "42";
var b = "0";
var c = 42;
var d = 0;
a + b; // "420"
c + d; // 42
```

> 有一个坑常常被提到，即 [] + {} 和 {} + []，它们返回不同的结果，分别是"[object Object]" 和 0。我们将在 5.1.3 节详细介绍。

3.布尔值到数字的隐式强制类型转换

```JavaScript
function onlyOne(a,b,c) {
 return !!((a && !b && !c) ||
 (!a && b && !c) || (!a && !b && c));
}
var a = true;
var b = false;
onlyOne( a, b, b ); // true
onlyOne( b, a, b ); // true
onlyOne( a, b, a ); // false
```

4.隐式强制类型转换为布尔值

(1) if (..) 语句中的条件判断表达式。
(2) for ( .. ; .. ; .. ) 语句中的条件判断表达式（第二个）。
(3) while (..) 和 do..while(..) 循环中的条件判断表达式。
(4) ? : 中的条件判断表达式。
(5) 逻辑运算符 ||（逻辑或）和 &&（逻辑与）左边的操作数（作为条件判断表达式）。

```JavaScript
var a = 42;
var b = "abc";
var c;
var d = null;
if (a) {
 console.log( "yep" ); // yep
}
while (c) {
 console.log( "nope, never runs" );
}
c = d ? a : b;
c; // "abc"
if ((a && d) || c) {
 console.log( "yep" ); // yep
}
```

5.|| 和 &&

```JavaScript
var a = 42;
var b = "abc";
var c = null;
a || b; // 42
a && b; // "abc"
c || b; // "abc"
c && b; // null
```

6.符号的强制类型转换

```javascript
var s1 = Symbol( "cool" );
String( s1 ); // "Symbol(cool)"
var s2 = Symbol( "not cool" );
s2 + ""; // TypeError
```

### 4.5: 宽松相等和严格相等

• 如果两边的值中有 true 或者 false，千万不要使用 ==。
• 如果两边的值中有 []、"" 或者 0，尽量不要使用 ==。

### 4.6: 抽象关系比较

ES5 规范 11.8.5 节定义了“抽象关系比较”（abstract relational comparison），分为两个部分：比较双方都是字符串（后半部分）和其他情况（前半部分）。

### 4.7: 小结

本章介绍了 JavaScript 的数据类型之间的转换，即强制类型转换：包括显式和隐式。

## 第5章: 语法

### 5.1: 语句和表达式

“句子”（sentence）是完整表达某个意思的一组词，由一个或多个“短语”（phrase）组成，它们之间由标点符号或连接词（and 和 or 等）连接起来。短语可以由更小的短语组成。有些短语是不完整的，不能独立表达意思；有些短语则相对完整，并且能够独立表达某个意思。这些规则就是英语的语法。

#### 5.1.1: 语句的结果值

```JavaScript
var b;
if (true) {
 b = 4 + 38;
} // 在控制台 /REPL 中输入以上代码应该会显示 42，即最后一个语句 / 表达式 b = 4 + 38 的结果值。
```

#### 5.1.2: 表达式的副作用

```JavaScript
function foo() {
 a = a + 1;
}
var a = 1;
foo(); // 结果值：undefined。副作用：a的值被改变
```

#### 5.1.3: 上下文规则

1.大括号

```JavaScript
// 标签为foo的循环
foo: for (var i=0; i<4; i++) {
 for (var j=0; j<4; j++) {
 // 如果j和i相等，继续外层循环
 if (j == i) {
 // 跳转到foo的下一个循环
 continue foo;
 }
 // 跳过奇数结果
 if ((j * i) % 2 == 1) {
 // 继续内层循环（没有标签的）
 continue;
 }
 console.log( i, j );
 }
}
// 1 0
// 2 0
// 2 1
// 3 0
// 3 2
```

2.代码块

还有一个坑常被提到（涉及强制类型转换，参见第 4 章）：
[] + {}; // "[object Object]"
{} + []; // 0
表面上看 + 运算符根据第一个操作数（[] 或 {}）的不同会产生不同的结果，实则不然。

3.对象解构

```JavaScript
function getData() {
 // ..
 return {
 a: 42,
 b: "foo"
 };
}
var { a, b } = getData();
console.log( a, b ); // 42 "foo"

```

4.else if 和可选代码块

if (b) { .. } else { .. } 实际上是跟在 else 后面的一个单独的语句，所以带不带 { } 都可以。换句话说，else if 不符合前面介绍的编码规范，else 中是一个单独的 if 语句。

### 5.2: 运算符优先级

```JavaScript
true || false && false; // true
(true || false) && false; // false
true || (false && false); // true
```

这说明 && 运算符先于 || 执行，而且执行顺序并非我们所设想的从左到右。原因就在于运
算符优先级。

内容较多只可意会不可言传

### 5.3: 自动分号

如果 JavaScript 解析器发现代码行可能因为缺失分号而导致错误，那么它就会自动补上分号。并且，只有在代码行末尾与换行符之间除了空格和注释之外没有别的内容时，它才会这样做。

```JavaScript
var a = 42, b
c;	
```

纠错机制: 我建议在所有需要的地方加上分号，将对 ASI 的依赖降到最低。(作者看法)

### 5.4: 错误

JavaScript 不仅有各种类型的运行时错误（TypeError、ReferenceError、SyntaxError 等），它的语法中也定义了一些编译时错误。

ES6 规范定义了一个新概念，叫作 TDZ（Temporal Dead Zone，暂时性死区）。TDZ 指的是由于代码中的变量还没有初始化而不能被引用的情况。

```JavaScript
{
 a = 2; // ReferenceError!
 let a;
}
// 对于提前使用变量的影响
{
 typeof a; // undefined
 typeof b; // ReferenceError! (TDZ)
 let b;
}
```

### 5.5: 函数参数

另一个 TDZ 违规的例子是 ES6 中的参数默认值（参见本系列的《你不知道的 JavaScript（下卷）》的“ES6 & Beyond”部分）：

```JavaScript
function foo( a = 42, b = a + 1 ) {
 console.log( a, b );
}
foo(); // 42 43
foo( undefined ); // 42 43
foo( 5 ); // 5 6
foo( void 0, 7 ); // 42 7
foo( null ); // null 1
// 区别对比
function foo( a = 42, b = a + 1 ) {
 console.log(
 arguments.length, a, b,
 arguments[0], arguments[1]
 );
}
foo(); // 0 42 43 undefined undefined
foo( 10 ); // 1 10 11 10 undefined
foo( 10, undefined ); // 2 10 11 10 undefined
foo( 10, null ); // 2 10 null 10 null
```

### 5.6: try...finally

```JavaScript
function foo() {
   try {
   	return 42;
   }
   finally {
   	console.log( "Hello" );
   }
   console.log( "never runs" );
}
console.log( foo() );
// Hello
// 42
 function foo() {
   try {
      throw 42;
   }
   finally {
      console.log( "Hello" );
   }
   console.log( "never runs" );
  }
console.log( foo() );
// Hello
// Uncaught Exception: 42
```

这里 return 42 先执行，并将 foo() 函数的返回值设置为 42。然后 try 执行完毕，接着执行 finally。最后 foo() 函数执行完毕，console.log(..) 显示返回值。

### 5.7: switch

简单的switch

```javascript
switch (a) {
 case 2:
 // 执行一些代码
 break;
 case 42:
 // 执行另外一些代码
 break;
 default:
 // 执行缺省代码
}
```

### 5.8: 小结

语句和表达式在英语中都能找到类比——语句就像英语中的句子，而表达式就像短语。表达式可以是简单独立的，否则可能会产生副作用。

# 第二部分: 异步和性能

## 第1章: 异步于将来

事实上，程序中现在运行的部分和将来运行的部分之间的关系就是异步编程的核心。

### 1.1: 分块的程序

```JavaScript
// 现在：
function now() {
 return 21;
}
function later() { .. }
var answer = now();
setTimeout( later, 1000 );
// 将来：
answer = answer * 2;
console.log( "Meaning of life:", answer );
```

任何时候，只要把一段代码包装成一个函数，并指定它在响应某个事件（定时器、鼠标点击、Ajax 响应等）时执行，你就是在代码中创建了一个将来执行的块，也由此在这个程序中引入了异步机制。

1.异步控制台

在某些条件下，某些浏览器的 console.log(..) 并不会把传入的内容立即输出。出现这种情况的主要原因是，在许多程序（不只是 JavaScript）中，I/O 是非常低速的阻塞部分。所以，（从页面 /UI 的角度来说）浏览器在后台异步处理控制台 I/O 能够提高性能，这时用户甚至可能根本意识不到其发生。

```JavaScript
var a = {
 index: 1
};
// 然后
console.log( a ); // ??
// 再然后
a.index++;
```

### 1.2: 事件循环

```JavaScript
// eventLoop是一个用作队列的数组
// （先进，先出）
var eventLoop = [ ];
var event;
// “永远”执行
while (true) {
 // 一次tick
 if (eventLoop.length > 0) {
 // 拿到队列中的下一个事件
 event = eventLoop.shift();
 // 现在，执行下一个事件
 try {
 event();
 }
 catch (err) {
 reportError(err);
 }
 }
}
```

有一个用 while 循环实现的持续运行的循环，循环的每一轮称为一个 tick。对每个 tick 而言，如果在队列中有等待事件，那么就会从队列中摘下一个事件并执行。这些事件就是你的回调函数。

### 1.3: 并行线程

并行线程的交替执行和异步事件的交替调度，其粒度是完全不同的。

```JavaScript
function later() {
 answer = answer * 2;
 console.log( "Meaning of life:", answer );
} 
```

在单线程环境中，线程队列中的这些项目是底层运算确实是无所谓的，因为线程本身不会被中断。但如果是在并行系统中，同一个程序中可能有两个不同的线程在运转，这时很可能就会得到不确定的结果。

```JavaScript
var a = 20;
function foo() {
 a = a + 1;
}
function bar() {
 a = a * 2;
}
// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", foo );
ajax( "http://some.url.2", bar ); 
// 线程1
foo():
 a. 把a的值加载到X
 b. 把1保存在Y
 c. 执行X加Y，结果保存在X
 d. 把X的值保存在a 
// 线程2
bar():
 a. 把a的值加载到X
 b. 把2保存在Y
 c. 执行X乘Y，结果保存在X
 d. 把X的值保存在a 
// 如果按照以下步骤执行，最终结果将会是什么样呢？
  1a (把a的值加载到X ==> 20)
  2a (把a的值加载到X ==> 20)
  1b (把1保存在Y ==> 1)
  2b (把2保存在Y ==> 2)
  1c (执行X加Y，结果保存在X ==> 22)
  1d (把X的值保存在a ==> 22)
  2c (执行X乘Y，结果保存在X ==> 44)
  2d (把X的值保存在a ==> 44)
// a 的结果将是 44。但如果按照以下顺序执行呢？
  1a (把a的值加载到X ==> 20)
  2a (把a的值加载到X ==> 20)
  2b (把2保存在Y ==> 2)
  1b (把1保存在Y ==> 1)
  2c (执行X乘Y，结果保存在X ==> 20)
  1c (执行X加Y，结果保存在X ==> 21)
  1d (把X的值保存在a ==> 21)
  2d (把X的值保存在a ==> 21)
  a 的结果将是 21。
```

JavaScript 从不跨线程共享数据，这意味着不需要考虑这一层次的不确定性。但是这并不意味着 JavaScript 总是确定性的。回忆一下前面提到的，foo() 和 bar() 的相对顺序改变可能会导致不同结果（41 或 42）。

**完整运行**

```JavaScript
var a = 1;
var b = 2;
function foo() {
 a++;
 b = b * a;
 a = b + 3;
}
function bar() {
 b--;
 a = 8 + b;
 b = a * 2;
}
// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", foo );
ajax( "http://some.url.2", bar ); 
块 1：
  var a = 1;
  var b = 2;
块 2（foo()）：
  a++;
  b = b * a;
  a = b + 3;
块 3（bar()）：
  b--;
  a = 8 + b;
  b = a * 2; 
输出 1：
  var a = 1;
  var b = 2;
// foo()
  a++;
  b = b * a;
  a = b + 3;
// bar()
  b--;
  a = 8 + b;
  b = a * 2;
  a; // 11
  b; // 22
输出 2：
  var a = 1;
  var b = 2;
// bar()
  b--;
  a = 8 + b;
  b = a * 2;
// foo()
  a++;
  b = b * a;
  a = b + 3;
  a; // 183
  b; // 180 
```

### 1.4: 并发

下面列出了事件循环队列中所有这些交替的事件：

```JavaScript
onscroll, 请求1 <--- 进程1启动
onscroll, 请求2
响应1 <--- 进程2启动
onscroll, 请求3
响应2
响应3
onscroll, 请求4
onscroll, 请求5
onscroll, 请求6
响应4
onscroll, 请求7 <--- 进程1结束
响应6
响应5
响应7 <--- 进程2结束
```

“进程”1 和“进程”2 并发运行（任务级并行），但是它们的各个事件是在事件循环队列中依次运行的。

#### 1.4.1: 非交互

两个或多个“进程”在同一个程序内并发地交替运行它们的步骤 / 事件时，如果这些任务彼此不相关，就不一定需要交互。如果进程间没有相互影响的话，不确定性是完全可以接受的。

```JavaScript
var res = {};
function foo(results) {
 res.foo = results;
}
function bar(results) {
 res.bar = results;
}
// ajax(..)是某个库提供的某个Ajax函数
ajax( "http://some.url.1", foo );
ajax( "http://some.url.2", bar ); 
```

#### 1.4.2: 交互

更常见的情况是，并发的“进程”需要相互交流，通过作用域或 DOM 间接交互。正如前面介绍的，如果出现这样的交互，就需要对它们的交互进行协调以避免竞态的出现。

```javascript
var res = [];
function response(data) {
 res.push( data );
}
// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", response );
ajax( "http://some.url.2", response ); 
```

这里的并发“进程”是这两个用来处理 Ajax 响应的 response() 调用。它们可能以任意顺序运行。

```JavaScript
var a;
function foo(x) {
 a = x * 2;
 baz();
}
function bar(x) {
 a = x / 2;
 baz();
}
function baz() {
 console.log( a );
}
// ajax(..)是某个库中的某个Ajax函数
ajax( "http://some.url.1", foo );
ajax( "http://some.url.2", bar ); 
//不管哪一个（foo() 或 bar()）后被触发，都不仅会覆盖另外一个给 a 赋的值，也会重复调用 baz()（很可能并不是想要的结果）。
// 改良版: 
var a;
function foo(x) {
 if (!a) {
 a = x * 2;
 baz();
 }
}
function bar(x) {
 if (!a) {
 a = x / 2;
 baz();
 }
}
function baz() {
 console.log( a );
}
// ajax(..)是某个库中的某个Ajax函数
ajax( "http://some.url.1", foo );
ajax( "http://some.url.2", bar ); 
// 条件判断 if (!a) 使得只有 foo() 和 bar() 中的第一个可以通过，第二个（实际上是任何后续的）调用会被忽略。也就是说，第二名没有任何意义！
```

#### 1.4.3: 协作

还有一种并发合作方式，称为并发协作（cooperative concurrency）。这里的重点不再是通过共享作用域中的值进行交互（尽管显然这也是允许的！）。这里的目标是取到一个长期运行的“进程”，并将其分割成多个步骤或多批任务，使得其他并发“进程”有机会将自己的运算插入到事件循环队列中交替运行。

```javascript
// 使代码更简洁
var res = [];
// response(..)从Ajax调用中取得结果数组
function response(data) {
 // 添加到已有的res数组
 res = res.concat(
 // 创建一个新的变换数组把所有data值加倍
 data.map( function(val){
 return val * 2;
 } )
 );
}
// ajax(..)是某个库中提供的某个Ajax函数
ajax( "http://some.url.1", response );
ajax( "http://some.url.2", response ); 
```

### 1.5: 任务

 ES6 中，有一个新的概念建立在事件循环队列之上，叫作任务队列（job queue）。这个概念给大家带来的最大影响可能是 Promise 的异步特性（参见第 3 章）。

```JavaScript
console.log( "A" );
setTimeout( function(){
 console.log( "B" );
}, 0 );
// 理论上的"任务API"异步调用类似promise
schedule( function(){
 console.log( "C" );
 schedule( function(){
 console.log( "D" );
 } );
} ); 
// A C D B
```

### 1.6: 语句顺序

尽管 JavaScript 语义让我们不会见到编译器语句重排序可能导致的噩梦，这是一种幸运，但是代码编写的方式（从上到下的模式）和编译后执行的方式之间的联系非常脆弱，理解这一点也非常重要。

### 1.7: 小结

通常需要对这些并发执行的“进程”（有别于操作系统中的进程概念）进行某种形式的交互协调，比如需要确保执行顺序或者需要防止竞态出现。这些“进程”也可以通过把自身分割为更小的块，以便其他“进程”插入进来。

## 第2章: 回调

### 2.1: continuation

```javascript
// A
setTimeout( function(){
 // C
}, 1000 );
// B 
// A C B
```

旦我们以回调函数的形式引入了单个 continuation（或者几十个，就像很多程序所做的那样！），我们就容许了大脑工作方式和代码执行方式的分歧。一旦这两者出现分歧（这远不是这种分歧出现的唯一情况，我想你明白这一点！），我们就得面对这样一个无法逆转的事实：代码变得更加难以理解、追踪、调试和维护。

### 2.2: 顺序的大脑

巴拉巴拉的说大脑可以多任务执行,但是有危险

#### 2.2.1: 执行与计划

> 唯一比不知道代码为什么崩溃更可怕的事情是，不知道为什么一开始它是工作的！这就是经典的“纸牌屋”心理：“它可以工作，可我不知道为什么，所以谁也别碰它！”你可能听说过“他人即地狱”（萨特）这种说法，对程序员来说则是“他人的代码即地狱”。而我深信不疑的是：“不理解自己的代码才是地狱。”回调就是主要元凶之一。

#### 2.2.2: 嵌套回调与链式回调

```javascript
listen( "click", function handler(evt){
 setTimeout( function request(){
   ajax( "http://some.url.1", function response(text){
     if (text == "hello") {
     	handler();
     }
     else if (text == "world") {
     	request();
     }
   } );
 }, 500) ;
} ); 

首先（现在）我们有：
listen( "..", function handler(..){
 // ..
} );
然后是将来，我们有：
setTimeout( function request(..){
 // ..
}, 500) ;
接着还是将来，我们有：
ajax( "..", function response(..){
 // ..
} );
最后（最晚的将来），我们有：
if ( .. ) {
 // ..
}
else .. 
// 首先，例子中的步骤是按照 1、2、3、4……的顺序，这只是一个偶然。实际的异步JavaScript 程序中总是有很多噪声，使得代码更加杂乱。在大脑的演习中，我们需要熟练地绕过这些噪声，从一个函数跳到下一个函数。对于这样满是回调的代码，理解
```

这种代码常常被称为回调地狱（callback hell），有时也被称为毁灭金字塔（pyramid of doom，得名于嵌套缩进产生的横向三角形状）。

```javascript
listen( "click", handler );
function handler() {
 setTimeout( request, 500 );
}
function request(){
 ajax( "http://some.url.1", response );
}
function response(text){
 if (text == "hello") {
 	handler();
 }
 else if (text == "world") {
 	request();
 }
} 
```

这种组织形式的代码不像前面以嵌套 / 缩进的形式组织的代码那么容易识别了，但是它和回调地狱一样脆弱，易受影响。为什么？
在线性（顺序）地追踪这段代码的过程中，我们不得不从一个函数跳到下一个，再跳到下一个，在整个代码中跳来跳去以“查看”流程。而且别忘了，这还是简化的形式，只考虑了最优情况。我们都知道，真实的异步 JavaScript 程序代码要混乱得多，这使得这种追踪的难度会成倍增加。

### 2.3: 信任问题

它以这样一个思路为中心：有时候 ajax(..)（也就是你交付回调 continuation 的第三方）不是你编写的代码，也不在你的直接控制下。多数情况下，它是某个第三方提供的工具。
我们把这称为控制反转（inversion of control），也就是把自己程序一部分的执行控制交给某个第三方。在你的代码和第三方工具（一组你希望有人维护的东西）之间有一份并没有明确表达的契约。

#### 2.3.1: 五个回调的故事

• 调用回调过早（在追踪之前）；
• 调用回调过晚（或没有调用）；
• 调用回调的次数太少或太多（就像你遇到过的问题！）；
• 没有把所需的环境 / 参数成功传给你的回调函数；
• 吞掉可能出现的错误或异常；......

#### 2.3.2: 不只是别人的代码

请思考这一点：你能够真正信任理论上（在自己的代码库中）你可以控制的工具吗？

不管你怎么做，这种类型的检查 / 规范化的过程对于函数输入是很常见的，即使是对于理论上完全可以信任的代码。大体上说，这等价于那条地缘政治原则：“信任，但要核实。”

### 2.4: 省点回调

回调设计存在几个变体，意在解决前面讨论的一些信任问题（不是全部！）。这种试图从回调模式内部挽救它的意图是勇敢的，但却注定要失败。
1.举例来说，为了更优雅地处理错误，有些 API 设计提供了分离回调（一个用于成功通知，一个用于出错通知）：

```javascript
function success(data) {
 console.log( data );
}
function failure(err) {
 console.error( err );
}
ajax( "http://some.url.1", success, failure ); 
```

在这种设计下，API 的出错处理函数 failure() 常常是可选的，如果没有提供的话，就是
假定这个错误可以吞掉。

2.还有一种常见的回调模式叫作“error-first 风格”（有时候也称为“Node 风格”，因为几乎所有 Node.js API 都采用这种风格），其中回调的第一个参数保留用作错误对象（如果有的话）。如果成功的话，这个参数就会被清空 / 置假（后续的参数就是成功数据）。不过，如果产生了错误结果，那么第一个参数就会被置起 / 置真（通常就不会再传递其他结果）：

```javascript
 // 出错？
 if (err) {
 console.error( err );
 }
 // 否则认为成功
 else {
 console.log( data );
 }
}
ajax( "http://some.url.1", response ); 
```

one:首先，这并没有像表面看上去那样真正解决主要的信任问题。这并没有涉及阻止或过滤不想要的重复调用回调的问题。现在事情更糟了，因为现在你可能同时得到成功或者失败的结果，或者都没有，并且你还是不得不编码处理所有这些情况。
two: 另外，不要忽略这个事实：尽管这是一种你可以采用的标准模式，但是它肯定更加冗长和模式化，可复用性不高，所以你还得不厌其烦地给应用中的每个回调添加这样的代码。

3.那么完全不调用这个信任问题又会怎样呢？如果这是个问题的话（可能应该是个问题！），你可能需要设置一个超时来取消事件。可以构造一个工具（这里展示的只是一个“验证概念”版本）来帮助实现这一点：

```javascript
function timeoutify(fn,delay) {
 var intv = setTimeout( function(){
   intv = null;
   fn( new Error( "Timeout!" ) );
   }, delay )
   ;
   return function() {
      // 还没有超时？
     if (intv) {
       clearTimeout( intv );
       fn.apply( this, arguments );
     }
   };
} 
// 使用"error-first 风格" 回调设计
function foo(err,data) {
 if (err) {
 	console.error( err ); 
 }
 else {
 	console.log( data );
 }
}
ajax( "http://some.url.1", timeoutify( foo, 500 ) ); 
```

由同步或异步行为引起的不确定性几乎总会带来极大的 bug 追踪难度。在某些圈子里，人们用虚构的十分疯狂的恶魔 Zalgo 来描述这种同步 / 异步噩梦。常常会有“不要放出 Zalgo”这样的呼喊，而这也引出了一条非常有效的建议：永远异步调用回调，即使就在事件循环的下一轮，这样，所有回调就都是可预测的异步调用了。

4.如果你不确定关注的 API 会不会永远异步执行怎么办呢？可以创建一个类似于这个“验证概念”版本的 asyncify(..) 工具：

```javascript
function asyncify(fn) {
 var orig_fn = fn,
   intv = setTimeout( function(){
     intv = null;
     if (fn) fn();
 	}, 0 );
     fn = null;
     return function() {
       // 触发太快，在定时器intv触发指示异步转换发生之前？
       if (intv) {
       fn = orig_fn.bind.apply(
       orig_fn,
       // 把封装器的this添加到bind(..)调用的参数中，
       // 以及克里化（currying）所有传入参数
       [this].concat( [].slice.call( arguments ) )
       );
     }
     // 已经是异步
     else {
       // 调用原来的函数
       orig_fn.apply( this, arguments );
     }
   };
 } 
 function result(data) {
 	console.log( a );
 }
 var a = 0;
 ajax( "..pre-cached-url..", asyncify( result ) );
 a++; 
```

### 2.5: 小结

第一，大脑对于事情的计划方式是线性的、阻塞的、单线程的语义，但是回调表达异步流程的方式是非线性的、非顺序的，这使得正确推导这样的代码难度很大。难于理解的代码是坏代码，会导致坏 bug。

我们需要一种更同步、更顺序、更阻塞的的方式来表达异步，就像我们的大脑一样。

第二，也是更重要的一点，回调会受到控制反转的影响，因为回调暗中把控制权交给第三方（通常是不受你控制的第三方工具！）来调用你代码中的 continuation。这种控制转移导致一系列麻烦的信任问题，比如回调被调用的次数是否会超出预期。

## 第3章: promise

>本章经常会使用“立即”一词，通常用来描述某个 Promise 决议（resolution）动作。但是，基本上在所有情况下，这个“立即”指任务队列行为（参见第1 章）方面的意义，而不是指严格同步的现在。

### 3.1: 什么是 Promise

Promise 是一种封装和组合未来值的易于复用的机制.

一旦 p 决议，不论是现在还是将来，下一个步骤总是相同的。

### 3.2: 具有 then 方法的鸭子类型

根据一个值的形态（具有哪些属性）对这个值的类型做出一些假定。这种类型检查（type check）一般用术语鸭子类型（duck typing）来表示——“如果它看起来像只鸭子，叫起来像只鸭子，那它一定就是只鸭子”（参见本书的“类型和语法”部分）。于是，对 thenable值的鸭子类型检测就大致类似于：

```javascript
if ( 
 p !== null && 
 ( 
 typeof p === "object" || 
 typeof p === "function" 
 ) && 
 typeof p.then === "function" 
) { 
 // 假定这是一个thenable! 
} 
else { 
 // 不是thenable 
}
// 优化修改
Object.prototype.then = function(){}; 
Array.prototype.then = function(){}; 
var v1 = { hello: "world" }; 
var v2 = [ "Hello", "World" ];
```

> 我并不喜欢最后还得用 thenable 鸭子类型检测作为 Promise 的识别方案。还有其他选择，比如 branding，甚至 anti-branding。可我们所用的似乎是针对最差情况的妥协。但情况也并不完全是一片黯淡。后面我们就会看到，thenable 鸭子类型检测还是有用的。只是要清楚，如果 thenable 鸭子类型误把不是 Promise 的东西识别为了 Promise，可能就是有害的。

### 3.3: Promise 信任问题

先回顾一下只用回调编码的信任问题。把一个回调传入工具 foo(..) 时可能出现如下问题：

• 调用回调过早；

• 调用回调过晚（或不被调用）；

• 调用回调次数过少或过多；

• 未能传递所需的环境和参数；

• 吞掉可能出现的错误和异常。

#### 3.3.7: 是可信任的 Promise 吗

Promise.resolve(..) 可以接受任何 thenable，将其解封为它的非 thenable 值。从 Promise.resolve(..) 得到的是一个真正的 Promise，是一个可以信任的值。如果你传入的已经是真正的 Promise，那么你得到的就是它本身，所以通过 Promise.resolve(..) 过滤来获得可信任性完全没有坏处。

#### 3.3.8: 建立信任

Promise 这种模式通过可信任的语义把回调作为参数传递，使得这种行为更可靠更合理。通过把回调的控制反转反转回来，我们把控制权放在了一个可信任的系统（Promise）中，这种系统的设计目的就是为了使异步编码更清晰。

### 3.4: 链式流

这种方式可以实现的关键在于以下两个 Promise 固有行为特性：

• 每次你对 Promise 调用 then(..)，它都会创建并返回一个新的 Promise，我们可以将其链接起来；

• 不管从 then(..) 调用的完成回调（第一个参数）返回的值是什么，它都会被自动设置为被链接 Promise（第一点中的）的完成。

```javascript
var p = Promise.resolve( 21 ); 
var p2 = p.then( function(v){ 
 console.log( v ); // 21 
 // 用值42填充p2
 return v * 2; 
} ); 
// 连接p2 
p2.then( function(v){ 
 console.log( v ); // 42 
} );

var p = Promise.resolve( 21 ); 
p.then( function(v){ 
 console.log( v ); // 21 
 // 创建一个promise并返回
 return new Promise( function(resolve,reject){ 
 // 引入异步！
 setTimeout( function(){ 
 // 用值42填充
 resolve( v * 2 ); 
 }, 100 ); 
 } ); 
} ) 
.then( function(v){ 
 // 在前一步中的100ms延迟之后运行
 console.log( v ); // 42 
} );
```

现在我们可以构建这样一个序列：不管我们想要多少个异步步骤，每一步都能够根据需要等待下一步（或者不等！）。

```javascript
request( "http://some.url.1/" ) 
// 步骤2：
.then( function(response1){ 
 foo.bar(); // undefined，出错！
 // 永远不会到达这里
 return request( "http://some.url.2/?v=" + response1 ); 
} ) 
// 步骤3：
.then( 
 function fulfilled(response2){ 
 // 永远不会到达这里
 }, 
 // 捕捉错误的拒绝处理函数
 function rejected(err){ 
 console.log( err ); 
 // 来自foo.bar()的错误TypeError 
 return 42; 
 } 
) 
// 步骤4：
.then( function(msg){ 
 console.log( msg ); // 42 
} )
```

第 2 步出错后，第 3 步的拒绝处理函数会捕捉到这个错误。拒绝处理函数的返回值（这段代码中是 42），如果有的话，会用来完成交给下一个步骤（第 4 步）的 promise，这样，这个链现在就回到了完成状态。

让我们来简单总结一下使链式流程控制可行的 Promise 固有特性: 

• 调用 Promise 的 then(..) 会自动创建一个新的 Promise 从调用返回。

• 在完成或拒绝处理函数内部，如果返回一个值或抛出一个异常，新返回的（可链接的）Promise 就相应地决议。

• 如果完成或拒绝处理函数返回一个 Promise，它将会被展开，这样一来，不管它的决议值是什么，都会成为当前 then(..) 返回的链接 Promise 的决议值。

### 3.5: 错误处理

在回调中，一些模式化的错误处理方式已经出现，最值得一提的是 error-first 回调风格：

````javascript
function foo(cb) { 
 setTimeout( function(){ 
 try { 
 var x = baz.bar(); 
 cb( null, x ); // 成功！
 } 
 catch (err) { 
 cb( err ); 
 } 
 }, 100 ); 
} 
foo( function(err,val){ 
 if (err) { 
 console.error( err ); // 烦 :( 
 } 
 else { 
 console.log( val ); 
 } 
} );
````

我们回到 Promise 中的错误处理，其中拒绝处理函数被传递给 then(..)。Promise 没有采用流行的 error-fifirst 回调设计风格，而是使用了分离回调（split-callback）风格。一个回调用于完成情况，一个回调用于拒绝情况：

```javascript
var p = Promise.reject( "Oops" ); 
p.then( 
 function fulfilled(){ 
 // 永远不会到达这里
 }, 
 function rejected(err){ 
 console.log( err ); // "Oops" 
 } 
);
```

#### 3.5.1: 绝望的陷阱

为了避免丢失被忽略和抛弃的 Promise 错误，一些开发者表示，Promise 链的一个最佳实践就是最后总以一个 catch(..) 结束，比如：

```javascript
var p = Promise.resolve( 42 ); 
p.then( 
 function fulfilled(msg){ 
 // 数字没有string函数，所以会抛出错误
 console.log( msg.toLowerCase() ); 
 } 
) 
.catch( handleErrors );
```

#### 3.5.2: 处理未捕获的情况

更常见的一种看法是：Promsie 应该添加一个 done(..) 函数，从本质上标识 Promsie 链的结束。done(..) 不会创建和返回 Promise，所以传递给 done(..) 的回调显然不会报告一个并不存在的链接 Promise 的问题。

```javascript
var p = Promise.resolve( 42 ); 
p.then( 
 function fulfilled(msg){ 
 // 数字没有string函数，所以会抛出错误
 console.log( msg.toLowerCase() ); 
 } 
) 
.done( null, handleErrors ); 
// 如果handleErrors(..)引发了自身的异常，会被全局抛出到这里
```

相比没有结束的链接或者任意时长的定时器，这种方案看起来似乎更有吸引力。但最大的问题是，它并不是 ES6 标准的一部分，所以不管听起来怎么好，要成为可靠的普遍解决方案，它还有很长一段路要走。

**浏览器有一个特有的功能是我们的代码所没有的：它们可以跟踪并了解所有对象被丢弃以及被垃圾回收的时机。所以，浏览器可以追踪 Promise 对象。如果在它被垃圾回收的时候其中有拒绝，浏览器就能够确保这是一个真正的未捕获错误，进而可以确定应该将其报告到开发者终端。**

#### 3.5.3: 成功的坑

• 默认情况下，Promsie 在下一个任务或时间循环 tick 上（向开发者终端）报告所有拒绝，如果在这个时间点上该 Promise 上还没有注册错误处理函数。

• 如果想要一个被拒绝的 Promise 在查看之前的某个时间段内保持被拒绝状态，可以调用defer()，这个函数优先级高于该 Promise 的自动错误报告。

```javascript
var p = Promise.reject( "Oops" ).defer(); 
// foo(..)是支持Promise的
foo( 42 ) 
.then( 
 function fulfilled(){ 
 return p; 
 }, 
 function rejected(err){ 
 // 处理foo(..)错误
 } 
); 
...
```

创建 p 的时候，我们知道需要等待一段时间才能使用或查看它的拒绝结果，所以我们就调用 defer()，这样就不会有全局报告出现。为了便于链接，defer() 只是返回这同一个promise。 

### 3.6: promise模式

#### 3.6.1: Promise.all([ .. ])

假定你想要同时发送两个 Ajax 请求，等它们不管以什么顺序全部完成之后，再发送第三个 Ajax 请求。考虑：

```javascript
// request(..)是一个Promise-aware Ajax工具
// 就像我们在本章前面定义的一样
var p1 = request( "http://some.url.1/" ); 
var p2 = request( "http://some.url.2/" ); 
Promise.all( [p1,p2] ) 
.then( function(msgs){ 
 // 这里，p1和p2完成并把它们的消息传入
 return request( 
 "http://some.url.3/?v=" + msgs.join(",") 
 ); 
} ) 
.then( function(msg){ 
 console.log( msg ); 
} );
```

Promise.all([ .. ]) 需要一个参数，是一个数组，通常由 Promise 实例组成。从 Promise.all([ .. ]) 调用返回的 promise 会收到一个完成消息（代码片段中的 msg）。这是一个由所有传入 promise 的完成消息组成的数组，与指定的顺序一致（与完成顺序无关）。

#### 3.6.2: Promise.race([ .. ])

尽管 Promise.all([ .. ]) 协调多个并发 Promise 的运行，并假定所有 Promise 都需要完成，但有时候你会想只响应“第一个跨过终点线的 Promise”，而抛弃其他 Promise。

这种模式传统上称为门闩，但在 Promise 中称为竞态。

```javascript
// request(..)是一个支持Promise的Ajax工具
// 就像我们在本章前面定义的一样
var p1 = request( "http://some.url.1/" ); 
var p2 = request( "http://some.url.2/" ); 
Promise.race( [p1,p2] ) 
.then( function(msg){ 
 // p1或者p2将赢得这场竞赛
 return request( 
 "http://some.url.3/?v=" + msg 
 ); 
} ) 
.then( function(msg){ 
 console.log( msg ); 
} );
```

1. 超时竞赛

   ```javascript
   // 前面定义的timeoutPromise(..)返回一个promise，
   // 这个promise会在指定延时之后拒绝
   // 为foo()设定超时
   Promise.race( [ 
    foo(), // 启动foo() 
    timeoutPromise( 3000 ) // 给它3秒钟
   ] ) 
   .then( 
    function(){ 
    // foo(..)按时完成！
    }, 
    function(err){ 
    // 要么foo()被拒绝，要么只是没能够按时完成，
    // 因此要查看err了解具体原因
    } 
   );
   ```

   在多数情况下，这个超时模式能够很好地工作。但是，还有一些微妙的情况需要考虑，并且坦白地说，对于 Promise.race([ .. ]) 和 Promise.all([ .. ]) 也都是如此。

2. finally

   角度提出这个问题的——通常最终它们会被垃圾回收——而是从行为的角度（副作用等）。Promise 不能被取消，也不应该被取消，因为那会摧毁 3.8.5 节讨论的外部不变性原则，所以它们只能被默默忽略。

```javascript
var p = Promise.resolve( 42 ); 
p.then( something ) 
.finally( cleanup ) 
.then( another ) 
.finally( cleanup );
```

####3.6.3: all([ .. ]) 和 race([ .. ]) 的变体

• none([ .. ])这个模式类似于 all([ .. ])，不过完成和拒绝的情况互换了。所有的 Promise 都要被拒绝，即拒绝转化为完成值，反之亦然。

• any([ .. ])这个模式与 all([ .. ]) 类似，但是会忽略拒绝，所以只需要完成一个而不是全部。

• first([ .. ]) 这个模式类似于与 any([ .. ]) 的竞争，即只要第一个 Promise 完成，它就会忽略后续的任何拒绝和完成。

• last([ .. ])这个模式类似于 first([ .. ])，但却是只有最后一个完成胜出。

#### 3.6.4: 并发迭代

举例来说，让我们考虑一下一个异步的 map(..) 工具。它接收一个数组的值（可以是Promise 或其他任何值），外加要在每个值上运行一个函数（任务）作为参数。map(..) 本身返回一个 promise，其完成值是一个数组，该数组（保持映射顺序）保存任务执行之后的异步完成值：

```javascript
if (!Promise.map) { 
 Promise.map = function(vals,cb) { 
 // 一个等待所有map的promise的新promise 
 return Promise.all( 
 // 注：一般数组map(..)把值数组转换为 promise数组
 vals.map( function(val){ 
 // 用val异步map之后决议的新promise替换val 
 return new Promise( function(resolve){ 
 cb( val, resolve ); 
 } ); 
 } ) 
 ); 
 }; 
}
```

### 3.7: Promise API 概述

#### 3.7.1: new Promise(..) 构造器

有启示性的构造器 Promise(..) 必须和 new 一起使用，并且必须提供一个函数回调。这个回调是同步的或立即调用的。这个函数接受两个函数回调，用以支持 promise 的决议。通常我们把这两个函数称为 resolve(..) 和 reject(..)：

####3.7.2: Promise.resolve(..) 和 Promise.reject(..)

```javascript
var fulfilledTh = { 
 then: function(cb) { cb( 42 ); } 
}; 
var rejectedTh = { 
 then: function(cb,errCb) { 
 errCb( "Oops" ); 
 } 
}; 
var p1 = Promise.resolve( fulfilledTh ); 
var p2 = Promise.resolve( rejectedTh ); 
// p1是完成的promise
// p2是拒绝的promise
```

.......

### 3.8: Promise 局限性

#### 3.8.1: 顺序错误处理

Promise 的设计局限性（具体来说，就是它们链接的方式）造成了一个让人很容易中招的陷阱，即 Promise 链中的错误很容易被无意中默默忽略掉.

关于 Promise 错误，还有其他需要考虑的地方。由于一个 Promise 链仅仅是连接到一起的成员 Promise，没有把整个链标识为一个个体的实体，这意味着没有外部方法可以用于观察可能发生的错误。

####3.8.2: 单一值

根据定义，Promise 只能有一个完成值或一个拒绝理由。在简单的例子中，这不是什么问题，但是在更复杂的场景中，你可能就会发现这是一种局限了。

#### 3.8.3: 单决议

Promise 最本质的一个特征是：Promise 只能被决议一次（完成或拒绝）。在许多异步情况中，你只会获取一个值一次，所以这可以工作良好。

####3.8.4: 惯性

要在你自己的代码中开始使用 Promise 的话，一个具体的障碍是，现存的所有代码都还不理解 Promise。如果你已经有大量的基于回调的代码，那么保持编码风格不变要简单得多。

#### 3.8.5: 无法取消的 Promise

一旦创建了一个 Promise 并为其注册了完成和 / 或拒绝处理函数，如果出现某种情况使得这个任务悬而未决的话，你也没有办法从外部停止它的进程。

#### 3.8.6: Promise 性能

这个特定的局限性既简单又复杂。

把基本的基于回调的异步任务链与 Promise 链中需要移动的部分数量进行比较。很显然，Promise 进行的动作要多一些，这自然意味着它也会稍慢一些。请回想 Promise 提供的信任保障列表，再与你要在回调之上建立同样的保护自建的解决方案来比较一下。

### 3.9: 小结

Promise 非常好，请使用。它们解决了我们因只用回调的代码而备受困扰的控制反转问题。

它们并没有摈弃回调，只是把回调的安排转交给了一个位于我们和其他工具之间的可信任的中介机制。

Promise 链也开始提供（尽管并不完美）以顺序的方式表达异步流的一个更好的方法，这有助于我们的大脑更好地计划和维护异步 JavaScript 代码。我们将在第 4 章看到针对这个问题的一种更好的解决方案

## 第4章: 生成器

在第 2 章里，我们确定了用回调表达异步控制流程的两个关键缺陷：

• 基于回调的异步不符合大脑对任务步骤的规划方式；

• 由于控制反转，回调并不是可信任或可组合的。

### 4.1: 打破完整运行

下面是实现这样的合作式并发的 ES6 代码：

```javascript
var x = 1; 
function *foo() { 
 x++; 
 yield; // 暂停！
 console.log( "x:", x ); 
} 
function bar() { 
 x++; 
}
// 构造一个迭代器it来控制这个生成器
var it = foo(); 
// 这里启动foo()！
it.next(); 
x; // 2 
bar(); 
x; // 3 
it.next(); // x: 3
```

因此，生成器就是一类特殊的函数，可以一次或多次启动和停止，并不一定非得要完成。尽管现在还不是特别清楚它的强大之处，但随着对本章后续内容的深入学习，我们会看到它将成为用于构建以生成器作为异步流程控制的代码模式的基础构件之一。

#### 4.1.1: 输入和输出

生成器函数是一个特殊的函数，具有前面我们展示的新的执行模式。但是，它仍然是一个函数，这意味着它仍然有一些基本的特性没有改变。比如，它仍然可以接受参数（即输入），也能够返回值（即输出）

```javascript
function *foo(x,y) { 
	return x * y; 
} 
var it = foo( 6, 7 ); 
var res = it.next(); 
res.value; // 42
```

1. 迭代消息传递

   除了能够接受参数并提供返回值之外，生成器甚至提供了更强大更引人注目的内建消息输入输出能力，通过 yield 和 next(..) 实现。

   ```javascript
   function *foo(x) { 
    var y = x * (yield); 
    return y; 
   } 
   var it = foo( 6 ); 
   // 启动foo(..) 
   it.next(); 
   var res = it.next( 7 ); 
   res.value; // 42
   ```

   注意，这里有一点非常重要，但即使对于有经验的 JavaScript 开发者也很有迷惑性：根据你的视角不同，yield 和 next(..) 调用有一个不匹配。一般来说，需要的 next(..) 调用要比 yield 语句多一个，前面的代码片段有一个 yield 和两个 next(..) 调用。

2.  两个问题的故事

   消息是双向传递的——yield.. 作为一个表达式可以发出消息响应 next(..) 调用，next(..) 也可以向暂停的 yield 表达式发送值。考虑下面这段稍稍调整过的代码：

   ```javascript
   function *foo(x) { 
    var y = x * (yield "Hello"); // <-- yield一个值！
    return y; 
   } 
   var it = foo( 6 ); 
   var res = it.next(); // 第一个next()，并不传入任何东西
   res.value; // "Hello" 
   res = it.next( 7 ); // 向等待的yield传入7
   res.value; // 42 
   // yield .. 和 next(..) 这一对组合起来，在生成器的执行过程中构成了一个双向消息传递系统。那么只看下面这一段迭代器代码：
   var res = it.next(); // 第一个next()，并不传入任何东西
   res.value; // "Hello" 
   res = it.next( 7 ); // 向等待的yield传入7 
   res.value; // 42
   ```

#### 4.1.2: 多个迭代器

从语法使用的方面来看，通过一个迭代器控制生成器的时候，似乎是在控制声明的生成器函数本身。但有一个细微之处很容易忽略：每次构建一个迭代器，实际上就隐式构建了生成器的一个实例，通过这个迭代器来控制的是这个生成器实例。

同一个生成器的多个实例可以同时运行，它们甚至可以彼此交互：

```javascript
function *foo() { 
 var x = yield 2; 
 z++; 
 var y = yield (x * z); 
 console.log( x, y, z ); 
} 
var z = 1; 
var it1 = foo(); 
var it2 = foo(); 
var val1 = it1.next().value; // 2 <-- yield 2 
var val2 = it2.next().value; // 2 <-- yield 2 
val1 = it1.next( val2 * 10 ).value; // 40 <-- x:20, z:2 
val2 = it2.next( val1 * 5 ).value; // 600 <-- x:200, z:3 
it1.next( val2 / 2 ); // y:300 
 // 20 300 3 
it2.next( val1 / 4 ); // y:10 
 // 200 10 3
```

### 4.2: 生成器产生值

#### 4.2.1: 生产者与迭代器

可以实现一个直接使用函数闭包的版本（参见本系列的《你不知道的 JavaScript（上卷）》的“作用域和闭包”部分），类似如下：

```javascript
var gimmeSomething = (function(){ 
   var nextVal; 
   return function(){ 
   if (nextVal === undefined) { 
   nextVal = 1; 
   } 
   else { 
   nextVal = (3 * nextVal) +6; 
   } 
   return nextVal; 
 }; 
})(); 
gimmeSomething(); // 1 
gimmeSomething(); // 9 
gimmeSomething(); // 33 
gimmeSomething(); // 105
```

实际上，这个任务是一个非常通用的设计模式，通常通过迭代器来解决。迭代器是一个定义良好的接口，用于从一个生产者一步步得到一系列值。JavaScript 迭代器的接口，与多数语言类似，就是每次想要从生产者得到下一个值的时候调用 next()。

可以为我们的数字序列生成器实现标准的迭代器接口：

```javascript
var something = (function(){ 
 var nextVal; 
 return { 
 // for..of循环需要
 [Symbol.iterator]: function(){ return this; }, 
 // 标准迭代器接口方法
 next: function(){ 
 if (nextVal === undefined) { 
 nextVal = 1; 
 } 
 else { 
 nextVal = (3 * nextVal) + 6; 
 } 
 return { done:false, value:nextVal }; 
 } 
 }; 
})(); 
something.next().value; // 1 
something.next().value; // 9 
something.next().value; // 33 
something.next().value; // 105
```

ES6 还新增了一个 for..of 循环，这意味着可以通过原生循环语法自动迭代标准迭代器：

```javascript
for (var v of something) { 
 console.log( v ); 
 // 不要死循环！
 if (v > 500) { 
 break; 
 } 
} 
// 1 9 33 105 321 969 
```

#### 4.2.2: iterable

前面例子中的 something 对象叫作迭代器，因为它的接口中有一个 next() 方法。而与其紧密相关的一个术语是 iterable（可迭代），即指一个包含可以在其值上迭代的迭代器的对象。

for..of 循环期望 something 是 iterable，于是它寻找并调用它的 Symbol.iterator 函数。我们将这个函数定义为就是简单的 return this，也就是把自身返回，而 for..of 循环并不知情

#### 4.2.3: 生成器迭代器

可以把生成器看作一个值的生产者，我们通过迭代器接口的 next() 调用一次提取出一个值。

可以通过生成器实现前面的这个 something 无限数字序列生产者，类似这样：

```javascript
function *something() { 
 var nextVal; 
 while (true) { 
 if (nextVal === undefined) { 
 nextVal = 1; 
 } 
 else { 
 nextVal = (3 * nextVal) + 6; 
 } 
 yield nextVal; 
 } 
}
```

现在，可以通过 for..of 循环使用我们雕琢过的新的 *something() 生成器。你可以看到，其工作方式基本是相同的：

```javascript
for (var v of something()) { 
 console.log( v ); 
 // 不要死循环！
 if (v > 500) { 
 break; 
 } 
} 
// 1 9 33 105 321 969
```

如果认真思考的话，你也许会从这段生成器与循环的交互中提出两个问题。

• 为什么不能用 for (var v of something) .. ？因为这里的 something 是生成器，并不是iterable。我们需要调用 something() 来构造一个生产者供 for..of 循环迭代。

• something() 调用产生一个迭代器，但 for..of 循环需要的是一个 iterable，对吧？是的。生成器的迭代器也有一个 Symbol.iterator 函数，基本上这个函数做的就是 return this，和我们前面定义的 iterable something 一样。换句话说，生成器的迭代器也是一个iterable ！

1.停止生成器

```javascript
 try { 
   var nextVal; 
   while (true) { 
     if (nextVal === undefined) { 
     nextVal = 1; 
   } 
   else { 
      nextVal = (3 * nextVal) + 6; 
   } 
     yield nextVal; 
   } 
 } 
 // 清理子句
 finally { 
 	console.log( "cleaning up!" ); 
 }
```

之前的例子中，for..of 循环内的 break 会触发 finally 语句。但是，也可以在外部通过return(..) 手工终止生成器的迭代器实例：

```javascript
var it = something(); 
for (var v of it) { 
 console.log( v ); 
 // 不要死循环！
 if (v > 500) { 
   console.log( 
   // 完成生成器的迭代器
   it.return( "Hello World" ).value 
 ); 
 // 这里不需要break 
 } 
} 
// 1 9 33 105 321 969 
// 清理！
// Hello World
```

### 4.3: 异步迭代生成器

生成器与异步编码模式及解决回调问题等，有什么关系呢?

```javascript
function foo(x,y,cb) { 
 ajax( 
 "http://some.url.1/?x=" + x + "&y=" + y, 
 cb 
 ); 
} 
foo( 11, 31, function(err,text) { 
 if (err) { 
 console.error( err ); 
 } 
 else { 
 console.log( text ); 
 } 
} );
// 如果想要通过生成器来表达同样的任务流程控制，可以这样实现：
function foo(x,y) { 
 ajax( 
 "http://some.url.1/?x=" + x + "&y=" + y, 
 function(err,data){ 
 if (err) { 
 // 向*main()抛出一个错误
 it.throw( err ); 
 } 
 else { 
 // 用收到的data恢复*main() 
 it.next( data ); 
 } 
 } 
 ); 
} 
function *main() { 
 try { 
 var text = yield foo( 11, 31 ); 
 console.log( text ); 
 } 
 catch (err) { 
 console.error( err ); 
 } 
} 
var it = main(); 
// 这里启动！
it.next();
```

**同步错误处理**

```javascript
function *main() { 
 var x = yield "Hello World"; 
 yield x.toLowerCase(); // 引发一个异常！
} 
var it = main(); 
it.next().value; // Hello World 
try { 
 it.next( 42 ); 
} 
catch (err) { 
 console.error( err ); // TypeError 
}
```

甚至可以捕获通过 throw(..) 抛入生成器的同一个错误，基本上也就是给生成器一个处理它的机会；如果没有处理的话，迭代器代码就必须处理：

```javascript
function *main() { 
 var x = yield "Hello World"; 
 // 永远不会到达这里
 console.log( x ); 
} 
var it = main(); 
it.next(); 
try { 
 // *main()会处理这个错误吗？看看吧！
 it.throw( "Oops" ); 
} 
catch (err) { 
 // 不行，没有处理！
 console.error( err ); // Oops 
}
```

### 4.4: 生成器 +Promise

首先，把支持 Promise 的 foo(..) 和生成器 *main() 放在一起：

```javascript
function foo(x,y) { 
 return request( 
 "http://some.url.1/?x=" + x + "&y=" + y 
 ); 
} 
function *main() { 
 try { 
 var text = yield foo( 11, 31 ); 
 console.log( text ); 
 } 
 catch (err) { 
 console.error( err ); 
 } 
}
var it = main(); 
var p = it.next().value; 
// 等待promise p决议
p.then( 
 function(text){ 
 it.next( text ); 
 }, 
 function(err){ 
 it.throw( err ); 
 } 
);
```

#### 4.4.1: 支持 Promise 的 Generator Runner

为了学习和展示的目的，我们还是自己定义一个独立工具，叫作 run(..)：

```javascript
// 在此感谢Benjamin Gruenbaum （@benjamingr on GitHub）的巨大改进！
function run(gen) { 
 var args = [].slice.call( arguments, 1), it; 
 // 在当前上下文中初始化生成器
 it = gen.apply( this, args ); 
 // 返回一个promise用于生成器完成
 return Promise.resolve() 
 .then( function handleNext(value){ 
 // 对下一个yield出的值运行
 var next = it.next( value ); 
 return (function handleResult(next){ 
 // 生成器运行完毕了吗？
 if (next.done) { 
 return next.value; 
 } 
 // 否则继续运行
 else { 
 return Promise.resolve( next.value ) 
 .then( 
 // 成功就恢复异步循环，把决议的值发回生成器
 handleNext, 
 // 如果value是被拒绝的 promise，
 // 就把错误传回生成器进行出错处理
 function handleErr(err) { 
 return Promise.resolve( 
 it.throw( err ) 
 ) 
 .then( handleResult ); 
 } 
 ); 
 } 
 })(next); 
 } ); 
}
```

ES7：async 与 await?

```javascript
function foo(x,y) { 
 return request( 
 "http://some.url.1/?x=" + x + "&y=" + y 
 ); 
} 
async function main() { 
 try { 
 var text = await foo( 11, 31 ); 
 console.log( text ); 
 }
 catch (err) { 
 console.error( err ); 
 } 
} 
main();
```

#### 4.4.2: 生成器中的 Promise 并发

最自然有效的答案就是让异步流程基于 Promise，特别是基于它们以时间无关的方式管理状态的能力（参见 3.1.1 节）

```javascript
function *foo() { 
 // 让两个请求"并行"
 var p1 = request( "http://some.url.1" ); 
 var p2 = request( "http://some.url.2" ); 
 // 等待两个promise都决议
 var r1 = yield p1; 
 var r2 = yield p2; 
 var r3 = yield request( 
 "http://some.url.3/?v=" + r1 + "," + r2 
 ); 
 console.log( r3 ); 
} 
// 使用前面定义的工具run(..)
run( foo );
```

这种流程控制模型如果听起来有点熟悉的话，是因为这基本上和我们在第 3 章中通过Promise.all([ .. ]) 工具实现的 gate 模式相同。因此，也可以这样表达这种流程控制：

```javascript
 // 让两个请求"并行"，并等待两个promise都决议
 var results = yield Promise.all( [ 
 request( "http://some.url.1" ), 
 request( "http://some.url.2" ) 
 ] ); 
 var r1 = results[0]; 
 var r2 = results[1]; 
 var r3 = yield request( 
 "http://some.url.3/?v=" + r1 + "," + r2 
 ); 
 console.log( r3 ); 
} 
// 使用前面定义的工具run(..) 
run( foo );
```

**隐藏的 Promise**

要注意你的生成器内部包含了多少 Promise 逻辑。我们介绍的使用生成器实现异步的方法的全部要点在于创建简单、顺序、看似同步的代码，将异步的细节尽可能隐藏起来。

```javascript
// 注：普通函数，不是生成器
function bar(url1,url2) { 
 return Promise.all( [ 
 request( url1 ), 
 request( url2 ) 
 ] ); 
} 
function *foo() { 
 // 隐藏bar(..)内部基于Promise的并发细节
 var results = yield bar( 
 "http://some.url.1", 
 "http://some.url.2" 
 ); 
 var r1 = results[0]; 
 var r2 = results[1]; 
 var r3 = yield request( 
 "http://some.url.3/?v=" + r1 + "," + r2 
 ); 
 console.log( r3 ); 
} 
// 使用前面定义的工具run(..)
run( foo );
```

### 4.5: 生成器委托

可能出现的情况是，你可能会从一个生成器调用另一个生成器，使用辅助函数 run(..)，就像这样

```javascript
function *foo() { 
 var r2 = yield request( "http://some.url.2" ); 
 var r3 = yield request( "http://some.url.3/?v=" + r2 ); 
 return r3; 
} 
function *bar() { 
 var r1 = yield request( "http://some.url.1" ); 
 // 通过 run(..) "委托"给*foo()
 var r3 = yield run( foo ); 
 console.log( r3 ); 
} 
run( bar );
```

但其实还有一个更好的方法可以实现从 *bar() 调用 *foo()，称为 yield 委托。yield 委托的具体语法是：yield * __（注意多出来的 *）。在我们弄清它在前面的例子中的使用之前，先来看一个简单点的场景：

```javascript
function *foo() { 
 console.log( "*foo() starting" ); 
 yield 3; 
 yield 4; 
 console.log( "*foo() finished" ); 
} 
function *bar() { 
 yield 1; 
 yield 2; 
 yield *foo(); // yield委托！
 yield 5; 
} 
var it = bar(); 
it.next().value; // 1 
it.next().value; // 2 
it.next().value; // *foo()启动
 // 3 
it.next().value; // 4 
it.next().value; // *foo()完成
 // 5
```

#### 4.5.1: 为什么用委托

yield 委托的主要目的是代码组织，以达到与普通函数调用的对称。

#### 4.5.2: 消息委托

```JavaScript
function *foo() {
 console.log( "inside *foo():", yield "B" );
 console.log( "inside *foo():", yield "C" );
 return "D";
}
function *bar() {
 console.log( "inside *bar():", yield "A" );
 // yield委托！
 console.log( "inside *bar():", yield *foo() );
 console.log( "inside *bar():", yield "E" );
 return "F";
}
var it = bar();
console.log( "outside:", it.next().value );
// outside: A
console.log( "outside:", it.next( 1 ).value );
// inside *bar(): 1
// outside: B
console.log( "outside:", it.next( 2 ).value );
// inside *foo(): 2
// outside: C
console.log( "outside:", it.next( 3 ).value );
// inside *foo(): 3
// inside *bar(): D
// outside: E
console.log( "outside:", it.next( 4 ).value );
// inside *bar(): 4
// outside: F 
```

(1) 值 3（通过 *bar() 内部的 yield 委托）传入等待的 *foo() 内部的 yield "C" 表达式。
(2) 然后 *foo() 调用 return "D"，但是这个值并没有一直返回到外部的 it.next(3) 调用。
(3) 取而代之的是，值 "D" 作为 *bar() 内部等待的 yield*foo() 表达式的结果发出——这个yield 委托本质上在所有的 *foo() 完成之前是暂停的。所以 "D" 成为 *bar() 内部的最后结果，并被打印出来。
(4) yield "E" 在 *bar() 内部调用，值 "E" 作为 it.next(3) 调用的结果被 yield 发出。

从外层的迭代器（it）角度来说，是控制最开始的生成器还是控制委托的那个，没有任何区别。

**异常也被委托！**

 和yield 委托透明地双向传递消息的方式一样，错误和异常也是双向传递的：

```JavaScript
function *foo() {
 try {
 yield "B";
 }
 catch (err) {
 console.log( "error caught inside *foo():", err );
 }
 yield "C";
 throw "D";
} 
生成器 ｜ 267
function *bar() {
 yield "A";
 try {
 yield *foo();
 }
 catch (err) {
 console.log( "error caught inside *bar():", err );
 }
 yield "E";
 yield *baz();
 // 注：不会到达这里！
 yield "G";
}
function *baz() {
 throw "F";
}
var it = bar();
console.log( "outside:", it.next().value );
// outside: A
console.log( "outside:", it.next( 1 ).value );
// outside: B
console.log( "outside:", it.throw( 2 ).value );
// error caught inside *foo(): 2
// outside: C
console.log( "outside:", it.next( 3 ).value );
// error caught inside *bar(): D
// outside: E
try {
 console.log( "outside:", it.next( 4 ).value );
}
catch (err) {
 console.log( "error caught outside:", err );
}
// error caught outside: F 
```

这段代码中需要注意以下几点。
(1) 调用 it.throw(2) 时，它会发送错误消息 2 到 *bar()，它又将其委托给 *foo()，后者捕获并处理它。然后，yield "C" 把 "C" 发送回去作为 it.throw(2) 调用返回的 value。
(2) 接下来从 *foo() 内 throw 出来的值 "D" 传播到 *bar()，这个函数捕获并处理它。然后yield "E" 把 "E" 发送回去作为 it.next(3) 调用返回的 value。
(3) 然后，从 *baz() throw 出来的异常并没有在 *bar() 内被捕获——所以 *baz() 和 *bar()都被设置为完成状态。这段代码之后，就再也无法通过任何后续的 next(..) 调用得到值 "G"，next(..) 调用只会给 value 返回 undefined。

#### 4.5.3: 异步委托

```javascript
function *foo() {
 var r2 = yield request( "http://some.url.2" );
 var r3 = yield request( "http://some.url.3/?v=" + r2 );
 return r3;
}
function *bar() {
 var r1 = yield request( "http://some.url.1" );
 var r3 = yield *foo();
 console.log( r3 );
}
run( bar ); 
```

这里我们在 *bar() 内部没有调用 yield run(foo)，而是调用 yield *foo()。
在这个例子之前的版本中，使用了 Promise 机制（通过 run(..) 控制）把值从 *foo() 内的return r3 传递给 *bar() 中的局部变量 r3。现在，这个值通过 yield * 机制直接返回。除此之外的行为非常相似。

#### 4.5.4: 递归委托

当然，yield 委托可以跟踪任意多委托步骤，只要你把它们连在一起。甚至可以使用 yield委托实现异步的生成器递归，即一个 yield 委托到它自身的生成器：

```JavaScript
function *foo(val) {
 if (val > 1) {
 // 生成器递归
 val = yield *foo( val - 1 );
 }
 return yield request( "http://some.url/?v=" + val );
}
function *bar() {
 var r1 = yield *foo( 3 );
 console.log( r1 ); 
生成器 ｜ 269
}
run( bar ); // 前面写的加载器
```

### 4.6: 生成器并发

回想一下第 1 章给出的一个场景：其中两个不同并发 Ajax 响应处理函数需要彼此协调，以确保数据交流不会出现竞态条件。我们把响应插入到 res 数组中，就像这样：

```JavaScript
function response(data) {
 if (data.url == "http://some.url.1") {
 res[0] = data;
 }
 else if (data.url == "http://some.url.2") {
 res[1] = data;
 }
} 
```

但是这种场景下如何使用多个并发生成器呢？

```JavaScript
// request(..)是一个支持Promise的Ajax工具
var res = [];
function *reqData(url) {
 res.push(
 yield request( url )
 );
} 
// 但是这种场景下如何使用多个并发生成器呢？
// request(..)是一个支持Promise的Ajax工具
var res = [];
function *reqData(url) {
 res.push(
 	yield request( url )
 );
} 
```

但是，实践中我们如何安排这些交互呢？首先，使用 Promise 手工实现：

```JavaScript
var it1 = reqData( "http://some.url.1" );
var it2 = reqData( "http://some.url.2" );
var p1 = it1.next();
var p2 = it2.next();
p1
.then( function(data){
 it1.next( data );
 return p2; 
} )
.then( function(data){
 it2.next( data );
} ); 
```

*reqData(..) 的两个实例都被启动来发送它们的 Ajax 请求，然后通过 yield 暂停。然后我们选择在 p1 决议时恢复第一个实例，然后 p2 的决议会重启第二个实例。通过这种方式，我们使用 Promise 配置确保 res[0] 中会放置第一个响应，而 res[1] 中会放置第二个响应。

```JavaScript
// request(..)是一个支持Promise的Ajax工具
var res = [];
function *reqData(url) {
 var data = yield request( url );
 // 控制转移
 yield;
 res.push( data );
}
var it1 = reqData( "http://some.url.1" );
var it2 = reqData( "http://some.url.2" );
var p1 = it.next();
var p2 = it.next();
p1.then( function(data){
 it1.next( data );
} );
p2.then( function(data){
 it2.next( data );
} );
Promise.all( [p1,p2] )
.then( function(){
 it1.next();
 it2.next();
} ); 
```

可能不那么明显的是，因为对称性，这种方法以更简单的形式暗示了一种可重用的工具。还可以做得更好。来设想一下使用一个称为 runAll(..) 的工具：

```JavaScript
// request(..)是一个支持Promise的Ajax工具
var res = [];
runAll(
   function*(){
   var p1 = request( "http://some.url.1" );
   // 控制转移
   yield;
   res.push( yield p1 );
 },
 function*(){
   var p2 = request( "http://some.url.2" );
   // 控制转移
   yield;
   res.push( yield p2 );
 }
); 
```

但是，如果继续扩展 runAll(..) 来提供一个内层的变量空间，以使多个生成器实例可以共享，将是非常有帮助的，比如下面这个称为 data 的空对象。还有，它可以接受 yield 的非Promise 值，并把它们传递到下一个生成器。

```JavaScript
// request(..)是一个支持Promise的Ajax工具
runAll(
 function*(data){
   data.res = [];
   // 控制转移（以及消息传递）
   var url1 = yield "http://some.url.2";
   var p1 = request( url1 ); // "http://some.url.1"
   // 控制转移
   yield;
   data.res.push( yield p1 );
 },
 function*(data){
   // 控制转移（以及消息传递）
   var url2 = yield "http://some.url.1";
   var p2 = request( url2 ); // "http://some.url.2"
   // 控制转移
   yield;
   data.res.push( yield p2 );
 }
); 
```

### 4.7: 形实转换程序

在通用计算机科学领域，有一个早期的前 JavaScript 概念，称为形实转换程序（thunk）。我们这里将不再陷入历史考据的泥沼，而是直接给出形实转换程序的一个狭义表述：JavaScript 中的 thunk 是指一个用于调用另外一个函数的函数，没有任何参数。
换句话说，你用一个函数定义封装函数调用，包括需要的任何参数，来定义这个调用的执行，那么这个封装函数就是一个形实转换程序。之后在执行这个 thunk 时，最终就是调用了原始的函数。

```JavaScript
function foo(x,y) {
 return x + y;
}
function fooThunk() {
 return foo( 3, 4 );
}
// 将来
console.log( fooThunk() ); // 7 
// 所以，同步的 thunk 是非常简单的。但如果是异步的 thunk 呢？我们可以把这个狭窄的thunk 定义扩展到包含让它接收一个回调。
function foo(x,y,cb) {
 setTimeout( function(){
 cb( x + y );
 }, 1000 );
}
function fooThunk(cb) {
 foo( 3, 4, cb );
}
// 将来
fooThunk( function(sum){
 console.log( sum ); // 7
} ); 
// 但是，你并不会想手工编写 thunk。所以，我们发明一个工具来做这部分封装工作。
function thunkify(fn) {
 var args = [].slice.call( arguments, 1 );
 return function(cb) {
 args.push( cb );
 return fn.apply( null, args );
 };
}
var fooThunk = thunkify( foo, 3, 4 );
// 将来
fooThunk( function(sum) {
 console.log( sum ); // 7
} ); 
```

暴露 thunkory 方法——而不是像前面的 thunkify(..) 那样把这个中间步骤隐藏——似乎是不必要的复杂性。但是，一般来说，在程序开头构造 thunkory 来封装已有的 API 方法，并在需要 thunk 时可以传递和调用这些 thunkory，是很有用的。两个独立的步骤保留了一个更清晰的功能分离。

```JavaScript
// 更简洁：
var fooThunkory = thunkify( foo );
var fooThunk1 = fooThunkory( 3, 4 );
var fooThunk2 = fooThunkory( 5, 6 );
// 而不是：
var fooThunk1 = thunkify( foo, 3, 4 );
var fooThunk2 = thunkify( foo, 5, 6 ); 

```

为了说明这种对称性，我们要首先把前面的 foo(..) 例子修改一下，改成使用 error-first 风格的回调：

```JavaScript
function foo(x,y,cb) {
 setTimeout( function(){
 // 假定cb(..)是error-first风格的
 cb( null, x + y );
 }, 1000 );
} 
// 现在我们对比一下 thunkify(..) 和 promisify(..)（即第 3 章中的 Promise.wrap(..)）的使用：
// 对称：构造问题提问者
var fooThunkory = thunkify( foo );
var fooPromisory = promisify( foo );
// 对称：提问
var fooThunk = fooThunkory( 3, 4 );
var fooPromise = fooPromisory( 3, 4 );
// 得到答案
fooThunk( function(err,sum){
 if (err) {
 console.error( err );
 }
 else {
 console.log( sum ); // 7 
 }
} );
// 得到promise答案
fooPromise
.then(
 function(sum){
 console.log( sum ); // 7
 },
 function(err){
 console.error( err );
 }
); 
```

### 4.8: ES6 之前的生成器

#### 4.8.1: 手工变换

```JavaScript
// request(..)是一个支持Promise的Ajax工具
function *foo(url) {
 try {
 console.log( "requesting:", url );
 var val = yield request( url );
 console.log( val );
 }
 catch (err) {
 console.log( "Oops:", err );
 return false;
 }
}
var it = foo( "http://some.url.1" ); 
// 首先要观察到的是，我们仍然需要一个可以调用的普通函数 foo()，它仍然需要返回一个迭代器。因此，先把非生成器变换的轮廓刻画出来：
function foo(url) {
 // ..
 // 构造并返回一个迭代器
 return {
 next: function(v) {
 // ..
 },
 throw: function(e) {
 // ..
 }
 };
}
var it = foo( "http://some.url.1" ); 
```

现在需要定义迭代器函数的代码，使这些函数正确调用 process(..)：

```JavaScript
function foo(url) {
 // 管理生成器状态
 var state;
 // 生成器变量范围声明
 var val;
function process(v) {
 switch (state) {
 case 1:
 console.log( "requesting:", url );
 return request( url );
 case 2:
 val = v;
 console.log( val ); 
 return;
 case 3:
 var err = v;
 console.log( "Oops:", err );
 return false;
 }
}
// 构造并返回一个生成器
return {
 next: function(v) {
 // 初始状态
 if (!state) {
 state = 1;
 return {
 done: false,
 value: process()
 };
 }
 // yield成功恢复
 else if (state == 1) {
 state = 2;
 return {
 done: true,
 value: process( v )
 };
 }
 // 生成器已经完成
 else {
 return {
 done: true,
 value: undefined
 };
 }
 },
 "throw": function(e) {
 // 唯一的显式错误处理在状态1
 if (state == 1) {
 state = 3;
 return {
 done: true,
 value: process( e )
 };
 }
 // 否则错误就不会处理，所以只把它抛回
 else {
 throw e;
 }
 }
 };
}
```

#### 4.8.2: 自动转换

前面的 ES6 生成器到前 ES6 等价代码的手工推导练习，向我们教授了概念上生成器是如何工作的。但是，这个变换非常复杂，并且对于代码中的其他生成器而言也是不可移植的。这部分工作通过手工实现十分不实际，会完全抵消生成器的一切优势。

如果使用 regenerator 来转换前面的生成器的话，以下是产生的代码（本书写作之时）：

```JavaScript
// request(..)是一个支持Promise的Ajax工具
var foo = regeneratorRuntime.mark(function foo(url) {
 var val;
 return regeneratorRuntime.wrap(function foo$(context$1$0) {
   while (1) switch (context$1$0.prev = context$1$0.next) {
     case 0:
     context$1$0.prev = 0;
     console.log( "requesting:", url ); 
     context$1$0.next = 4;
     return request( url );
     case 4:
     val = context$1$0.sent;
     console.log( val );
     context$1$0.next = 12;
     break;
     case 8:
     context$1$0.prev = 8;
     context$1$0.t0 = context$1$0.catch(0);
     console.log("Oops:", context$1$0.t0);
     return context$1$0.abrupt("return", false);
     case 12:
     case "end":
     return context$1$0.stop();
 	}
 }, foo, this, [[0, 8]]);
}); 
```

### 4.9: 小结

1.生成器是 ES6 的一个新的函数类型，它并不像普通函数那样总是运行到结束。取而代之的是，生成器可以在运行当中（完全保持其状态）暂停，并且将来再从暂停的地方恢复运行。
2.这种交替的暂停和恢复是合作性的而不是抢占式的，这意味着生成器具有独一无二的能力来暂停自身，这是通过关键字 yield 实现的。不过，只有控制生成器的迭代器具有恢复生成器的能力（通过 next(..)）。yield/next(..) 这一对不只是一种控制机制，实际上也是一种双向消息传递机制。yield .. 表达式本质上是暂停下来等待某个值，接下来的 next(..) 调用会向被暂停的 yield 表达式传回一个值（或者是隐式的 undefined）。
3.在异步控制流程方面，生成器的关键优点是：生成器内部的代码是以自然的同步 / 顺序方式表达任务的一系列步骤。其技巧在于，我们把可能的异步隐藏在了关键字 yield 的后面，把异步移动到控制生成器的迭代器的代码部分。
4.换句话说，生成器为异步代码保持了顺序、同步、阻塞的代码模式，这使得大脑可以更自然地追踪代码，解决了基于回调的异步的两个关键缺陷之一。

## 第5章: 程序性能

### 5.1: Web Worker

如果你有一些处理密集型的任务要执行，但不希望它们都在主线程运行（这可能会减慢浏览器 /UI），可能你就会希望 JavaScript 能够以多线程的方式运行。

```JavaScript
// 以下是如何侦听事件（其实就是固定的 "message" 事件）：
w1.addEventListener( "message", function(evt){
 // evt.data
} );
// 也可以发送 "message" 事件给这个 Worker：
w1.postMessage( "something cool to say" );
// 在这个 Worker 内部，收发消息是完全对称的：
// "mycoolworker.js"
addEventListener( "message", function(evt){
 // evt.data
} );
postMessage( "a really cool reply" ); 
```

#### 5.1.1: Worker 环境

在 Worker 内部是无法访问主程序的任何资源的。这意味着你不能访问它的任何全局变量，也不能访问页面的 DOM 或者其他资源。记住，这是一个完全独立的线程。
但是，你可以执行网络操作（Ajax、WebSockets）以及设定定时器。还有，Worker 可以访问几个重要的全局变量和功能的本地复本，包括 navigator、location、JSON 和applicationCache。

**Web Worker 通常应用于哪些方面呢？**

• 处理密集型数学计算
• 大数据集排序
• 数据处理（压缩、音频分析、图像处理等）
• 高流量网络通信

#### 5.1.2: 数据传递

你可能已经注意到这些应用中的大多数有一个共性，就是需要在线程之间通过事件机制传递大量的信息，可能是双向的。

#### 5.1.3: 共享 Worker

```javascript
// 在这种情况下，创建一个整个站点或 app 的所有页面实例都可以共享的中心 Worker 就非常有用了。
// 这称为 SharedWorker，可通过下面的方式创建（只有 Firefox 和 Chrome 支持这一功能）：var w1 = new SharedWorker( "http://some.url.1/mycoolworker.js" );因为共享 Worker 可以与站点的多个程序实例或多个页面连接，所以这个 Worker 需要通过某种方式来得知消息来自于哪个程序。这个唯一标识符称为端口（port），可以类比网络socket 的端口。因此，调用程序必须使用 Worker 的 port 对象用于通信：
w1.port.addEventListener( "message", handleMessages );
// ..
w1.port.postMessage( "something cool" );
// 还有，端口连接必须要初始化，形式如下：
w1.port.start();
// 在共享 Worker 内部，必须要处理额外的一个事件："connect"。这个事件为这个特定的连接提供了端口对象。保持多个连接独立的最简单办法就是使用 port 上的闭包（参见本系列《你不知道的 JavaScript（上卷）》的“作用域和闭包”部分），就像下面的代码一样，把这个链接上的事件侦听和传递定义在 "connect" 事件的处理函数内部：
// 在共享Worker内部
addEventListener( "connect", function(evt){
 // 这个连接分配的端口
 var port = evt.ports[0];
 port.addEventListener( "message", function(evt){
 // ..
 port.postMessage( .. );
 // ..
 } );
 // 初始化端口连接
 port.start();
} ); 
除了这个区别之外，共享和专用 Worker 在功能和语义方面都是一样的
```

> 如果有某个端口连接终止而其他端口连接仍然活跃，那么共享 Worker 不会终止。而对专用 Worker 来说，只要到实例化它的程序的连接终止，它就会终止。

#### 5.1.4: 模拟 Web Worker

编 写 了 一 个 模 拟 Worker 的 概 要 实 现（https://gist.github.com/getify/1b26accb1a09aa53ad25）。它是很基本的，但如果双向消息机制正确工作，并且“onerror”处理函数也正确工作，那么它应该可以提供简单的 Worker 支持。如果需要的话，你也可以扩展它，实现更多的功能，比如 terminate() 或伪共享 Worker。

### 5.2: SIMD

单指令多数据（SIMD）是一种数据并行（data parallelism）方式，与 Web Worker 的任务并行（task parallelism）相对，因为这里的重点实际上不再是把程序逻辑分成并行的块，而是并行处理数据的多个位。

### 5.3: asm.js

asm.js（http://asmjs.org）这个标签是指 JavaScript 语言中可以高度优化的一个子集。通过小心避免某些难以优化的机制和模式（垃圾收集、类型强制转换，等等），asm.js 风格的代码可以被 JavaScript 引擎识别并进行特别激进的底层优化。

#### 5.3.1: 如何使用 asm.js 优化

关于 asm.js 优化，首先要理解的是类型和强制类型转换（参见本书的“类型和语法”部分）。如果 JavaScript 引擎需要跟踪一个变量在各种各样的运算之间的多个不同类型的值，才能按需处理类型之间的强制类型转换，那么这大量的额外工作会使得程序优化无法达到最优。

#### 5.3.2: asm.js 模块

对 JavaScript 性能影响最大的因素是内存分配、垃圾收集和作用域访问。asm.js 对这些问题提出的一个解决方案就是，声明一个更正式的 asm.js“模块”，不要和 ES6 模块混淆。请参考本系列《你不知道的 JavaScript（下卷）》的“ES6 & Beyond”部分。

### 5.4: 小结

本部分的前四章都是基于这样一个前提：异步编码模式使我们能够编写更高效的代码，通常能够带来非常大的改进。但是，异步特性只能让你走这么远，因为它本质上还是绑定在一个单事件循环线程上。

## 第6章: 性能与调试

### 6.1: 性能测试

#### 6.1.1: 重复

要确保把异常因素排除，你需要大量的样本来平均化。你还会想要知道最差样本有多慢，最好的样本有多快，以及最好和最差情况之间的偏离度有多大，等等。你需要知道的不仅仅是一个告诉你某个东西跑得有多快的数字，还需要得到某个可以计量的测量值告诉你这个数字的可信度有多高。

#### 6.1.2: Benchmark.js

关于 Benchmark.js 的使用还有很多要学的。但是，关键在它处理了为给定的一段 JavaScript 代码建立公平、可靠、有效的性能测试的所有复杂性。如果你想要对你的代码进行功能测试和性能测试，这个库应该最优先考虑。

### 6.2: 环境为王

对特定的性能测试来说，不要忘了检查测试环境，特别是比较任务 X 和 Y 这样的比对测试。仅仅因为你的测试显示 X 比 Y 快，并不能说明结论 X 比 Y 快就有实际的意义。

**引擎优化**

你无法可靠地推断，如果在你的独立测试中 X 比 Y 要快上 10μs，就意味着 X 总是比 Y 要快，就应该总是使用 X。性能并不是这样发挥效力的。它要比这复杂得多。

### 6.3: jsPerf.com

尽管在所有的 JavaScript 运行环境下，Benchmark.js 都可用于测试代码的性能，但有一点一定要强调，如果你想要得到可靠的测试结论的话，就需要在很多不同的环境（桌面浏览器、移动设备，等等）中测试汇集测试结果。

**完整性检查**

jsPerf 是一个很好的资源，但认真分析的话，出于本章之前列出的多种原因，公开发布的测试中有大量是有缺陷或无意义的。

### 6.4: 写好测试

要写好测试，需要认真分析和思考两个测试用例之间有什么区别，以及这些区别是有意还是无意的。

### 6.5: 微性能

在考虑对代码进行性能测试时，你应该习惯的第一件事情就是你所写的代码并不总是引擎真正运行的代码。在第 1 章讨论编译器语句重排序问题时，我们简单介绍过这个问题。不过这里要说的是，有时候编译器可能会决定执行与你所写的不同的代码，不只是顺序不同，实际内容也会不同。

#### 6.5.1: 不是所有的引擎都类似

各种浏览器中的不同 JavaScript 引擎可以都是“符合规范的”，但其处理代码的方法却完全不同。JavaScript 规范并没有任何性能相关的要求，好吧，除了 ES6 的“尾调用优化”，这部分将在 6.6 节介绍。

引擎可以自由决定一个运算是否需要优化，可能进行权衡，替换掉运算次要性能。对一个运算来说，很难找到一种方法使其在所有浏览器中都运行得较快。

#### 6.5.2: 大局

我相信这么解释高德纳的意思是合理的：“非关键路径上的优化是万恶之源。”所以，关键是确定你的代码是否在关键路径上——如果在的话，就应该优化！

### 6.6: 尾调用优化

简单地说，尾调用就是一个出现在另一个函数“结尾”处的函数调用。这个调用结束后就没有其余事情要做了（除了可能要返回结果值）。

```javascript
// 举例来说，以下是一个非递归的尾调用：
function foo(x) {
 return x;
}
function bar(y) {
 return foo( y + 1 ); // 尾调用
}
function baz() {
 return 1 + bar( 40 ); // 非尾调用
}
baz(); // 42 
// 考虑到前面递归的 factorial(..)，这次重写成 TCO 友好的：
function factorial(n) {
 function fact(n,res) {
 if (n < 2) return res;
 return fact( n - 1, n * res );
 }
 return fact( n, 1 );
}
factorial( 5 ); // 120 
```

### 6.7: 小结

遗憾的是，很多常用的性能测试执迷于无关紧要的微观性能细节，比如 x++ 对比 ++x。编写好的测试意味着理解如何关注大局，比如关键路径上的优化以及避免落入类似不同的JavaScript 实现细节这样的陷阱中。