---
title: "TS tsconfig.json文件配置"
date: 2024-03-31 13:57:17
categories:
- TypeScript
tags:
- TypeScript
toc: true # 是否显示目录
---
> tsconfig.js文件配置
<!-- more -->
## files
指定要包含在程序中的文件的允许列表。如果找不到任何文件，则会发生错误。   
当您只有少量文件并且不需要使用 glob 来引用许多文件时，这非常有用。如果您需要的话，请使用`include`.
```js
{
  "compilerOptions": {},
  "files": [
    "core.ts",
    "sys.ts",
    "types.ts",
    "scanner.ts",
    "parser.ts",
    "utilities.ts",
    "binder.ts",
    "checker.ts",
    "tsc.ts"
  ]
}
```

## extend
`extends`是一个字符串，其中包含要继承的另一个配置文件的路径。该路径可以使用 Node.js 样式解析。  
首先加载基础文件中的配置，然后被继承配置文件中的配置覆盖。配置文件中找到的所有相对路径都将相对于它们源自的配置文件进行解析。   
值得注意的是继承配置文件中的`files、include`、 和会覆盖基础配置文件中的配置文件，并且配置文件之间不允许循环
```js
// configs/base.json
{
  "compilerOptions": {
    "noImplicitAny": true,
    "strictNullChecks": true
  }
}
```
```js
// tsconfig.json
{
  "extends": "./configs/base",
  "files": ["main.ts", "supplemental.ts"]
}
```
## compilerOptions
构成了 `TypeScript` 配置的大部分，并且涵盖了该语言的工作方式

```js
{
  "compilerOptions": {
    "target": "esnext", // 指定ts被编译为的ES的版本,esnext:最新版本，可设置其他，es3、es5、es6、es2015、es2016、es2017、es2018、es2019、es2020、esnext

    "module": "esnext", // 指定要使用的模块化的规范，包括：none,common.js,system,amd,umd,es6,es2015,es2020,esnext

    "moduleResolution": "node",
    "strict": true,
    "noLib": false,
    "forceConsistentCasingInFileNames": true,
    // "allowSyntheticDefaultImports": false,
    "strictFunctionTypes": false,
    "jsx": "preserve",
    "baseUrl": ".",
    // "allowJs": false,
    "sourceMap": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "experimentalDecorators": true,
    "lib": ["dom", "esnext"], // 指定项目中要使用的库
    "noImplicitAny": false,
    "skipLibCheck": true,
    "typeRoots": [
      "./types"
    ],
    "types": [
      "vite/client",
      "unplugin-vue-define-options/macros-global"
    ],
    "removeComments": true,
    "paths": {
      "@/*": ["src/*"],
      "#/*": ["types/*"]
    }
  },
  "include": [  // 指定需要编译的文件 **：任意目录；* 任意文件; ?：任意字符;
    "tests/**/*.ts",
    "src/**/*.ts",
    "src/**/*.d.ts",
    "src/**/*.tsx",
    "src/**/*.vue",
    "types/**/*.d.ts",
    "types/*.d.ts",
    "types/**/*.ts",
    "*.d.ts",
    "./auto-imports.d.ts"
  ],
  "exclude": ["node_modules", "dist", "**/*.js"] // 指定不需要编译的文件
}
```
## 其他
```js
{
  "compilerOptions": {
    "outDir": "./dist", // 指定编译后文件的目录
    "outFile": "./dist/app.js", //  合并为一个文件，所有全局作用域中的代码合并到同一文件中
    "allowJs": true, // 是否对js文件进行编译，默认false
    
    "checkJs": true, // 是否对js文件检查语法规范，默认false

    "removeComments": false, // 是否移除注释

    "noEmit": true, // 不生成编译后的文件

    "noEmitOnError": true, // 有错误时不生成编译后的文件

    "strict": true, // 严格模式总开关
    
    "alwaysStrict": false, // 启用严格模式，默认false

    "noImplicitAny": true, // 不允许隐式any

    "noImplicitThis": true, // 不允许不明确类型的this

    "strictNullChecks": true, // 严格的检查空值
  
}
```