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
<div id="subTree">
  <form>
    <input type="text"/>
  </form>
  <p>Paragraph</p>
  <span>Span</span>
</div>
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
#### 6.2.3 Communicating with a generator
* We can also send data to a generator, thereby achieving two-way communication! 

###### SENDING VALUES AS GENERATOR FUNCTION ARGUMENTS
* unlike standard functions, generators can even receive data after their execution has started, whenever we resume them by requesting the next value.

```
// Listing 6.8 Sending data to and receiving data from a generator
function* NinjaGenerator(action) {
  const imposter = yield "Hattori " + action;
  assert(imposter === "Hanzo", "The generator has been infiltrated");

  yield "Yoshi (" + imposter + ") " + action;
}

const ninjaIterator = NinjaGenerator("skulk");

const result1 = ninjaIterator.next();
assert(result1.value === "Hattori skulk", "Hattori is skulking");

const result2 = ninjaIterator.next("Hanzo");
assert(result2.value === "Yoshi (Hanzo) skulk", "We have an imposter!");
```

###### USING THE NEXT METHOD TO SEND VALUES INTO A GENERATOR
*  This passed-in value is used by the generator as the value of the whole yield expression, in which the generator was currently suspended, as shown in figure 6.3.
* We use yield to return data from a generator, and the iterator’s next() method to pass data back to the generator.

###### THROWING EXCEPTIONS
* This feature that enables us to throw exceptions back to generators might feel a bit strange at first. 
* we’ll use this feature to improve asyn- chronous server-side communication.
```
// Listing 6.9 Throwing exceptions to generators
function* NinjaGenerator() {
  try {
    yield "Hattori";
    fail("The expected exception didn't occur");
  } catch (e) {
    assert(e === "Catch this!", "Aha! We caught an exception");
  }
}
const ninjaIterator = NinjaGenerator();
const result1 = ninjaIterator.next();
assert(result1.value === "Hattori", "We got Hattori");
ninjaIterator.throw("Catch this!");
```

#### 6.2.4 Exploring generators under the hood
* a generator works almost like a small program, a state machine that moves between states:
  * `Suspended start` — When the generator is created, it starts in this state. None of the generator’s code is executed.
  * `Executing` — The state in which the code of the generator is executed. The execu- tion continues either from the beginning or from where the generator was last suspended. A generator moves to this state when the matching iterator’s next method is called, and there exists code to be executed.
  * `Suspended yield` — During execution, when a generator reaches a yield expres- sion, it creates a new object carrying the return value, yields it, and suspends its execution. This is the state in which the generator is paused and is waiting to continue its execution.
  * `Completed` — If during execution the generator either runs into a return state- ment or runs out of code to execute, the generator moves into this state.
  
###### TRACKING GENERATORS WITH EXECUTION CONTEXTS
* Figure 6.6 gives a snapshot at two positions in the application execution. 
* An interesting thing happens when the program execution leaves the generator, as shown in figure 6.7. Typically, when program execution returns from a standard func- tion, the matching execution context is popped from the stack and completely dis- carded. But this isn’t the case with generators.
  * The matching NinjaGenerator stack item is popped from the stack, but it’s not dis- carded, because the ninjaIterator keeps a reference to it. You can see it as an ana- logue to closures.
  * Generators, on the other hand, have to be able to resume their execution. Because the execution of all functions is handled by execution contexts, the iterator keeps a reference to its execution context, so that it’s alive for as long as the iterator needs it.
* Another interesting thing happens when we call the next method on the iterator: `const result1 = ninjaIterator.next();`
  * If this was a standard straightforward function call, this would cause the creation of a new next() execution context item, which would be placed on the stack
  * But as you might have noticed, generators are anything but standard, and a call to the next method of an iterator behaves a lot differently. It reactivates the matching execution context, in this case, the NinjaGenerator context, and places it on top of the stack, continuing the execution where it left off, as shown in figure 6.8.
  * But this time there’s no `yield` expression, and instead the program encounters a return statement. This returns the value Yoshi skulk and completes the generator’s execution by moving the generator into the Completed state.

