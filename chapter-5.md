# Functions for the master: closures and scopes
* closures reduce the amount and complexity of code needed to add advanced features, but also enable us to do things that otherwise wouldn’t be possible, or would be too complex to be feasible.

## 5.1 Understanding closures
* A closure allows a function **to access and manipulate variables that are external** to that function.
* Closures allow a function to access all the variables, as well as other func- tions, that are in scope when the function itself is defined.
```
// Listing 5.1 A simple closure
var outerValue = "ninja";
function outerFunction(){
  assert(outerValue === "ninja","I can see the ninja.");
}
outerFunction();
```

```
// Listing 5.2 Another closure example
var outerValue = "samurai";
var later;
function outerFunction(){
  var innerValue = "ninja";
  
  function innerFunction(){
    assert(outerValue === "samurai", "I can see the samurai.");
    assert(innerValue === "ninja", "I can see the ninja.")
  }
  later = innerFunction; // Stores a reference to innerFunction in the later variable.
}

outerFunction();
later();
```
* When we declare innerFunction inside the outer function, not only is the function declaration defined, but a closure is created that encompasses the function definition as well as all variables in scope at the point of function definition. 
* When innerFunction eventually executes, even if it’s executed after the scope in which it was declared goes away, it has access to the original scope in which it was declared through its closure
* Closures create a “safety bubble” of the function and the variables in scope at the point of the function’s definition, so that the function has all it needs to execute.
* Although all this structure isn’t readily visible (there’s no “closure” object holding all of this information that you can inspect), storing and refer- encing information in this way has a direct cost.
* All that information needs to be held in memory until it’s absolutely clear to the JavaScript engine that it’s no longer needed (and is safe to garbage-collect), or until the page unloads.

## 5.2 Putting closures to work
#### 5.2.1 Mimicking private variables
* JavaScript **doesn’t have native support for private variables**. But by using a closure, we can achieve an acceptable approximation
  * There're only [function variable and global variable](https://www.w3schools.com/js/js_scope.asp)
```
// Listing 5.3 Using closures to approximate private variables
function Ninja() {
  var feints = 0; // <-> this.feints = 0;
  this.getFeints = function () {
    return feints;
  };
  this.feint = function () {
    feints++;
  };
}

var ninja1 = new Ninja();
ninja1.feint();
assert(ninja1.feints === undefined, "And the private data is inaccessable to us");
assert(ninja1.getFeints() === 1, "We're able to access the internal feint count");

var ninja2 = new Ninja();
assert(ninja2.getFeints() === 0, "The second ninja object gets its own feints variable.");
```
* recall that when using the new keyword on a function, a new object instance is created, and the function is called with that new object as its context, to serve as a constructor to that object. So this within the function refers to a newly instantiated object.

######

##
####
######

##
####
######

##
####
######

##
####
######
