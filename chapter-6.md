# 6 Functions for the future: generators and promises
* `Generators` are a special type of function. Whereas a standard function produces at most a single value while running its code from start to finish, generators produce multiple values, on a per request basis, while suspending their execution between these requests
* `Promises`, on the other hand, are a new, built-in type of object that help you work with asynchronous code. A promise is a placeholder for a value that we don’t have yet but will at some later point. They’re especially good for working with multiple asynchronous steps.

## 6.1 Making our async code elegant with generators and promises
* This code has a big problem. Getting data from a server is a long-running operation, and because JavaScript relies on a single-threaded execution model, we’ve just blocked our UI until the long-running operations finish.
```
try {
  var ninjas = syncGetJSON("ninjas.json");
  var missions = syncGetJSON(ninjas[0].missionsUrl);
  var missionDetails = syncGetJSON(missions[0].detailsUrl); //Study the mission description
} catch (e) {
  //Oh no, we weren't able to get the mission details
}
```
* To solve this problem, we can rewrite it with callbacks, which will be invoked when a task finishes, without blocking the UI: [is it really solved?](./chapter-6-callback.html)
```
// it’s messy, it adds a lot of boilerplate error-handling code, and it’s plain ugly. 
getJSON("ninjas.json", function (err, ninjas) {
  if (err) {
    console.log("Error fetching list of ninjas", err);
    return;
  }
  getJSON(ninjas[0].missionsUrl, function (err, missions) {
    if (err) {
      console.log("Error locating ninja missions", err);
      return;
    }
    getJSON(missions[0].detailsUrl, function (err, missionDetails) {
      if (err) {
        console.log("Error locating mission details", err);
        return;
      }
      //Study the intel plan
    });
  });
});

```
## 6.2 Working with generator functions
* A generator is a function that generates a sequence of values, but not all at once, as a standard function would, but on a per request basis.
* after a value is produced, a generator function doesn’t end its exe- cution, as a normal function would. Instead, a generator is merely suspended. Then, when a request for another value comes along, the generator resumes where it left off.
```
// Listing 6.1 Using a generator function to generate a sequence of values
function* WeaponGenerator(){
  yield "Katana";
  yield "Wakizashi";
  yield "Kusarigama";
}

for(let weapon of WeaponGenerator()) {
  assert(weapon !== undefined, weapon);
}

// const iter = WeaponGenerator()
// iter.next();
// iter.next();
// iter.next();
```

#### 6.2.1 Controlling the generator through the iterator object
* Making a call to a generator doesn’t mean that the body of the generator function will be executed. Instead, an iterator object is created, an object through which we can com- municate with the generator.

###### ITERATING THE ITERATOR
```
// Listing 6.3 Iterating over generator results with a while loop
function* WeaponGenerator() {
  yield "Katana";
  yield "Wakizashi";
}

const weaponsIterator = WeaponGenerator();
let item;
while (!(item = weaponsIterator.next()).done) {
  assert(item !== null, item.value);
}
```
###### YIELDING TO ANOTHER GENERATOR
```
// Listing 6.4 Using yield* to delegate to another generator
function* WarriorGenerator() {
  yield "Sun Tzu";
  yield* NinjaGenerator();
  yield "Genghis Khan";
}
function* NinjaGenerator() {
  yield "Hattori";
  yield "Yoshi";
}
for (let warrior of WarriorGenerator()) {
  assert(warrior !== null, warrior);
}

// Sun Tzu, Hattori, Yoshi, Genghis Khan.
```

#### 6.2.2 Using generators
###### USING GENERATORS TO GENERATE IDS
```
// Listing 6.5 Using generators for generating IDs
function* IdGenerator() {
  let id = 0;
  while (true) {
    yield ++id;
  }
}

const idIterator = IdGenerator();
const ninja1 = { id: idIterator.next().value };
const ninja2 = { id: idIterator.next().value };
const ninja3 = { id: idIterator.next().value };

assert(ninja1.id === 1, "First ninja has id 1");
assert(ninja2.id === 2, "Second ninja has id 2");
assert(ninja3.id === 3, "Third ninja has id 3");
```
* Writing infinite loops isn’t something that we generally want to do in a standard function. But with generators, everything is fine! 
* Whenever the gen- erator encounters a yield statement, the generator execution is suspended until the next method is called again.

###### USING GENERATORS TO TRAVERSE THE DOM
```
// Listing 6.6 Recursive DOM traversal
<div id="subTree">
  <form>
    <input type="text" />
  </form>
  <p>Paragraph</p>
  <span>Span</span>
</div>

<script>
  function traverseDOM(element, callback) {
    callback(element);
    element = element.firstElementChild;
    while (element) {
      traverseDOM(element, callback);
      element = element.nextElementSibling;
    }
  }
  
  const subTree = document.getElementById("subTree");
  traverseDOM(subTree, function (element) {
    assert(element !== null, element.nodeName);
  });
</script>
```

```
// Listing 6.7 Iterating over a DOM tree with generators
<script>
  function* DomTraversal(element) {
    yield element;
    element = element.firstElementChild;
    while (element) {
      yield* DomTraversal(element);
      element = element.nextElementSibling;
    }
  }
  const subTree = document.getElementById("subTree");
  for (let element of DomTraversal(subTree)) {
    assert(element !== null, element.nodeName);
  }
</script>

```
## 
####
######

## 
####
######
