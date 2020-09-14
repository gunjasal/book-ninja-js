# 7. Object orientation with prototypes
* A `prototype` is an object to which the search for a particular property can be dele- gated to. 
* Prototypes serve a similar purpose to that of classes in classical object-oriented languages.

## 7.1 Understanding prototypes
* objects are collections of named properties with values.
```
let obj = {
  prop1: 1, // value
  prop2: function () {}, // function
  prop3: {}, // other object
};
```
* In JavaScript, inheri- tance is implemented with prototyping.
* Every object can have a reference to its `prototype`, an object to which the search for a particular property can be delegated to, if the object itself doesn’t have that property.

## 7.2 Object construction and prototypes
*  Like object-oriented languages such as Java and C++, JavaScript employs the new operator to instantiate new objects via constructors, but there’s no true class definition in JavaScript.
* Instead, the new operator, applied to a constructor function (as you saw in chapter 3), triggers the creation of a newly allocated object.
* Figure 7.4, As you can see, a function, when created, gets a new object that’s assigned to its proto- type property. The prototype object initially has only one property, constructor, that references back to the function (we’ll revisit the constructor property later).

#### 7.2.1 Instance properties
* instance members will hide properties of the same name defined in the prototype. See figure 7.5.
* Figure 7.5 If a property can be found on the instance itself, the prototype isn’t even consulted!
* Figure 7.6 Every instance gets its own version of the properties created within the constructor, but they all have access to the same prototype’s properties.
  * In this example, we’d have three versions of the swingSword method that all per- form the same logic. Because each method copy behaves the same, creating multiple copies often doesn’t make sense, because it only consumes more memory.
  * From that perspective, it makes sense to place object methods only on the function’s prototype, because in that way we have a single method shared by all object instances.

#### 7.2.2 Side effects of the dynamic nature of JavaScript
* You’ve already seen that JavaScript is a dynamic language in which properties can be easily added, removed, and modified at will. The same thing holds for prototypes, both function prototypes and object prototypes. 
```
// Listing 7.4 With prototypes, everything can be changed at runtime
const ninja1 = new Ninja();
Ninja.prototype.swingSword = function () {
  return this.swung;
};
assert(ninja1.swingSword(), "Method exists, even out of order");

Ninja.prototype = {
  pierce: function () {
    return true;
  },
};

assert(ninja1.swingSword(), "Our ninja can still swing!");

const ninja2 = new Ninja();
assert(ninja2.pierce(), "Newly created ninjas can pierce");
assert(!ninja2.swingSword, "But they cannot swing!");
```
* Figure 7.7 After construction, ninja1 has the property swung, and its pro- totype is the Ninja prototype that has only a constructor property.
* Figure 7.8 Because the ninja1 instance references the Ninja prototype, even changes made after the instance was constructed are accessible.
* Figure 7.9 The function’s prototype can be replaced at will. The already constructed instances reference the old prototype!
* Figure 7.10 All newly created instances reference the new prototype.

#### 7.2.3 Object typing via constructors
* Figure 7.11 The prototype object of each function has a constructor property that references the func- tion.
* By using the constructor property, we can access the function that was used to create the object.
```
// Listing 7.6 Instantiating a new object using a reference to a constructor
function Ninja() {}
const ninja = new Ninja();
const ninja2 = new ninja.constructor();
assert(ninja2 instanceof Ninja, "It's a Ninja!");
assert(ninja !== ninja2, "But not the same Ninja!");
```

## 7.3 Achieving inheritance

####
######

##
####
######
