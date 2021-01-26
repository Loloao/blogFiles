# Typescript pro

## tsconfig

- 只有在直接使用`tsc`这个命令而不指定某个文件的时候，才会使用`tsconfig.json`终端配置项。如果没指定编译哪个文件夹，会对根目录下的`ts`文件进行编译。
- `include`或`exclude`限制想要编译的文件
- `noImplicitAny`使用`any`类型时也必须显式指定
- `strictNullChecks`表示`null`能够设定为 type 类型
- `rootDir`表示需要编译的文件地址
- `outDir`表示编译过后的文件地址
- `incremental`表示只编译新增的内容
- `allowJs`表示是否对 js 文件进行编译，此时 js 文件也会打包到输出目录。
- `checkJs`对 js 语法进行检测
- `sourceMap`编译后会生成 sourceMap
- `noUnusedLocals`表示未使用的变量是否警告
- 具体看官方文档
- 当把所有文件都打包到一个文件时，需要使用`amd`的引用方式模块化方式，即`module:"amd"`
- `experimentalDecorators`和`emitDecoratorMetadata`设为`true`表示可以在项目中使用装饰器语法
- aaa
- `extends`可以继承 tsconfig 文件
- `exclude`为一个数组设置哪个文件不需要被编译成 js，文件目录可以设置通配符，比如`./src/**/*.internal.ts`表示所有文件夹下的带`.internal.ts`都为不需要编译的文件
- `include`则是需要编译成 js 的文件路径，`src/m???.ts`表示以`m`开头的四字文件
- `files`配置项不能使用通配符，它拥有最高权限，即使文件存在于`exclude`中，它也会被编译

### compilerOptions

- `outDir`可以配置编译后的 js 文件放哪

- `noEmit`表示不会编译成 js 文件，默认情况下，ts 总会被编译成 js 文件

- `noEmitOnError`有错误才不会被编译成 js 文件，有时我们可能不需要类型检测但需要输出后的 js 文件

- `declaration`表示会输出`.d.ts`的类型解释文件

- `declarationDir`类型解释文件输出到哪个文件夹

- `stripInternal`为 true 表示如果类型上方加上`/** @internal */`则不会生成类型解释文件

- `sourceMap`是否生成 sourceMap 文件

- `inlineSources`输出的`.map`文件会包含`source`路径

- `inlineSourceMap`输出单个`.map`文件而不是多个分离的文件

- `mapRoot`sourceMap 文件的位置

- `noImplicitAny`类型推断不会推断为`any`

- `strictNullChecks`表示`undefined`和`null`将不会被识别为任意类型，此配置和上面的`noImplicitAny`都推荐在新项目中使用

- `suppressImplicitAnyIndexErrors`对象可以直接增加属性而不会因为类型被推断为`any`而报错。

- `suppressExcesPropertyErrors`对象可以直接增加它的`interface`所没有的属性

- `allowUnreachableCode`比如`return`加分号之后可以写语句

- `allowUnusedLabels`表示加上了 label 可以不适用，此处的 lable 加在循环前面的名称，上面四项最好不使用

- `noUnusedLocals`不能存在不用的变量

- `noUnusedParameters`不能存在不用的参数

- `noFallthroughCasesInSwitch`在`switch`中的`case`语句后需要加上`break`

- `noImplicitReturns`在`function`中的条件语句中如果有返回值则必须所有情况下都返回一个值

- `module`用于设定 ts 的模块规则

  - `none`所有变量和函数都是全局的，不能使用关于模块的关键字
  - `commonjs`会以 nodejs 的模块规则输出 js 文件，这意味着所输出的 js 可以在 nodejs 的环境下运行
  - `umd`(Universal Module Definition)任何情况下都能使用
  - `system`会将代码输出在一个`system`变量上，具体见 systemjs
  - `amd`即 requirejs
  - `es2015`即 es 模块标准

- `target`表示会输出什么形式的 js，比如 commonjs，es5，es6

- `moduleResolution`如果为`node`表示模块引入的路径和 nodejs 一致，如果为`classic`表示模块引入的路径和标准引入规则一致，一般用于支持很早以前的 ts 版本。

  ```typescript
  // 下面语句如果是 node 则会从 node_modules 中寻找 a
  // 如果是 classic 则会从本目录往下寻找
  import { a } from 'a'
  ```

- `traceResolution`会打印出引用的模块路径，一般为`false`

