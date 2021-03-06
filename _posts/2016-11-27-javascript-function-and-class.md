---
layout: post
title: JavaScript Function and Class
categories:
- Language
tags:
- JavaScript
---

## 1. Pain in the Ass 
In Javascript since ES6, the `Function`, `Class` and object inheritance are things that confused me for a while. This article is a summary of my reading of [Exploring ES6](http://exploringjs.com/es6.html). 

## 2. Function Concept

### 2.1. Three Roles of Function
A function is a callable entity in ES6 that play three roles. 

1. Real (non-method) function
    * Traditional functions created via function expressions/declarations. 
    * Arrow functions created in an expression form. 
    * Generator functions created via generator function expressions/declarations. 
2. Method
    * Methods created by method definition in object literals and class defintion. 
    * Generator methods created by generator method definitions in object literal and class definition. 
3. Constructor
    * Classes created via class definitions. 

The `typeof(entity)` results of all the callable entities are `function`. Arrow functions are made for non-method functions because they pick up `this` from their surrounding lexical scopes -- so-called "lexical `this`". In JavaScript, a new scope is created only when a new function is created.   

### 2.2. Recommendations for Using Callable Entities
Prefer arrow functions as callable because of the lexical `this` and compact format. Use traditional function expression when there is an implicit `this` or the callback has multiple lines. 

Avoid using IIFEs in ES6 by using a module or block with a `let` or `const` declaration. The only case to use IIFE is to produce a result via multiple statements and IIAF (immediately-invoked arrow function) can be used. In IIAF, the parentheses must be around arrow function. For example: 

```js
(() => {
    return 123
})();  // the parantheses are around arrow function and the expression ended with a semicolon. 
```

In ES6, use classes, not constructor functions, as constructor. 

## 3. Use Function  

### 3.1. Traditional Function Definition

```js
// Function expression:
const fooFun = function (x) { ?????? }

// Function declaration:
function fooFun (x) { ?????? }
```

### 3.2. Method Definition

```js
// Method definitions can appear inside object literals
const obj = {
    add(x, y) {
        return x + y;
    }, // comma is required

    sub(x, y) {
        return x - y;
    }, // comma is optional
}

```

### 3.3. Method Call 
There are two ways to call methods:

* dispatch: `obj.someMethod(args)`
* direct: `someFunc.call(thisValue, arg0, arg1)`  or `someFunct.apply(thisVAlue, argArray)` 

With ES6, it is rare to use the direct call syntax because of 1) the new spread operator, 2) `Array.from(obj, callback)` to convert an array-like object to an array, and 3) the new rest parameter declared via a triple dot. 

## 4. Class Concept

### 4.1. Overview
ES6 classes are essentially traditional constructor functions. The `typeof` operation of a class returns "function". A class can only be invoked via `new`, not via a functionall. 

Unlike function, class definitions are **not hoisted**.

### 4.2. Class Definition
Class can be defined via expression or declaration. Only constructor method can call `super()`. Only class methods can access `super` property. 

```js
// declared
class A {}
class B extends A {}

// statement 
const C = class {}
let E = class D {}
```

There is no separator `,` between members of class definitions. It's best a best practice to define only methods, but not data properties, in a class body. Defint instance properties in `constructor()`. 

## 5. Class, Function and Relationships Among Everything
Two key points to understand class inheritance or object chain are: 

1. `[[Prototype]]` (the legacy `__proto__`) refers to the parent object and define the inheritance relationships between two objects. A parent is the prototype of the child, as defined in the `isPrototypeOf()` method. Use `Object.getPrototypeOf(obj)` to get the parent object. The child has all properties of its parent.  
2.  `prototype` is a normal property whose value is a special "shadown" object assoicated with the corresponding callable entity. Because a class is a function, it has this property. The `prototype` object of a class has all instance methods and a special `constructor` property that refers back to the paired class. All static methods are properties of the class itself. When a child class `extends` a parent class, the child class's prototype also inherites (its `[[Prototype]]` or `__proto__` refers) to the prototype of the parent class. All instances of a class inherites from the `prototype` of the class, therefore an instance has all properties defined in the `prototype` of the class.  

As an example, when a class `Foo` is defined, a special object `Foo.prototype` is created with the special `constructor` method and other user-defined methods. It is the parent of all instances of the class `Foo`. 