## 6.3 Working with promises
#### 6.3.1 Understanding the problems with simple callbacks
#### 6.3.2 Diving into promises
* A promise is an object that serves as a placeholder for a result of an asynchronous task. 
```
// Listing 6.11 A closer look at promise order of execution
report("At code start");
var ninjaDelayedPromise = new Promise((resolve, reject) => {
  report("ninjaDelayedPromise executor");
  setTimeout(() => {
    report("Resolving ninjaDelayedPromise");
    resolve("Hattori");
  }, 500);
});

assert(ninjaDelayedPromise !== null, "After creating ninjaDelayedPromise");
ninjaDelayedPromise.then((ninja) => { // This callback will always be called asynchronously, regardless of the current state of the promise.
  assert(
    ninja === "Hattori",
    "ninjaDelayedPromise resolve handled with Hattori"
  );
});

const ninjaImmediatePromise = new Promise((resolve, reject) => {
  report("ninjaImmediatePromise executor. Immediate resolve.");
  resolve("Yoshi");
});

ninjaImmediatePromise.then((ninja) => {
  assert(ninja === "Yoshi", "ninjaImmediatePromise resolve handled with Yoshi");
});
report("At code end");
```

#### 6.3.3 Rejecting promises
#### 6.3.4 Creating our first real-world promise
#### 6.3.5 Chaining promises
###### CATCHING ERRORS IN CHAINED PROMISES
#### 6.3.6 Waiting for a number of promises
###### RACING PROMISES
```
// Listing 6.17 Waiting for a number of promises with Promise.all
Promise.all([
  getJSON("data/ninjas.json"),
  getJSON("data/mapInfo.json"),
  getJSON("data/plan.json"),
])
  .then((results) => {
    const ninjas = results[0],
      mapInfo = results[1],
      plan = results[2];
    assert(
      ninjas !== undefined && mapInfo !== undefined && plan !== undefined,
      "The plan is ready to be set in motion!"
    );
  })
  .catch((error) => {
    fail("A problem in carrying out our plan!");
  });
```

```
// Listing 6.18 Racing promises with Promise.race
Promise.race([
  getJSON("data/yoshi.json"),
  getJSON("data/hattori.json"),
  getJSON("data/hanzo.json"),
])
  .then((ninja) => {
    assert(ninja !== null, ninja.name + " responded first");
  })
  .catch((error) => fail("Failure!"));

```
## 6.4 Combining generators and promises
```
// Lisiting 6.19 Combining generators and promises
async(function* () {
  try {
    const ninjas = yield getJSON("data/ninjas.json");
    const missions = yield getJSON(ninjas[0].missionsUrl);
    const missionDescription = yield getJSON(missions[0].detailsUrl); //Study the mission details
  } catch (e) {
    //Oh no, we weren't able to get the mission details
  }
});

function async(generator) {
  var iterator = generator();

  function handle(iteratorResult) {
    if (iteratorResult.done) {
      return;
    }

    const iteratorValue = iteratorResult.value;
    if (iteratorValue instanceof Promise) {
      iteratorValue
        .then((res) => handle(iterator.next(res)))
        .catch((err) => iterator.throw(err));
    }
  }

  try {
    handle(iterator.next());
  } catch (e) {
    iterator.throw(e);
  }
}
```

```
getJSON("data/ninjas.json", (err, ninjas) => {
  if (err) {
    console.log("Error fetching ninjas", err);
    return;
  }
  getJSON(ninjas[0].missionsUrl, (err, missions) => {
    if (err) {
      console.log("Error locating ninja missions", err);
      return;
    }
    console.log(misssions);
  });
});

// vs

async(function* () {
  try {
    const ninjas = yield getJSON("data/ninjas.json");
    const missions = yield getJSON(ninjas[0].missionsUrl);
    //All information recieved
  } catch (e) {
    //An error has occurred
  }
});

// vs 

(async function () {
  try {
    const ninjas = await getJSON("data/ninjas.json");
    const missions = await getJSON(missions[0].missionsUrl);
    console.log(missions);
  } catch (e) {
    console.log("Error: ", e);
  }
})();
```
#### 6.4.1 Looking forward—the async function
