**最近看了许多关于 Angular 的文章，感觉是时候学习了，但是学习之前得先学习 TypeScript，正好以后准备用 TypeScript 写 node，一举两得。**

## 基础类型

* number, string, boolean

* 数组

  ```typescript
  let list: number[] = [1, 2, 3] // 推荐这种写法
  let list: Array<number> = [1, 2, 3]
  const arr: (number | string)[] = [1, '2']
  ```

* 元祖（Tuple）

  ```typescript
  // 注意下面的元祖只能有两个元素，多了的访问会报错
  let x: [string, number] // 也就是定义了多种类型元素的数组
  // 元组一般应用场景是 csv 或 excel 文件的格式
  ```

* 枚举

  ~~~typescript
  // 有以下 js 代码
  const Status = {
    OFFLINE: 0,
    ONLINE: 1,
    DELETED: 2
  }
  
  function getResult(status) {
    if (status === Status.OFFLINE) {
      return 'offline'
    } else if (status === Status.ONLINE) {
      return 'online'
    } else if (status === Status.DELETED) {
      return 'deleted'
    }
    return 'error'
  }
  
  // 在 ts 中可以用枚举类型进行编写
  // 它的值是它的索引值
  enum Status {
    OFFLINE,
    ONLINE,
    // ONlINE = 4, 可以直接设置索引值，后面的值会直接 +1
    DELETED
  }
  
  console.log(status[0]) // 打印出 'OFFLINE'
  ~~~

* any

  ```typescript
  // 在重构 js代码 时很有用
  let notSure: any = 4
  notSure = 'may'
  let list: any[] = [1, true, 'free']
  ```

* void

  ```typescript
  // 和 any 相反，表示没有任何类型，一般用在函数中
  function warnUser(): void {
    console.log('This is my word')
  }
  ```

* null，undefined

  ```typescript
  // 可以这样设置，但在 strict模式 下会报错
  let num: number = 3
  num = null
  ```

* never

  ```typescript
  // 它是任何类型的子类型，一般用在函数返回值为报错或是无法结束的
  function error(message: string):never  {
    throw new Error(message)
  }
  
  function infinite():never {
    while(true) {}
  }
  ```

* object

* 类型注解(type annotation)

  ```typescript
  // 就是我们告诉TS变量是什么
  // 如果 TS 能够自动分析变量类型，我们就什么也不需要做了
  // 如果 TS 无法分析变量类型的话，我们就需要使用类型注解
  let count:number
  count = 123
  
  // 如果不加变量注释，TS 就不会知道变量的类型，需要自己加
  function getTotal(firstNumber: number, secondNumber: number) {
      return firstNumber + secondNumber
  }
  ```

* 类型推断

  ```typescript
  // 此时 TS 能够推断出 num 为 number 类型
  let num = 1
  // 在之后强制定义类型，告诉电脑我知道自己在干什么
  let someValue: any = 'this is a string'
  // 以下两种写法一样
  let strLength: number = (<string>someValue).length
  // jsx 只支持下一种
  let strLength: number = (someValue as string).length
  ```

* 类型别名

  ```typescript
  type User = {name: string, age: number}
  const objectArr: User[] = [{name: 'dell', age: 28}
  // TS 不会强制你的类的每一项都是类的实例
  class Teacher {
    name: string;
    age: nubmer;
  }
  const objectArr: Teacher[] = [{name: 'dell', age: 28}, new Teacher()]
  ```



## 接口

### interface

 ```typescript
// 只要你声明的这个属性有这个属性且类型对了，就不会报错
interface LabelledValue {
  label: string
}
// 不会报错
let myObj = { size: 10, label: 'Size 10 Object' }

interface Person {
  name: string;
  age?: number;
  say(): string
}
// 类应用 interface
class User implements Person {
  greeting = 'dell'
}

// interface 和 type 相类似，但并不完全一致
 ```

### 可选属性

```typescript
interface Square {
  // 表示这个属性可以有也可以没有
  color?: string
  area: num
}
// 使用场景
function createSquare(config: SquareConfig): Square {
  let newSquare = { color: 'white', area: 100 }
  if (config.color) {
    newSquare.color = config.color
  }
  if (config.width) {
    newSquare.area = config.width * config.width
  }
  return newSquare
}

let mySquare = createSquare(config: { color: 'block'})
```