- `diagnostics`会打印编译时的各项指标信息

- `listFiles`打印编译时的有关文件，`d.ts`也会打印

- `listEmittedFiles`在输出时会打印在那个文件，错误信息非常详细

- `pretty`在打印信息时对关键字会有彩色标注

- `locale`国际化

- `noImplicitUseStrict`默认情况下 ts 在 module 文件中会使用严格模式，而在 global 文件中不会，这个配置会在 module 文件中解除严格模式

- `alwaysStrict`global 文件中也默认为严格模式

- `experimentalDecorators`允许使用装饰器

- `emitDecoratorMetadata`允许使用`reflectMetadata`，这项和上面这项在 angular 中需要为 true

- `emitBOM`在输出文件的开头加入 BOM 头

- `removeComments`输出的 js 文件不会有注释

- `newLine`设置每行结尾是为`crlf`(windows)或为`lf`(unix)

- `typeRoots`要包含的类型声明文件路径列表

- `types`类型声明文件中能够用的类型声明依赖，如果为空数组则依赖的类型声明文件不会生效

- `noEmitHelpers`当编译 ts 文件时，像`extends`关键字等在 es3 中不存在的 feature 需要使用 js 代码做兼容，这种兼容代码统称为 helper，这时每个文件都会有同样的兼容代码，当设为 true 时，输出的 js 文件就不会做兼容

- `importHelpers`会把所有兼容 js 代码放在同一文件，这个文件由 ts 团队维护，需要`npm i tslib`

- `rootDir`如果要编译的文件夹只有一个，那么这个文件夹不会被放到`outDir`下的文件夹中，但它的文件依旧会放置在`outDir`下。这个值可以使单个文件夹依旧能够保留，一般设为`"./"`

- `outFile`可以让输出的文件只包含在一个文件中，可以设为`./dist/bundle.js`会创建一个文件夹

- `preserveConstEnums`可以使用`const`来定义`enum`，同时使用`const`时，输出的 js 文件还是会实现`enum`

  **react 相关**

  - `jsx`
    - `preserve`会把`.tsx`后缀文件编译成`.jsx`文件，文件内容依旧包含`jsx`写法
    - `react`会输出为`.js`文件，`jsx`会输出为`React.createElement`方程
    - `react-native`输出为`.js`文件，但是包含`jsx`

  - `reactNamespace`可以设置`jsx`写法编译为那种函数，默认为`React.createElement`
    - `React`默认值
    - 其他值需要导入第三方库，之后`jsx`会直接调用这个第三方库的`createElement`方法

  - `jsxFactory`默认为`React.createElement`，可以为第三方库，作用同`reactNamespace`

  - `noImplicitThis`如果`this`被设为`any`则会报错，可以在定义方法的括号里设定`this`的类型，但在使用这个方法的时候却不能传入参数

## 语法

- 变量后面跟着`!`表示这个变量一定不是`undefined`或`null`，表示此变量已经初始化

- `keyof T`产生的类型是`T`的属性名称字符串字面量类型构成的联合类型

  ```typescript
  function getProperty<T, K extends keyof T>(ogj: T, key: K) {
    return obj[key]
  }
  ```

- 创建`readonly`的对象

  ```typescript
  type Stringified<T> = {
    [P in keyof T]: string
  }
  
  type ReadOnly<T> = {
    readonly [P in keyof T]: T[P]
  }
  ```


## 类型

- 相同名称的 interface 会合并，但后面同名的 interface 中的相同属性不能设为不同的类型。如果 interface 中有方法，则它们的参数和返回值类型会合并

- 一般情况下对象的类型是`Object`，但在`Object.create`和`WeakMap`的参数中却是`object`，它不能表示为初始值，而像`string`等初始值可以表示为`Object`类型

- 我们可以为引入的 module 中的 interface 添加额外的类型

  ```typescript
  import { of } from 'rxjs/Observable'
  declare module 'rxjs/Observable' {
    interface Observable<T> {
      toJames(): Observable<'James'>
    }
  }
  
  Observable.prototype.toJames = function () {
    return Observable.of('James')
  }
  ```

  - `enum`前加一个`const`定义可以使编译后的 js 不再有`enum`的 js 实现，而是直接赋值为`enum`中定义的值，但它不能赋值为一个可以变的值
  - `declare`可以用来定义一个全局变量，但是编译成 js 时，这个全局变量并不会输出，所以注意编译后的 js 应该加上这个变量

