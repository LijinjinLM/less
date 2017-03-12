##Less学习笔记
###一种动态样式语言
##特性
1. LESS 将 CSS 赋予了动态语言的特性，如 变量， 继承， 运算， 函数. 
2. LESS 既可以在 客户端 上运行 (支持IE 6+, Webkit, Firefox)，也可以借助Node.js或者Rhino在服务端运行。
（1）客户端运行
在HTML页面中加载.less加载less.js,得到css,浏览器才可以显示，效率低
（2）服务器运行
借助第三方工具 koala
构建工具 gulp webpack
node.js 来执行

##bootstrap定制
1. bootstrap.less可以删除不需要的组件
2. 配置Variables.less文件达到定制外观的效果（变量文件）

##变量
 less通过@来定义变量；如：@color:#ccc;
 不仅可以用变量来管理属性值，也可以用在选择器名称，属性名，URL以及@import语句中；
###1.选择器变量
####less:
//定义了一个选择器变量
 @mySelector:myName;
   \.@{mySelector}{
   width: 100px;
   height:100px;
   color:red;
 }
####编译后的css
  .myName {
    width: 100px;
    height: 100px;
    color: red;
  }
###2.URL变量
####less:
  @img:'image';
//应用
 .myimg{
      width:100px;
      height: 100px;
      background: url('@{img}/1.jpg') no-repeat;
 }
####编译后的css
  .myimg {
    width: 100px;
    height: 100px;
    background: url('image/1.jpg') no-repeat;
  }
###3.属性变量
####less:
  @property:color;
  .mydiv{
    width: 100px;
    height:100px;
    @{property}:#eee ;
  }
####编译后的css
  .mydiv {
    width: 100px;
    height: 100px;
    color: #eee ;
  }
##混合(mixins)
   类似于函数。混合可以将一个定义好的class A轻松的引入到另一个class B中，从而简单实现class B继承 class A中的所有属性。我们还可以带参数地调用，就像使用函数一样。
### (1)无参
####less:
  //定义一个color类
  .color{
    color:#eee;
    background-color:#f00;
  }
  //应用
  .myBox{
    width:100px;
    height:100px;
    .color;
  }
####编译后的css
  .color {
    color: #eee;
    background-color: #f00;
  }
  .myBox {
    width: 100px;
    height: 100px;
    color: #eee;
    background-color: #f00;
  }
