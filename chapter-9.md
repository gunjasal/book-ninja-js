# 9 Dealing with collections
## 9.1 Arrays

#### 9.1.1 Creating arrays
#### 9.1.2 Adding and removing items at either end of an array
* `push` adds an item to the end of the array.
* `unshift` adds an item to the beginning of the array.
* `pop` removes an item from the end of the array.
* `shift` removes an item from the beginning of the array.
#### 9.1.3 Adding and removing items at any array location
* `splice`
#### 9.1.4 Common operations on arrays
* `Iterating` (or traversing) through arrays
* `Mapping` existing array items to create a new array based on them
* `Testing` array items to check whether they satisfy certain conditions
* `Finding` specific array items
* `Aggregating` arrays and computing a single value based on array items (for example, calculating the sum of an array)
```
// Listing 9.5 Using the forEach method
const ninjas = ["Yagyu", "Kuma", "Hattori"];
ninjas.forEach((ninja) => {
  assert(ninja !== null, ninja);
});

// Listing 9.7 Mapping an array
const weapons = ninjas.map(ninja => ninja.weapon);

// Listing 9.8 Testing arrays with the every and some methods
const ninjas = [
  {name: "Yagyu", weapon: "shuriken"},
  {name: "Yoshi" },
  {name: "Kuma", weapon: "wakizashi"}
];
const allNinjasAreNamed = ninjas.every(ninja => "name" in ninja);
const someNinjasAreArmed = ninjas.some(ninja => "weapon" in ninja);

// Listing 9.9 Finding array items
const ninjaWithWakizashi = ninjas.find(ninja => {
  return ninja.weapon === "wakizashi";
});
const armedNinjas = ninjas.filter(ninja => "weapon" in ninja);

// Listing 9.12 Aggregating items with reduce
const numbers = [1, 2, 3, 4];
const sum = numbers.reduce((aggregated, number) => aggregated + number, 0);
```
#### 9.1.5 Reusing built-in array functions
```
// Listing 9.13 Simulating array-like methods
<body>
  <input id="first" />
  <input id="second" />
  <script>
    const elems = {
      length: 0,
      add: function (elem) {
        Array.prototype.push.call(this, elem);
      },
      gather: function (id) {
        this.add(document.getElementById(id));
      },
      find: function (callback) {
        return Array.prototype.find.call(this, callback);
      },
    };
    elems.gather("first");
    assert(
      elems.length === 1 && elems[0].nodeType,
      "Verify that we have an element in our stash"
    );
    elems.gather("second");
    assert(
      elems.length === 2 && elems[1].nodeType,
      "Verify the other insertion"
    );
    elems.find((elem) => elem.id === "second");
    assert(found && found.id === "second", "We've found our element");
  </script>
</body>
```

## 9.2 Maps
#### 9.2.1 Don’t use objects as maps
```
//Listing 9.14 Objects have access to properties that weren’t explicitly defined
const dictionary = {
  ja: {
    "Ninjas for hire": "レンタル用の忍者",
  },
  zh: {
    "Ninjas for hire": "忍者出租",
  },
  ko: {
    "Ninjas for hire": " 고용 닌자 ",
  },
};
assert(
  dictionary.ja["Ninjas for hire"] === "レンタル用の忍者",
  "We know how to say 'Ninjas for hire' in Japanese!"
);
assert(
  typeof dictionary.ja["constructor"] === "undefined",
  dictionary.ja["constructor"] // fails -> function Object() { [native code] }
);
```
* As you learned in chapter 7, all objects have prototypes; even if we define new, empty objects as our maps, they still have access to the properties of the prototype objects. One of those properties is constructor (recall that constructor is the property of the prototype object that points back to the constructor function), and it’s the culprit behind the mess we now have on our hands.
* In addition, with objects, keys can only be string values; if you want to create a map- ping for any other value, that value will be silently converted into a string without any- one asking you anything!

> JUST READ TO THE END OF THE CHAPTER