## 冷知识

- `ts-loader`编译的时候可以进行类型检查，它把 ts 直接变成 es5， 而`babel-loader`的`@babel/preset-typescript`不能进行类型检查
- 之前是`ts-lint`进行 ts 代码统一检查，但现在官方使用`eslint`
- ts 之前分为内部模块和外部模块，现在内部模块改为命名空间，外部模块也就是`import`和`export`，外部模块中可以有内部模块。
- `namespace`编译过后就是一个闭包

## 技巧

- 可以用类`implements`interface 之后使用`type`或来对对应的类型进行识别

  ```typescript
  export interface Action {
    type: string
  }
  
  export class Add implements Action {
    readonly type = 'Add'
    constructor(public payload: string) {}
  }
  
  export class RemoveAll implements Action {
    readonly type = 'Remove All'
  }
  
  export type TodoActions = Add | RemoveAll
  
  // 引用时
  function todoReducer (
    action: TodoActions,
    state: ITodoState = { todos: [] }
  ): ITodoState {
    switch (action.type) {
      case 'Add': {
        return {
          // 此处可以直接识别 type 而识别 action 的类型
          todos: [...state.todos, action.payload]
        }
      }
    }
  }
  ```

- 可以设置对象属性的`readonly`

  ```typescript
  interface IPet {
    name: string
    age: number
    favoritePark?: string
  }
  
  type ReadonlyPet = {
    // 此处的 -? 表示可有可无的属性也必须 readonly
    readonly [K in keyof IPet]-?: IPet[K]
  }
  ```

- `type`和`interface`在某些情况下可以相互转换

  ```typescript
  type Eat = (food: string) => void
  type AnimalList = string[]
  
  // 以下可以相互转换
  interface IEat {
    (food: string): void
  }
  
  interface IAnimalList {
    [index: number]: string
  }
  
  // 两种 type 的属性都会存在
  type Cat = IPet & IFeline
  // 可转换为
  interface ICat extends IPet, IFeline {}
  
  // 但是 interface 只能为一个特定的类型而 type 可以为多种
  type PetType = IDog | ICat
  // 报错
  interface IPet extends PetType {}
  // class 也只能为一种类型，下面报错
  class Pet implements PetType {}
  
  // 同名 interface 可以合并而 type 不行
  interface Foo = { a: string }
  interface Foo = { b: string }
  // foo 拥有 a，b 属性
  let foo: Foo
  // 此 feacture 可以使用在库的使用中
  
  import * as $ from 'jQuery'
  interface JQuery { hideChildren(): JQuery }
  $.fn.extend({hideChildren: function() { }})
  $('test').hideChildren()
  ```


- 当不知道类型但又不想设为`any`时，可以设为`unknown`这样必须确定类型才能使用

  ```typescript
  interface IComment {
    date: Date
    message: string
  }
  
  interface IDataService {
    getData(): unknown
  }
  
  let service: IDataService
  
  const response = service.getData()
  // 下面语句报错，但为 any 时不会报
  response.a.b.c.d
  // 下面语句不报错
  if (typeof response === 'string') {
    console.log(response.toUpperCase())
  }
  ```

- 使用`extends`可以根据泛型的类型来做出一些判断

  ```typescript
  interface Book {
    id: string
    tableOfContents: string[]
  }
  
  interface Tv {
    id: number
    diagonal: number
  }
  
  interface IItemService {
    // 此处通过 extends 判断泛型类型来确定返回值类型
    getItem<T extends string | number>(id: T): T extends string ? Book: Tv
  }
  
  let itemService: IItemService
  
  const book = itemService.getItem("10")
  const tv = itemService.getItem(10)
  ```

- 可以通过函数参数的类型来确定函数返回值的类型，在一些库中非常有用

  ```typescript
  function generateId(seed: number) {
    return seed + 5
  }
  
  // 下面的语句表示根据返回值的类型来确定 lookupEntity 参数的类型
  type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any
  type Id = ReturnType<typeof generateId>
  
  lookupEntity(generateId(10))
  
  function lookupEntity(id: Id) {
    // query DB for entity by ID
  }
  
  // 比如我们想取到 promise 的 resolve 的值的 类型
  type UnpackPromise<T> = T extends Promise<infer K>[] ? K : any
  const arr = [Promise.resolve(true)]
  
  type ExpectedBoolean = UnpackPromise<typeof arr>
  ```

  