###(2) 有参
####less:
  //定义一个color类
  .color (@color:#eee){
    color:@color;
    background-color:#f00;
  }
  //应用
  .myBox1{
    width:100px;
    height:100px;
    .color();//不传参时，即用默认的参数；
    .color(#0f0);//传入参数的情况
  }
####编译后的css
  .myBox1 {
    width: 100px;
    height: 100px;
    color: #eee;
    color: #0f0;
    background-color: #f00;
  }
  有时候我们会看到如下的用法
  .color () {
      color:#eee;
  }
  当你希望.color()不出现在css里，且有不能引用其它类的时候，就可以这样子写。
###(3)在媒体查询里的嵌套写法稍有区别
####less:
  //媒体查询
  .screencolor{
    @media screen {
      color:blue;
      @media (min-width: 768px) {
        color: red;
      }
    }
    @media tv {
      color: black;
    }
  }
####编译后的css
  @media screen {
    .screencolor {
      color: blue;
    }
  }
  @media screen and (min-width: 768px) {
    .screencolor {
      color: red;
    }
  }
  @media tv {
    .screencolor {
      color: black;
    }
  }
##匹配模式
匹配模式的写法类似于混合。它的作用类似于if语句。
####less:
  .mixin(dark,@color) {
    color: darken(@color, 15%);
  }
  .mixin(light,@color) {
    color: lighten(@color, 15%);
  }
  //下面代码的作用是，不管匹配上面哪种模式，下面样式都会加进去。注意：第一个参数一定是@_，后面的每个参数都要加上
  .mixin(@_,@color) {
    width:100px;
    height:100px;
  }
  .line {
    .mixin(dark,#FF0000);
  }
####编译后的css
  .line {
    color: #b30000;
    width: 100px;
    height: 100px;
  }
##嵌套
1. 在一个选择器中嵌套另一个选择器来实现继承；（&表示当前选择器的父选择器）
2. 媒体查询和嵌套媒体查询
####less:
 ul{
   width: 100px;
   li{
     background:green;
     a{
       color: red;
       &:hover{
         color: blue;
       }
     }
   }
 }
####编译后的css
 ul {
   width: 100px;
 }
 ul li {
   background: green;
 }
 ul li a {
   color: red;
 }
 ul li a:hover {
   color: blue;
 }
##运算
任何数值，颜色和变量都可以进行运算；less可以进行加、减、乘、除的运算；
####less：
 @base: 5%;
 @base-color:#eee;
 @filler: @base * 2;
 @other: @base + @filler;
 .myBox3{
   color: #888 / 4;
   background-color: @base-color + #111;
   height: 100% / 2 + @filler;
 }
####编译后的css
 .myBox3 {
   color: #222222;
   background-color: #ffffff;
   height: 60%;
 }
##函数
less提供了内置的函数，详情请参考 http://lesscss.cn/functions/#functions-overview
##命名空间和访问器
有时候，出于组织的目的，或者为了提供一些封装，你会希望将你的混合模块(mixins) 组合在一起。以便稍候复用或者分发。命名空间相当于一个混合的集合；可以在需要的地方引用单独的样式。
 #bundle {
   .button () {
     display: block;
     border: 1px solid black;
     background-color: grey;
     &:hover { background-color: white }
   }
   .tab { ... }
   .citation { ... }
 }
 你只需要在 #header a中像这样引入 .button:
 #header a {
   color: orange;
   #bundle > .button;
 }
 需要注意的是命名空间内声明的变量将只作用于该命名空间，你会用它来引用一个mixin (#Namespace > .mixin-name)。但你不能这么做： (#Namespace > @this-will-not-work)。
##作用域
less 中的作用域跟其他编程语言非常类似，首先会从本地查找变量或者混合模块，如果没找到的话会去父级作用域中查找，直到找到为止.
 @var: red;
 #page {
   @var: white;
   #header {
     color: @var; // white
   }
 }
 
 #footer {
   color: @var; // red  
 }
##arguments变量
arguments变量包含了全部的传进来的参数。如果不想一个参数一个参数的写，那么可以用arguments参数。
####less:
 .border(@w:10px,@c:red,@xx:solid) {
     border:@arguments;
 }
 div{
     .border(20px);
 }
####编译后的css
 div {
   border: 20px red solid;
 }
##避免编译
有时候我们需要输出一些不正确的CSS语法或者使用一些 LESS不认识的专有语法.
要输出这样的值我们可以在字符串前加上一个 ~, 例如:
####less:
 .class {
   filter: ~"ms:alwaysHasItsOwnSyntax.For.Stuff()";
 }
####编译后的CSS
 .class {
   filter: ms:alwaysHasItsOwnSyntax.For.Stuff();
 } 
##！important关键字
  类似于css ！important关键字。(优先选择)
##扩展
extend是一个less伪类，它会合并它所在的选择其和它所匹配的引用。
####less:
 nav ul{
   &:extend(.inline);
   background:blue;
 }
 .inline{
   color:red;
 }
####编译后的CSS
 nav ul {
   background: blue;
 }
 .inline,
 nav ul {
   color: red;
 } 
##注释
/*会编译为css注释*/
//编译成css的时候自动过滤掉
##文件引入
你可以在main文件中通过下面的形势引入 .less 文件, .less 后缀可带可不带:
  @import "lib.less";
  @import "lib";
 如果你想导入一个css文件而且不想less对它进行处理，只需要使用.css后缀就可以:
  @import "lib.css";
 这样LESS就会跳过它不去处理它.
 

  
