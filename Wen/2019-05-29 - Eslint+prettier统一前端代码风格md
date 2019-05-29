> 行尾要不要加分号？用tab还是空格？单引号还是双引号？如何规范团队代码风格？
>
> 本文主要介绍使用EsLint+Prettier帮助我们检查Javascript编程时的语法错误，统一代码风格。

### （1）先来讲讲Eslint的基础知识

####  Eslint安装

```javascript
// 本地安装
npm install eslint --save-dev
// 在项目目录下，运行以下代码将会产生一个.eslintrc的配置文件，文件内容包含一些校验规则。
eslint --init
```

#### 解读生成的`.eslintrc`文件中的配置规则

```javascript
{
  "parserOptions": {
    "ecmaVersion": 6,  // 指定ECMAScript支持的版本，6为ES6//
    "sourceType": "module", // 指定来源的类型，有两种”script”或”module”
    "ecmaFeatures": { //启动JSX
      "jsx": true
    },
    "parser": "babel-eslint"
  },
  "rules": {
    "semi": "error",
    "prettier/prettier": "error", // prettier监测直接报错！！！
    "no-console": "off"
  },
  "root": true,
  "env": {
    "node": true
  },
  "extends": [
    "plugin:vue/essential",
    "eslint:recommended",
    "@vue/prettier"
  ]
}
```

- **rules**

    1. `semi`,`no-console` ... 等都是规则名称，`off`,`error`...等都是规则校验的执行方式。
    2. `"off"` or `0` -  不提示
    3. `"warn"` or `1` - 不符合校验规则只给以警告，但不影响项目编译执行
    4. `"error"` or `2` -  不符合校验会直接报错！

- **parserOptions**
  
   EsLint通过parserOptions，允许指定校验的ecma的版本，及ecma的一些特性。
  
- **Parser**

   EsLint默认使用esprima做脚本解析，当然你也切换他，比如切换成babel-eslint解析去支持jsx。

- **Environments**

   Environment可以预设好其他环境的全局变量，如brower、node环境变量、es6环境变量、mocha环境变量等

#### 如何自定义配置Eslint？

EsLint提供了2个种方式进行自定义配置Eslint:

1. 在所要验证的文件中，直接使用Javascript注释嵌套配置信息
2. 使用JavaScript、JSON或YAML文件，比如前面提到的`.eslintrc`文件，当然你也可以在package.json文件里添加`eslintConfig`字段，EsLint都会自动读取验证。
> 关于Eslit的更多参考链接：
>
> 1. Eslint官方网址：<https://eslint.org/docs/user-guide/getting-started>
>
> 2. ESLint 配置文件 .eslintrc 示例及说明：<https://www.jianshu.com/p/a09a5a222a76>

### （2）Prettier又是什么？为什么要使用它？

> 首先，对应ESLint大多都很熟悉，用来进行代码的校验，但是Prettier（直译过来就是"更漂亮的"😂）听得可能就比较少了。Prettier是一个能够完全统一你和同事代码风格的利器。

#### 安装Prettier

首先肯定是需要安装`prettier`，并且你的项目中已经使用了ESLint，有`eslintrc.js`配置文件。

```javascript
npm i -D prettier
```
安装插件：eslint-plugin-prettier插件会调用prettier对你的代码风格进行检查，其原理是先使用prettier对你的代码进行格式化，然后与格式化之前的代码进行对比，如果过出现了不一致，这个地方就会被prettier进行标记。

```javascript
npm i -D eslint-plugin-prettier
```

接下来，我们需要在`.eslintrc`配置文件中的rules中添加，`"prettier/prettier": "error"`，表示被prettier标记的地方抛出错误信息。

#### 如何对Prettier进行配置？

一共有三种方式支持对Prettier进行配置：

1. 根目录创建`.prettierrc `文件，能够写入YML、JSON的配置格式，并且支持`.yaml/.yml/.json/.js`后缀；
2. 根目录创建`.prettier.config.js `文件，并对外export一个对象；
3. 在`package.json`中新建`prettier`属性。

以`.prettierrc.js`为例子，简单介绍各个配置的作用：

```javascript
// prettier[options]更多配置的参考链接：https://prettier.io/docs/en/options.html
module.exports = {
  printWidth: 120, //一行的字符数，如果超过会进行换行，默认为80
  singleQuote: true, //字符串是否使用单引号，默认为false，使用双引号
  trailingComma: 'none', //是否使用尾逗号，有三个可选值"<none|es5|all>"
  semi: true, //行尾是否使用分号，默认为true
  bracketSpacing: true, //对象大括号直接是否有空格，默认为true，效果：{ foo: bar }
  proseWrap: 'never',
  jsxBracketSameLine: true
};
```

>关于`prettierrc`的参考链接如下：
>
>1. ESlint与Prettier统一前端规范：<https://segmentfault.com/a/1190000015315545>
>2. Prettier官方网址： <https://prettier.io/docs/en/options.html>
>3. [参考链接2](<https://mp.weixin.qq.com/s?__biz=MzA5MjQ0Mjk2NA==&mid=2247484589&idx=1&sn=92c860ef58b574aea351adf81a01a8e6&chksm=906c5c96a71bd5804165d46d5bb95a1871de57853f51fb12f24b3e78b535e02839dc7c0d1801&mpshare=1&scene=1&srcid=&key=7cdbdfe8ad7a4f03d4542c113eb588baa3254d05718328c8b1d2e9cba66ea7b503401ba03d2629e9846f3b29cca3880f4e6df8c76818a86dd278efd6f56c35760b56b835a1e7463f4c615090266de8f8&ascene=1&uin=MjgxNjYzNDMwNA%3D%3D&devicetype=Windows+10&version=62060739&lang=zh_CN&pass_ticket=Qbp9gzdj49nzarhhzW3ZujbiV7TlJsTkzqxWgsg2SPp8UEMS3kmWp5ZGLxeYWhX%2F>)
>


