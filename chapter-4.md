# Functions for the journeyman: understanding function invocation
## 4.1 Using implicit function parameters
* in addition to the parameters that we’ve explicitly stated in the function definition, function invocations are usually passed two implicit parameters: **arguments and this.**
#### 4.1.1 The arguments parameter - [listing-4.1.html](https://github.com/gunjasal/book-ninja-js/blob/master/chapter-4-listing-4.1.html)
* The arguments parameter is a collection of all arguments passed to a function.
* The arguments object has a property named length that indicates the exact number of arguments.
* The arguments parameter is not a JavaScript array, and if you try to use array methods on arguments
* with rest parameters, the need for the arguments parameter has been greatly reduced. (The rest parameter is a real array)

###### ARGUMENTS OBJECT AS AN ALIAS TO FUNCTION PARAMETERS - [listing-4.2.html](https://github.com/gunjasal/book-ninja-js/blob/master/chapter-4-listing-4.2.html)
* If we set a new value to, for example, arguments[0], the value of the first parameter will also be changed. 
* [javascript pass-by-value vs pass-by-reference?](https://stackoverflow.com/questions/6605640/javascript-by-reference-vs-by-value)

###### AVOIDING ALIASES
* use strict"
```
"use strict";

function infiltrate(person) {
  assert(person === "gardener", "The person is a gardener");
  assert(arguments[0] === "gardener", "The first argument is a gardener");

  arguments[0] = "ninja";

  assert(arguments[0] === "ninja", "The first argument is now a ninja");

  assert(person === "gardener", "The person is still a gardener");
}

infiltrate("gardener");
```

#### 4.1.2 The this parameter: introducing the function context
* **When a function is invoked**, in addition to the parameters that represent the explicit arguments provided in the function call, an implicit parameter named this is passed to the function.
* this parameter refers to an object that’s associated with the function invocation. For this reason, it’s often termed the function context.
* in JavaScript, **invoking a function as a method** is only one way that a function can be invoked.
* this parameter points to isn’t (as in Java or C#) defined only by how and where the function is defined; it can also be heavily influenced by how the function is _invoked_.

## 4.2 Invoking functions
* We can invoke a function in four ways
  * As a function — `skulk()`
  * As a method — `ninja.skulk()`, which ties the invocation to an object, enabling object-oriented programming
  * As a constructor — `new Ninja()`, in which a new object is brought into being
  * Via the function’s `apply` or `call` methods — `skulk.call(ninja)` or `skulk.apply(ninja)`
* 
  
#### 4.2.1 Invocation as a function
* When invoked in this manner, the function context (the value of the this keyword) can be two things: 
  * In nonstrict mode, it will be the global context (the window object)
  * whereas in strict mode, it will be undefined.
```
function ninja() {
  return this;
}

function samurai() {
  "use strict";
  return this;
}

assert(ninja() === window, "In a 'nonstrict' ninja function, the context is the global window object");
assert( samurai() === undefined, "In a 'strict' samurai function, " + "the context is undefined");
```
#### 4.2.2 Invocation as a method
* When we invoke a function as a method of an object, that object becomes the function context and is available within the function via the this parameter.
```
function whatsMyContext() {
  return this;
}
assert(whatsMyContext() === window, "Function call on window");

var getMyThis = whatsMyContext;
assert(getMyThis() === window, "Another function call in window");

var ninja1 = {
  getMyThis: whatsMyContext
};
assert(ninja1.getMyThis() === ninja1, "Working with 1st ninja");

var ninja2 = {
  getMyThis: whatsMyContext
};
assert(ninja2.getMyThis() === ninja2, "Working with 2nd ninja");
```

#### 4.2.3 Invocation as a constructor
* constructor function vs function constructor
```
// constructor function
function whatsMyContext(){ return this; }
new whatsMyContext();

// function constructor
new Function('a','b','return a+b');
```

###### THE SUPERPOWERS OF CONSTRUCTORS
```
function Ninja() { 
  this.skulk = function() {
    return this;
  };
}

var ninja1 = new Ninja();
var ninja2 = new Ninja();

assert(ninja1.skulk() === ninja1, "The 1st ninja is skulking"); 
assert(ninja2.skulk() === ninja2, "The 2nd ninja is skulking");
```
* Calling a function with the keyword new triggers the following steps:
  1. A new empty object is created.
  2. This object is passed to the constructor as the this parameter, and thus becomes the constructor’s function context.
  3. The newly constructed object is returned as the new operator’s value (with an exception that we’ll get to in short order).

###### CONSTRUCTOR RETURN VALUES
* 

## 
####
######

## 
####
######
