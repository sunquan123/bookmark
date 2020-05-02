#                            Typescript

#### 1、安装typescript

```
npm install -g typescript
```

#### 2、tsc xx.ts命令将ts文件编译成js文件执行

#### 3、ts具有class与interface

我们尝试书写class和interface看看两者转换后的代我们可以发现class编译了大量代码，但是interface并没有转换成任何JS，当我们定义大量的class，并且还有着复杂的继承关系时，编译过后的代码体积将更加庞大。

##### 最佳实践

由于考虑到class和interface在TS中编译结果的不同，我们面对不同的场景，使用正确的约束数据类型的方式，对我们代码性能层面的提高就尤为重要。

###### **什么时候使用class**

当需要使用class时，我们通常会考虑三个方面

- 是否需要创建多个实例
- 是否需要使用继承
- 是否需要特定的单例对象

###### 什么时候使用interface

对于从服务器端api获取或者业务场景中模拟的数据，提倡使用interface去定义，这些数据通常是不会经常变化和调整的，这些数据可能仅仅只表示某些状态，或者是UI上的文本。

#### 4、变量定义

```ts
let notsure:any=1;//定义了一个变量
```

其中any类型允许你在编译时可选择地包含或移除类型检查。

let notsure:Object类似，但是`Object`类型的变量只是允许你给它赋任意值 - 但是却不能够在它上面调用任意的方法，即便它真的有这些方法

```ts
let notSure: any = 4;
notSure.ifItExists(); // okay, ifItExists might exist at runtime
notSure.toFixed(); // okay, toFixed exists (but the compiler doesn't check)

let prettySure: Object = 4;
prettySure.toFixed(); // Error: Property 'toFixed' doesn't exist on type 'Object'.
```

#### 5、类型断言

类型断言有两种形式。 其一是“尖括号”语法：

```ts
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
```

另一个为`as`语法：

```ts
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```

#### 6、为什么推荐使用let代替var?

 `var`声明可以在包含它的函数，模块，命名空间或全局作用域内部任何位置被访问，同一个作用域的同名变量共享同一个对象的引用。

当用`let`声明一个变量，它使用的是*词法作用域*或*块作用域*。 不同于使用 `var`声明的变量那样可以在包含它们的函数外访问，块作用域变量在包含它们的块或`for`循环之外是不能访问的。

#### 7、类

如果一个基类有构造器，则子类的构造器内必须先执行基类的构造器，再访问子类的成员变量。

```ts
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
}
class Snake extends Animal {
    constructor(name: string) {super(name);}
}
```

- 修饰符public任何地方都可以访问

- private修饰的成员指定了以该类为基准。当两个类结构相同时，如果有private成员存在，则ts认为两个对象类型不同，无法互相引用。

```ts
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino;
animal = employee; // 错误: Animal 与 Employee 不兼容.
```

- `protected`修饰符与 `private`修饰符的行为很相似，但 `protected`成员在派生类中仍然可以访问。

#### 8、函数

- 函数参数类型、返回值类型都可以指定，类似Java。 TypeScript能够根据返回语句自动推断出返回值类型，因此我们可以省略返回值类型。

```ts
function add(x: number, y: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number { return x + y; };
```

- TypeScript里传递给一个函数的参数个数必须与函数期望的参数个数一致。在TypeScript里我们可以在参数名旁使用 `?`实现可选参数的功能，但是可选参数必须写在必选参数后面。

```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}

let result1 = buildName("Bob");  // works correctly now
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");  // ah, just right
```

- 在TypeScript里，我们也可以为参数提供一个默认值当用户没有传递这个参数或传递的值是`undefined`时。 它们叫做有默认初始化值的参数。 让我们修改上例，把last name的默认值设置为`"Smith"`。

```ts
function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // works correctly now, returns "Bob Smith"
let result2 = buildName("Bob", undefined);       // still works, also returns "Bob Smith"
let result3 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result4 = buildName("Bob", "Adams");         // ah, just right
```

#### 9、枚举

##### 数字枚举

```ts
enum Direction {
    Up = 1,
    Down,
    Left,
    Right
}
```

