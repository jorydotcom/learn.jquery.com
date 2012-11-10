---
title   : Handling Events
level: beginner
source: http://jqfundamentals.com/legacy
attribution: 
  - jQuery Fundamentals
---
jQuery provides the method `.on()` to respond to any event on the selected elements. This is called an _event binding_. Although `.on()` isn't the only method provided for event binding, it is a best practice to use this for jQuery 1.7+. This is covered further in the [History of jQuery Events](/events/history-of-events) section.

The `.on() method provides several useful features:

  - Bind any event triggered on the selected elements to an event handler.
  - Bind multiple events to one event handler.
  - Bind multiple events and multiple handlers to the selected elements.
  - Use details about the event in the event handler.
  - Pass data to the event handler for custom events.
  - Bind events to elements that will be rendered in the future.

## Simple Event Binding
```
// When any <p> tag is clicked, we expect to see '<p> was clicked' in the console.
$("p").on("click", function() {

  console.log("<p> was clicked");

});
```

### Many Events, One Event Handler

Suppose you want to trigger the same event whenever the mouse hovers over or leaves the selected elements. The best practice for this is to use "mouseenter mouseleave." Note the difference between this and the next example:

```
// When a user focuses on or changes any input element, we expect a console message
// bind to multiple events
$("div").on( "mouseenter mouseleave", function() {

  console.log("mouse hovered over or left a div");

});
```

### Many Events and Handlers

Suppose instead you want different event handlers for when the mouse enters and leaves an element, to show and hide a tooltip on hover, for example. This following is more common (note that `.on()` accepts an object containing multiple events and handlers):

```
$("div").on({

  mouseenter: function() {

    console.log("hovered over a div");

  },

  mouseleave: function() {

    console.log("mouse left a div");

  },

  click: function() {

    console.log("clicked on a div");

  }

});
```

### The Event Object

Handling events can be tricky. It's often helpful to use the extra information contained in the event object passed to the event handler for more control. For a breakdown of the event object, see the [Inside the Event Handling Function](/events/inside-event-handling-function/).

```
$("div").on( "click", function(event) {

  console.log("event object:");

  console.dir( event );

});
```

### Passing Data to the Event Handler

You can pass your own data to the event object:

```
$("p").on( "click", {foo: "bar"}, function(event) {

  console.log( "event data: " + event.data.foo + " (should be "bar")" );

});
```

### Binding Events to Elements that Don't Exist Yet

This is called _event delegation_. See the [Event Delegation](/events/event-delegation/) section for a full explanation.

```
$("ul").on( "click", "li", function() {

  console.log("Something in a <ul> was clicked, and we detected that it was an <li> element.");

});
```

## Connecting Events to Run Only Once

Sometimes you need a particular handler to run only once &#8212; after that, you may want no handler to run, or you may want a different handler to run. jQuery provides the `.one()` method for this purpose:

```
// Switching handlers using the `.one()` method
$("p").one( "click", function() {

  console.log("You just clicked this for the first time!");

  $( this ).click(function() {

    console.log("You have clicked this before!");

  });

});
```

The `.one()` method is especially useful to do some complicated setup the first time an element is clicked, but not subsequent times.

`.one()` accepts the same arguments as `on()`, which means it supports multiple events to one or multiple handlers, passing custom data and event delegation.

## Disconnecting Events

Although all the fun of jQuery occurs in the `.on()` method, its counterpart is just as important. `.off()` cleans up the event binding when it's not needed anymore. Complex user interfaces with lots of event bindings can bog down browser performance, so using the `.off()` method diligently is a best practice to ensure that you only have the event bindings that you need, when you need them.

```
// Unbinding all click handlers on a selection
$("p").off("click");
```

```
// Unbinding a particular click handler, using a reference to the function
var foo = function() {

  console.log("foo");

};

var bar = function() {

  console.log("bar");

};

$("p").on( "click", foo ).bind( "click", bar );

$("p").off( "click", bar ); // foo is still bound to the click event
```