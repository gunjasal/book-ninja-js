# 8 Controlling access to objects
## 8.1 Controlling access to properties with getters and setters
#### 8.1.1 Defining getters and setters
* In JavaScript, getter and setter methods can be defined in two ways:
  * By specifying them within object literals or within ES6 class definitions
  * By using the built-in Object.defineProperty method
```
// Listing 8.2 Defining getters and setters in object literals
const ninjaCollection = {
  ninjas: ["Yoshi", "Kuma", "Hattori"],
  get firstNinja() {
    report("Getting firstNinja");
    return this.ninjas[0];
  },
  set firstNinja(value) {
    report("Setting firstNinja");
    this.ninjas[0] = value;
  },
};

assert(ninjaCollection.firstNinja === "Yoshi", "Yoshi is the first ninja");

ninjaCollection.firstNinja = "Hachi";
assert(
  ninjaCollection.firstNinja === "Hachi" && ninjaCollection.ninjas[0] === "Hachi",
  "Now Hachi is the first ninja"
);
```
```
// Listing 8.3 Using getters and setters with ES6 classes
class NinjaCollection {
  constructor() {
    this.ninjas = ["Yoshi", "Kuma", "Hattori"];
  }

  get firstNinja() {
    report("Getting firstNinja");
    return this.ninjas[0];
  }
  set firstNinja(value) {
    report("Setting firstNinja");
    this.ninjas[0] = value;
  }
}
```
```
// Listing 8.4 Defining getters and setters with Object.defineProperty
function Ninja() {
  let _skillLevel = 0;
  Object.defineProperty(this, "skillLevel", {
    get: () => {
      report("The get method is called");
      return _skillLevel;
    },
    set: (value) => {
      report("The set method is called");
      _skillLevel = value;
    },
  });
}
```

###### 

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