![Object Relationships](http://exploringjs.com/es6/images/classes----methods_150dpi.png)

### 5.1. Sample Inheritance Tree Code
We use the following code to define a simple inheritance tree. 

```js
class Foo {
    constructor(prop) {
        this.prop = prop;
    }

    static staticMethod() {   // property of Foo
        return 'classy';
    }

    prototypeMethod() {   // property of Foo.prototype
        return 'hello from prototype';
    }
}

class Bar extends Foo {
    constructor(prop, extra) {
        super(prop)
        this.extra = extra // must after super() to use this
    }
}

let foo = new Foo()
let bar = new Bar()
```

### 5.2. The `isPrototypeOf` Inheritance Tree
We use "<=" to represent `isPrototypeOf` relationship. We can also use `Object.getPrototypeOf()` checking from bottom up. We  have the following interitance tree:  

`Object.prototype` <= `Function.prototype` <= `Object` / `Function` / `Foo`  

`Object.prototype` <= `Foo.prototype` <= `Bar.prototype`  

`Foo` <= `Bar`

`Foo.prototype` <= `foo`

`Bar.prototype` <= `bar`

And the inheritance is transitive, i.e., we have  `Foo.prototype` <= `bar`. Or all the way to the root: `Object.prototype` <= `bar`. 

The following statements are true and help to understand these concepts. 
```JavaScript 
// Object.prototype is the root. It has a null prototype  
Object.getPrototypeOf(Object.prototype) === null

// everything inherits from the Object.prototype, this is the callable entity 
Object.prototype.isPrototypeOf(Function.prototype)

// Functions and classes inherits from Function.prototype, directly or via class inheritance 
Function.prototype.isPrototypeOf(Object)   // Object is a callable entity !
Function.prototype.isPrototypeOf(Function)
Function.prototype.isPrototypeOf(Foo)

// all class prototypes inherit from Object.prototype, directly or via class prototype inheritance
Object.prototype.isPrototypeOf(Foo.prototype)
Foo.prototype.isPrototypeOf(Bar.prototype)

// inheritance between classes
Foo.isPrototypeOf(Bar)

// an inheritance inherits its class's prototype
Foo.prototype.isPrototypeOf(foo)
Bar.prototype.isPrototypeOf(bar)
Foo.prototype.isPrototypeOf(bar)

// all the way to the root
Object.prototype.isPrototypeOf(bar)
```

## 6. Inherited Properties and Inheritance
Given the above inheritance tree and the fact that a child inherits all properties of its parent, we have the following observation: 

#### 6.1. `Object.prototype` is the Root 
Every object has the following properties generated from `Object.getOwnPropertyNames(Object.prototype)`.

```js 
[ '__defineGetter__',
  '__defineSetter__',
  'hasOwnProperty',
  '__lookupGetter__',
  '__lookupSetter__',
  'propertyIsEnumerable',
  'constructor',
  'toString',
  'toLocaleString',
  'valueOf',
  'isPrototypeOf',
  '__proto__' ]
``` 

### 6.2. `Function.prototype` is the Callable Root
They have the following properties generated from `Object.getOwnPropertyNames(Function.prototype)`.

```js
[ 'length',
  'name',
  'arguments',
  'caller',
  'apply',
  'bind',
  'call',
  'toString',
  'constructor' ]
```

It helps to explain that a callable entity (a function or a class) has `call`, `bind` and `apply` methods. 

Because all classes and functions have a `name` property, this can be used to find an object's class name via `obj.constructor.name`. 

### 6.3 Instance Inheritance 
All instance inherits properties from the prototype of its class and the prototypes of ancestor classes till the root `Object.prototype`. 

These properties are instance properties and methods. 

### 6.4. Class Inheritance
Every class inherits static methods/properties from its parent and ancestor classes. 

## 7. `instanceof` and `typeof`

### 7.1. The `instanceof` Algorithm
The [instanceof algorithm](http://www.ecma-international.org/ecma-262/6.0/#sec-instanceofoperator) for the statement `objectOne instanceof objectTwo` is as the following:  

1. `objectTwo` is a callable entity
2. `objectTwo.prototype` is an ancestor of `objectOne`. 

Javascript throws `TypeError` if the right hand side (RHS) `objectTwo` is not a callable entity. The algorithm first gets the `objectTwo.prototype`, then check if whether it is an ancestor of `objectOne`. 

Because an callable entity's `prototype` has a `constructor` pointing back to the callable entity, i.e., `objectTwo.prototype.constructor === objectTwo`, the second statement can be interpreted as: the `objectTwo` is the constructor of an ancestor of `objectOne`. 

In a normal case, `bar` is an instance of `Bar` because `Bar.prototype.isPrototypeOf(bar)` is true. `bar` is also an instance of `Foo` because of `Foo.prototype.isPrototypeOf(Bar.prototype)`. One level up, `bar` is an instance of `Object` because of `Object.prototype.isPrototypeOf(Foo.prototype)`. 

It's interesting to check the `Bar.prototype` object. It is NOT an instance of `Bar` because `Bar.prototype.isPrototypeOf(Bar.prototype)` is a false expression. However, it is an instance of `Foo`, a somehow surprising result. The reason is that `Foo.prototype.isPrototypeOf(Bar.prototype)` is true.  

There are special casses: `Function` and `Object` are instances of each other, i.e., the following statements are true.  

```js
Function instanceof Function
Object instanceof Function
Function instanceof Object 
Object instanceof Object
``` 

## 8. The `typeof` Operator
As explained in this stackoverflow article http://stackoverflow.com/questions/899574/which-is-best-to-use-typeof-or-instanceof, use `typeof` for simple buildin types such as `string`, `true`, `99.99`, `{}`. It only reports top level types such as `object`, `number`, `boolean`, `string`, and `function`. 

Special cases for `typeof` operator: 

```js
typeof null === 'object'
typeof Function === 'function'
typeof undefined === 'undefined'
typeof NaN === 'number'
typeof 'abc' === 'string'
typeof false === 'boolean'

typeof Object === 'function'
typeof Boolean === 'function'
typeof String === 'function'
typeof (new String('abc'))  === 'object'
```

## 9. The `constructor` Hierarchy
Every object inherits the `constructor` property from `Object.prototype`.  The `constructor` has a special meaning when determine the result of the `instanceof` operator. 

Let "<=" represents the meaning of "is the constructor of" relationship, then we have:

`Function` <= `Function`, i.e., the `Function` is the constrctor of itself. 

`Function` <= `Function.prototype`, i.e., the `Function` is the constructor of its prototype. 

`Function` <= `Object` <= `Object.prototype`

`Function` <= `Foo` <= `Foo.prototype` / `foo`

`Function` <= `Bar` <= `Bar.prototype` / `bar`

The `constructor` hierarchy helps to explain the "construction" of all callable entities including functions and classes.

The following statement: 

```js
function myFunction() {}
class myKlass {}
``` 

can be thought (might be wrong) as the following: 

```js
myFunction = new Function()
myKlass = new Function()
```

It alo helps to explain that the following  expressions having a result of 'function'

```js
typeof Object
typeof Function
typeof Function.prototype
typeof Foo
typeof Bar
```