如上，我们定义了一个数字枚举， `Up`使用初始化为 `1`。 其余的成员会从 `1`开始自动增长。 换句话说， `Direction.Up`的值为 `1`， `Down`为 `2`， `Left`为 `3`， `Right`为 `4`。

不使用初始化的数字枚举从0开始计数。

##### 字符串枚举

字符串枚举的概念很简单，但是有细微的 [运行时的差别](https://www.tslang.cn/docs/handbook/enums.html#enums-at-runtime)。 在一个字符串枚举里，每个成员都必须用字符串字面量，或另外一个字符串枚举成员进行初始化。

```ts
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
```

#### 10、迭代器

当一个对象实现了[`Symbol.iterator`](https://www.tslang.cn/docs/handbook/symbols.html#symboliterator)属性时，我们认为它是可迭代的。 一些内置的类型如 `Array`，`Map`，`Set`，`String`，`Int32Array`，`Uint32Array`等都已经实现了各自的`Symbol.iterator`。 对象上的 `Symbol.iterator`函数负责返回供迭代的值。

### `for..of` vs. `for..in` 语句

`for..of`和`for..in`均可迭代一个列表；但是用于迭代的值却不同，`for..in`迭代的是对象的 *键* 的列表，而`for..of`则迭代对象的键对应的值。

下面的例子展示了两者之间的区别：

```ts
let list = [4, 5, 6];

for (let i in list) {
    console.log(i); // "0", "1", "2",
}

for (let i of list) {
    console.log(i); // "4", "5", "6"
}
```

#### 11、模块

模块在其自身的作用域里执行，而不是在全局作用域里；这意味着定义在一个模块里的变量，函数，类等等在模块外部是不可见的，除非你明确地使用[`export`形式](https://www.tslang.cn/docs/handbook/modules.html#export)之一导出它们。 相反，如果想使用其它模块导出的变量，函数，类，接口等的时候，你必须要导入它们，可以使用 [`import`形式](https://www.tslang.cn/docs/handbook/modules.html#import)之一。

模块是自声明的；两个模块之间的关系是通过在文件级别上使用imports和exports建立的。

模块使用模块加载器去导入其它的模块。 在运行时，模块加载器的作用是在执行此模块代码前去查找并执行这个模块的所有依赖。 大家最熟知的JavaScript模块加载器是服务于Node.js的 [CommonJS](https://en.wikipedia.org/wiki/CommonJS)和服务于Web应用的[Require.js](http://requirejs.org/)。

TypeScript与ECMAScript 2015一样，任何包含顶级`import`或者`export`的文件都被当成一个模块。相反地，如果一个文件不带有顶级的`import`或者`export`声明，那么它的内容被视为全局可见的（因此对模块也是可见的）。

#### 12、接口规则

如果定义了一个函数的输入类型-->接口，在调用方传入一个对象字面量的时候会进行额外的类型检查：

如果对象字面量包含了接口不存在的属性时，ts会检查并报错。

```
interface Person{
    name:string;
    age:number;
}
function draw(person:Person){
    console.log(person.name);
}
let pic=draw({name:"mike",age:12,sex:"asdas"});
//类型“{ name: string; age: number; sex: string; }”的参数不能赋给类型“Person”的参数。对象文字可以只指定已知属性，并且“sex”不在类型“Person”中。
```

Tips:如何避开额外类型检查？

- ```ts
  let pic=draw({name:"mike",age:12,sex:"asdas"} as **Person**);//使用as类型断言
  ```

- ```ts
  interface Person{
      name:string;
      age:number;
      [propName:string]:any;
  }//添加一个字符串索引签名
  ```

- ```ts
  let temp={name:"mike",age:12,sex:"saas"};
  let pic2=draw(temp);//将这个对象赋值给一个另一个变量
  ```

函数类型接口

可以不要求具体函数的参数名与接口一致

```ts
interface Write{
    (paper:string,pen:string):boolean;
}
let myWrite:Write;
myWrite=function(whitePaper:string,blackPen:string):boolean{
return whitePaper.length>-1;
};
```

索引类型接口

适用于dictionary模式的数据

  ```ts
interface MembershipArray{
     readonly [id:number]:Person;
     }
let membershipArray:MembershipArray=[{name:"mike",age:12},{name:"kalo",age:13}];
console.log(membershipArray[0]);
  ```

 类接口

