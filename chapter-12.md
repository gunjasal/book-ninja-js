# 12 Working with DOM
This chapter covers
```
■ Inserting HTML into the DOM
■ Understanding DOM attributes and DOM properties
■ Discovering computed styles
■ Dealing with layout thrashing
```
* developing web applications definitely doesn’t get any easier when we throw the browser’s Document Object Model (DOM) into the mix.
* Even presumably simple operations like cloneNode and removeChild have relatively complex implementations.
* This raises two questions -> The most compelling reason is _performance_.
  * Why is this code so complex?
  * Why do you need to understand how it works if the library will take care of it for you?
  
## 12.1 Injecting HTML into the DOM
* It can be technically challenging to implement this functionality correctly 
* Consider this example of 
```
// creating HTML elements from an HTML string that we can use with jQuery
$(document.body).append("<div><h1>Greetings</h1><p>Yoshi here</p></div>")

// And compare that with an approach that uses only the DOM API
const h1 = document.createElement("h1");
h1.textContent = "Greetings";

const p = document.createElement("p");
p.textContent = "Yoshi here";

const div = document.createElement("div");

div.appendChild(h1);
div.appendChild(p);
document.body.appendChild(div);
```
* As the first one looks better, we’ll implement our own way of doing clean DOM manipulation from scratch. The implementation requires the following steps:
  1. Convert an arbitrary but valid HTML string into a DOM structure.
  2. Inject that DOM structure into any location in the DOM as efficiently as possible.


#### 12.1.1 Converting HTML to DOM
* Converting an HTML string to a DOM structure uses a tool that you’re most likely already familiar with: the `innerHTML` property of DOM elements.
  1. Make sure that the HTML string contains valid HTML code.
  2. Wrap the string in any enclosing markup that’s required by browser rules.
  3. Insert the HTML string, using innerHTML, into a dummy DOM element.
  4. Extract the DOM nodes back out.

###### PREPROCESSING THE HTML SOURCE STRING
* To start, we’ll need to clean up the source HTML to meet our needs.
```
<option>Yoshi</option>  // problem #1 should be contained within a select element.
<option>Kuma</option>
<table/>               // problem #2 the self-closing works for only a small subset of elements (table not being one of them).
```
* Let’s start with solving the problem of self-closing elements.
```
// Listing 12.1 Making sure that self-closing elements are interpreted correctly
const tags = /^(area|base|br|col|embed|hr|img|input|keygen|link|menuitem|meta|param|source|track|wbr)$/i;

function convert(html) {
  return html.replace(/(<(\w+)[^>]*?)\/>/g, (all, front, tag) => {
    return tags.test(tag) ? all : front + "></" + tag + ">";
  });
}

assert(convert("<a/>") === "<a></a>", "Check anchor conversion.");
assert(convert("<hr/>") === "<hr/>", "Check hr conversion.");
```

###### HTML WRAPPING
* According to the semantics of HTML, some HTML elements must be within certain container elements before they can be injected. For example, an `<option>` element must be contained within a `<select>`.
* We can solve this problem in two ways, both of which require constructing a mapping between problematic elements and their containers:
  * The string could be injected directly into a specific parent by using `innerHTML`, where the parent has been previously constructed using the built-in document createElement. Although this may work in some cases and in some browsers, it isn’t universally guaranteed.
  * The string could be wrapped with the appropriate required markup and then injected directly into any container element (such as a `<div>`). This is more foolproof, but it’s also more work.
* The second technique is preferred; it involves little browser-specific code, in contrast to the first approach, which requires a fair amount of mostly browser-specific code.
  * The set of problematic elements that need to be wrapped in specific container ele- ments is fortunately a rather manageable seven.
  
####
####
######
######

## 12.2
####
####
####
######
######

##
####
####
####
######
######
