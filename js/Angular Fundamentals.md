# Angular Fundamentals

Learn how to build modern,maintainable, front-end applications

## angular cli

### build tools

- `ng serve`运行项目在浏览器上

- `ng lint`对代码进行 lint 规范

- `ng test`进行测试

- `ng build`打包代码

- `ng generate`用于创建 component 和 service，后一个参数可以加上放置路径，例如

  `ng generate service _services/constants`

## Component

### template

- 当想在 template 中的组件标签中插入模板时，需要在对应组件上加入`<ng-content></ng-content>`即可

- `ng-content`使用`select`属性添加选择器区分插入的位置，在插入的模板中通过选择器区分插入的位置，但是选择器不能使用 id

  ```html
  // 需要插入的组件 app-controls
  <div>
    <ng-content select='.above'></ng-content>
    <ng-content select='.blow'></ng-content>
  </div>
  
  // 插入模板
  <app-controls>
    <h1 class='above'>Content projection above</h1>
    <h1 class='blow'>Content projection blow</h1>
  </app-controls>
  ```

### lifeCycle

当 Angular 实例化组件类并渲染组件视图及其子视图时，组件实例的生命周期就开始了。生命周期一直伴随着变更检测，Angular 会检查数据绑定属性何时发生变化，并按需更新视图和组件实例。当 Angular 销毁组件实例并从 DOM 中移除它渲染的模板时，生命周期就结束了。当 Angular 在执行过程中创建、更新和销毁实例时，指令就有了类似的生命周期。

**构造函数**

- 在 Angular 设置好输入属性之后设置组件。构造函数应该只把初始局部变量设置为简单的值

**ngOnChanges**

- 在 direction 和 component 都能使用

- 当 Angular 设置或重新设置数据绑定的输入属性时响应。 该方法接受当前和上一属性值的 `SimpleChanges` 对象，所以你在这里执行的任何操作都会显著影响性能
- 在`ngOnInit()`之前以及所绑定的一个或多个输入属性的值发生变化时都会调用

**ngOnInit**

* 在 direction 和 component 都能使用

- 在 Angular 第一次显示数据绑定和设置指令/组件的输入属性之后，初始化指令/组件
- 在第一轮`ngOnChanges()`完成之后调用，只调用一次
- 只有*在构造完成之后*才会设置指令的数据绑定输入属性。如果要根据这些属性对指令进行初始化，请在运行 `ngOnInit()` 时设置它们

**ngDoCheck()**

- 在 direction 和 component 都能使用

- 检测，并在发生 Angular 无法或不愿意自己检测的变化时作出反应
- 紧跟在每次执行变更检测时的`ngOnChanges()`和首次执行变更检测时的`ngOnInit()`后调用

**ngAfterContentInit()**

- 只能在 component 使用，即使组件不 implement 它的 interface，也会直接调用

- 当 Angular 把外部内容投影进组件视图或指令所在视图之调用，也就是
- 第一次`ngDoCheck()`之后调用，只调用一次

**ngAfterContentChecked()**

- 只能在 component 使用

- 每当 Angular 检查完投影到组件或指令中的内容之后调用
- `ngAfterContentInit()`和每次`ngDoCheck()`之后调用

**ngAfterViewInit()**

- 只能在 component 使用

- 当 Angular 初始化完组件视图及其子视图或包含该指令的视图之后调用。
- 第一次`ngAfterContentChecked`之后调用，只调用一次

**ngAfterViewChecked()**

- 只能在 component 使用

- 当 Angular 初始化完组件视图和子视图或包含该指令的视图的变更检测之后调用。
- `ngAfterViewInit()`和每次`ngAfterContentChecked()`之后调用

**ngOnDestroy()**

- 在 direction 和 component 都能使用

- 每当 Angular 每次销毁指令/组件之前调用并清扫。在这里反订阅可观察对象和分离事件处理器以防内存泄漏
  - 取消订阅可观察对象和 DOM 事件。
  - 停止 interval 计时器。
  - 反注册该指令在全局或应用服务中注册过的所有回调。
- 在 Angular 销毁指令或组件之前调用

### 组件交互

通过输入型绑定把数据从父组件传到子组件，通过`@input`引入父组件传入的值

- `@input('master') masterName: string`设置别名

- 可以通过`setter`截听输入属性值的变化

  ```typescript
  export class NameChildComponent {
    private _name = '';
  
    @Input()
    set name(name: string) {
      this._name = (name && name.trim()) || '<no name set>';
    }
  
    get name(): string { return this._name; }
  }
  ```

- 当需要监视多个、交互式输入属性的时候，`ngOnChanges()`更合适

子组件暴露一个`EventEmitter`属性，当事件发生时，子组件利用该属性`emits(向上弹射)`事件。父组件绑定到这个事件属性，并在事件发生时作出回应。子组件的`EventEmitter`属性是一个输出属性，通常带有`@Output 装饰器` 

- `@Output() voted = new EventEmitter<boolean>`

- ```typescript
  // 把本地变量(#timer)放在(<countdown-timer>)标签中，用来代表子组件
  @Component({
    selector: 'app-countdown-parent-lv',
    template: `
    <h3>Countdown to Liftoff (via local variable)</h3>
    <button (click)="timer.start()">Start</button>
    <button (click)="timer.stop()">Stop</button>
    <div class="seconds">{{timer.seconds}}</div>
    <app-countdown-timer #timer></app-countdown-timer>
    `,
    styleUrls: ['../assets/demo.css']
  })
  ```

  使用**本地变量**方法是个简单便利的方法，但它也有局限性，因为父组件-子组件的连接必须全部在父组件的模板中进行。父组件本身的代码对子组件没有访问权。如果父组件的类需要读取子组件的属性值或调用子组件的方法，就不能使用**本地变量**方法

- 当父组件类需要这种访问时，可以把子组件作为`ViewChild`，注入到父组件里面

  

  ```typescript
  import { CountdownTimerComponent }  from './countdown-timer.component';
  
  export class CountdownViewChildParentComponent implements AfterViewInit {
  
    @ViewChild(CountdownTimerComponent)
    private timerComponent: CountdownTimerComponent;
  
    seconds() { return 0; }
  
    ngAfterViewInit() {
      // Redefine `seconds()` to get from the `CountdownTimerComponent.seconds` ...
      // but wait a tick first to avoid one-time devMode
      // unidirectional-data-flow-violation error
      setTimeout(() => this.seconds = () => this.timerComponent.seconds, 0);
    }
  
    start() { this.timerComponent.start(); }
    stop() { this.timerComponent.stop(); }
  }
  ```

  被注入的计时器组件只有在 Angular 显示了父组件视图之后才能访问

我们还可以使用 service 来进行组件之间的通信

## Service

service 一般用于组件通信，放置在父组件中的`providers`后，再在需要使用此 service 的组件中 import，然后在`constructor`函数中初始化即可

- 如果某个组件想用某个不在父组件上注册过的 service，可以在`@component`中加上`providers: [ServiceName]`来对单个组件进行注册

