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
report(typeof ninja1 === "object"); // true
report("ninja1 = " + printObject(ninja1)); // ninja = {skulk: function () { return this; }}
```
* Calling a function with the keyword new triggers the following steps:
  1. A new empty object is created.
  2. This object is passed to the constructor as the this parameter, and thus becomes the constructor’s function context.
  3. The newly constructed object is returned as the new operator’s value (with an exception that we’ll get to in short order).

###### CONSTRUCTOR RETURN VALUES
* If the constructor returns an object, that object is returned as the value of the whole new expression, and the newly constructed object passed as this to the constructor is discarded.
* If, however, a nonobject is returned from the constructor, the returned value is ignored, and the newly created object is returned.
```
// Listing 4.8 Constructors returning primitive values
function Ninja() {
  this.skulk = function () {
    return true;
  };

  return 1;
}

assert(Ninja() === 1, "Return value honored when not called as a constructor");

var ninja = new Ninja();

assert(typeof ninja === "object", "Object returned when called as a constructor");
assert(typeof ninja.skulk === "function", "ninja object has a skulk method");
report("ninja = " + printObject(ninja)); // ninja = {skulk: function () { return true; }}
report("ninja.skulk() = " + ninja.skulk()); // ninja.skulk() = true
```

```
// Listing 4.9 Constructors explicitly returning object values
var puppet = {
  rules: false,
};

function Emperor() {
  this.rules = true;
  return puppet;
}

var emperor = new Emperor();
assert(emperor === puppet, "The emperor is merely a puppet!"); // !!!!
assert(emperor.rules === false, "The puppet does not know how to rule!"); // !!!!
```

###### CODING CONSIDERATIONS FOR CONSTRUCTORS
* The intent of constructors is to initialize the new object that will be created by the function invocation to initial conditions. 
* Constructors are usually named as a noun that describes the object that’s being constructed and start with an uppercase character
```
function Ninja() { 
  this.skulk = function() { // skulk property would be created on window in nonstrict mode—not a particularly useful operation
    return this;
  };
}
var whatever = Ninja();
```

#### 4.2.4 Invocation with the apply and call methods
* if we had called the function via button.click(), the context would have been the button
* But in this example, **the event-handling system of the browser defines the context of the invocation to be the target element of the event, which causes the context to be the &lt;button&gt; element, not the button object.**
* [`apply` on addEventListner method](https://stackoverflow.com/questions/2891096/addeventlistener-using-apply)
```
// Listing 4.10 Binding a specific context to a function
<button id="test">Click Me!</button>
<script>
  function Button(id) {
    this.id = id;
    this.clicked = false;
    this.click = function () {
      this.clicked = true;
      report("this = " + this);
      report("button = " + button);
      report("print button = " + printObject(button));
      assert(button.clicked, "The button has been clicked");
    };
  }

  var button = new Button(1);
  assert(button.id === 1, "button's id is 1");
  report("button = " + button);
  report("print button = " + printObject(button));
  report("init finished. -------------------");

  var elem = document.getElementById("test");
  elem.addEventListener("click", button.click);
</script>
```

###### USING THE APPLY AND CALL METHODS
* JavaScript provides a means for us to invoke a function and to explicitly specify any object we want as the function context. `apply and call`
* As first-class objects (created, by the way, by the built-in Function constructor), functions can have properties just like any other object type, including methods.
```
// Listing 4.11 Using the apply and call methods to supply the function context
function juggle() {
  var result = 0;
  for (var n = 0; n < arguments.length; n++) {
    result += arguments[n];
  }
  this.result = result;
}

var ninja1 = {};
var ninja2 = {};
juggle.apply(ninja1, [1, 2, 3, 4]);
juggle.call(ninja2, 5, 6, 7, 8);

assert(ninja1.result === 10, "juggled via apply");
assert(ninja2.result === 26, "juggled via call");
```

###### FORCING THE FUNCTION CONTEXT IN CALLBACKS
```
// Listing 4.12 Building a forEach function to demonstrate setting a function context
function forEach(list, callback) {
  for (var n = 0; n < list.length; n++) {
    callback.call(list[n], n);
  }
}

var weapons = [
  { type: "shuriken" },
  { type: "katana" },
  { type: "nunchucks" },
];

forEach(weapons, function (index) {
  assert(
    this === weapons[index],
    "Got the expected value of " + weapons[index].type
  );
});
```

## 4.3 Fixing the problem of function contexts
#### 4.3.1 Using arrow functions to get around function contexts
* arrow functions don’t get their own implicit this parameter when we call them; instead they remember the value of the this parameter at the time they were created.
* In our case, the click arrow function was created inside a constructor function, where the `this` parameter is the newly constructed object, so whenever we (or the browser) call the click function, the value of the this parame- ter will always be bound to the newly constructed button object.
```
// Figure 4.6 Arrow functions don't have their own context. 
// Instead, the context is inherited from the function in which they’re defined. 
// The this parameter in our arrow function callback refers to the button object.

function Button(){
  this.clicked = false; 
  this.click = () => {
    this.clicked = true;
    assert(button.clicked, "The button has been clicked");
  };
}  
```
###### CAVEAT: ARROW FUNCTIONS AND OBJECT LITERALS
* Figure 4.7 If an arrow function is defined within an object literal that’s defined in global code, the value of the this param- eter associated with the arrow function is the global window object.
```
<button id="test">Click Me!</button>
<script>
  assert(this === window, "this === window");

  var button = {
    clicked: false,
    click: () => {
      this.clicked = true;
      assert(button.clicked, "The button has been clicked");
      assert(this === window, "In arrow function this === window");
      assert(window.clicked, "Clicked is stored in window");
    },
  };

  var elem = document.getElementById("test");
  elem.addEventListener("click", button.click);
</script>
```
