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
*  In JavaScript, inheritance works slightly differently than in other popular object-oriented languages. 
```
// Listing 7.8 Achieving inheritance with prototypes
function Person() {}
Person.prototype.dance = function () {};

function Ninja() {}
Ninja.prototype = new Person();

const ninja = new Ninja();
assert(
  ninja instanceof Ninja,
  "ninja receives functionality from the Ninja prototype"
);
assert(ninja instanceof Person, "... and the Person prototype");
assert(ninja instanceof Object, "... and the Object prototype");
```
* The best technique for creating such a prototype chain is to use an instance of an object as the other object’s prototype:
  * For example: `Ninja.prototype = new Person();`
 
#### 7.3.1 The problem of overriding the constructor property
* If we take a closer look at figure 7.14, we’ll see that by setting the new Person object as a prototype of the Ninja constructor, we’ve lost our connection to the Ninja construc- tor that was previously kept by the original Ninja prototype.
  * This is a problem, because the constructor property can be used to determine the function with which the object was created.

###### CONFIGURING OBJECT PROPERTIES
* In JavaScript, every object property is described with a property descriptor through which we can configure the following keys:
```
// Listing 7.9 Configuring properties
var ninja = {};
ninja.name = "Yoshi";
ninja.weapon = "kusarigama";

Object.defineProperty(ninja, "sneaky", {
  configurable: false,
  enumerable: false,
  value: true,
  writable: true,
});

assert("sneaky" in ninja, "We can access the new property");
for (let prop in ninja) {
  assert(prop !== undefined, "An enumerated property: " + prop);
}
```
###### FINALLY SOLVING THE PROBLEM OF OVERRIDING THE CONSTRUCTOR PROPERTY
```
// Listing 7.10 Fixing the constructor property problem
function Person() {}
Person.prototype.dance = function () {};

function Ninja() {}
Ninja.prototype = new Person();

Object.defineProperty(Ninja.prototype, "constructor", {
  enumerable: false,
  value: Ninja,
  writable: true,
});

const ninja = new Ninja();

assert(
  ninja.constructor === Ninja,
  "Connection from ninja instances to Ninja constructor reestablished!"
);
for (let prop in Ninja.prototype) {
  assert(prop === "dance", "The only enumerable property is dance!");
}
```

##
####
######
