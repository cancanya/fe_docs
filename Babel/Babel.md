## 概述

​	Babel 是一个工具链，主要用于将 ES2015+ 版本的 JavaScrip t代码转为 ES5 等向后兼容的 JS 代码，从而可以运行在低版本浏览器或其它环境中。

​	Babel 的工作，是将 A 版本源码转换为 B 版本源码，所以通常也被叫做转换编译器。

​	Babel 的转换内容主要有两个：**语法转换**、**补齐 API**。

​	Babel 默认只做语法（syntax）转换，即在 transform 阶段（工作流程中介绍）做转译。而像常用的 Promise、Object.assign、Array.includes、Set() 等API，则需要引入额外的 polyfill（垫片）来做转换处理。所谓垫片，是指垫平不同浏览器之间差异的东西，通俗说就是实现了低版本浏览器本身不支持的API的代码。



## 工作流程

​	babel 主要有三个阶段：parse（解析） —> transform（转换） —> generate（生成）

![177DD6AB-43D1-43C1-B521-49E0EF66C3A8](/var/folders/yt/c4vzc8c955j4l6wlpf704bm00000gn/T/com.yinxiang.Mac/WebKitDnD.CW50is/177DD6AB-43D1-43C1-B521-49E0EF66C3A8.png)



#### 	解析阶段

​		代码解析，也就是我们常说的 Parser，用于将一段代码（文本）解析成一个数据结构，在 Babel 中也就是解析成一个 AST（抽象语法树）。

​     AST 抽象语法树，是源代码的抽象语法结构树。之所以是抽象的，是因为并不会把实际代码中的每个细节都展现出来，仅展示结构/内容相关的细节。（在线生成AST的工具：[https://astexplorer.net](https://astexplorer.net/)）

​     Babel 使用的是 @babel/parse 解析器（很大程度基于 acorn 和 acorn-jsx）。acorn 是一个轻量级 JavaScript 解析器，webpack 基于这个库转换 AST。

​     文本 —> AST 的转换过程有两个关键步骤：词法分析 和 语法分析。

- 词法分析： 将代码（字符串）分割为 token 流，即语法单元组成的数组

  `

  // 假设 tokenizer 是一个词法分析器

  const result = tokenizer(’n*n')

  console.log(result);

  /**

  [

   { type: { ... }, value: "n", start: 0, end: 1, loc: { ... } },

   { type: { ... }, value: "*", start: 2, end: 3, loc: { ... } },

   { type: { ... }, value: "n", start: 4, end: 5, loc: { ... } },

   ...

  ]

  **/

  `

- 语法分析：分析上述 token 流，并生成 AST

  语法分析要比词法分析复杂得多，因为要分析各种语法的可能性。只有经过语法分析的 token 流才能成为有结构的 AST。做语法分析最好依照标准，大多数 JavaScript Parser 都遵循 estree规范（https://github.com/estree/estree）     



### 	转换阶段

​		转换步骤接收 AST 并对其进行遍历，在此过程中对节点进行添加、更新、删除等操作，最后生成一个新的 AST。这是 Babel 或其他编译器中最复杂的过程，同时也是大部分插件将要介入工作的部分。    	

### 	生成阶段

​		这一阶段对上一阶段生成的新 AST 进行深度优先遍历，然后转换成字符串形式的代码，同时还会创建 sourceMap。



