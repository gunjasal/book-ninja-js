# Building the page at runtime
## 2.1 The lifecycle overview
1. Page building — Set up the user interface.
2. Event handling — Enter a loop waiting for events to occur, and start invoking event handlers.

## 2.2 The page-building phase
1. Parsing the HTML and building the Document Object Model (DOM)
2. Executing JavaScript code
* Step 1 is performed when the browser is processing HTML nodes, and step 2 is per- formed whenever a special type of HTML element—the script element (that contains or refers to JavaScript code)—is encountered. During the page-building phase, the browser can switch between these two steps as many times as necessary, as shown in figure 2.3

#### 2.2.1 Parsing the HTML and building the DOM
* It’s important to emphasize that, although the HTML and the DOM are closely linked, with the DOM being constructed from HTML, they aren’t one and the same. You should think of the HTML code as a blueprint the browser follows when constructing the initial DOM—the UI—of the page. The browser can even fix problems that it finds with this blueprint in order to create a valid DOM.
> HTML specification and DOM specification
> The current version of HTML is HTML5, whose specification is available at https:// html.spec.whatwg.org/. If you need something more readable, we recommend Mozil- la’s HTML5 guide, available at https://developer.mozilla.org/en-US/docs/Web/ Guide/HTML/HTML5.

#### 2.2.2 Executing JavaScript code
* All JavaScript code contained in the script element is executed by the browser’s JavaScript engine; for example, Firefox’s Spidermonkey, Chrome and Opera’s V8, or Edge’s (IE’s) Chakra.
###### GLOBAL OBJECTS IN JAVASCRIPT
* The primary global object that the browser exposes to the JavaScript engine is the window object, which represents the window in which a page is contained
*  One of the most important properties of the global window object is the document, which represents the DOM of the current page. By using this object, the JavaScript code can alter the DOM of the page to any degree, by modifying or removing existing elements, and even by creating and inserting new ones.

###### DIFFERENT TYPES OF JAVASCRIPT CODE
* We broadly differentiate between two different types of JavaScript code: global code and function code.
  * Function code is the code contained in a function.
  * Global code is the code outside functions.
###### EXECUTING JAVASCRIPT CODE IN THE PAGE-BUILDING PHASE
* When the browser reaches the script node in the page-building phase, it pauses the DOM construction based on HTML code and starts executing JavaScript code instead.
* we can’t select and modify the ul element with the ID second, because that element is found after the current script node and hasn’t yet been reached and created. That’s one of the reasons people tend to put their script elements at the bottom of the page.
* _**All user-defined global variables created during the execution of JavaScript code in one script element are normally accessible to JavaScript code in other script elements.**_ This happens because the global window object, which stores all global JavaScript variables, is alive and accessible during the entire lifecycle of the page.

## 2.3 Event handling
#### 2.3.1 Event-handling overview
* The browser execution environment is, at its core, based on the idea that only a single piece of code can be executed at once: the so-called single-threaded execution model.
* Whenever an event occurs, the browser should execute the associated event-handler function. 
* To do this, the browser uses an event queue, as shown in figure 2.8.
* All generated events (it doesn’t matter if they’re user-generated, like mouse moves or key presses, or server-generated, such as Ajax events) are placed in the same event queue, in the order in which they’re detected by the browser. 
* _**It’s important to note that the browser mechanism that puts events onto the queue is external to the page-building and event-handling phases.**_
* _**The processing that’s nec- essary to determine when events have occurred and that pushes them onto the event queue doesn’t participate in the thread that’s handling the events.**_

###### EVENTS ARE ASYNCHRONOUS
* We say that the handling of events, and therefore the invocation of their handling functions, is asynchronous.

#### 2.3.2 Registering event handlers
* event handlers are functions that we want executed when- ever a particular event occurs.
* there are two ways to register events:
  * By assigning functions to special properties
  * By using the built-in addEventListener method

#### 2.3.3 Handling events
* As we’ve already mentioned, due to the single-threaded exe- cution model, only a single event handler can be executed at once. Any following events are processed only after the execution of the current event handler is fully complete!

```
<!DOCTYPE html>
<html>
  <head>
    <title>Web app lifecycle</title>
    <style>
      #first { color: green;}
      #second { color: red;}
    </style>
  </head>
  <body>
    <ul id="title"></ul>
    <ul id="first"></ul>
    
    <script>
      function addMessage(element, message){
        var messageElement = document.createElement("li"); 
        messageElement.textContent = message;
        element.appendChild(messageElement);
      }
      
      var first = document.getElementById("first");
      addMessage(first, "Page loaded");
    //   setTimeout(function(){ alert("Hello"); }, 3000);
      setTimeout(() => addMessage(first, "set timeout finished"), 3000);
    </script>
    
    <ul id="second"></ul>
    
    <script>
      document.body.addEventListener("mousemove", function() {
        var second = document.getElementById("second");
        addMessage(second, "Event: mousemove");
      });

      document.body.addEventListener("click", function(){
        var second = document.getElementById("second");
        addMessage(second, "Event: click");
      });
    </script>
  </body>
</html>
```
