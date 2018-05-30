### 1. ES6 class

- Class definition

```javascript
// ES6
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  static getType() {
    return 'Human Being';
  }

  getName() {
    return `${this.firstName} ${this.lastName}`;
  }
}

// ES5
function Person(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}

Person.getType = function() {
  return 'Human Being';
}

Person.prototype.getName = function() {
  return this.firstName + ' ' + this.lastName;
}

// Usage
typeof Person // "function"
Person === Persone.prototype.constructor // true
Person.getType() // "Human Being"

const person = new Person('Ye', 'Huang');
person.getName() // "Ye Huang"

person.hasOwnProperty('firstName') // true
person.hasOwnProperty('lastName') // true
person.hasOwnProperty('getName') // false
person.__proto__.hasOwnProperty('getName') // true
```

- Class inheritance

```javascript
// ES6
class Developer extends Person {
  constructor(firstName, lastName, jobTitle) {
    super(firstName, lastName);
    this.jobTitle = jobTitle;
  }

  getDeveloperInfo() {
    return `${super.getName()}: ${this.jobTotle}`;
  }
}

// ES5
function Developer(firstName, lastName, jobTitle) {
  Person.call(this, firstName, lastName);
  this.jobTitle = jobTitle;
}

Developer.prototype = Object.create(Person.prototype);
Developer.prototype.constructor = Developer;
Developer.prototype.getDeveloperInfo = function() {
  return Person.prototype.getName.call(this) + ': ' + this.jobTitle;
}
```

### 2. for...in / for...of

- `for...in` iterates over all enumerable property names of an object.