### 只读属性

```typescript
// 保证属性不能被修改
interface Point {
  readonly x: number
  readonly y: number
}
// 不能对数组的项进行修改，甚至不能用方法
let ro: ReadonlyArray<number> = [1, 2, 3]
/* 注意 number[] 和 ReadonlyArray<number> 不是同一类型，
 * 不能互相转换，但是可以用类型断言
*/
```

### 多个属性

```typescript
interface Person {
  name: string;
  age?: number;
  // 此时可以加上任意属性和属性值
  [propName: string]: any
}

// 不报错
getPersonName({
  name: "dell",
  sex: 'male'
})
```

### 函数类型

```typescript
// 函数类型定义参数和返回值
interface SearchFunc {
  (source: string, subString: string): boolean
}

let mySearch: SearchFunc
// 注意参数名不用保持一致，参数也可以不用定义类型，它会进行类型推断
mySearch = function(source: string, subString: string):boolean {
  let result = source.search(subString)
  return result > -1
}

// 结构函数参数会导致语法问题，此时应该如下处理
function add({first, second}: {first: number, second: number}): number {
	return first + second
}

// 有时一些内置函数会返回没有类型注释的值，比如 JSON.parse 此时需要自己注释
interface Person {
  name: string;
  age: number;
}
const rawData = '{"name": "dell"}'
const newData: Person = JSON.parse(rawData)
```

### 可索引类型

```typescript
interface StringArray {
  [index: number]: string
}

let myArray: StringArray
myArray = ['Bob', 'Fred']
let myStr:string = myArray[0]
```

## 类

```typescript
class Greeter {
  greeting: string
  
  constructor(message: string) {
    this.greeting = message
  }
  
  greet() {
    return 'Hello, ' + this.greeting
  }
}

let greeter = newGreeter('news')
```

### 继承

```typescript
class Animal {
  name: string
  
  constructor (name: string) {
    this.name = name
  }
  
  move(distance: number = 0) {
    console.log(`${this.name} mobed ${distance}m`)
  }
}

class snake extends Animal {
  constructor(name: string) {
		super(name)
  }
  move(distance:number = 5) {
    console.log('Slithering...')
    super.move(distance)
  }
}
```

### 私有域

```typescript
// 当没有加上修饰符时默认是 public 在类的内部外部都可使用
class Person {
  // 此时外部访问不到，但是子类可以访问
  private name: string
  // 此时外部和子类都不可访问
  protected age: number
  // 此时这个属性只读
  readonly gender: string
  // 此时不能实例化这个类
  protected constructor(name: string) {
    this.name = name
  }
}

class Employee extends Person {
  private department: string
  
  constructor(name: string, departmeent: string) {
    super(name)
    this.department = department
  }
  // 此时报错，因为 age 是 protect 的属性
  getElevatorPitch() {
    return `Hello, my age is ${this.age}`
  }
}

// 如果两个类拥有相同的 private 属性，则不能兼容
class Animal {
  // 可以直接在参数前加上访问修饰符，尽量不用参数属性
  constructor(private name: string) {
    this.name = name
  }
}
class Employee {
  private name: string
  constructor(name: string) {
    this.name = name
  }
}
Animal = Employee // 报错
```

### 存取器

```typescript
let passcode = 'secret passcode'

class Employee {
  private _fullName: string
  get fullName():string {
    return this._fullName
  }
  
  set fullName(newName: string) {
    if (passcode && passcode === 'secret passcode') {
      this._fullName = newName
    } else {
      console.log('Error: Unauthorized update of employee!')
    }
  }
}

// 单例模式
class Demo {
  private static instance: Demo
  // 将 constructor 变为 private，则在外部不能使用 new 来直接调用类
  private constructor() {}
  static getInstance() {
    if (!this.instance) {
      this.instance = new Demo()
    }
    return this.instance
  }
}

const demo1 = Demo.getInstance()
```

### 静态属性

```typescript
class Grid {
  static origin = {x:0, y: 0}
  scale: number
}
```

### 抽象类

