# First-class functions for the novice: definitions and arguments
Most important, in JavaScript, functions are first-class objects, or first-class citizens as they’re often called. They coexist with, and can be treated like, any other JavaScript object. Just like the more mundane JavaScript data types, they can be referenced by variables, declared with literals, and even passed as function parameters.
* First class object(https://stackoverflow.com/a/245208/5448419)
  * A first class object is an entity that can be dynamically created, destroyed, passed to a function, returned as a value, and have all the rights as other variables in the programming language have.

## 3.1 What’s with the functional difference?
* In JavaScript, objects enjoy certain capabilities:
  * They can be created via literals: {}
  * They can be assigned to variables, array entries, and properties of other objects:
  * They can be passed as arguments to functions:
  * They can be returned as values from functions:
  * They can possess properties that can be dynamically created and assigned:

#### 3.1.1 Functions as first-class objects
* We say that functions are first-class objects, which can also be
  * Created via literals `function ninjaFunction() {}`
  * Assigned to variables, array entries, and properties of other objects
    ```
    var ninjaFunction = function() {}; 
    ninjaArray.push(function(){}); 
    ninja.data = function(){};
    ```
  * Passed as arguments to other functions
    ```
    function call(ninjaFunction){
      ninjaFunction();
    }
    call(function(){});
    ```
  * Returned as values from functions
    ```
    function returnNewNinjaFunction() {
      return function(){};
    }
    ```
  * They can possess properties that can be dynamically created and assigned:
    ```
    var ninjaFunction = function(){}; 
    ninjaFunction.name = "Hanzo";
    ```
* _**Functions are objects, just with an additional, special capability of being invokable**_ : Functions can be called or invoked in order to perform an action.

#### 3.1.2 Callback functions
* Whenever we set up a function to be called at a later time, whether by the browser in the event-handling phase or by other code, we’re setting up a callback.

###### SORTING WITH A COMPARATOR
```
var values = [0, 3, 2, 5, 7, 4, 8, 1];
values.sort(function(value1, value2){ return value1 - value2; });
values.sort((v1,v2) => v1 - v2);
```

## 3.2 Fun with functions as objects
#### 3.2.1 Storing functions - [listing 3.2](./chapter-3-listing-3.2.html)
* allows us to easily manage related functions—for example, callbacks that have to be invoked when something of interest occurs.

#### 3.2.2 Self-memoizing functions - [listing 3.3](./chapter-3-listing-3.3.html)
* allows the function to remember previously computed values, thereby improving the performance of subsequent invocations.
* This approach has two major advantages:
  * The end user enjoys performance benefits for function calls asking for a previously computed value.
  * It happens seamlessly and behind the scenes; neither the end user nor the page author needs to perform any special requests or do any extra initialization in order to make it all work.
* its disadvantages may need to be weighed against its advantages:
  * Any sort of caching will certainly sacrifice memory in favor of performance.
  * Purists may consider that caching is a concern that shouldn’t be mixed with the business logic; a function or a method should do one thing and do it well. But
don’t worry; in chapter 8, you’ll see how to tackle this complaint.
  * It’s difficult to load-test or measure the performance of an algorithm such as this one, because our results depend on the previous inputs to the function.
  
## 3.3 Defining functions
* JavaScript functions are usually defined by using a [function literal](https://en.wikipedia.org/wiki/Anonymous_function)
* JavaScript provides a couple of ways to define functions, which can be divided into four groups:
  * Function declarations and function expressions - `function myFun(){ return 1;}`
  * Arrow functions (often called lambda functions - `myArg => myArg*2`
  * Function constructors - `new Function('a', 'b', 'return a + b')`
  * Generator functions - `function* myGen(){ yield 1; }`

#### 3.3.1 Function declarations and function expressions
###### FUNCTION DECLARATIONS
```
// function declaration
function myFunctionName ( myFirstArg, mySecondArg ) {
  myStatement1;
  myStatement2;
}

// function defined within another function
function ninja() {
  function hiddenNinja() {
    return "ninja here";
  }
  return hiddenNinja();
}
```

###### FUNCTION EXPRESSIONS
* functions that are always a part of another statement (for example, as the right side of an assignment expression, or as an argument to another function) are called function expressions.
* function expressions are always a part of another statement.
* or as an argument to another function call, or as a function return value
* function declarations, the function name is mandatory, whereas for function expressions it’s completely optional.
```
// number literals
var a = 3;
myFunction(4);

// function literals
var a = function() {};
myFunction(function(){});
```

```
function myFunctionDeclaration() { // declaration
  function innerFunction() {} // declaration
}

var myFunc = function () {}; // expression
myFunc(function() { // expression as an argument
  return function() {}; // expression as an return value
});

(function namedFunctionExpression() {})(); // named expression as part of a function that will be immidiately invoked

+(function () {})(); // expressions that will be immidiately invoked, as arguments to unary operators
-(function () {})();
!(function () {})();
~(function () {})();
```

```
// function expression invocation
var doNothing = function(){};
doNothing();

function doSomething(action) {
  action();
}
```

###### IMMEDIATE FUNCTIONS
```
// Standard function call
myFunctionName(3);

// immediate call to a function expression
(function(){})(3); // note the parenthesis!!
```

#### 3.3.2 Arrow functions
* fat-arrow operator
```
var values = [0, 3, 2, 5, 7, 4, 8, 1]; 
values.sort(function(value1,value2){
  return value1 – value2; 
});

values.sort((value1,value2) => value1 – value2);

// simple
var greet = name => "Greetings " + name;

// block of code
var greet = name => {
  var helloString = 'Greetings ';
  return helloString + name;
};
```
* In this case, the return value of the arrow function behaves as in a standard function. If there’s no return statement, the result of the function invocation will be undefined, and if there is, the result will be the value of the return expression.

## 3.4 Arguments and function parameters
* A parameter is a variable that we list as part of a function definition.
* An argument is a value that we pass to the function when we invoke it.
```
function practice (ninja, weapon, technique) { ... }
practice ("Yoshi", "sword", "shadow sword", "katana"); // Excess arguments aren’t assigned to parameters.
practice ("Yoshi"); // undefined is assigned to parameter weapon. undefined is assigned to parameter technique.
```

#### 3.4.1 Rest parameters
* By prefixing the last-named argument of a function with an ellipsis (...), we turn it into an array called the rest parameters, which contains the remaining passed-in arguments.
* Only the last function parameter can be a rest parameter.
```
function multiMax(first, ...remainingNumbers){ ...
}
```

#### 3.4.2 Default parameters
```
// Tackling default parameters before ES6
function performAction(ninja, action){
  action = typeof action === "undefined" ? "skulking" : action;
  return ninja + " " + action;
}

// default parameters
function performAction(ninja, action = "skulking"){
   return ninja + " " + action;
}

assert(performAction("Fuma") === "Fuma skulking", "The default value is used for Fuma");
assert(performAction("Fuma", undefined) === "Fuma skulking", "The default value is used for Fuma");
assert(performAction("Yagyu", "sneaking") === "Yagyu sneaking", "Yagyu can do whatever he pleases, even sneak!");

// Referencing previous default parameters
function performAction(ninja, action = "skulking", message = ninja + " " + action) {
  return message;
}
```
