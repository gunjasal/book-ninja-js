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
    assert(outerValue === "samurai", "I can see the samurai."); assert(innerValue === "ninja", "I can see the ninja.")
  }
  later = innerFunction;
}

outerFunction();
later();
```
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

##
####
######
