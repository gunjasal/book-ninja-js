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
#### 3.2.1 Storing functions
* allows us to easily manage related functions—for example, callbacks that have to be invoked when something of interest occurs.

#### 3.2.2
* allows the function to remember previously computed values, thereby improving the performance of subsequent invocations.

