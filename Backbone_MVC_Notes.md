## MVC Basics

* Controllers translate click events into intention (invoking a model function)
* Models effect state changes in other models (by broadcasting events)
* Views update as a result of model changes (by listening for changes)

## Backbone Basics

Backbone.js provides several classes (Model, Collection, View, Router) that you can extend to define the building blocks of your application. To build an app with Backbone.js, you first create the Models, Collections, and Views of your application. You then bring these components to life by defining a "Router" that provides the entry points of your application through a set of (deep-linkable) URLs.

With Backbone.js, your code is organized in self-contained entities (Models, Collections, Views): No more free-hanging and unrelated blocks of code.

* models: The persistent application data; keeps track of your data
    * a Model is just an object. 'Model' is a technical term in Backbone
      (and other similar frameworks) used to talk about the objects used
      to store application data.
    * we represent data as Models
* Views: The rendered data visible to the user
* Collections: Groupings of models using underscore functions
  
### Elegant REST Integration

Backbone.js also provides a natural / magical / elegant integration with RESTful services. If your back-end data is exposed through a pure RESTful API, retrieving (GET), creating (POST), updating (PUT), and deleting (DELETE) models is incredibly easy using the Backbone.js simple Model API.

## Model

![](http://backbonejs.org/docs/images/intro-model-view.svg)

* Orchestrates data and business logic.
* Loads and saves from the server.
* Emits events when data changes.

A **Model** manages an internal table of data attributes, and triggers "change" events when any of its data is modified. Models handle syncing data with a persistence layer — usually a REST API with a backing database. Design your models as the atomic reusable objects containing all of the helpful functions for manipulating their particular bit of data. Models should be able to be passed around throughout your app, and used anywhere that bit of data is needed. Models have an `initialize` method that Backbone calls every time you create a new model from the class that the initialize function is defined on. Any time you use `var amessage = new Message();` or similar to create an instance of your class (your model instance), `initialize` is run when the model is created.

The Model doesn't care how Views are organized, it simply announces updates to its data as necessary through the framework's event system.

### Data Binding:

With Backbone.js, you bind Views to Models so that when a Model's data changes, all the Views bound to that Model automatically re-render. No more complex UI synchronization code.

## View

* Listens for changes and renders UI.
* Handles user input and interactivity.
* Sends captured input to the model.

A **View** is an atomic chunk of user interface. It often renders the data from a specific model, or number of models — but views can also be data-less chunks of UI that stand alone. Models should be generally unaware of views. Instead, views listen to the model "change" events, and react or re-render themselves appropriately.

### What is `el`?

`el` is basically a reference to a DOM element and all views must have one. Two ways to associate a DOM element with a view: 

1. a new element can be created for the view and subsequently added to the DOM or
2. a reference can be made to an element which already exists in the page.

Deets in da [docs](http://backbonejs.org/#View-el).

**Note**: When declaring a View, `options`, `el`, `tagName`, `id` and `className` may be defined as functions, if you want their values to be determined at runtime, which is cool and minty fresh.

#### Cool, but wth is `$el`?

A cached jQuery object for the view's element. A handy reference instead of re-wrapping the DOM element all the time. In the [docs](http://backbonejs.org/#View-$el).

Note: `view.$el` is equivalent to `$(view.el), and `view.$(selector)` is equivalent to `$(view.el).find(selector)`.

And links, since I _never_ remember dem [selector patterns](https://www.w3.org/TR/selectors-3/#selectors) or the [jQuery flavors](https://api.jquery.com/category/selectors/).

Like a ninja, apply your View to a different DOM element [with `setElement`](http://backbonejs.org/#View-setElement).


The "el" property represents the markup portion of the view that will be rendered; to get the view to actually render to the page, you need to add it as a new element or append it to an existing element.

### Render

We often define a `render()` utility within our View which is responsible for rendering the contents of the `Model` using a JavaScript templating engine (optionally provided by Underscore.js) and updating the contents of our View, referenced by `this.$el`.

We can add a `render()` callback as a Model subscriber, so the View can be triggered to update when the Model changes.

A common Backbone convention is to return `this` at the end of `render()`. This is useful for a number of reasons, including:

* Making views easily reusable in other parent views.
* Creating a list of elements without rendering and painting each of them individually, only to be drawn once the entire list is populated.

## Collections

![](http://backbonejs.org/docs/images/intro-collections.svg)

A **Collection** helps you deal with a group of related models, handling the loading and saving of new models to the server and providing helper functions for performing aggregations or computations against a list of models. Aside from their own events, collections also proxy through all of the events that occur to models within them, allowing you to listen in one place for any change that might happen to any model in the collection.

## Controller

In MVC, Controllers respond to requests and perform appropriate actions which may result in changes to the Model and updates to the View. In Backbone,  Controller responsibility is typically addressed within the View. In a single-page application, rather than having requests in the traditional sense, we have events. Events can be traditional browser DOM events (e.g., clicks) or internal application events such as Model changes.

So does Backbone have Controllers? Not really. Backbone's Views typically contain "Controller" logic, and Routers are used to help manage application state, but neither are true Controllers according to classical MVC.

The events attribute can fulfill the role of the Controller configuration, defining how events occurring within the View's DOM element are to be routed to event-handling methods defined in the View.

![](https://addyosmani.com/backbone-fundamentals/img/backbone_mvc.png)

URL routing, DOM events (e.g., mouse clicks), and Model events (e.g., attribute changes) all trigger handling logic in the View. The handlers update the DOM and Models, which may trigger additional events. Models are synced with Data Sources which may involve communicating with back-end servers.

## Backbone.Events

Backbone gives us the ability to listen for events on our models, similar to how we register events on the DOM using jQuery. If something in our model changes, we can listen for that change and react accordingly when the event happens.  When we want to register a listener, we use the method `model.on`. Whenever a UI action causes an attribute of a model to change, the model triggers a "change" event; all the Views that display the model's state can be notified of the change, so that they are able to respond accordingly, re-rendering themselves with the new information. **Events** is a module that can be mixed in to any object, giving the object the ability to bind and trigger custom named events. Events do not have to be declared before they are bound, and may take passed arguments. For example:

```javascript
var object = {};

_.extend(object, Backbone.Events);

object.on("alert", function(msg) {
  alert("Triggered " + msg);
});

object.trigger("alert", "an event");
```

For example, to make a handy event dispatcher that can coordinate events among different areas of your application: `var dispatcher = _.clone(Backbone.Events)`

>**When you extend a Backbone component, you always get back a constructor function.**

So, because extending a Backbone component gives us a constructor function, that means we need to create an instance of the component before using it. `var kermit = new KermitModel();`

* * *

Sources:

1. <https://learn.makerpass.com/groups/rpt09/courses/reactorcore/course.backcast>
2. <http://backbonejs.org/>
3. <https://github.com/jashkenas/backbone/wiki/Backbone%2C-The-Primer>
4. <https://addyosmani.com/backbone-fundamentals/>