```typescript
// 抽象类通常是作为其他类的基类使用，一般不能被直接实例化
abstract class Department {
  name: string
  
  contructor(name: string) {
    this.name = name
  }
  
  printName(): void {
    consle.log('Department name ' + this.name)
  }
  
  // 子类必须自己实现抽象方法
  abstract printMeeting(): void
}

class AccountingDepartment extends Department {
  constructor() {
    super('Accounting ad Auditing')
  }
  
  printMeeting():void {
    console.log('The Accounting Department meets each Monday at 10 am')
  }

  genterateReports(): void {
    console.log('Generating accounting reports...')
  }
}

let department: Department = new AccountingDepartment()
// 此处因为抽象类中没有这个方法直接调用会报错
department.genterateReports() // 报错
```

### 类的高级技巧

```typescript
class Greeter {
  constructor(message?: string) {
    this.greeting = message
  }
}
// typeof 可以改变静态属性
let greeterMaker: typeof Greeter = Greeter
greeterMaker.standardGreeting = 'Hey there'
```

### 接口继承

```typescript
class Point {
  x: number
  y: number
}

interface Poinit3d extends Poing {
  z: number
}

let point3d: Point3d = {x: 1, y: 2, z: 3}
```

## 函数

```typescript
function buildName(firstName: string, lastName?: sring) {
  return firstName + ' ' + lastName
}

let result1 = buildName('Bob')
let result2 = buildName('Bob', 'Adams', 'Sr.')
let result3 = buildName('Bob', 'Adams')
let result4 = buildName(undefined, 'Adams')
```

### this

```typescript
interface Card {
  suit: string
  card: number
}

interface Deck {
  suits: string[]
  cards: number[]
  // 这样就定义了 this，这个 this 不是一个真正的参数
  // 设定 this: void 表示不能访问 this
  // 如果想访问 this 可以使用箭头函数
  createCardPicker(this: Deck): () => Card
}

let deck: Deck = {
  suits: ['hearts', 'spades', 'clubs', 'diamonds'],
  cards: Array(52),
  createCardPicker: function () {
    return () => {
      let pickedCard = Math.floor(Math.random() * 52)
      let pickedSuit = Math.floor(pickedCard / 13)
      
      return {
        suit: this.suits[pickedSuit],
        card: pickedCard % 13
      }
    }
  }
}
```

### 重载

```typescript
// 以下是同一函数重载的声明，当传入参数不同时的声明
// 注意要将最精确的放在前面
function pickCard({suit: string; card: number}[]):number
function pickCard(number):{suit: string; card: number}

// interface 也可实现函数重载
interface JQuery {
  (readyFunc: () => void): void
  (selector: string): JqueryIntance
}
```

## 泛型

```typescript
// 泛型 generic 泛指的类型
// <T> 使得函数的参数和返回值的类型一致
function identity<T>(arg: T): T {
  return arg
}
let output = identity<string>('myString')
// 自动推断类型
let output = identity('myString')

function loggingIdentity<T>(arg: T[]): T[] {
  consoel.log(arg.length)
}

// 泛型还能定义两个
function join<T, P>(first: T, second: P) {
  return `${first}${second}`
}
join<number, string>(1， '1')

// 在类中使用泛型
interface Item {
  name: string
}
class DataManager<T extends Item> {
  constructor(private data: T[]) {}
  getItem(index: number): string {
    return this.data[index].name
  }
}

// 用于限制泛型只能为 number 或 string 类型
class DataManager<T extends number | string> {
  // ...
}

const data = new DataManager([{name: 'dell'}])
data.getItem(0)


const func: <T>(param: T) => T = <T>(param: T) => {
  return param
}

// keyof 语法
// 用于推断出对象中对应 key 的值的类型
interface Person {
  name: string;
  age: number;
  gender: string;
}

class Teacher {
  constructor(private info: Person) {}
  // getInfo(key: string) {
    // 注意这里的 key 值无法保证为 Person 的属性，所以 ts 会报错
    // 可以用 if key === 来进行判断，但 ts 无法判断返回值是为 string 还是 number 还是 undefined
    // return this.info[key]
  // }
  
  // 此时可以完美识别 Person 的属性
  getInfo<T extends keyof Person>(key: T):Person[T] {
    return this.info[key]
  }
}

const teacher = new Teacher({
  name: 'dell',
  age: 18,
  gender: 'male'
})

const test = teacher.getInfo('name')
```

