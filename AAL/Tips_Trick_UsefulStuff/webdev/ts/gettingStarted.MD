# Installation

https://www.typescriptlang.org/download

* Run `sudo npm install typescript -g` for a global installation or `npm install typescript --save-dev` for a project installation

# Ressources

* https://www.youtube.com/watch?v=BwuLxPH8IDs
* https://www.typescriptlang.org/docs/handbook/intro.html

# Typrescript.lang notes

## Ts for js Programmers

* TS knows the types of JS objects and often assigns the correct types (**Types by Inference**)
* Dynamic Programming construct in js makes type inference harder. For example:

```ts
const user = {
    name: "Harpun".
    id: 0
};
```
Better approach in Typescript would be to use `interface` declaration

```js
interface User {
    name: string;
    id: number;
}
// Now declare a user like this
const user: User = {
    name: "Hayes",
    id: 0,
};
```
Another approach would be to use the interface declaration with classejs classes
```ts
interface User {
    name: string;
    id: number;
}

class UserAcc {
    name: string;
    id: number;

    constructor(name: string, id: number) {
        this.name = name;
        this.id = id;
    }
}

const user: User = new UserAccount("Murphy", 1);
```

Or you can use interfaces to annotate functions and their return values

```ts
function getAdminsUser(): User{
    //...
}

function deleteUser(user: User){
//...
}
```


# Understanding Typescript 2022: Udemy Course

### List of Types in typescript

javascript "types"

* boolean, 
* bigint
* null
* number
* string
* symbol
* undefined

typescript extends this types

* any (allow all types)
* unknown (type must be declared by the one using it)
* never 
* void (Function that returns unkown or nothing)

## Ts type

Type is similiar to interface intended for building types (the later should be prefered in most cases). In typescript you can create new types by combining primitives

* Type can be used to create unions 

# Misc
# Resource: Typescript Course Begginers (YT)
## Implicit Typecasts

* `const input2 = document.getElementById("num2")! as HTMLInputElement;` &Rightarrow; "!" tells ts that this item will never be Nil and typecasts it to an HTMLInput ELement  (Implicit Typecasts)
* adding `+` in front of a parameter given to a function silences the linter warning (string not compatible with numeric)
## Ch1. Basics & Basic Types
### Core Types:
These are the types, js and ts have in common 

* Number: Ts (Js) makes no difference between float or integer
* String: Using Strings with backticks can be used for string interpolation in modern js
* boolean: Only supports true and false
* Objects: Are comparable to pythons dictionaries, i.e. they are key value pairs that can be nested infinitly 
* Arrays: Can store values of the same type or different types if you use `any[]` for initialization (However this has the downside of no type checking)

### Typescript specific types

* tuples: is a fixed size array
* enums: Is a custom type which assings labels to numbers and are great if you need humanly readable labels
* Any: Allows any type but is strongly discouraged because it takes away the advantages of typescript
* Union  types: we can give multiple types to one parameter in a function like `function  add(n1: number |string, n2: number | string)...` using the pipe operator `|` we can specify as many types as we need.
* Literal types: you can give them to a function parameter and these are the only types allowed as parameters, for example `function  add(n1: number |string, n2: number | string, resultConversion: 'as-number' | 'as-text')...`

### Type aliases

With type aliases you can create your own types using the `type` operator. Typically in conjunction with union types `type Combinable = number | string`

### Functions

* Functions can have return types. If you do not give a type it is inferred by ts. 
* Functions without a return statement have the return type `void`
* If you want to return a function use the type `Function`
* The arrow notation is used if you want too create a Function type with specific parameters and return types (So called **function types**), e.g. `let combineValues: (a: number, b: number) => number;`
* Typescript can also take functions as input parameter for a function, e.g. `function addAndHandle(n1: number, n2: number, cb: (num: number) => void){...}`

### Extra Types

* `Unknown`: Is used if we do not know what the user will enter. It is sligtly different to `any` because the following will **not** work with unkown only with any, because any is the **most flexible** type in ts which disables all type checking. `Unkown` is more restrictive, because we have to check the type if we assign it to another variable.
    ```ts
    let userInput: unknown;
    let userName: string;

    userInput = 5;
    userInput = 'Max'
    userName = userInput;
    ```
