# Changelog

All notable changes to microMVC+ are documented in this file.

---

## [1.0.0] - February 2026

**Author :** Fabien Conéjéro

### Architecture

- UMD wrapper from the start. The library works as an AMD module, a CommonJS module,
  and a plain browser global without requiring any build tool or module loader.
- No bundler required. The dist file is the source file.
- Element, Component, and Application are separated into clearly documented sections
  inside a single file.

### Component methods

- `add(id, obj, ext)` : registers a new element. Emits a console.warn if the id already exists.
- `has(id)` : returns true if an element with the given id is registered.
- `remove(id)` : unregisters and deletes an element from the component.
- `call(path, argument)` : calls a method on a named element using dot-notation.
- `publish(context, event)` : fires a namespaced event. Logs the call automatically.
- `publishAsync(context, event)` : deferred publish that returns a Promise.
- `subscribe(context, scope, callback)` : listens to an event. Returns a unique token.
- `once(context, scope, callback)` : listens once, auto-removes after first trigger. Returns a token.
- `unsubscribe(token)` : removes a specific subscription by token.
- `getLog()` : returns a copy of the event log array.
- `clearLog()` : empties the event log.
- `destroy()` : wipes all subscribers and the event log.

### Application methods

- `destroy()` : calls destroy on all three components and resets the model state.

### Models : state management

- `setState(key, value)` : sets a state value, publishes "state.changed" with key, value, and previous.
- `getState(key)` : retrieves a state value. With no argument, returns a shallow copy of full state.
- `resetState()` : clears all state and publishes "state.reset".

### Element methods

- `publish(context, event)` : fires a namespaced event scoped to this element.
- `publishAsync(context, event)` : async version, returns a Promise.
- `destroy()` : removes this element from its parent component.

### Code quality

- `hasOwnProperty` guard on the prototype copy loop inside `add()`.
- `call()` emits a console.warn if the element or method is not found.
- `once()` subscribers cleaned up in reverse order to avoid index shifting bugs.
- Subscriber tokens are unique strings combining timestamp and random suffix.

### Documentation

- Full JSDoc on every method, written at three levels : beginner, junior dev, teacher.
- README includes a full API reference, practical examples, a feature comparison table,
  and a FAQ section taken word for word from a real conversation with a complete beginner.
- SECURITY.md explains the client/server trust boundary in plain language.
- CHANGELOG.md (this file).
