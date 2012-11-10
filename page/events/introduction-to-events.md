---
title:        Introducing Events
level: beginner
source: http://jqfundamentals.com/legacy
attribution: 
  - jQuery Fundamentals
---
Web pages are all about interaction. Users perform a countless number of actions such as moving a mouse pointer over a page, clicking on an element, and typing in textboxes &#8212; all of these are examples of events. In addition to these user events, there are a slew of events that occur &#8212; like when the page is loaded, when video begins playing or is paused, and so on. An event is fired whenever something interesting occurs on the page, meaning that the browser basically announces that something has happened. It's this announcement that allows developers to "listen" for events and react to them appropriately.

## DOM Events

There are a myriad of event types, but perhaps the ones that are easiest to understand are user events, such as when someone clicks on an element or types into a form. These types of events occur on an element, so when a user clicks on a button for example, the button has had an event occur on it. While user interactions aren't the only types of DOM events, they're certainly the easiest to understand when starting out. MDN has a good reference of [available DOM events](https://developer.mozilla.org/en/DOM/DOM_event_reference).

## Ways to Listen for Events

There are many ways to listen for events. Events are constantly occurring on a given webpage, but the developer is only notified about these events if they're *listening* for them. Listening for an event basically means waiting for the browser to tell you that a specific event has occurred, so you can specify how the page should react.

### Event Handlers

Event handlers are functions that specify to the browser what to do when an event occurs. This function is executed whenever the event occurs (or until the event is unbound).

For instance, to alert a message whenever a user clicks on a button, the code might look something like this:

```
<button onClick="alert('Hello')">Say hello</button>
```

The event we want to listen to is specified by the button's `onClick` attribute, and the event handler is the `alert()` function that alerts "Hello" to the user. While this works, it's an abysmal way to achieve this functionality for a couple of reasons:

1. This couples the view code (HTML) with the interaction code (JS). That means whenever the functionality needs updating, the HTML would have to be edited. This is a bad practice and a maintenance nightmare.
2. It's not scalable. Attaching this functionality onto numerous buttons would bloat the page with a bunch of repetitious code and destroy maintainability.

Utilizing inline event handlers like this can be considered *obtrusive JavaScript,* but it's opposite, *unobtrustive JavaScript* is a much more common way of discussing the topic. The notion of *unobtrusive JavaScript* is that your HTML and JS are kept separate and are therefore more maintainable. Separation of concerns is important because it keeps like pieces of code together (i.e. HTML, JS, CSS) and unlike pieces of code apart, facilitating changes and enhancements. Furthermore, unobtrustive JavaScript stresses the importance of adding the least amount of cruft to a page as possible. If a user's browser doesn't support JavaScript, then it shouldn't be intertwined with the markup of the page. 

To prevent naming collisions, JS code should utilize a single namespace for different pieces of functionality or libraries. jQuery is a good example of this, in that the `jQuery` object/constructor (and also the `$` alias to `jQuery`) only utilizes a single global variable, and all of jQuery's functionality is packaged into that one object.

The below example shows how to accomplish the desired task unobtrusively by removing the `onclick` attribute and replacing it with an `id`, which is used to "hook onto" the button from within a separate script file.

```
<button id="helloBtn">Say hello</button>
```

```
//Event binding using addEventListener
var helloBtn = document.getElementById("helloBtn");

helloBtn.addEventListener( "click", function(event) {

  alert("Hello.");

}, false );
```

This saves a reference to the button element by calling `getElementById` and assigning its return value to a variable. `addEventListener` is then called with an event handler function that will be called whenever the event occurs. While there's nothing wrong with this code as it will work fine in modern browsers, it won't fare well in versions of IE prior to IE9. This is because Microsoft chose to implement a different method, `attachEvent`, as opposed to the W3C standard `addEventListener`, and didn't change it until IE9 was released. This is another example of Query is beneficial &#8212; it abstracts away browser inconsistencies, allowing developers to use a single API for these types of tasks.

```
//Event binding using a convenience method
$("#helloBtn").click(function(event) {

  alert("Hello.");

});
```

The `$("#helloBtn")` code selects the button element using the `$` function and returns a jQuery object. The jQuery object has a bunch of methods available to it such as `.click()`, which resides in the jQuery object's prototype. Visit [The jQuery Object](/using-jquery-core/jquery-object) section for more information about the jQuery object.

In the above example, the `.click()` method is called on the jQuery object and gets passed an anonymous function event handler that's going to be executed when a user clicks the button, alerting "Hello." to the user.

There are a number of ways that events can be listened for using jQuery:

```
//The many ways to bind events with jQuery
// Attach an event handler directly to the button using jQuery's
// shorthand `click` method.
$("#helloBtn").click(function(event) {

  alert("Hello.");

});

// Attach an event handler directly the to button using jQuery's
// `bind` method, passing it an event string of `click`
$("#helloBtn").bind( "click", function(event) {

  alert("Hello.");

});

// As of jQuery 1.7, attach an event handler directly to the button
// using jQuery's `on` method.
$("#helloBtn").on( "click", function(event) {

  alert("Hello.");

});

// As of jQuery 1.7, attach an event handler to the `body` element that
// is listening for clicks, and will respond whenever *any* button is
// clicked on the page.
$("body").on({
  click: function(event) {

    alert("Hello.");

  }, "button"
);

// An alternative to the previous example, using slightly different syntax.
$("body").on( "click", "button", function(event) {

  alert("Hello.");

});
```

As of jQuery 1.7, all events are bound via the `.on()` method, whether it's called directly or a shortcut method is used such as `.bind()` or `.click()`, which are mapped to the `.on()` method internally. With this in mind, it's beneficial to use the `.on()` method because the others are all just syntactic sugar, and utilizing the `.on()` method is going to result in faster and more consistent code.

In the first example of `.on()` above, a string of `.click()` is passed as the first argument to the `.on()` method, and an anonymous function is passed as the second. This looks a lot like the `.bind()` method before it. An event handler is attached directly to `#helloBtn`. If there were any other buttons on the page, they wouldn't alert "Hello." when clicked because the event is only attached to `#helloBtn`.

In the second `.on()` example, an object (denoted by the curly braces `{}`) gets passed that has a property of `.click()` whose value is an anonymous function. The second argument to the `.on()` method is a jQuery selector string of `button`. While examples 1–3 above are functionally equivalent, example 4 is different in that the `<body>` element is listening for click events that occur on *any* button element, not just `#helloBtn`. 

The final example above is exactly the same as the one preceding it, but instead of passing an object, an event string, selector string, and the callback are passed. Both of these are examples of event delegation, a process by which an element higher in the DOM tree listens for events occuring on its children.

### Event Delegation

Event delegation works because of the notion of *event bubbling*. For most events, whenever something occurs on a page (like when an element is clicked), the event travels from the element it occurred on, up to its parent, then up to the parent's parent, and so on, until it reaches the root element, also known as the `window`. While event bubbling and delegation work well, the delegating element should always be as close to the delegatees as possible so the event doesn't have to travel way up the DOM tree before its handler function is called.

The two main pros of event delegation over binding directly to an element (or set of elements) are performance and the aforementioned event bubbling. Imagine having a large table of 1000 cells and binding to an event for each cell. That's 1000 separate event handlers that the browser has to attach, even if they're all mapped to the same function. Instead of binding to each individual cell, use delegation to listen for events that occur on the parent table and react accordingly. One event would be bound instead of 1000, resulting in far better performance and memory management.

Event bubbling affords us the ability to add cells via [AJAX](/ajax), without having to bind events directly to those cells since the parent table is listening for clicks and is therefore notified of clicks on its children. Without delegation, developers would have to constantly bind events for every cell that's added &#8212; a performance issue and potentially a maintenance nightmare.

## The Event Object

```
//Binding a named function
function sayHello( event ) {

  alert("Hello.");

}

$("#helloBtn").on( "click", sayHello );
```

In the slightly different example above, the function `sayHello()` is defined and then passed into the `.on()` method instead of an anonymous function. It's important to realize that you can also pass defined functions as event handlers as well. Named functions are important if different elements or different events should perform the same functionality.

In all DOM event callbacks, jQuery passes an *event object* argument that contains information about the event, such as precisely when and where it occurred, what type of event it was, which element the event occured on, and a plethora of other information. Of course you don't have to call it `event`; you could call it `e` or whatever you want, but `event` is a common convention.

If the element has default functionality for a specific event (like a link opens a new page, a button in a form submits the form, etc.;), that default functionality can be cancelled. This is often useful for AJAX requests. When a user clicks on a button to submit a form via AJAX, you would want to cancel the button/form's default action (which is to submit it to the form's `action` attribute), and would instead do an AJAX request to accomplish the same task for a more seamless experience. To do this, utilize the event object and call its `.preventDefault()` method. Another way to prevent the event from bubbling up the DOM tree is using `.stopPropagation()` so that parent elements aren't notified of its occurrence (in the case that event delegation is being used).

```
//Preventing a default action from occurring and stopping the event bubbling
$("form").on( "submit", function(event) {

  event.preventDefault(); // Prevent the form"s default submission.

  event.stopPropagation(); // Prevent event from bubbling up DOM tree,
                           // prohibiting delegation

  // Make an AJAX request to submit the form data

});
```

When utilizing both `.preventDefault()` and `.stopPropagation()` simultaneously, you can instead `return false;` to achieve both in a more concise manner, but it's advisable to only `return false;` when both are actually necessary and not just for the sake of terseness. A final note on `.stopPropagation()` is that when using it in delegated events, the soonest that event bubbling can be stopped is when the event reaches the element that is delegating it.

It's also important to note that the event object contains a property called `.originalEvent`, which is the event object that the browser itself created. jQuery wraps this native event object with some useful methods and properties. 

Finally, to inspect the event itself and see all of the data it contains, log the event in the browser's console using `console.log()`. This will allow you to see all of an event's properties, which can be really helpful for debugging.

```
//Logging an event's information
$("form").on( "submit", function(event) {

  event.preventDefault(); // Prevent the form's default submission.

  console.log( event ); // Log the event object for inspectin'

  // Make an AJAX request to submit the form data

});
```