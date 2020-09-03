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
* To solve this problem, we can rewrite it with callbacks, which will be invoked when a task finishes, without blocking the UI: ![is it really solved?](./chapter-6-callback.html)
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