> Enumerable properties are those properties whose internal [[Enumerable]] flag is set to true, which is the default for properties created via simple assignment or via a property initializer (properties defined via Object.defineProperty and such default [[Enumerable]] to false)([source1](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties), [source2](http://perfectionkills.com/extending-native-builtins/)).

```javascript
// By default, object property assignment (with the "=")
// makes your properties enumerable: true
Object.prototype.objCustomOne = function value() {};
Array.prototype.arrCustomOne = function value() {};

// By default, object.defineProperties
// makes your properties enumerable: false
Object.defineProperties(Object.prototype, {
  objCustomTwo: { value: function() {} }
});
Object.defineProperties(Array.prototype, {
  arrCustomTwo: { value: function() {} }
});

const arr = ['a', 'b', 'c'];
arr['newProp'] = 'd';

for (let i in arr) {
  console.log(i); // 0, 1, 2, newProp, arrCustomOne, objCustomOne
}
```

- `for...of` iterates over [iterable objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#iterable)(Array, [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map), [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set), String, TypedArray, arguments object, etc.)

```javascript
// Array
const arr = ['a', 'b', 'c'];

for (let i of arr) {
  console.log(i); // a, b, c
}

// Map
const newMap = new Map();
const keyString = 'a string';
const keyObject = {'a': 1};
const keyFunction = function a() {};

newMap.set(keyString, 'String as key');
newMap.set(keyObject, 'Object as key');
newMap.set(keyFunction, 'Function as key');

for (let i of newMap) {
  console.log(i);
  // ["a string", "String as key"]
  // [Object, "Object as key"]
  // [function, "Function as key"]
}

// Set
const newSet = new Set([1, 1, 1, 2, 2, 2]);

for (let i of newSet) {
  console.log(i); // 1, 2
}

```

### 3. Closure

> A closure is an inner function that has access to the outer (enclosing) function’s variables—scope chain. The closure has three scope chains: it has access to its own scope (variables defined between its curly brackets), it has access to the outer function’s variables, and it has access to the global variables.
>
>The inner function has access not only to the outer function’s variables, but also to the outer function’s parameters. Note that the inner function cannot call the outer function’s arguments object, however, even though it can call the outer function’s parameters directly.

```javascript
function printName(firstName, lastName) {
  const introSegment = 'Your name is';

  function fullName() {
    return `${introSegment} ${firstName} ${lastName}`;
  }

  return fullName();
}

// Usage
printName('Ye', 'Huang'); // Your name is Ye Huang
```
- Closure has access to the outer function's variable even after the outer function returns:

```javascript
function printName(firstName) {
  const introSegment = 'Your name is';

  return function fullName(lastName) {
    return `${introSegment} ${firstName} ${lastName}`;
  }
}

// Usage
let name = printName('Ye');
console.log(name('Huang')); // Your name is Ye Huang
```

- Closure stores references to the outer function's variables:

```javascript
function idHandler() {
  var id = 1;

  var _getId = function() {
    return id;
  }

  var _setId = function(newId) {
    id = newId;
  }

  return {
    getId: _getId,
    setId: _setId
  }
}

// Usage
var idH = idHandler();
console.log(idH.getId()); // 1
console.log(idH.setId(2)); // undefined
console.log(idH.getId()); // 2
```

- Using IIFE to aviod bugs caused by closures:

```javascript
// Bug version
function idCreator(idArray) {
  var i;
  var uid = 100;
  for (i = 0; i < idArray.length; i++) {
    idArray[i]['id'] = function() {
      return uid + i;
    }
  }

  return idArray;
}

// Usage
var idArray = [{id: 0}, {id: 1}, {id: 2}];
var newIdArray = idCreator(idArray);
console.log(newIdArray[0].id()); // 103 - should be 100

// Correct version
function idCreator(idArray) {
  var i;
  var uid = 100;
  for (i = 0; i < idArray.length; i++) {
    idArray[i]['id'] = function(j) {
      // the j is the i passed in on invocation of this IIFE
      return function() {  
        return uid + j;
      }() // IIFE returns the value of (uid + j) instead of a function
    }(i) // Passing the i variable as a parameter​
  }

  return idArray;
}

// Usage
var idArray = [{id: 0}, {id: 1}, {id: 2}];
var newIdArray = idCreator(idArray);
console.log(newIdArray[0].id, newIdArray[1].id); // 100 101
```

### 4. Promises, Generators, Async / Await

- Promises

```javascript
function createPromise(status) {
  return new Promise((resolve, reject) => {
    if (status) {
      setTimeout(() => {
        resolve(status);
      }, 1000);
    } else {
      reject('Error');
    }
  });
}

// Usage
createPromise(1).then((data) => {
  console.log(data); // Print 1 after one second.
}).catch((error) => {
  console.log(error);
});

createPromise(0).then((data) => {
  console.log(data);
}).catch((error) => {
  console.log(error); // Print 'Error' immediately.
});

// Promise.all
var promises1 = [1, 2, 3, 4, 5, 6, 7].map(i => {
  return createPromise(i);
});

var promises2 = [1, 2, 0].map(i => {
  return createPromise(i);
});

Promise.all(promises1).then(data => {
  console.log(data); // [1, 2, 3, 4, 5, 6, 7]
}).catch(error => {
  console.log(error);
});

Promise.all(promises2).then(data => {
  console.log(data);
}).catch(error => {
  console.log(error); // Error
});

// Promise.race
Promise.race(promises1).then((data) => {
  console.log(data); // 1
}).catch(error => {
  console.log(error);
});

Promise.race(promises2).then((data) => {
  console.log(data);
}).catch(error => {
  console.log(error); // Error
});
```

- Generators

> 利用了协程机制，调用Generator函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象。必须调用遍历器对象的next方法，使得指针移向下一个状态。也就是说，每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。换言之，Generator 函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。

```javascript
// fibonacci-generator
// https://gist.github.com/jfairbank/8d36e4bde9c16dc0bac7
function* fibonacci(n) {
  const infinite = !n && n !== 0;
  let current = 0;
  let next = 1;

  while(infinite || n--) {
    yield current;
    [current, next] = [next, current + next];
  }
}

// recursive-fibonacci-generator
function* fibonacci(n = null, current = 0, next = 1) {
  if (n === 0) {
    return current;
  }

  let m = n !== null ? n - 1 : null;

  yield current;
  yield *fibonacci(m, next, current + next);
}

// Usage
let [...first10] = fibonacci(10);
console.log(first10); // [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

- [Async / Await](http://es6.ruanyifeng.com/#docs/async)

> When an async function is called, it returns a Promise. When the async function returns a value, the Promise will be resolved with the returned value.  When the async function throws an exception or some value, the Promise will be rejected with the thrown value.
>
> An async function can contain an await expression, that pauses the execution of the async function and waits for the passed Promise's resolution, and then resumes the async function's execution and returns the resolved value.

```javascript
function createPromise(x) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (x) {
        resolve(x);
      } else {
        reject('Error');
      }
    }, 1000);
  });
}