### 泛型接口

```typescript
// 推荐方式
interface GenericIdentityFn<T> {
  (arg: T): T
}
// 也可以设为各种字母
let myIdentity: GenericIdentityFn<number> = identity
```

### 泛型类

```typescript
class GenericNumber<T> {
  zeroValue: T
  add: (x: T, y: T) => T
}

let myGenericNumber = new GenericNumber<number>()
myGenericNumber.zeroValue = 0
myGenericNumber.add = function(x, y) {
  return x + y
}
```

### 泛型约束

```typescript
interface lengthwise {
  length: number
}
function loggingIdentity<T extends lengthwise>(arg: T): T {
  console.log(arg.length)
  return arg
}
// K 表示存在 T 的属性中
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key]
}
// 工厂函数
function create<T>(c: {new(): T}): T {
  return new c()
}

class BeeKeeper {
  hasMask: boolean
}
```

## 高级类型

### 交叉类型

```typescript
// 几种类型之和，可以用于 assign 方法
function extend<T, U>(first: T, second: U): T & U {
  let result = {} as T & U
  for (let id in first) {
    result[id] = first[id] // 报错
    result[id] = first[id] as any // 通过
  }
  for (let id in second) {
    if (!result.hasOwnProperty(id)) {
      result[id] = second[id] // 报错
    }
  }
  return result
}

class Person {
  constructor(public name: string) {
		// ..
  }
}

interface Loggable {
  log(): void
}

class ConsoleLogger implements Loggable {
  log() {
    // ..
  }
}

var jim = extend(new Person('jim'), new ConsoleLogger())
jim.name // 可以调用
jim.log() // 可以调用
```

### 联合类型

```typescript 
// 几种类型之一
function padLeft(value: string, padding: string | number) {
  if (typeof padding === 'number') {
    return Array(padding + 1).join(' ') + value
  }
  if (typeof padding === 'string') {
    return padding + value
  }
  throw new Error(`Expected string or number got ${padding}`)
}

padLeft('Hello world', true) // 报错

interface Bird {
  fly()
  layEggs()
}

interface Fish {
  swim()
  layEggs()
}

function getSmallPet(): Fish | Bird {
  // ..
}
// 只能使用共有成员
let pet = getSmallPet()
pet.layEggs() // 通过
pet.swim() // 报错
```

### 类型保护

```typescript
// 联合类型才会应用类型保护
interface Bird {
  fly()
  layEggs()
}

interface Fish {
  swim()
  layEggs()
}

function getSmallPet(): Fish | Bird {
  // ..
}
// 类型保护的一种：类型未识
function isFish(pet: Fish | Bird):pet is Fish {
  return (pet as Fish).swim !== undefined
}

if (isFish(pet)) {
  pet.swim()
} else {
  pet.fly()
}

// instanceof 判断
// 如果是实例可以使用 instanceof 判断
if（pet instanceof Bird) {
  pet.fly()
}

// 类型断言
function trainAnial(animal: Bird | Dog) {
  if (animal.fly) {
    (animal as Bird).sing()
  } else {
    (animal as Dog).bark()
  }
}

// in 语法做类型保护，可代替上面的 isFish 函数
function trainAnial(animal: Bird | Dog) {
  if ('fly' in animal) {
    animal.sing()
  } else {
    animal.bark()
  }
}

// typeof 做类型保护
// 如果是基本类型可以直接用 if typeof 判断之后调用方法
function add(first: string | number, second: string | number) {
  if (typeof first === 'string' || typeof second === 'string') {
    return `${first}${second}`
  }
  return first + second
}
```

### 可以为 null 的类型

```typescript
function f(x: number, y?:number) {
  return x + (y || 0)
}

f(1, undefined) // 通过
f(1, null) // 报错，只能为 undefined 和 number

function broken(name: string | null): string {
  function postfix(epither: string) {
    // 直接告诉编辑器 name 不为 null
    return name!.chart(0) +  '. the ' + epither
  }
  // 这里避免了为 null
  name = name || 'Bob'
  return postfix(name)
}
```

