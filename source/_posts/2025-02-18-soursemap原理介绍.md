---
title: soursemap原理介绍
date: 2025-02-18 11:04:41
tags:
categories:
---

# 什么是 sourceMap？

sourcemap 是现代 web 开发体系中不可或缺的一部分，它给开发者提供了一种调试生产环境压缩和编译后代码的手段，是一种经过工具处理后的代码产物和源代码之间的映射关系。Sourcemap 最初是由 Google 发起并开发的，作为 Web 开发中调试和分析压缩 JavaScript 代码的解决方案。2009 年左右，Google 收购了一家名为 Closure Compiler 的 JavaScript 压缩工具，Closure Compiler 支持生成 sourcemap，并填充了该工具的大量功能。经过一段时间的发展，sourcemap 成为了一个通用的标准规范。2011 年，Mozilla 发布了基于 sourcemap 的 Firefox 开发者工具，这意味着 sourcemap 成为了多个主流浏览器的标准之一。至此，sourcemap 成为了前端开发中必不可少的调试和分析工具。

# 为什么需要 sourcemap？

JavaScript 是一种弱类型、解释执行的语言，它所用的源代码通常比较容易读懂。然而，在发布时，为了获得更快的速度和更小的文件大小，往往会对 JavaScript 代码进行压缩和混淆，这为开发者在调试和排错时带来了很大的困难。这时就需要 sourcemap 来帮助开发者定位问题。

