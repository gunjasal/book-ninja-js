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
####
######
######