### 字符串字面量类型

```typescript
type Easing = 'ease-in' | 'ease-out' | 'ease-in-out'

class UIElement {
  animate(dx: number, dy: nuber, easing: Easing) {
    if (easing === 'ease-in') {
    // ..
    } else if (easing === 'ease-out') {
    } else if (easing === 'ease-in-out') {
    } else {
      // 这里执行不到
    }
  }
}

let button = new UIElement()
button.animate(0, 0, 'ease-in')
button.animate(0, 0, 'uneasy') // 报错
```

### 命名空间

```typescript
// 以 /// 开头来表示 namespace 所依赖的 namespace
///<reference path='./components.ts' />

namespace name {
  //...
  export property var a = '111'
}
    
console.log(name.a) /// '111'
```

## 装饰器

### 类装饰器

```typescript
// 装饰器本身就是一个函数，它会在类创建完成之后立即对类进行装饰
// 而不是在创建实例之后进行修饰
// 它能拿到类的构造函数然后归构造函数进行一些修改
function decorator(constructor) {
  constructor.prototype.getName = () => console.log('kankan')
}
@decorator
class Test {}

// 先装饰的装饰器会后被执行
@decorator1
@decorator2
class Test {}

// 如果想分情况使用装饰器，可以通过一个函数返回一个装饰器
function testDecorator() {
  return function(constructor: any) {
    constructor.prototype.getName = () => {
      console.log('dell')
    }
  }
}
@testDecorator()
class Test {}

// 标准写法
// 有 new 的意思是这是一个构造函数
function testDecorator () {
  return function <T extends new (...args: any[]) => {}>(constructor: T) {
    return class extends constructor {
      getName() {
        return this.name
      }
    }
  }
}

const Test = testDecorator()(
  class {
    name: string
    constructor(name: string) {
      this.name = name
    }
  }
)

const test = new Test('dell')
test.getName()
```

### 方法装饰器

```typescript
// 装饰器对应的参数和 Object.defineProperty(obj, prop, descriptor) 非常类似
// 普通方法，target 对应的是类的 prototype， key 是装饰的方法的名字
// 如果是静态方法，target 对应的是类的构造函数
// descriptor 是用来控制函数的属性特性的
// 它会等类创建完成后立即进行修改
function getNameDecorator(target: any, key: string, descriptor: PropertyDescriptor) {
  descriptor.writable = true
}

class Test {
  name: string
  constructor (name: string) {
    this.name = name
  }
  @getNameDecorator
  getName() {
    return this.name
  }
}
```

### 访问器的装饰器

```typescript
// 装饰器对应的参数和 Object.defineProperty(obj, prop, descriptor) 非常类似
// target 对应的是类的 prototype， key 是装饰的方法的名字
// descriptor 是用来控制函数的属性特性的
// get 或 set 不能用相同的装饰器
// 它会等类创建完成后立即进行修改
function visitDecorator(target: any, key: string, descriptor: PropertyDescriptor) {
  descriptor.writable = true
}

class Test {
  private _name: string
  constructor (name: string) {
    this._name = name
  }

  get name {
    return this._name
  }
  
  @visitDecorator
  set name (name: string) {
    this._name = name
  }
}
```

### 属性装饰器

```typescript
// target 对应的是类的 prototype， key 是装饰的方法的名字
// 返回值可以定义属性的特性
// 它会等类创建完成后立即进行修改
function nameDecorator(target: any, key: string): any {
  const descriptor: PropertyDescriptor = {
    writable: false
  }
  return descriptor
}

class Test {
  @nameDecorator
  name = 'Dell'
}
```

### 参数装饰器

```typescript
// target 对应的是类的 prototype， key 是装饰的方法的名字, paramIndex 为参数所在的名字
// 返回值可以定义属性的特性
// 它会等类创建完成后立即进行修改
function paramDecorator(target: any, method: string, paramIndex: number): any {
  const descriptor: PropertyDescriptor = {
    writable: fasle
  }
  return descriptor
}

class Test {
  getInfo(@paramDecorator name: string, age: number) {
    console.log(name, age)
  }
}
```





