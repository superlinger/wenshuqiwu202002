
您是否仍在使用var？ 是什么阻止您切换到使用const和let？ 在下面分享您的经验！
# 停止使用“ var”声明JavaScript中的变量
## const和let的介绍
![](1!lBV-Mwzgk19GL36A1geEnA.jpeg)

我们都从某个地方开始。 我学习了使用var关键字声明变量的JavaScript。 它很简单而且有效，但是我已经改变了。

如果您编写类似var x = 5的代码，则需要停止。 好吧，老实说，您不必这样做，但您应该这样做。

我们经常将编程语言视为刻板的一组规则。 现实情况是，与任何口头语言一样，编程语言也在不断发展。

现在，我使用关键字const并让我们在JavaScript中声明所有变量，所以您也应该这样做。

让我们学习如何以及为什么。
# 声明真实常数

顾名思义，变量表示变化的值。 尽管声明和不接触变量并不一定有什么问题，但是如果我们试图编写具有语义意义的代码，则应该区分变量和常量。

常量与变量相反，变量的声明值不变。 从历史上看，为定义一个常量，我们使用所有大写字母，例如有毒动物的鲜艳颜色。

不用依赖约定，引入const关键字为我们提供了一个明确的选项，用于声明哪些内容不会更改。

要使用const关键字，只需将其替换为var即可，现在无法修改该值。
```
// the old way// var sales_tax = 0.06;// the new wayconst sales_tax = 0.06;sales_tax = 0.08; // "TypeError: Assignment to constant variable.
```

实现简单，原理简单。 使用const是理所当然的。 但是什么时候合适呢？

诸如税率或单位之间的转换之类的数字比率很容易选择。 您将看到使用const的另一个地方是使用箭头符号声明函数。
```
const myFunction = (x,y) => {  // Do stuff}myFunction(1,2)
```

现在，您的函数无法覆盖。
# 修正JavaScript的古怪范围

JavaScript缺乏范围清晰性，这常常导致开发和故障排除的失败。 以下是JS范围异常的摘要：
+ 可以对变量使用两次var（重新声明）
+ 默认情况下，顶级变量是全局变量（全局对象）
+ 变量可以在声明（吊装）之前使用
+ 循环中的变量重用了相同的引用（闭包）

使用let可以澄清并解决许多这些奇怪的问题。 让我们仔细看看。
## 重新声明

这很简单，您无法重新声明使用let制作的变量。
```
var x = 0;let y = 0;var x = 1;let y = 1; // "SyntaxError: Identifier 'y' has already been declared
```
## 全局对象

在顶层，用var声明的变量作为属性添加到全局对象，而let变量则不。
```
var x = 0;let y = 0;console.log(window.x); // 0console.log(window.y); // undefined
```
## 吊装

用let定义的变量只能在其块范围内访问，而var变量将提升到功能级别。
```
// Using varconsole.log(i);  // undefinedfor(var i=0; i<4; i++) {  console.log(i);}console.log(i); // 4// Using letconsole.log(j); // "ReferenceError: j is not definedfor(let j=0; j<4; j++) {  console.log(j);}console.log(j); // "ReferenceError: j is not defined
```
## 关闭

这是一个新概念很难理解，但是闭包是引用自由变量的函数。

当我们有一个带有var变量的闭包时，会记住该引用，当在该变量更改的循环内时，这可能会很麻烦。
```
var functions = [];for (var i = 0; i < 3; i++) {  functions[i] = () => { console.log(i); };}for (var j = 0; j < 3; j++) {  functions[j]();}// 3 3 3
```

使用let每次都会创建一个新引用。
```
var functions = [];for (let i = 0; i < 3; i++) {  functions[i] = () => { console.log(i); };}for (var j = 0; j < 3; j++) {  functions[j]();}// 0 1 2
```

undefined
# 告诫

尽管let和const应该有效地替换var关键字，但生活并不总是那么简单。 由于这些关键字是在ECMAScript 2015（ES6）中引入的，因此，如果您使用的平台不允许使用该关键字，那么您就不走运了。 此外，您需要确保采取措施以确保代码可在目标受众的浏览器中正常运行。
```
(本文翻译自Jonathan Hsu的文章《Stop Using ‘var’ to Declare Variables in JavaScript》，参考：https://levelup.gitconnected.com/stop-using-var-to-declare-variables-in-javascript-6c0caec16f43)
```
