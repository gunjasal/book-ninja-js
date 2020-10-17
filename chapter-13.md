# 13 Surviving Events
* Chapter 2 included a short discussion on the JavaScript single-threaded execution model and introduced the `event loop` and the `event queue`, in which events wait for their turn to be processed. 
## 13.1 Diving into the event loop
* As you might have figured out, the event loop is more complicated than its presenta- tion in chapter 2. 
* For starters, instead of a single event queue, which holds only events, the event loop has at least two queues that, in addition to events, hold other actions performed by the browser. 
  * These actions are called tasks and are grouped into two categories: `macrotasks` (or often just called tasks) and `microtasks`.
* Examples of `macrotasks` include 
  * creating the main document object, 
  * parsing HTML, 
  * executing mainline (or global) JavaScript code, 
  * changing the current URL, 
  * as well as various events such as page loading, input, network events, and timer events. 
  * From the browser’s perspective, a macrotask represents one discrete, self-contained unit of work. 
    * After running a task, the browser can continue with other assignments such as re-rendering the UI of the page, or performing garbage collection.
* `Microtasks`, on the other hand, are 
  * smaller tasks that update the application state and 
  * should be executed before the browser continues with other assignments such as re-rendering the UI. 
  * Examples include promise callbacks and DOM mutation changes. 
  * Microtasks should be executed as soon as possible, in an asynchronous way, but with- out the cost of executing a whole new macrotask. 
    * Microtasks enable us to execute cer- tain actions before the UI is re-rendered, thereby avoiding unnecessary UI rendering that might show inconsistent application state.
####
####
####
######
######
