# microMVC+

**A modern, zero-dependency JavaScript MVC micro-framework.**
Built from scratch for 2026 by Fabien Conéjéro.

[![Version](https://img.shields.io/badge/version-1.0.0-blue)](https://github.com/madjeek-web)
[![License : MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Zero dependencies](https://img.shields.io/badge/dependencies-0-brightgreen)](https://github.com/madjeek-web)
[![JavaScript ES5+](https://img.shields.io/badge/JavaScript-ES5%2B-f7df1e)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![UMD compatible](https://img.shields.io/badge/module-UMD-orange)](https://github.com/umdjs/umd)
[![Build status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com/madjeek-web)

---

## What is microMVC+ ?

microMVC+ is a minimalist JavaScript framework that brings the MVC pattern (Model, View, Controller)
to the browser in fewer than 300 lines of code, with zero dependencies, no build step, and no configuration.

It uses the prototype style, the publish/subscribe messaging bus, the flat composition philosophy,
and an extremely fast learning curve. It includes all the features that are genuinely useful in 2026.

If you have never heard of MVC and are reading this for the first time, the FAQ section below
explains the whole concept in plain language. It was written as a real conversation, because
real conversations are how people actually learn.

---

## Who is this for ?

- Beginners who want to learn how MVC works before touching React, Vue, or Angular.
- Teachers and trainers who need a framework small enough to read in one sitting.
- Developers who want a zero-dependency reactive layer for a small project without the overhead
  of a full modern framework.
- Anyone curious about how the core of a framework actually works under the hood.

---

## Three ways to understand this library

### For a 14-year-old beginner

microMVC+ is a small toolbox in JavaScript for building websites in an organized way.
It splits your code into 3 parts : the Model (the data, like your list of contacts),
the View (what the user sees on screen), and the Controller (the boss that links the two together).
These 3 parts can talk to each other by sending "messages" (publish/subscribe),
like notifications on a phone. The whole thing is ultra-lightweight, with zero external
dependencies. Think of it as a mini IKEA kit for code : simple, effective, and you build it yourself.

### For a junior developer

microMVC+ is a prototype-based mediator pattern implementation in plain JavaScript.
Each component (controllers, views, models) acts as a pub/sub bus.
Elements registered inside a component can publish namespaced events using dot-notation,
and any other element across any component can subscribe to those events.
The models component additionally exposes a reactive key-value state store that publishes
a "state.changed" event on every mutation, making one-directional data flow trivial to implement
without JSX, a virtual DOM, or a build pipeline.

### For teachers and trainers

microMVC+ follows a deliberate architectural decision : flat composition over deep
inheritance. Each Component holds a recursive subscriber namespace tree traversed by a regex walker.
The publish method walks this tree and invokes all callbacks at matching depth levels,
enabling hierarchical event scoping with dot-notation without any external dependency.

The state store on models is patched directly onto the Component instance inside the Application
constructor, keeping the Component prototype clean and generic. This is intentional :
it demonstrates to students that composition is often more readable than inheritance.

The source code is designed to be read in full. Every method is documented at three levels.
Encourage your students to read micromvc-plus.js from top to bottom before using it.

---

## version 1.0.0 :

| Feature | First version | microMVC+ v1.0.0 |
|---|---|---|
| UMD module wrapper | AMD only, required a module loader | AMD, CommonJS, and browser global |
| unsubscribe | Not available | Yes, via token returned by subscribe() |
| once() | Not available | Yes, auto-removes after first trigger |
| has(id) | Not available | Yes, check element existence |
| remove(id) | Not available | Yes, remove a registered element |
| destroy() | Not available | Yes, on components and Application |
| State management | Not available | getState / setState / resetState on models |
| publishAsync | Not available | Yes, returns a Promise |
| Event log | Not available | Yes, getLog() / clearLog() on each component |
| Duplicate warning | Silent | console.warn with clear message |
| hasOwnProperty guard | Missing | Added on prototype copy loop |
| Dependencies | None (runtime) | None |
| Lines of code | ~80 lines (src) | ~280 lines |

---

## Installation

**Direct download (browser)**

Download `micromvc-plus.js` and include it in your page :

```html
<script src="js/micromvc-plus.js"></script>
```

**npm (optional)**

```bash
npm install micromvc-plus
```

**CDN (coming soon)**

A CDN link will be available after the first stable release on npm.

---

## File structure

```
your-project/
    index.html
    css/
        style.css
    js/
        micromvc-plus.js    <- the library
        app.js              <- your code
```

Open the project with VS Code like any standard web project. No terminal, no build tool, no config.

---

## Getting started in 5 minutes

**Create the application**

```javascript
var app = new microMVC.Application();
```

**Add a model**

```javascript
app.models.add('userModel', {
    _name : '',

    setName : function (name) {
        this._name = name;
        this.publish('nameChanged', { name : name });
    },

    getName : function () {
        return this._name;
    }
});
```

**Add a view**

```javascript
app.views.add('userView', {
    _constructor : function () {
        this.models.subscribe('userModel.nameChanged', this, this.render);
    },

    render : function (event) {
        document.getElementById('username').textContent = event.name;
    }
});
```

**Add a controller**

```javascript
app.controllers.add('userController', {
    updateName : function (name) {
        this.models.call('userModel.setName', name);
    }
});
```

**Trigger an action**

```javascript
// Direct call
app.controllers.userController.updateName('Fabien');

// Or via component call
app.controllers.call('userController.updateName', 'Fabien');
```

---

## Full API reference

### Application

```javascript
var app = new microMVC.Application();
app.VERSION          // "1.0.0"
app.controllers      // Component instance
app.views            // Component instance
app.models           // Component instance (with state management)
app.destroy()        // Tears down all components and resets state
```

### Component methods (controllers, views, models)

```javascript
component.add(id, object, extClass)     // Register a new element
component.has(id)                       // Returns true if element exists
component.remove(id)                    // Unregisters an element
component.call('id.method', argument)   // Call a method on a named element
component.publish(context, event)       // Fire a namespaced event
component.publishAsync(context, event)  // Fire asynchronously, returns Promise
component.subscribe(context, scope, callback)  // Listen to an event, returns token
component.once(context, scope, callback)       // Listen once, auto-removes, returns token
component.unsubscribe(token)            // Remove a subscription by token
component.getLog()                      // Returns array of all publish calls
component.clearLog()                    // Empties the log
component.destroy()                     // Clears all subscribers and log
```

### Models : state management

```javascript
app.models.setState('balance', 2500)     // Set a value, publishes "state.changed"
app.models.getState('balance')           // Returns 2500
app.models.getState()                    // Returns a copy of the full state object
app.models.resetState()                  // Clears all state, publishes "state.reset"
```

Reacting to state changes in a view :

```javascript
app.views.add('myView', {
    _constructor : function () {
        this.models.subscribe('state.changed', this, function (e) {
            console.log(e.key, e.value, e.previous);
        });
    }
});
```

### Element methods (available inside any model, view, or controller)

```javascript
this.publish('eventName', { data : 'here' })      // Publish scoped to this element
this.publishAsync('eventName', { data : 'here' }) // Async version, returns Promise
this.destroy()                                     // Remove this element from its component
this.controllers   // Reference to app.controllers
this.models        // Reference to app.models
this.views         // Reference to app.views
```

### subscribe() return token and unsubscribe()

```javascript
var token = app.views.subscribe('userModel.nameChanged', null, function (e) {
    console.log(e.name);
});

// Later, when the view is no longer needed :
app.views.unsubscribe(token);
```

### publishAsync() and Promises

```javascript
app.models.publishAsync('userModel.nameChanged', { name : 'Alice' })
    .then(function (result) {
        console.log('All subscribers notified for :', result.context);
    });

// With async/await (modern environments)
await app.models.publishAsync('userModel.nameChanged', { name : 'Bob' });
```

### once() : fire only once

```javascript
app.models.once('userModel.nameChanged', null, function (e) {
    console.log('First name ever set :', e.name);
    // This callback will never fire again after the first time
});
```

### Event log for debugging

```javascript
// After interacting with your app :
var log = app.models.getLog();
console.table(log);
// Each entry : { time : timestamp, context : 'eventName', event : { ... } }
```

---

## Practical example : real-time notification counter

This example shows the exact pattern that makes MVC valuable :
one model, three different views all updating at the same time from a single publish call.

```javascript
var app = new microMVC.Application();

app.models.add('notifModel', {
    _count : 0,

    addNotification : function (message) {
        this._count++;
        this.publish('updated', { count : this._count, message : message });
    }
});

// View 1 : the bell icon
app.views.add('bellView', {
    _constructor : function () {
        this.models.subscribe('notifModel.updated', this, function (e) {
            document.getElementById('bell-count').textContent = e.count;
        });
    }
});

// View 2 : the page title
app.views.add('titleView', {
    _constructor : function () {
        this.models.subscribe('notifModel.updated', this, function (e) {
            document.title = '(' + e.count + ') My App';
        });
    }
});

// View 3 : a notification list
app.views.add('listView', {
    _constructor : function () {
        this.models.subscribe('notifModel.updated', this, function (e) {
            var li = document.createElement('li');
            li.textContent = e.message;
            document.getElementById('notif-list').prepend(li);
        });
    }
});

// Trigger from anywhere
app.models.notifModel.addNotification('Alice liked your photo.');
// All three views update instantly. No page reload. No extra code.
```

---

## Demo

An interactive demo is available that covers six real scenarios :
bank transfers, real-time notifications, task management with state,
live profile editing with once(), the event log debugger, and publishAsync.

- Demo 1 :
The demo file is `demo.html`. Open it directly in a browser alongside `micromvc-plus.js`.
It is also deployed at : <a href="https://madjeek-web.github.io/demo.html" target="_blank">https://madjeek-web.github.io/demo.html</a>

- Demo 2 :
Standalone demo HTML file, with the library integrated directly into it in a single line. No external dependency, this html file is sufficient by itself.
The file is called `demo-standalone-self-contained.html`. The library is integrated directly into the `<head>` in a single `<script>` tag. Zero external dependency : <a href="https://madjeek-web.github.io/demo-standalone-self-contained.html" target="_blank">https://madjeek-web.github.io/demo-standalone-self-contained.html</a>


---

## When should you use microMVC+ ?

| Situation | Use microMVC+ ? |
|---|---|
| Static presentation website | No, plain HTML and CSS are enough |
| Static blog | No |
| Social network, dashboard | Yes |
| Live chat, real-time scores | Yes |
| E-commerce cart with live totals | Yes |
| App with login and user profile | Yes |
| Learning how MVC works | Yes, this is the best use case |

The central question is : does data change and need to update the screen somewhere ?
If yes, use microMVC+. If your page is purely static, you do not need it.

---

## FAQ : real questions, real answers

The section below is a real conversation between a beginner and Fabien Conéjéro.

This is the most honest documentation possible.

---

**Explain this project in 5 lines to a 14-year-old.**

microMVC+ is a small toolbox in JavaScript for building websites in an organized way.
It splits your code into 3 parts : the Model (the data, like your list of contacts),
the View (what the user sees on screen), and the Controller (the boss that links the two together).
These 3 parts can talk to each other by sending "messages" (publish/subscribe),
like notifications on a phone. The whole thing is ultra-lightweight, with zero external
dependencies. Think of it as a mini IKEA kit for code : simple, effective, and you build it yourself.

---

**Ok, what is its purpose, why use it, and how ? What is the point ?**

The problem it solves : imagine you are coding a game or a web app.
At first you have 50 lines of code and everything is fine.
But after 500 lines, your code becomes a plate of spaghetti. Everything is mixed together,
impossible to change without breaking everything.

The solution is to separate your code into 3 clear roles.

The Model holds raw data. For example : "the player has 100 health points."
The View is what the user sees. For example : the health bar displayed on screen.
The Controller is the boss. For example : "the player got hit, tell the model to remove 10 HP,
then tell the view to update the display."

Why use it ? Because if you want to change the display, you only touch the View.
If you change the game rules, you only touch the Model. The other parts do not move.
That is the genius of it.

How does it work concretely ? The 3 parts talk to each other through a message system.

```javascript
// The model shouts "the player lost health !"
app.models.publish("playerHealth", { hp: 90 });

// The view listens to this message and updates the screen automatically
app.views.subscribe("playerHealth", this, function(event) {
    displayHealth(event.hp);
});
```

In one sentence : it is a mini-framework to keep your code clean, organized,
and easy to modify, even when your project grows large.

---

**So it is more for interactive websites, non-static, with features and elements
that update in real time ? For example, when I change my profile photo on Facebook.
And also for mobile applications ?**

Yes, exactly. You understood the idea perfectly.

The perfect use cases for microMVC+ (or any MVC framework) : you change your profile photo
and the avatar updates everywhere on the page without reloading. A live chat where messages
appear in real time. An e-commerce cart where you add a product and the total recalculates
instantly. A dashboard with graphs moving in real time.

All of this is exactly the kind of thing where MVC shines, because the data changes,
the model is updated, and the view refreshes automatically. Clean and without chaos.

For mobile though, pay attention. microMVC+ is pure JavaScript for browsers.
So for a mobile web app (in a browser on a phone) yes, it works.
For a native iOS or Android app, no, not directly.
For native mobile, developers use other tools that use the same MVC concept but adapted :
Swift/SwiftUI for iOS, Kotlin for Android, or React Native and Flutter for both at once.

The MVC concept itself is everywhere. It is a code philosophy, not just a technology.
microMVC+ is just a tiny, lightweight version of that philosophy, perfect for learning
or for small interactive web projects, without the heaviness of large frameworks
like Angular or React.

---

**Concretely, where does it go ? Is it JavaScript code that you put in your web pages
with VS Code ? Or another method ? I do not really see how you use it in real life.**

Yes, exactly that. It is just files in your classic web project.

```
my-project/
    index.html
    css/
        style.css
    js/
        micromvc-plus.js    <- the library
        app.js              <- YOUR code
```

You open it with VS Code, like any web project. Nothing special.

In your index.html you just link the files :

```html
<script src="js/micromvc-plus.js"></script>
<script src="js/app.js"></script>
```

And in app.js you write your logic. In a real dev career, you will rarely see microMVC+.
It is a pedagogical mini-library. In real companies people use React, Vue.js, or Angular.
But they all do the same thing as microMVC+, just more powerfully.
So understanding microMVC+ means understanding the core concept behind 90% of modern web apps.
It is a very good starting point.

---

**So it is more for PHP websites that communicate and send requests, not for static
presentation websites or for live event and action type applications ?**

Almost. But there are some important nuances.

You are right about static websites. A page that just presents a company with text
and images needs zero MVC. Plain HTML and CSS is enough. MVC would be overkill.

But be careful about the rest. MVC is not necessarily linked to PHP.
PHP is on the server side (back-end). microMVC+ is on the browser side (front-end).
These are two different things that can work together or separately.

And most importantly, live real-time applications are exactly the perfect use case for MVC.

A live football match on a website : the score changes, the Model is updated,
the display refreshes, the View updates automatically.
The user clicks "notify me" and the Controller handles the action.
That is exactly what MVC handles best.

The real question is : does data change and need to update on screen ?
If yes, use MVC. If not, no need.

---

**So this system is kind of intelligent, it reacts to events and user actions,
for example with conditions like "if the user does this, show that."
Also for live calculations ? Like an instant bank transfer where my account is debited
and I can see the amount on my account has decreased. This avoids having to log back
into the site later to find out how much is left after the transfer.
Or it avoids waiting and refreshing the page. Is MVC JS capable of complex things
like large calculations ? I cannot really get a sense of the possibilities.
Are they limited or infinite ? What is the speed, the time between when my instant
transfer is validated, credited immediately to the beneficiary's account,
and the number of seconds it takes MVC to calculate what is left in my account
and display it live ? I want detailed explanations. Start from the assumption that
I am a young teenager, and then explain it a second time as if I were a real junior dev.
Two clearly distinct explanations.**

Explanation for a 14-year-old :

Is it intelligent ? Yes, a bit like a video game. In Fortnite, when you get hit, your health
bar drops immediately. You do not wait for the game to restart to see how much you have left.
It is exactly the same for your bank transfer.

The speed ? MVC on the browser side is nearly instant. We are talking a few milliseconds
(1 second equals 1000 milliseconds). The new balance updates so fast you cannot even blink.

But pay attention. The real delay is not caused by MVC. It is the journey the information takes :
You to the Internet to your bank's server to verification and back to your screen.
This round trip can take 1 to 3 seconds depending on your connection.
MVC, once it receives the response, displays the result in under 10 milliseconds.

Its limits ? MVC alone cannot decide if you have enough money. That is the bank server's job.
MVC is just the super-fast messenger that displays the result.

Explanation for a junior dev :

MVC is a pattern, not a calculation engine. You need to separate two things clearly.

First, the business logic (calculations, rules) runs on the server side (PHP, Node.js, Java...).
It is the bank's server that verifies the balance, applies security rules, validates the transaction,
and updates the database.

Second, reactive display is where MVC and the front-end intervene.
Once the server responds, the Model is updated and the View re-renders automatically.

The complete flow of a bank transfer :

```
[User clicks "Validate"]
    -> Controller captures the event
    -> HTTP/API request sent to the server    (network : 200ms to 2s)
    -> Server validates, calculates, writes to DB  (back-end : 50ms to 500ms)
    -> JSON response returned : { balance: 1840.50, status: "ok" }
    -> MVC Model updated with the new data   (1ms)
    -> View re-renders automatically          (5 to 16ms, one frame at 60fps)
```

The real performance of MVC on the front-end : the JavaScript engine in modern browsers
(V8 in Chrome) is extremely fast. A simple re-render is one frame, meaning 16ms at 60fps.
For the user it is imperceptible.

The real performance bottlenecks come from network latency, heavy server-side calculations,
and the DOM if you have thousands of elements to re-render simultaneously.

Can MVC do complex calculations ? JavaScript can technically handle fairly heavy calculations,
but in practice we keep the front-end light. The golden rule :

Everything that touches security, money, or sensitive data must always go through the server.
A user can modify JS in their browser. Never trust the front-end for critical business logic.

Real limits : no serious cryptographic calculation client-side, no direct database access,
not suited for massive data processing like machine learning or big data.
microMVC+ specifically is too lightweight for large production projects.
In real life you will use React, Vue, or Angular which handle re-rendering much better
with diff algorithms and a virtual DOM.

Its possibilities : for UX and interface reactivity, the possibilities are nearly infinite.
Dynamic forms, real-time dashboards, live notifications via WebSockets, data-reactive
animations, all of that without ever reloading the page.

---

**Yes but then it is not really useful for much. For example, a clickable button
with a CSS hover effect does the same thing, or when I fill in an online form,
or even when I play a Flash game on PC. Why is MVC there ?
For example, parallax effects when the mouse position moves, you do not need MVC for that.
I do not see when you would actually use it.**

You are right about everything you cited. And that is an excellent observation.

None of this needs MVC : CSS hover (that is pure styling, zero logic),
mouse parallax (a few lines of JS are enough), a simple form (native HTML handles it),
a Flash game (the game engine is built in, no MVC needed).

MVC becomes useful only when you have this : does the same data appear in several places
on your page and change ?

Concrete Facebook example : your notification count is displayed in the browser tab,
in the bell at the top, and in the mobile menu. All three at the same time.
When someone likes your post, all three places must update simultaneously.

Without MVC you have to write the same code 3 times to update 3 places.
With MVC, the Model changes once and all 3 Views update automatically.

The real boundary is data complexity.

CSS hover ? No MVC needed.
Parallax ? 10 lines of JS.
Simple form ? Native HTML.
One piece of data displayed in 5 different places ? MVC.
50 users seeing the same live data ? MVC.
A cart with promotions plus tax plus live stock ? MVC.

Honestly ? microMVC+ specifically is used by almost nobody in production.
It is a pedagogical project to understand the concept.
In real life developers use React or Vue which do the same thing but 100 times more powerfully.
But if you understood microMVC+, you understood the principle behind all these tools.

---

**Ok, I found this project online on GitHub. Why put your code there ?
Who is it useful for ?**

GitHub is like YouTube but for code.
Developers put their projects there for several reasons.
To show their work : it is their portfolio, like a technical CV.
To share : anyone can download and use the code for free.
To collaborate : other devs can report bugs or improve the code.
To save : GitHub is also an online backup for their work.

microMVC+ is a project built from scratch, from zero.
It is a personal interpretation of the MVC concept, ultra-lightweight, without copying React or Angular.

---

**microMVC+ from scratch, from zero. What does "from scratch" mean ?
Why not just use React directly ? What does microMVC+ have more than other frameworks ?
What can I use it for ?**

"From scratch" is an expression that developers use all the time.
It means "homemade, from nothing." Like a pizza from scratch means you make the dough
yourself instead of buying a frozen one.

Why not just use React directly ? Because MVC is not software you download.
It is just an idea, a concept, a way of thinking about your code.
It is like a pizza recipe. The recipe exists but every pizza maker makes it their own way.

React is an MVC pizza. Angular is an MVC pizza. microMVC+ is an MVC pizza.
They all follow the same basic idea but each one was coded differently.

Why build your own ? To learn : the best way to understand something is to build it yourself.
Personal challenge : like remaking a known game for fun.
Lightness : React and Angular are huge and heavy. microMVC+ is something tiny.
To understand the internals of all these tools before using them.

What does microMVC+ have more than other frameworks ? Honestly, what it has is mainly
what it does NOT have.

React has 45 000 lines of code and dozens of dependencies.
microMVC+ has around 280 lines and zero dependencies.

That is the design choice : make the smallest possible MVC that still works and teaches.

What can you use it for ? The MIT license means you can do absolutely anything with the code :
use it in your own project, modify it, improve it, sell it in a commercial product,
use it as inspiration to create your own version, or learn from it to understand
how MVC works under the hood.

The only condition : keep the author name in the license file. That is all.

Concretely, the recommendation is to use it to learn.
Read the code. It is around 280 lines, it is readable.
Understanding how microMVC+ was built from scratch is 100 times more educational
than looking at React without understanding why it exists.
It is the perfect entry point before tackling real professional frameworks.

---

## Credits

microMVC+ : Copyright (c) 2026 Fabien Conéjéro

---

## License

MIT. See the LICENSE file for the full text.