* Thus `unkown` is the better type over `any` if we do not know the type of a variable at compilation time (although union types are more preferable than `unknown`)
* `never`: Another type indicating functions do not return anything, consider the following code block:

    ```ts
    function generateError(message: string, code: number): never{
        throw {message: message, errorCode: code};
    }

    generateError('An error occured!', 500);
    ```

* This function never returns anything, because it cannot return anything because it will throw an error before ending the function and thus ending the script.

### Further Ressources

* https://www.typescriptlang.org/docs/handbook/2/everyday-types.html

## Ch2. Typescript Compiler and it's options

* **Watchmode**: Using the `-w` flag in the compiler will start in watchmode. Everytime the watched file changes it will recompile
* **Compiline multiple files**: We need to create a tsconfig.json file by running `tsc --init`. Then we cann run `tsc` without a specific file and all files will be compiled. Note that the tsconfig file offers a lot of other option which will be explained later on. 
* **Including/Exlcluding Files**: Add `exclude: []` as object to the json file outside `compilerOptions{...}`. Usually you want to always exclude "node_modules". However this is the default. `include: []` is the exact opposite to include. Only the files/folders listed here are then compiled. There is also a `file: []` option. However it has the functionality as `include` only that no folders are allowed (May only be feasible for small projects).
* **compilerOptions**: Setting the options for the compiler which also can be set by flags and are offered by `tsc --help`
  * **target**: Which javascript language specification should be used. Newer versions may be better performant but the compatibility with older browser may be not supported 
  * **lib**: Specify which libraries to use. If not set some defaults are assumed, e.g. all dom actions. 
  * **allowJs** and **checkJs**: Not usable in a pure ts project. But can be useful in pure js or mixed projects because it enables some ts checks for js
  * **sourceMap**: This will create `file.js.map` files while compiling. Ultimately this allows to debug also the ts files in the webdeveloper console in the debugger. 
  * **outDir** and **rootDir**: With **rootDir** We can specifcy folders where the source files are lying, e.g. `src/` and where the output (compilation results) are lying, e.g. `dist/`
  * **removeComments**: Will remove all comments in the output files. Useful if you want to make files smaller. 
  * **noEmit**: If this is true, no output files will be created
  * **downlevelIteration**: If this is true in some niche cases loops behave different between older and new es versions. If this is the case only then this option should be activated
  * **noEmitOnError**: Boolean. If set to true, if the compilation fails for any fail no overall output will be created.
  * **strict**: Activates all options below **strict** in the tsconfig file (All are booleans).  
    * **noImplicitAny**: Ensure that now implicit any type is used somewhere in the code, meaning if we forgot too give a type to a parameter in a function and the type cannot be infered we talk about an implicit any
    * **strictNullChecks**: Remember the button is null problem when using `document.querySelector(...)`. Strict null checks complains when we do not expliticy mark values that could be null on runtime. 
    * **strictFunctionTypes**: Niche parameter, catching some errors related to classes and inheritance
    * **strictBindCallApply**: If we use one of those add type checking to this functions
  * Everything under **Additional Checks** helps ensuring code quality, similiar to what a linter would do. For example if we define a variable which is nevers used.
    * **UnusedLocalVariables**: If inside a function a unused variable is created ts will complain.
    * **noImplicitReturns**: If a function does not return anything in all cases (e.g. returning from an if statement) the compiler will complain
* **Debugging with vscode**: Use the extension Debugger for Chrome/Firefox. Then you can set breakpoints inside ts files. Note that the sourceMap option in tsconfig should be enabled

### Ressources for Compiler 

* Tsconfig Docs https://www.typescriptlang.org/docs/handbook/tsconfig-json.html
* Compiler Config Docs https://www.typescriptlang.org/docs/handbook/compiler-options.html
* VS Code TS Debugging https://code.visualstudio.com/docs/typescript/typescript-debugging

## Ch3. Next-gen JS & TS

