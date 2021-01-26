## 初始化目录
我们要从一个空目录开始，先新建这个目录，做一些必要的初始化工作：

```shell
$ mkdir my-react
$ cd my-react

$ git init
$ npm init新建如下目录结构：
```

### react-project

- config 打包配置
- public 静态文件夹
- index.html
- favicon.ico
- src 源码目录

## 规范git提交
协作开发时，git提交的内容如果没有规范，就不好管理项目，我们用`husky` + `commitlint` 来规范git提交。我们先在根目录下建立 `.gitignore` 文件，忽略不需要要的文件。然后安装工具：

```shell
$ npm i -D husky
$ npm i -D @commitlint/cli
```

`husky` 会为 git 增加钩子，在`commit` 时执行一系列操作，`commitlint` 可以检查` git message` 是否符合规则。
在 `package.json` 中增加配置如下：

```json
"husky": {
  "hooks": {
    "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
  }
},
```

在根目录新建文件` .commitlintrc.js`，根据具体情况配置：

```javascript
module.exports = {
  parserPreset: {
    parserOpts: {
      headerPattern: /^(\w*)(?:\((.*)\))?:\s(.*)$/,
      headerCorrespondence: ['type', 'scope', 'subject']
    }
  },
  rules: {
    'type-empty': [2, 'never'],
    'type-case': [2, 'always', 'lower-case'],
    'subject-empty': [2, 'never'],
    'type-enum': [
      2,
      'always',
      ['feat', 'fix', 'update', 'docs', 'style', 'refactor', 'test', 'chore', 'release', 'revert']
    ]
  }
}
```


这样即可完成配置，具体的使用方法参考`commitlint`文档

## React hello, world
安装react，写一个react hello, world

现在让主角 React 登场：

`$ npm i react react-dom`
新建一个 hello, world 结构，这里直接用ts书写：

```typescript
// src/index.tsx
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import './style.css';

ReactDOM.render(<App />, document.getElementById('root'));
// src/App.tsx
import React from 'react';
import './app.css';

const App: React.FC = () => {
  return (<div>hello, world</div>);
};

export default App;
```


我们还需要一个html模板：

<!-- public/index.html -->

<!DOCTYPE html>
<html lang="zh-CN">
  <head>
    <meta charset="utf-8" />
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no" />
    <title>react-app</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
  </body>
</html>
完整的结构参考 代码示例


## 团队规范

### eslint

我们通常使用`lint`工具来检查代码不规范的地方，以下是将` eslint`、`typescript `和 `webpack` 结合使用的例子。

首先安装依赖：

`$ npm i -D eslint babel-eslint eslint-loader eslint-plugin-jsx-control-statements`
`$ npm i -D eslint-plugin-react @typescript-eslint/parser @typescript-eslint/eslint-plugin `
然后在根目录新建eslint配置文件`.eslintrc.js`：

```javascript
module.exports = {
  "root": true,
  "env": {
    "browser": true,
    "node": true,
    "es6": true,
    // "jquery": true
    "jest": true,
    "jsx-control-statements/jsx-control-statements": true // 能够在jsx中使用if，需要配合另外的babel插件使用
  },
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "sourceType": 'module',
    "ecmaFeatures": {
      "jsx": true,
      "experimentalObjectRestSpread": true
    }
  },
  "globals": {
    // "wx": "readonly",
  },
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:jsx-control-statements/recommended", // 需要另外配合babel插件使用
  ],
  "settings": {
    "react": {
      "version": "detect" // 自动读取已安装的react版本
    }
  },
  "plugins": ["@typescript-eslint", "react", "jsx-control-statements"],
  "rules": {
    "no-extra-semi": 0, // 禁止不必要的分号
    "quotes": ['error', 'single'], // 强制使用单引号
    "no-unused-vars": 0 // 不允许未定义的变量
    // ...你自己的配置
  }
};
```

我们可能希望检查或不检查某些特定的文件，可以在根目录新建.eslintignore，以下配置不检查src目录以外的js文件：

```
**/*.js
!src/**/*.js
```

还需要配置`webpack`，才能在开发时启用`eslint`：

```javascript
// webpack.base.js
module: {
  rules: [
    // 把这个配置放在所有loader之前
    {
      enforce: 'pre',
      test: /\.tsx?$/,
      exclude: /node_modules/,
      include: [APP_PATH],
      loader: 'eslint-loader',
      options: {
        emitWarning: true, // 这个配置需要打开，才能在控制台输出warning信息
        emitError: true, // 这个配置需要打开，才能在控制台输出error信息
        fix: true // 是否自动修复，如果是，每次保存时会自动修复可以修复的部分
      }
    }
  ]
}
```

### prettier
除了约束开发时的编码规范外，我们一般还希望在提交代码时自动格式化代码，但我们只希望处理当前提交的代码，而不是整个代码库，否则会把提交记录搞得乱七八糟，`prettier`和`lint-staged`可以完成这项任务。

先安装工具：

`$ npm i -D prettier eslint-plugin-prettier eslint-config-prettier`
`$ npm i -D lint-staged`
在根目录增加prettier配置`.prettierrc.js`，同样的也可以增加忽略配置`.prettierignore`（建议配置为与lint忽略规则一致）：

