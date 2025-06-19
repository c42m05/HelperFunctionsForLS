# Helper Utils for Lens Studio

A collection of utility functions and classes to simplify common tasks in [Lens Studio](https://lensstudio.snapchat.com/), including attribute handling, batch operations, text effects, color conversion, deep copying, easing animations, and more.

---

## 📦 Installation

Include the helper script in your Lens Studio project. Exported utilities:

```js
const { BatchUtils, QuickUtils, SOGT } = require("path/to/HelperUtils.js");
```

---

## 🧰 BatchUtils

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

## ⚡ QuickUtils

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

## 🎞️ SOGT: Simple Object Game Timer

A tweening utility class with cycle control, pingpong mode, and easing.

```js
const timer = new SOGT();
timer.duration = 2;
timer.loop = Infinity;
timer.pingpong = true;

const easedValue = timer.play({ easeFunction: "Cubic", easeType: "InOut" });
```

### Methods:
- `play({ callback, cycleCallback, easeFunction, easeType })`
- `pauseResume()`
- `reset()`
- `reverse()`

### Read-only Properties:
- `timer.cycleIndex`
- `timer.progression`
- `timer.isPaused`
- `timer.isReversed`

---

## 🧪 EasingFunctions

Built-in easing support for animation/tweening:

```js
EasingFunctions.Quadratic.InOut(0.5); // → eased value
```

Available easings:
- `Linear`
- `Quadratic`
- `Cubic`
- `Quartic`
- `Quintic`
- `Sinusoidal`
- `Exponential`
- `Circular`
- `Elastic`
- `Back`
- `Bounce`

---
> Created by **c4205M (c42m05)**