[![pEMaXE6.png](https://s21.ax1x.com/2025/02/18/pEMaXE6.png)](https://imgse.com/i/pEMaXE6)

<!-- more -->

# sourcemap 是什么样的

[![pEMaqD1.png](https://s21.ax1x.com/2025/02/18/pEMaqD1.png)](https://imgse.com/i/pEMaqD1)

[![pEMaLHx.png](https://s21.ax1x.com/2025/02/18/pEMaLHx.png)](https://imgse.com/i/pEMaLHx)

- version：Source map 的版本，目前为 3。
- file：转换后的文件名。
- sourceRoot：转换前的文件所在的目录。如果与转换前的文件在同一目录，该项为空。
- sources：转换前的文件。该项是一个数组，表示可能存在多个文件合并。
- sourcesContent：转换前的文件内容数组。该项可选，用于调试。
- names：转换前的所有变量名和属性名。
- mappings：记录位置信息的字符串，下文详细介绍。

无论你是否关注，你已经在不知不觉中用到了 sourcemap 了
控制台报错具体信息和行数都使用了 sourcemap

# 支持 sourcemap 的工具

现在，几乎所有的现代浏览器和 JavaScript 工具都支持 sourcemap，包括 Chrome、Firefox、Safari、Edge、Webpack、Babel、Rollup、ESbuild、vite 等。
还有一些单独的库 source-map、source-map-js、convert-source-map、uglify-js

# 如何生成 sourcemap

## 使用 Webpack

webpack.config.js：

```javascript
const path = require("path");

module.exports = {
  mode: "production",
  devtool: "source-map",
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.js",
  },
};
```

webpack 中使用 devtool 配置来生成 sourcemap，这里使用了 source-map，它会生成一个单独的 sourcemap 文件。

## 使用 Vite

vite.config.js：

```javascript
module.exports = {
  build: {
    sourcemap: true,
    outDir: "dist",
  },
};
```

在 Vite 中启用 sourcemap 很简单，只需要配置 sourcemap 为 true 即可。

## 使用 Babel

babel.config.js：

```javascript
module.exports = {
  presets: ["@babel/preset-env"],
  sourceMaps: true,
};
```

在 babel.config.js 中配置 sourceMaps 能够生成内联的 sourcemap 数据。

## 使用 UglifyJS

在使用 UglifyJS 混淆代码时，可以设置 uglifyOptions 来控制 sourcemap 的相关选项。

```javascript
const UglifyJsPlugin = require("uglifyjs-webpack-plugin");

module.exports = {
  //...其他配置
  optimization: {
    minimize: true,
    minimizer: [
      new UglifyJsPlugin({
        cache: true,
        parallel: true,
        sourceMap: true, // 启用 sourcemap
        uglifyOptions: {
          compress: {
            drop_console: true,
          },
        },
      }),
    ],
  },
};
```

在这里 UglifyJS 会自动为压缩后的代码生成一个 .map 文件，它包含了压缩前代码和压缩后代码之间的映射。同时，通过设置 sourceMap 为 true，可以启用生成 sourcemap 的功能。

# 如何使用 sourcemap

- 浏览器开发者工具 默认支持，可配置
- npm 库

# sourcemap 支持扩展字段

类似 package.json 文件可以扩展字段给第三方使用
自定义字段需要以 x\_开头作为命名空间，例如：

- x_google_ignoreList 添加忽略文件
  此插件可以在已经生成的 soucemap 文件中忽略某些不关心文件的加载，加快调试工具的打开速度

# sourcemap 常见类型

- eval
- cheap-source-map
- cheap-module-source-map
- source-map
- inline-source-map
- nosources-source-map
- hidden-source-map

不同类型的 sourcemap 提供了不同级别的调试信息和性能特性。以下是常见的 sourcemap 类型及其区别：

1. eval:

- 这种模式下，每个模块都会被封装到一个 eval() 函数中，并且 sourcemap 会作为数据 URI 内嵌其中。
- 优点：生成速度最快，因为每个模块都独立处理。
- 缺点：由于使用了 eval，可能会影响执行性能，并且安全性较低。

2. cheap-source-map:

- 这种模式生成的是基础的 sourcemap 文件，只包含行级别的映射，不包括列信息。
- 优点：生成速度快，适合快速构建和调试。
- 缺点：不提供具体的列号，因此定位问题时只能精确到行，而不能精确到具体的位置。

3. cheap-module-source-map:

- 类似于 cheap-source-map，但它还包含了加载的 loader 的 sourcemap 信息（如果有）。
- 优点：比 cheap-source-map 更详细，可以更好地调试经过 loader 处理过的代码。
- 缺点：依然只包含行级别的信息，没有列信息。

4. source-map:

- 这是最完整和详细的一种 sourcemap，生成单独的 .map 文件，包含所有的源代码、行和列的映射。
- 优点：最详细的调试信息，可以精确到列，非常适合生产环境中的错误追踪。
- 缺点：生成速度慢，占用更多存储空间。

5. inline-source-map:

- 将整个 sourcemap 直接内联到编译后的 JavaScript 文件中，以 base64 编码的数据 URI 形式存在。
- 优点：便于调试，因为不需要额外的 .map 文件；适合小型项目和简单的调试场景。
- 缺点：增加了编译后文件的大小，对于大型项目不太适用。

6. nosources-source-map:

- 在生成的 sourcemap 中不包含源代码内容，仅包含映射信息。这意味着浏览器可以显示出错位置，但无法展示源代码。
- 优点：保护源代码隐私，同时仍能进行基本的错误追踪。
- 缺点：调试体验不如其他类型的 sourcemap 好，因为看不到实际的源代码。

7. hidden-source-map:

- 生成完整的 sourcemap 文件，但不会在编译后的代码中添加任何引用注释。通常用于生产环境，在出现错误时通过外部工具手动关联 sourcemap。
- 优点：用户无法轻易访问 sourcemap，提高了安全性；开发者仍然可以通过错误报告系统来调试问题。
- 缺点：需要额外的步骤来关联 sourcemap 和错误报告。

### 总结

- eval: 最快生成速度，适合开发阶段快速迭代。
- cheap-source-map: 快速生成，行级别调试信息，适合一般调试需求。
- cheap-module-source-map: 包含 loader 信息，适合复杂构建流程的调试。
- source-map: 最详细的调试信息，适合生产环境中的错误追踪。
- inline-source-map: 源码内联，方便调试，但增加文件大小。
- nosources-source-map: 不包含源代码，保护隐私，适合敏感代码的调试。
- hidden-source-map: 完整 sourcemap 无引用注释，提高安全性，适合生产环境。
  根据项目的规模、调试需求和安全考虑，选择合适的 sourcemap 类型可以显著提升开发效率和调试体验。

[webpack 详细说明](https://webpack.docschina.org/configuration/devtool/)

## 类型示例:

[](https://github.com/webpack/webpack/tree/main/examples/source-map)

# http 响应头 sourcemap

HTTP 响应头 sourcemap 的优点是可以避免在生成的代码中添加额外的注释，减少代码的大小。可以更灵活地控制是否暴露源映射文件的地址，比如可以根据请求的来源或者身份验证来决定是否返回 SourceMap 响应头。可以更方便地修改或删除源映射文件的地址，而不需要修改生成的代码。缺点是需要服务器端支持，并且可能与缓存机制冲突。
注释方式的优点是简单易用，不需要服务器端配置，并且可以兼容 HTTP/1.0 缓存。缺点是会增加生成的代码的体积，以及暴露 source map 的位置给普通用户。

```javascript
HTTP/1.1 200 OK
Content-Type: application/javascript
SourceMap: /path/to/file.js.map

<optimized-javascript>
```

# sourcemap name 来源

js 文件解析为抽象语法树 中定义的变量
可以使用 babel 来生成 ast 验证

```javascript
const fs = require('fs');
const path = require('path');
const babel = require('@babel/core');

const inputFilePath = path.join(\_\_dirname, 'demo.js');

babel.transformFile(inputFilePath, {
ast:true,
}, (err, result) => {
if (err) {
console.error('Error transforming file:', err);
return;
}

if (result.ast) {
fs.writeFileSync('ast.json', JSON.stringify(result.ast));
}

console.log('ast generate successfully!');
});
```

# sourcemap mappings 原理

`"mappings": "+LACOA,MAAM,S,GACTC,EAAAA;EAAAA,GAA8B,UAA1B;yBAAqB,G,GAAz"`

## mappings 说明

属性的值分为三层

第一层是行对应，以分号（;）表示，每个分号对应转换后源码的一行。所以，第一个分号前的内容，就对应源码的第一行，以此类推。

第二层是位置对应，以逗号（,）表示，每个逗号对应转换后源码的一个位置。所以，第一个逗号前的内容，就对应该行源码的第一个位置，以此类推。

第三层是位置转换，以 VLQ 编码表示，代表该位置对应的转换前的源码位置。

## 位置对应规则

每个位置字段最多可以表示五个位置信息，位置通常用数字表示。

- 第一位，表示这个位置相对于前一个位置（转换后的代码）的第几列。
- 第二位，表示这个位置属于 sources 属性中的哪一个文件。
- 第三位，表示这个位置属于转换前代码的第几行。
- 第四位，表示这个位置相对于前一个位置（转换前的代码）的第几列。
- 第五位，表示这个位置属于 names 属性中的哪一个变量。

说明：

1. 通常前四位是一直存在的，第五位可能不存在，极端情况下只有一位。
2. 表示位置的位数不一定是五，可能更多，原因是表示的数字很大。

单个字母代表的是特定的源代码位置，通常是一些无法被精确映射到行和列的位置。这些位置可能是空格、注释、或其他一些无关紧要的字符。在生成 sourcemap 文件时，这些位置也会被记录下来，以保证完整性。

# VLQ 编码

VLQ（Variable Length Quantity，可变长度数量）是一种将数值编码到字符串中的算法，通常用于将 sourcemap 文件的行列信息编码为字符串，以实现压缩和优化文件大小。
VLQ 编码是变长的。如果（整）数值在-15 到+15 之间（含两个端点），用一个字符表示；超出这个范围，就需要用多个字符表示。它规定，每个字符使用 6 个两进制位，正好可以借用 Base 64 编码的字符表。
在这 6 个位中，左边的第一位（最高位）表示是否"连续"（continuation）。如果是 1，代表这６个位后面的 6 个位也属于同一个数；如果是 0，表示该数值到这 6 个位结束。

```
　　Continuation
　　|　　　　Sign
　　|　　　　 |
　　V 　　　　V
　　１０１０１１
```

这 6 个位中的右边最后一位（最低位）的含义，取决于这 6 个位是否是某个数值的 VLQ 编码的第一个字符。如果是的，这个位代表"符号"（sign），0 为正，1 为负；如果不是，这个位没有特殊含义，被算作数值的一部分。

## 编码流程

例如：对 100 进行编码

1. 将 100 改写成二进制形式 1100100
2. 在最右侧补充符号位。因为 100 大于 0，所以符号为补 0，变成 11001000
3. 从右边的最低位开始，将上一步结果每隔 5 位，进行分段，即变成 110 和 01000 两段，如果最高位不足 5 位，在前面补 0，因此最终变成 00110 和 01000。
4. 将上面的多段数字顺序颠倒排列，即 01000 00110
5. 在每一段的最前面添加一个“连续位”，除了最后一段为 0，其他都是 1，即变成 101000 和 000110
6. 将每一段转成 base64 编码，即 o 和 G，参考
7. 所以 100 vlq-base64 编码为 oG

# 实践

## 错误捕获上报

```javascript
import axios from "axios";
import XbaseLogger from "@frameworker/api/logger/xbase-logger";

const sourceMap = require("source-map");

sourceMap.SourceMapConsumer.initialize({
  "lib/mappings.wasm": "https://example.com/mappings.wasm",
});
const logger = XbaseLogger.getLogger(XbaseLogger.error_log);
// 获取给定 map 内容的原始信息
const getSource = async (rawSourceMap, lineno, colno) => {
  // 通过 sourceMap 库转换为 sourceMapConsumer 对象
  const consumer = await new sourceMap.SourceMapConsumer(rawSourceMap);
  // 传入要查找的行列数，查找到压缩前的源文件及行列数
  const sm = consumer.originalPositionFor({
    line: lineno, // 压缩后的行数
    column: colno, // 压缩后的列数
  });
  consumer.destroy();
  return sm;
};
const reportException = async (message, source, lineno, colno) => {
  try {
    // 读取 map 文件
    const fetchResult = await axios.get(`${source}.map`);
    if (fetchResult.status === 200) {
      const result = await getSource(fetchResult.data, lineno, colno);
      logger.info("windowonerror", {
        desc: "由 sourcemap 文件还原的错误",
        message,
        source: result.source,
        lineno: result.line,
        colno: result.column,
      });
    } else {
      logger.info("windowonerror", {
        desc: "原始的错误",
        message,
        source,
        lineno,
        colno,
      });
    }
  } catch (err) {
    console.warn("未抓取到 map 文件", err);
    logger.info("windowonerror", {
      desc: "原始的错误",
      message,
      source,
      lineno,
      colno,
    });
  }
};
// 仅 build 后的代码，才上报错误
if (process.env.VUE_APP_MODE === "production") {
  window.onerror = (message, source, lineno, colno, error) => {
    console.error("onerror", {
      message,
      source,
      lineno,
      colno,
      error,
    });
    if (message.includes("Script error")) return; // 脚本错误数量太多，无可用信息，暂时忽略
    reportException(message, source, lineno, colno, error);
  };
}
```

# 调试线上代码

- case1:
  sourcemap 隐藏
  开发调试时手动指定文件
  [![pEMw8OA.md.png](https://s21.ax1x.com/2025/02/18/pEMw8OA.md.png)](https://imgse.com/i/pEMw8OA)
- case2:
  通过 SourceMapDevToolPlugin 插件，配置 soucemap url
  [![pEMabuR.md.png](https://s21.ax1x.com/2025/02/18/pEMabuR.md.png)](https://imgse.com/i/pEMabuR)
  url 做内网访问或者用户鉴权处理
- case3:
  服务端配置 sourcemap 响应头，动态下发 path 信息，通过用户权限判断是否下发

  # 附录

  ## [source map revision 3 proposal](https://docs.google.com/document/d/1U1RGAehQwRypUTovF1KRlpiOFze0b-_2gc6fAH0KY0k/edit#heading=h.djovrt4kdvga)

  ## [(COder/DECoder) AND SOURCEMAP V3 MAPPINGS PARSER](https://www.murzwin.com/base64vlq.html)

  ## [map-visualization](https://evanw.github.io/source-map-visualization/)

  ## [source-map-visualization](https://sokra.github.io/source-map-visualization/)

  ## base64 编码表

| 词组 | 字符 | 词组 | 字符 | 词组 | 字符 | 词组 | 字符 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 0    | A    | 16   | Q    | 32   | 9    | 48   | w    |
| 1    | B    | 17   | R    | 33   | h    | 49   | x    |
| 2    | C    | 18   | S    | 34   | i    | 50   | y    |
| 3    | D    | 19   | T    | 35   | j    | 51   | z    |
| 4    | E    | 20   | U    | 36   | k    | 52   | 0    |
| 5    | F    | 21   | V    | 37   | l    | 53   | 1    |
| 6    | G    | 22   | W    | 38   | m    | 54   | 2    |
| 7    | H    | 23   | X    | 39   | n    | 55   | 3    |
| 8    | I    | 24   | Y    | 40   | o    | 56   | 4    |
| 9    | J    | 25   | Z    | 41   | p    | 57   | 5    |
| 10   | K    | 26   | a    | 42   | q    | 58   | 6    |
| 11   | L    | 27   | b    | 43   | r    | 59   | 7    |
| 12   | M    | 28   | c    | 44   | s    | 60   | 8    |
| 13   | N    | 29   | d    | 45   | t    | 61   | 9    |
| 14   | O    | 30   | e    | 46   | u    | 62   | +    |
| 15   | P    | 31   | f    | 47   | v    | 63   | /    |