async function add(x) {
  let a = createPromise(20);
  let b = createPromise(30);
  return x + await a + await b;
}

async function subtract(x) {
  let a = createPromise(5);
  let b = createPromise(10);
  return x - await a - await b;
}

add(10).then(data => {
  return subtract(data);
}).catch(error => {
  console.log('Add error: ', error);
}).then(data => {
  console.log('Final result: ', data);
}).catch(error => {
  console.log('Subtract error: ', error);
});
```

### 5. Arrow functions

- No binding of `this`

```javascript
// Not working
function Age(age) {
  this.age = age;
}

Age.prototype.addAgeToPersons = function(arr) {
  return arr.map(function(item) {
    return item + ': ' + this.age;
  });
}

// Error: Cannot read property 'age' of undefined
var a = new Age('25');
a.addAgeToPersons(['Person A', 'Person B', 'Person C']);

// Solution 1
Age.prototype.addAgeToPersons = function(arr) {
  var that = this;
  return arr.map(function(item) {
    return item + ': ' + that.age;
  });
}

// Solution 2
Age.prototype.addAgeToPersons = function(arr) {
  return arr.map(function(item) {
    return item + ': ' + this.age;
  }, this);
}

// Solution 3
Age.prototype.addAgeToPersons = function(arr) {
  return arr.map(function(item) {
    return item + ': ' + this.age;
  }.bind(this));
}

// Solution 4 - arrow function
Age.prototype.addAgeToPersons = function(arr) {
  return arr.map((item) => {
    return item + ': ' + this.age;
  });
}

// Usage
var a = new Age('25');
a.addAgeToPersons(['Person A', 'Person B', 'Person C']);
// ["Person A: 25", "Person B: 25", "Person C: 25"]
```

- No binding of `arguments`

```javascript
function foo() {
  var f = (i) => arguments[0] + i;
  return f(2);
}

function boo() {
  var f = function(i) {
    arguments[0] + i;
  }
  return f(2);
}

// Usage
console.log(foo(10)); // 12
console.log(boo(10)); // undefined
```

- Use as the `non-method` function

```javascript
'use strict';
var obj = {
  i: 10,
  b: () => console.log(this.i, this),
  c: function() {
    console.log(this.i, this);
  }
}
obj.b(); // prints undefined, Object {...}
obj.c(); // prints 10, Object {...}
```

- Use of the `new` operator

```javascript
var Foo = () => {};
var foo = new Foo(); // TypeError: Foo is not a constructor
```

- Use of `prototype` property

```javascript
var Foo = () => {};
console.log(Foo.prototype); // undefined
```

- Use of the `yield` keyword

>The yield keyword may not be used in an arrow function's body (except when permitted within functions further nested within it). As a consequence, arrow functions cannot be used as generators.

### 6. 一些被问过的问题

- Map / WeakMap / Set / WeakSet 的区别和用途。
- Javascript垃圾回收机制？
- Generator的原理，什么是协程？协程和进程有什么区别？
- Promise / Generator / Async 用法和区别。有什么API？
- 如何定义变量？
- 闭包是什么？实际项目中如何用闭包？
- Computed Property Names有什么作用和好处？
