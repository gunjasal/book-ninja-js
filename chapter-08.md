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
#### 8.1.2 Using getters and setters to validate property values
```
function Ninja() {
  let _skillLevel = 0;
  Object.defineProperty(this, "skillLevel", {
    get: () => _skillLevel,
    set: (value) => {
      if (!Number.isInteger(value)) {
        throw new TypeError("Skill level should be a number");
      }
      _skillLevel = value;
    },
  });
}
```
#### 8.1.3 Using getters and setters to define computed properties
```
// Listing 8.6 Defining computed properties
const shogun = {
  name: "Yoshiaki",
  clan: "Ashikaga",
  get fullTitle() {
    return this.name + " " + this.clan;
  },
  set fullTitle(value) {
    const segments = value.split(" ");
    this.name = segments[0];
    this.clan = segments[1];
  },
};
```

## 8.2 Using proxies to control access
* A proxy is a surrogate through which we control access to another object.
* We can use proxies when we’d traditionally use getters and setters, such as for log- ging, data validation, and computed properties. 
```
// Listing 8.7 Creating proxies with the Proxy constructor
const emperor = { name: "Komei" };
const representative = new Proxy(emperor, {
  get: (target, key) => {
    report("Reading " + key + " through a proxy");
    return key in target ? target[key] : "Don't bother the emperor!";
  },
  set: (target, key, value) => {
    report("Writing " + key + " through a proxy");
    target[key] = value;
  },
});

assert(emperor.name === "Komei", "The emperor's name is Komei");
assert(
  representative.name === "Komei",
  "We can get the name property through a proxy"
);
assert(
  emperor.nickname === undefined,
  "The emperor doesn’t have a nickname "
);

assert(
  representative.nickname === "Don't bother the emperor!",
  "The proxy jumps in when we make inproper requests"
);
representative.nickname = "Tenno";
assert(emperor.nickname === "Tenno", "The emperor now has a nickname");
assert(
  representative.nickname === "Tenno",
  "The nickname is also accessible through the proxy"
);
```
* In this example, we’ve used the get and set traps, but many other built-in traps allow us to define handlers for various object actions 
  * The `apply` trap will be activated when calling a function, and the `construct` trap when using the new operator.
  * The `get` and `set` traps will be activated when reading/writing to a property.
  * The `enumerate` trap will be activated for `for-in` statements.
  * `getPrototypeOf` and `setPrototypeOf` will be activated for getting and setting the prototype value.
  
#### 8.2.1 Using proxies for logging
```
// Listing 8.9 Using proxies makes it easier to add logging to objects
function makeLoggable(target) {
  return new Proxy(target, {
    get: (target, property) => {
      report("Reading " + property);
      return target[property];
    },
    set: (target, property, value) => {
      report("Writing value " + value + " to " + property);
      target[property] = value;
    },
  });
}

let ninja = { name: "Yoshi" };
ninja = makeLoggable(ninja);

assert(ninja.name === "Yoshi", "Our ninja Yoshi");
ninja.weapon = "sword";
```
#### 8.2.2 Using proxies for measuring performance
```
// Listing 8.10 Measuring performance with proxies
function isPrime(number) {
  if (number < 2) {
    return false;
  }
  for (let i = 2; i < number; i++) {
    if (number % i === 0) {
      return false;
    }
  }
  return true;
}

isPrime = new Proxy(isPrime, {
  // Provides an apply trap that will be called whenever a proxy is called as a function
  apply: (target, thisArg, args) => { 
    console.time("isPrime");
    const result = target.apply(thisArg, args);
    console.timeEnd("isPrime");
    return result;
  },
});
isPrime(1299827);
```
#### 8.2.3 Using proxies to autopopulate properties
```
// Listing 8.11 Autopopulating properties with proxies
function Folder() {
  return new Proxy(
    {},
    {
      get: (target, property) => {
        report("Reading " + property);
        if (!(property in target)) {
          target[property] = new Folder();
        }
        return target[property];
      },
    }
  );
}
const rootFolder = new Folder();

try {
  rootFolder.ninjasDir.firstNinjaDir.ninjaFile = "yoshi.txt";
  pass("An exception wasn’t raised");
} catch (e) {
  fail("An exception has occurred");
}
```

#### 8.2.4 Using proxies to implement negative array indexes
```
// Listing 8.12 Negative array indexes with proxies
function createNegativeArrayProxy(array) {
  if (!Array.isArray(array)) {
    throw new TypeError("Expected an array");
  }
  return new Proxy(array, {
    get: (target, index) => {
      index = +index;
      return target[index < 0 ? target.length + index : index];
    },
    set: (target, index, val) => {
      index = +index;
      return (target[index < 0 ? target.length + index : index] = val);
    },
  });
}
```

#### 8.2.5 Performance costs of proxies
* The fact that all our operations have to pass in through the proxy adds a layer of indirection that enables us to implement all these cool features, but at the same time it introduces a significant amount of additional processing that impacts performance.
* It turns out that in Chrome, proxies are around 50 times slower; in Firefox, they’re about 20 times slower.