* only the keywords `const` and `let` should be used to assign variables. Using `const` we can only assign a variable once. `var` is available but should not be used anymore because with var js only knows global or function spaces. Whereas let also knows block spaces (e.g. defined inside an if statement).
* **Arrow functions**: Allow us to write functions more precise like: 
    ```ts
    const add = (a: number, b: number) => a + b; 
    ```
* Note that this syntax only works for one liners, see the .ts file for more examples
* Compared to older js version we can also assign **default values** to **function parameter** the same way we would do in python.
* **Spread Operator**: Use the spread operator `...` in front of iterables  to unpack them. For example this can be useful if we want to combine two arrays. Note that this can also be used for objects. 
* **Rest Parameters**: This somewhat similiar to args in python &Rightarrow; Adding variable parameters to functions using the spread operator allows variable length parameters in functions. This also works with tuples
* **Array and object destructuring**: This can be used if we want to unpack an array into different variables, e.g. `const [hobby1, hobby2, ...remaining] = hobbies;`. The same can be applied to objects 

##### Ressources and Links

## Ch4. Classes and Interfaces
### Basics

* **OOP**: Can help to resemble natural structures thus making it easier to understand and use them in your code. E.g. Product List, Product, Shopping card  may be object in an online shop
* **Classes & Instances**: Blueprints for objects (How should an object look like) &Rightarrow; An object created from class is called **instance** of a class. Classes make creation of same objects way easier
* Classes and instances are constructed like:

```ts
// Class "Blueprint"
class Departement {
    name: string ;

    constructor(n: string) { 
        this.name = n;
    }
}
// Instance of class Departement
const accounting = new Departement("Accounting");

console.log(accounting)
```

* Note that below ES6 the compiled js for this code uses constructor functions which looks quite different than the class syntax
* `this` can be a tricky keyword from time to time. As rule of thumb you can memorize, this will usually refer to the object calling a class method and not to an object a reference was done to (E.g. when copying a function of an isntance). If you add the `this` keyword to a method the compiler can catch some errors of this kind.
* Using the `private` keyword you can make propertys (property = variable of a class) private so that they cannot be accessed outside of the functions. `public` can also be used in front of variables. Buts its the default. Thus there is no need to use it. private and public keywords are only available in really modern js
* To declare propertys, there is also a shorthand notation like:

```ts
class Departement {
    // private id: string;
    // private name: string ;
    private employees: string[] = [];

    constructor(private id: string, public name: string) { 
        //this.name = n;
    }
```

* Using the keyword `readonly` after private or public you can define that the value of a property can never be changed after initilalization. (This only exists in ts)

### OOP Features

* We can let a class inherit from another class by using the `extends` keyword, e.g. `class ITDepartement extends Departement {...}`. Note ts only supports single inheritance.
* With a constructor in the child class we need to use the keyword `super` to properly initialize the constructor. `super` must be called at first place in the constructor. 
* If we create a class method of the same name as from a parent class we ovverride it. 
* `protected` is another modifier for variables that allows to change variables outside of the parent class (e.g. in a child class) but not from outside the classes. 
* `getters` and `setters`: Used to set and get values for privates properties, e.g.:

```ts
class AccountingDepartement extends Departement {
    // We an only use the shorthand initilization. But this is to make clear that we have to use super before all other this calls.
    private _lastReport: string;

    get lastReport(){
        if(this._lastReport){
            return this._lastReport;
        }
        throw new Error('No report found.')
        //return "Test";
    }

    set lastReport(value: string){
        if(!value){
            throw  new Error('Please pass in a valid value!')
        }
        this._lastReport = value
    }
```

* **static** propertoies and classes: Usually for utility functions or global constants. E.g. the `Math` class defined by vanilla js is such an example. They are created with the `static` keyword. Note that you cannot access them with the `this` keyword inside your class. 
* **Abstract Classes**: Way of "forcing" a developer that a method is available (and you cannot provide this method in the parent class). Are defined like this `abstract describe(this: Departement): void;`. With an abstract method you only define how a method should look like and not what it should do. Note that a class that contains an abstract method must also be defined as abstract like `abstract class ClassName` &Rightarrow; Abstract classes cannot be instantiated themselves only their child classes. 
* **Private Constructor** (Or singleton pattern): A singleton is a class that allows only one instance to be created of that class and all other variables are references to this instance. See AccountingDepartmenet in classes.ts as example. In short we need to mark the constructor as `private` and at a 
`static` instance / getInstance property/method. 

