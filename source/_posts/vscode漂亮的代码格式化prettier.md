---
title: vscode漂亮的代码格式化prettier
date: 2021-04-25 17:57
tags:
---

首先在vscode内安装插件[Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
在你的项目里新建下面文件
<!-- more -->
.prettierrc.js
```javascript
module.exports = {
  trailingComma: 'none', // 在对象或数组最后一个元素后面是否加逗号
  useTabs: false, // 缩进不使用tab，使用空格
  printWidth: 80, // 最大列宽 默认80
  tabWidth: 2, // 缩进字节数
  semi: true, // 句尾添加分号
  singleQuote: true, // 使用单引号代替双引号
  bracketSpacing: true, // 在对象，数组括号与文字之间加空格 "{ foo: bar }"
  jsxBracketSameLine: true, // 将>多行JSX元素的放在最后一行的末尾，而不是放在下一行
  jsxSingleQuote: true, // 在JSX中使用单引号而不是双引号
  arrowParens: 'always', // always:(x) => x  avoid: x => x
  vueIndentScriptAndStyle: true, // 是否缩进Vue文件中的代码<script>和<style>标记
  insertPragma: false // 顶部插入<!-- @format -->
};

```
以上是我列举的我的配置，你也可以去[官网](https://prettier.io/docs/en/options.html)查看其它配置。