```javascript
// 这个配置需要与eslint一致，否则在启用 eslint auto fix 的情况下会造成冲突
module.exports = {
  "printWidth": 120, //一行的字符数，如果超过会进行换行，默认为80
  "tabWidth": 2,
  "useTabs": false, // 注意：makefile文件必须使用tab，视具体情况忽略
  "singleQuote": true,
  "semi": true,
  "trailingComma": "none", //是否使用尾逗号，有三个可选值"<none|es5|all>"
  "bracketSpacing": true, //对象大括号直接是否有空格，默认为true，效果：{ foo: bar }
};
修改eslint配置.eslintrc.js：

module.exports = {
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:jsx-control-statements/recommended", // 需要另外配合babel插件使用
    "prettier" // 注意顺序
  ],
  "plugins": ["@typescript-eslint", "react", "jsx-control-statements", "prettier"], // 注意顺序
  "rules": {
    "prettier/prettier": 2, // 这样prettier的提示能够以错误的形式在控制台输出
  }
};
```


然后我们要配置`lint-staged`，在提交代码时自动格式化代码。

修改`package.json`：

```json
"husky": {
  "hooks": {
    "pre-commit": "lint-staged"
  }
},
"lint-staged": {
  "src/**/*.{jsx,js,tsx,ts}": [
    "prettier --write",
    "eslint --fix",
    "git add"
  ]
}
```

### 用`editorconfig`统一编辑器规范
有些编辑器能够根据配置提示会自动格式化代码，我们可以为各种编辑器提供一个统一的配置。

在根目录新建`.editorconfig`即可，注意不要与已有的lint规则冲突：

```
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
```

### 使用jest
使用`jest`可以帮助我们测试代码，在项目中使用`jest`的实现方式有很多种，文本不具体展开讨论，只提供一些必备的工具和配置。

必备工具：`$ npm i -D jest babel-jest ts-jest @types/jest`
参考配置`jest.config.js`，测试文件均放在__test__目录中：

```javascript
module.exports = {
  transform: {
    '^.+\\.tsx?$': 'ts-jest',
  },
  testRegex: '(/__tests__/.*|(\\.|/)(test|spec))\\.(jsx?|tsx?)$',
  moduleFileExtensions: ['ts', 'tsx', 'js', 'jsx', 'json', 'node'],
};
```

### 美化webpack输出信息
webpack在开发时的输出信息有一大堆，可能会干扰我们查看信息，以下提供一个美化、精简输出信息的建议。

精简以下开发服务器输出信息，修改`webpack.dev.js`：

```javascript
// ...webpack configs
stats: {
  colors: true,
  children: false,
  chunks: false,
  chunkModules: false,
  modules: false,
  builtAt: false,
  entrypoints: false,
  assets: false,
  version: false
}
```

美化一下打包输出，安装依赖：`$ npm i -D ora chalk`
修改`config/build.js`：

```javascript
const ora = require('ora');
const chalk = require('chalk'); // 如果要改变输出信息的颜色，使用这个，本例没有用到
const webpack = require('webpack');
const webpackConfig = require('./webpack.prod');

const spinner = ora('webpack编译开始...\n').start();

webpack(webpackConfig, function (err, stats) {
  if (err) {
    spinner.fail('编译失败');
    console.log(err);
    return;
  }
  spinner.succeed('编译结束!\n');

  process.stdout.write(stats.toString({
    colors: true,
    modules: false,
    children: false,
    chunks: false,
    chunkModules: false
  }) + '\n\n');
});
```


分别运行打包和开发命令，控制台界面是不是清爽多了？

## 路由的配置
本段提供一个`react-router`的实践。

安装依赖：

`$ npm i react-router-dom react-router-config @types/react-router-dom @types/react-router-config`
`$ npm i @loadable/component`
新建`src/router.ts`：

```javascript
import loadable from '@loadable/component'; // 按需加载

export const basename = ''; // 如果访问路径有二级目录，则需要配置这个值，如首页地址为'http://tianzhen.tech/blog/home'，则这里配置为'/blog'

export const routes = [
  {
    path: '/',
    exact: true,
    component: loadable(() => import('@/pages/demo/HelloWorldDemo/HelloWorldDemoPage')), // 组件需要你自己准备
    name: 'home', // 自定义属性
    title: 'react-home' // 自定义属性
    // 这里可以扩展一些自定义的属性
  },
  {
    path: '/home',
    exact: true,
    component: loadable(() => import('@/pages/demo/HelloWorldDemo/HelloWorldDemoPage')),
    name: 'home',
    title: 'HelloWorld'
  },
  // 404 Not Found
  {
    path: '*',
    exact: true,
    component: loadable(() => import('@/pages/demo/404Page/404Page')),
    name: '404',
    title: '404'
  }
];
```

改造`index.tsc`，启用路由：

```react
import React from 'react';
import { BrowserRouter } from 'react-router-dom';
import { renderRoutes } from 'react-router-config';
import { routes, basename } from './router';
import '@/App.less';

const App: React.FC = () => {
  return <BrowserRouter basename={basename}>{renderRoutes(routes)}</BrowserRouter>;
};

export default App;
```


我们还可以利用路由为每个页面设置标题。

先写一个hook：

```javascript
import { useEffect } from 'react';

export function useDocTitle(title: string) {
  useEffect(() => {
    const originalTitle = document.title;
    document.title = title;
    return () => {
      document.title = originalTitle;
    };
  });
}
```

把`hook`应用在需要修改标题的组件中即可：

```react
import React from 'react';
import { useDocTitle } from '@/utils/hooks/useDocTitle';

import Logo from './react-logo.svg';
import './HelloWorldDemoPage.less';

const HelloWorldDemoPage: React.FC<Routes> = (routes) => {
  const { route } = routes; // 获取传入的路由配置
  useDocTitle(route.title); // 修改标题
  return <div className="App">hello, world</div>;
};

export default HelloWorldDemoPage;
```