### Interfaces

In its simplest form describe the structure of an object. This only exists in typescript not vanilla js. Convention is the same as classes that the **first letter** should be **uppercase**

* An interface is created with `interface Person {...}` it can hold definitions of variables and functions:

```ts
interface Person {
    name: string;
    age: number;

    greet(phrase: string): void;
}
```

* Interfaces can also be implemented in classes. This teels ts that this class needs to adhere to the structure of the interface. It is used like this: `class ClassName implements InterfaceName{...}`
* Interfaces also can have the `readonly` identifier. 
* Inheritance is also available for interfaces using the `extends` keyword. 
* Interfaces can also be used to define the structure of a function. 
* Interface properties and methods can  be made optional by adding a question mark behind its name. (The same works for classes) 


See ch4*/app.ts for examples about interfaces

#### Additional Ressources
* **JS Classes**: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes
* **TS Interfaces**: https://www.typescriptlang.org/docs/handbook/2/objects.html
## Ch5. Advanced Types

* **Intersection types** allow us to combine multiple types to one type that contains all variables, e.g. `type ElevatedEmployee = Admin & Employee;`. Something similiar can be achieved  by extending an interface by multiple interfaces. 
* **Type guards**: In general a type guard is to protect a programm from failing if you allow union types (e.g. strings and numbers) and catch the error using if. 
* Using the `in` operator we can create more complex type guards that fail `if(objectExits)` checks:

```ts

function printEmployeeInfo(emp: UnknownEmployee){
    console.log('Name: ' + emp.name);
    if('privileges' in emp){
        console.log('Privileges: ' + emp.privileges);
    }

    if('startDate' in emp){
        console.log('Privileges: ' + emp.startDate);
    }
}

```

* We can also use the `instanceof` keyword if we use classes to define a typeguard on classes.
* **Discrimanted Unions**: This makes implementing type guards easier for interfaces. This can be accomplisehd by adding a type variable to an interface.
* **Type casting**: We can use type casting especially if we work with the dom, e.g. cast an unknown HTMLElement to type input `const userInputElement = <HTMLInputElement>document.getElementById('user-input');`. To not clash with jsx syntax the same can be achieved using another syntax `const userInputElement = document.getElementById('user-input')! as HTMLInputElement;`
* **Index types**: Can be used if we have a interface where wo do not know the number of elemts an object will have.

```ts
interface ErrorContainer {
    [prop: string]: string;
}
```
* This means that we only know that we have properties which have a name interpretable as string and the value is also interpretable as string. 
* **Function overloads**: We can overload a function with different parameter types so that a return type can be identified at compilation time, e.g.:

```ts
function add(a: number, b: number): number
function add(a: Combinable, b: Combinable){
    // This is called a type guard
    if(typeof a === 'string' || typeof b === 'string'){
        return a.toString() + b.toString()
    }

    return a + b;
}
```
* **Optional chaining**: Relevant if we do not know the type of data at compilation time, e.g. data fetched from a database. Optional chaining is used with the `?` operator for example: `console.log(fetchedUserData?.job?.title);`.
const storedData = userInput ?? 'DEFAULT';
* **Nullish Coalescing**: Is useful if you want to define a fallback value in case some data is null or undefined, this is set with the `??` operator, e.g. `const storedData = userInput ?? 'DEFAULT';`

#### Additional ressources

https://www.typescriptlang.org/docs/handbook/2/types-from-types.html

## Ch6. Generics

Generics are types that work with other types and may provide different functionality based on the type its associated with. One example would be arrays, e.g. `const names: Array<string>= [];` then the ts compiler will know that all elements associated to names will have the type string. 

