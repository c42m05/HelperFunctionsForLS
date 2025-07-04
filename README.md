# Helper Utils for Lens Studio

<img src="https://images.ctfassets.net/ub38vssza5h3/7FynBv68WKAHYYAe7XQhlk/dfa0ebd5e13c3bdd5b0f34345e65170f/LS.png" width="100" height="100" alt="Lens Studio">

A collection of utility functions and classes to simplify common tasks in Lens Studio, including attribute handling, batch operations, text effects, color conversion, deep copying, easing animations, and more.

---

## Installation

Include the helper script in your Lens Studio project. Exported utilities:

```js
const { BatchUtils, QuickUtils, SOGT } = require("path/to/HelperUtils.js");
```

---

## BatchUtils

### `writeAttributes(root, attr, readOnly = false, slice = 1)`
Binds values from an object to another, with optional getter/setter access.

```js
const root = {};
const attrs = { _speed: 5, _health: 100 };
BatchUtils.writeAttributes(root, attrs, false, 1);
print(root.speed); // 5
root.health = 80;
print(attrs._health); // 80
```

---

### `createSceneComponent(root, objectName, componentNames, override = false)`
Creates a scene object and attaches components to it.

```js
const comp = BatchUtils.createSceneComponent(root, "MyObject", ["Component.Text"]);
comp.Text.text = "Hello!";
```

---

### `batchExecute(funcName, callees = [], property = null)`
Calls the named function on multiple objects safely.

```js
BatchUtils.batchExecute("init", [obj1, obj2], data);
```

---

## QuickUtils

### `delayedCall(delay, script, callback)`
Runs a callback after a delay using an update loop.

```js
QuickUtils.delayedCall(2.5, script, () => print("Done!"));
```

---

### `deepCopyObject(copyFrom, copyTo, options)`
Deep-copies data from one object to another. Supports vector instances.

```js
QuickUtils.deepCopyObject(source, target, { recursive: true });
```

---

### `flattenObjectHierarchy(parent, result)`
Flattens an object tree into a flat map or array.

```js
const flatMap = QuickUtils.flattenObjectHierarchy(sceneObj);
```

---

### `convertHex(hex, inVec4 = true)`
Converts hex color string to a normalized vec3 or vec4.

```js
const color = QuickUtils.convertHex("#FF8800"); // → vec4(1, 0.533, 0, 1)
```

---

### `typewriter(text, script, options)`
Animates text typing letter-by-letter with event support.

```js
QuickUtils.typewriter(myText, script, {
  reset: 50,
  skipOn: "TapEvent",
  callback: () => print("Finished!")
});
```

---

### `singleCall(fn)`
Ensures a function is called only once until reset.

```js
const once = QuickUtils.singleCall(() => print("Only once!"));
once(); // prints
once(); // ignored
once.reset();
once(); // prints again
```

---

## Custom Tween

The `CustomTween` module provides a robust set of easing functions and the `SOGT` class for advanced animation control.


### `EasingFunctions`

A collection of common easing functions, categorized into `In`, `Out`, and `InOut` variants. These functions can be directly assigned to the `easeFunction` property of an `SOGT` instance.

  
**Available Easing Functions (and their variants):**

* `Linear.InOut`

* `Quadratic.In`, `Out`, `InOut`

* `Cubic.In`, `Out`, `InOut`

* `Quartic.In`, `Out`, `InOut`

* `Quintic.In`, `Out`, `InOut`

* `Sinusoidal.In`, `Out`, `InOut`

* `Exponential.In`, `Out`, `InOut`

* `Circular.In`, `Out`, `InOut`

* `Elastic.In`, `Out`, `InOut`

* `Back.In`, `Out`, `InOut`

* `Bounce.In`, `Out`, `InOut`

  

### `SOGT` Class

A flexible tweening controller that supports defining animation duration, looping, ping-pong behavior, and applying various easing functions.

  
**Constructor:**

```javascript

const timer =  new  CustomTween.SOGT();

```

  
**Properties:**

* `duration: number` (default: 1) - The total time in seconds for one full animation cycle.

* `easeFunction: EaseFunction` (default: `EasingFunctions.Linear.InOut`) - The easing function to apply to the progression. Can be any function from `CustomTween.EasingFunctions`.

* `loop: number` (default: 1) - The number of times the animation should loop. Set to `0` for continuous looping.

* `pingpong: boolean` (default: false) - If true, the animation will reverse direction on alternate loops.


**Read-only Properties:**

* `cycleIndex: number` - The current completed loop cycle count.

* `progression: number` - The progression of the current cycle (from 0 to 1).

* `isPaused: boolean` - True if the animation is currently paused.

* `isReversed: boolean` - True if the animation is currently playing in reverse.


**Methods:**

#### `play({ cycleCallback, callback, easeFunction } = {})`

Advances the animation based on the time delta. This method should be called in an `UpdateEvent` listener.

  

* `options.cycleCallback: function(): void` (optional) - Called at the end of each loop cycle (when `progression` reaches 1 or 0 in reverse).

* `options.callback: function(): void` (optional) - Called when all specified loops (`loop` property) are completed.

* `options.easeFunction: EaseFunction` (optional) - Overrides the current `easeFunction` for this specific `play` call.

Returns the eased progression value (from 0 to 1).

```javascript

const timer =  new  CustomTween.SOGT();
timer.duration =  2;
timer.loop =  0; // Infinite loop
timer.pingpong =  true;
timer.easeFunction =  CustomTween.EasingFunctions.Quadratic.InOut;  // Set a default easing

script.createEvent("UpdateEvent").bind(()  =>  {

	const easedValue = timer.play({
		cycleCallback:  ()  => print("Cycle Finished!"),
		callback:  ()  => print("Animation Complete!"),  // Will only be called if loop is a finite number
		// You can also override easeFunction here if needed for a specific play call
		// easeFunction: CustomTween.EasingFunctions.Elastic.Out
	});

	// Use easedValue to animate properties (e.g., position, scale, opacity)
	myObject.getTransform().setLocalPosition(new vec3(easedValue * 10, 0, 0));
});

```

  
#### `pauseResume()`

Toggles the paused state of the animation. If paused, `play()` will not advance the time.

  
```javascript

timer.pauseResume();  // Pauses the animation

timer.pauseResume();  // Resumes the animation

```

  
#### `reset()`

Resets the animation timer to its initial state (time to 0, flow to 1, cycle to 0, unpaused). Callbacks are reset to be callable again.

  
```javascript

timer.reset();

```

  
#### `reverse()`

Reverses the direction of the animation flow. If playing forward, it will start playing backward from the current `progression`. Callbacks are reset to be callable again.

  
```javascript

timer.reverse();

```

---
> Created by **c4205M (c42m05)**
