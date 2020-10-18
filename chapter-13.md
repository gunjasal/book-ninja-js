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
* The implementation of an event loop should use at least one queue for macrotasks and at least one queue for microtasks. 
  * Event loop implementations usually go beyond that, and have several queues for different types of macro- and microtasks.
  * This enables the event loop to prioritize types of tasks; for example, giving priority to performance- sensitive tasks such as user input. 
  * On the other hand, because there are many browsers and JavaScript execution environments out in the wild, you shouldn’t be surprised if you run into event loops with only a single queue for both types of tasks together.
* The event loop is based on two fundamental principles:
  ```
  * Tasks are handled one at a time.
  * A task runs to completion and can’t be interrupted by another task.
  ```
* On a high level, figure 13.1 shows that in a single iteration, the event loop **first checks the macrotask queue**, and if there’s a macrotask waiting to be executed, starts its exe- cution. Only after the task is fully processed (or if there were no tasks in the queue), the event loop **moves onto processing the microtask queue**.
* Note the difference between handling the macrotask and microtask queues: In a single loop iteration, 
  * one macrotask at most is processed (others are left waiting in the queue), 
  * whereas all microtasks are processed.
* Now that we have a high-level understanding of the event loop, let’s check some of the interesting details shown in figure 13.1:
  * Both task queues are placed outside the event loop
  * Both types of tasks are executed one at a time
  * All microtasks should be executed before the next rendering
  * The browser usually tries to render the page 60 times per second, to achieve 60 frames per second (60 fps), a frame rate that’s often considered ideal for smooth motion, such as animations—meaning, the browser tries to render a frame every 16 ms.
  
#### 13.1.1 An example with only macrotasks
* ...
#### 13.1.2 An example with both macro- and microtasks
* ...
## 13.2 Taming timers: time-outs and intervals
* setTimeout, clearTimeout, setInterval, clearInterval
* It’s important to understand that a timer’s delay isn’t guaranteed. This has a great deal to do with the event loop, as we’ll see in the next section.
#### 13.2.1 Timer execution within the event loop
* You’ve already examined exactly what happens when an event occurs. But timers are different from standard events
  ```
  // Listing 13.3 Pseudocode for our time-out and interval demo
  <button id="myButton"></button>
  <script>
    setTimeout(function timeoutHandler() {
      /*Some timeout handle code that runs for 6ms*/
    }, 10);
    setInterval(function intervalHandler() {
      /*Some interval handle code that runs for 8ms*/
    }, 10);

    const myButton = document.getElementById("myButton");
    myButton.addEventListener("click", function clickHandler() {
      /*Some click handle code that runs for 10ms*/
    });

    /*Code that runs for 18ms*/
  </script>
  ```
* Timer events, just like input events (such as mouse events), are placed in the task queue. Note that both the timer and interval are initiated with a 10 ms delay, and that after this period, their matching tasks are placed in the task queue.
  * the tasks are added to the queue in the order in which the handlers are registered: first the time-out handler and then the inter- val handler.
* Unlike the setTimeout function, which expires only once, the setInterval func- tion fires until we explicitly clear it. So, at around 20 ms, another interval fires. Nor- mally, this would create a new task and add it to the task queue. But this time, because an instance of an interval task is already queued and awaiting execution, this invocation is dropped. `The browser won’t queue up more than one instance of a specific inter- val handler`.
* That’s why we were extra careful when saying that a timer provides the capability to asynchronously delay the execution of a piece of code by at least a certain number of milliseconds. Because of the single-threaded nature of JavaScript, we can control only when the timer task is added to the queue, and not when it’s finally executed!

###### DIFFERENCES BETWEEN TIME-OUTS AND INTERVALS
* the setTimeout variant of the code will always have at least a 10 ms delay after the previous callback execution (depending on the state of the event queue, it may end up being more, but never less), 
  * whereas setInterval will attempt to execute a call- back every 10 ms regardless of when the last callback was executed.
  ```
  setTimeout(function repeatMe() {
    /* Some long block of code... */ setTimeout(repeatMe, 10);
  }, 10);
  setInterval(() => {
    /* Some long block of code... */
  }, 10);
  ```
#### 13.2.2 Dealing with computationally expensive processing
* Because timers are capable of effectively suspending the execution of a piece of JavaScript until a later time, they can also break individual pieces of code into frag- ments that aren’t long enough to cause the browser to hang. Taking this into account, we can convert intensive loops and operations into nonblocking operations.
## 13.3 Working with events
* As within most other functions, within the event handler, we can use the this keyword. People often colloquially say that within an event handler, the this keyword refers to the object on which the event has occurred, but as we’ll soon find out, this isn’t exactly true. Instead, the this keyword refers to the element on which the event handler has been registered.

#### 13.3.1 Propagating events through the DOM
* In Netscape’s event model, the event handling starts with the top element and trickles down to the event target element. In our case, the event handlers would be executed in the following order: document click handler, outerContainer click han- dler, and finally innerContainer click handler. This is called event capturing.
* Microsoft chose to go the other way around: start from the targeted element and bubble up the DOM tree. In our case, the events would be executed in the following order: innerContainer click handler, outerContainer click handler, and document click handler. This is called `event bubbling`.
* The standard set by the W3 Consortium
  1. Capturing phase — An event is first captured at the top element and trickled down to the target element.
  2. Bubbling phase — After the target element has been reached in the capturing phase, the event handling switches to bubbling, and the event bubbles up again from the target element to the top element.
#### 13.3.2 Custom events
* Imagine a scenario in which you want to perform an action, but you want to trigger it under a variety of conditions from different pieces of code, perhaps even from code that’s in shared script files. A novice would repeat the code everywhere it’s needed. A journeyman would create a global function and call it from everywhere it’s needed. A ninja would use custom events. But why?
###### LOOSE COUPLING
* One of the advantages of event handlers is that we can establish as many as we want, and these handlers are completely independent of each other. So event handling is a good example of loose coupling.
###### AN AJAX-Y EXAMPLE
* ...
###### CREATING CUSTOM EVENTS
* ...