* **Generic Functions**: Can help us clarify more what type an object has if we work with objects, for example writing a merge function that will combine 2 objects:

    ```ts
    function merge<T, U>(objA: T, objB: U) {
    return Object.assign(objA, objB)
    }
    ```

* Only with the generic types we can do `merge({name: 'Max'}, {age: 30}).age`
* **Constraints**: We can say that generic must be of a specific type so that a function does not silenty fail if we give them a type we do not expect, e.g.:

    ```ts
    function merge<T extends object, U extends object>(objA: T, objB: U) {
        return Object.assign(objA, objB)
    }
    ```

* This will then throw an error for `const mergedObj2 = merge({name: 'Max'}, 30)`
* We can also check that a type should have a specific property, e.g. a `length` property. We can use interfaces for that purpose, see the function `countAndDescribe` as example
* **keyof** Constraint: To tell typescript that an generic object adheres to a specific feature we can use keyof, e.g. 

```ts
function extractAndConvert<T extends object, U extends keyof T>(obj:  T, key: U) {
    return obj[key];
}
```

* **Generic classes**: Same idea as functions by increasing the flexibility of a type, e.g. see `DataStorage` class. 
* **Generic Utility Types**: Build in generic types that can be handy for special use cases, e.g. `ReadOnly` or `Partial`. 
* Union types are not the same as generics, because although we can set multiple types with union types, once they are set they cannot take another type. Whereas with generics this is possible
* **More on generics**: https://www.typescriptlang.org/docs/handbook/generics.html

## Ch7. Decorators

* To use decorators we need to change tsconfig: `target` must be es6 or greater and `experimentalDecorators` must be true.
* Decorators are functions that start with a capital letter
* Decorators are added to classes or functions using the `@` keyword
* **Decorator Factories**: Are functions that return a new function (similiar to python decorators). In Ts however this allows us to configure our decorator. Decorator functions are build like this:

```ts
function Logger(logString: string){
    return function(constructor: Function){
        console.log(logString)
        console.log(constructor)
    }
}
```

* It is also possible to add multiple decorators to one class or function. Logs are executed in bottom up order. But outside the decorator factory (inner function) the order is top down. 
* **Property Decoratros**: We can also use decorators on properties, then the decorator takes 2 paramerter `target` and `propertyName`. It is then executed when the class is created (See Ch7/app.ts). 
* If you want to use decorators for **getters/setters or methods** you use the decorator structure `function Log3(target: any, name: string, descriptor: PropertyDescriptor)`
* **Parameter** decorators: Can be used on parameters inside functions and have the structure `function Log4(target: any, name: string, position: number)` they are then used for example `getPriceWithTax(@Log4 tax: number)` and could be used on any parameter inside a function definiition.  
* A decorator is executed when you define a function or class **not** when it is executed (I think this is a key different to python). For that you would use event listeners. However it may be possible to tweak a decorator so that it is executed every time a function/class is called. See WithTemplate decorator as example. The key difference is that for example for classes the decorator **returns** a new constructor which has the logic inside we would like to see at creation of an object
* **Autobind Decorator**: Use case we want to bind an eventListener to a class method everytime we create an instance from it, e.g.

```ts
function Autobind(_: any, _2: string, descriptor: PropertyDescriptor){
    const origMethod = descriptor.value;
    const adjDescriptor: PropertyDescriptor = {
        configurable: true,
        enumerable: false,
        get() {
            const boundFn = origMethod.bind(this)
            return boundFn;
        }
    };
    return adjDescriptor
}

class Printer {
    message = 'This works!';

    @Autobind
    showMessage(){
        console.log(this.message);
    }
}

const p = new Printer();
const button = document.querySelector('button')!;
// Without the Autobind we had to call it like this
// button.addEventListener('click', p.showMessage.bind(p))
button.addEventListener('click', p.showMessage)
```

* **Validation** with decorators: Especially useful if you want to validate that the input of a user input, database is of the desired form. 
* **More Ressources**: https://www.typescriptlang.org/docs/handbook/decorators.html

## Ch8: Building a drag and drop element

# Common pitfalls

* If you use button and document.query you need to defer the script or otherwise the button will not be found (Will be null)