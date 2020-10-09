# 11  Code modularization techniques
## 11.1  Code modularization techniques
* Pre-ES6 JavaScript has only two types of scopes: global scope and function scope. It doesn’t have something in between, a namespace or a module that would allow us to group certain functionality together.

#### 11.1.1 Using objects, closures, and immediate functions to specify modules
* Hiding module internals
  * As we already know, calling a JavaScript function cre- ates a new scope in which we can define variables that are visible only within the current function. So, one option for hiding module internals is using functions as modules. In this way, all function variables become internal module variables that are hidden from the outside world.
* Defining module interfaces
  * Implementing module internals through function variables means that those variables are accessible from only within the module. But if our modules are to be used from other code, we have to be able to define a clean interface through which we can expose the functionality offered by the module. One way of achieving this is by taking advantage of objects and closures. The idea is that, from our function module, we return an object that represents the public interface of our module. That object should contain methods offered by the module, methods that will, through closures, keep alive our internal mod- ule variables, even after our module function has finished its execution.
  
###### FUNCTIONS AS MODULES
```
(function countClicks() {
  let numClicks = 0;
  document.addEventListener("click", () => {
    alert(++numClicks);
  });
})();
```
###### THE MODULE PATTERN: AUGMENTING FUNCTIONS AS MODULES WITH OBJECTS AS INTERFACES
* The module interface is usually composed of a set of variables and functions that our module provides to the outside world. The easiest way to create such an interface is to use the humble JavaScript object.
```
// Listing 11.1 The module pattern
const MouseCounterModule = (function () {
  let numClicks = 0;
  const handleClick = () => {
    alert(++numClicks);
  };
  return {
    countClicks: () => {
      document.addEventListener("click", handleClick);
    },
  };
})();
assert(
  typeof MouseCounterModule.countClicks === "function",
  "We can access module functionality"
);
assert(
  typeof MouseCounterModule.numClicks === "undefined" &&
    typeof MouseCounterModule.handleClick === "undefined",
  "We cannot access internal module details"
);

```
* This pattern of using immediate functions, objects, and closures to create modules in JavaScript is called the module pattern.

###### AUGMENTING MODULES
```
// Listing 11.2 Augmenting modules
(function (module) {
  let numScrolls = 0;
  const handleScroll = () => {
    alert(++numScrolls);
  };
  module.countScrolls = () => {
    document.addEventListener("wheel", handleScroll);
  };
})(MouseCounterModule);

assert(
  typeof MouseCounterModule.countClicks === "function",
  "We can access initial module functionality"
);
assert(
  typeof MouseCounterModule.countScrolls === "function",
  "We can access augmented module functionality"
);
```
* Figure 11.3 When augmenting a module, we extend its external interface with new functionality, usually by passing the module to another immediate function. In this example, we add the ability to countScrolls to our MouseCounterModule. Notice that two separate functions are defined in dif- ferent environments, **and they can’t access each other’s internal variables.**
  * Module extensions, when performed through separate immediate functions, can’t share private module internals, because every function invo- cation creates a new scope. Although this is a shortcoming, it’s not a show- stopper, and we can still use the module pattern to keep our JavaScript applications modular.
* Unfortunately, there are more problems with the module pattern. When we start building modular applications, the modules themselves will often depend on other modules for their functionality. **Unfortunately, the module pattern doesn’t cover the management of these dependencies.**
  * To deal with these issues, a couple of competing standards have arisen, namely Asynchronous Module Definition (AMD) and CommonJS.
  
#### 11.1.2 Modularizing JavaScript applications with AMD and CommonJS
* the main difference is that AMD was designed explicitly with the browser in mind, whereas Com- monJS was designed for a general-purpose JavaScript environment (such as servers, with Node.js)

###### AMD
* Currently, the most popular AMD implementation is RequireJS (http://requirejs.org/).

#### CommonJS
* CommonJS uses file-based modules, so we can specify one module per file. 

## 11.2 ES6 modules
* Similar to CommonJS, ES6 modules have a relatively simple syntax, and ES6 modules are file based (one module per file).
* Similar to AMD, ES6 modules provide support for asynchronous module loading.

#### 11.2.1 Exporting and importing functionality
###### DEFAULT EXPORTS
###### RENAMING EXPORTS AND IMPORTS


######
######
