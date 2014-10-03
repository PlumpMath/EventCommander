Event Commander
===============

An EventBus and Command Pattern / Undo-Redo library for [Dart](https://www.dartlang.org/).

## Event Bus
The `EventBus` is the backbone for all of the Command/Undo communication behind the scenes,
but may be used on its own to fire and listen for (a.k.a. 'publish/subscribe') Events.

#### Basic usage

```dart
var event_bus = new EventBus();

event_bus.on(MyEventType, (MyEventType event) => doSomething());
...
event_bus.signal(new MyEventType()); // doSomething() will be called
```

#### EventHandler
* A function that accepts an `Event`, and is meant to be called whenever an event of the appropriate type is fired.

#### EventListener
* `listening_to : Type` - Property that lists what type of `Event` the listener is listening to.

* `handler : EventHandler` - The `EventHandler` function that is called whenever an `event` of the type
specified by `listening_to` is signaled.

* `stopListening() : void` - Unregisters the listener from its corresponding `EventBus`, and stops the `handler`
from being called and handling future events.

#### EventBus
* `on(Type event_type, void eventHandler(Event event)) : EventListener` -
Registers a function (`EventHandler`) to get called whenever an event of `event_type` is signaled.
Returns an `EventListener` that can be used to stop the registered `EventHandler` from handling events.

* `signal(Event event) : Future` -
Fires/propagates the event instance passed to it, and sends it to all the appropriate `EventHandlers` subscribed/listening
to events of that type. Returns a `Future` in case some computation needs to happen after all the corresponding
`EventHandlers` have been notified of the event.

* `stopListener(EventListener listener) : void` -
Same as `EventListener.stopListening()`. Unregisters an `EventHandler` function (via its corresponding `EventListener`)
from the `EventBus`. Stops the `EventHandler` from being called and receiving future events from this `EventBus` instance.

* `clearAllListeners() : void` -
Removes all `EventListeners`/`EventHandlers` from the `EventBus` instance.

* `hasListener(EventListener listener) : bool` -
Checks whether the given `listener` is registered with the `EventBus` instance.


## Events
Custom Events may be created by subclassing the `Event` class, and may contain any assortment
of properties and behaviors like you would find in a regular Dart class.

```dart
class MyEvent extends Event {
  String description;
  MyEvent(this.description);
}
```

Events support a multiple-inheritance scheme whereby listeners for a particular Event Type
may also be notified of other Events implementing/extending that Event Type interface.
This is accomplished by adding super-class/parent events to a `Set<Type> parents` property
in your `Event` class.

```dart
class MyEvent extends Event {
  String description;
  MyEvent(this.description);
}

class MyOtherEvent extends Event {
  int number;
  MyOtherEvent(this.number);
}

// Class must commit to 'implement' MyElement and MyOtherElement to ensure that
// listeners of those events can make use of this event
class MultiEvent extends Event implements MyEvent, MyOtherElement {
  int number;
  String description;

  MyChildEvent(this.number, this.description) {
    this.parents.addAll([MyEvent, MyOtherEvent]);
  }
}
...
event_bus.on(MyEvent, (MyEvent e) => doA(e.description));
event_bus.on(MyOtherEvent, (MyOtherEvent e) => doB(e.number));

event_bus.signal(new MultiEvent(1, 'event')); // triggers both doA() and doB()
```


## Commands

TODO

### Undo/Redo

TODO

## Install

Add `event_commander` to your `pubspec.yaml` file to install it from pub:

    dependencies:
      event_commander: any

or keep up with the latest developments on this git repo:

    dependencies:
      event_commander:
        git: https://github.com/oblique63/EventCommander.git

then just run `$ pub get` and you'll be all set to go.

__EventCommander__ has no additional/external dependencies, and is compatible with both client-side and server-side code.

## Import
For _Event Bus_ features only:

`import 'package:event_commander/event_bus.dart';`


For _Event Bus_ and _Command/Undo_ features:

`import 'package:event_commander/event_commander.dart';`