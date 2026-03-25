# Frontend code

## Closure

https://github.com/chenx/Reading/blob/master/JavaScript/Closure.md

```
var name = 'global name'; // global variable

// Closure created with var in function scope (instead of global scope).
function init() {
  var name = "Mozilla"; // name is a local variable created by init
  function displayName() {
    // displayName() is the inner function, that forms a closure
    console.log(name); // use variable declared in the parent function
  }
  displayName();
}

// Closure created with const in block/function scope (instead of global scope).
function init2() {
// const init2 = () => { // This also works.
  const name = "Mozilla 2"; // name is a local variable created by init
  function displayName() {
    // displayName() is the inner function, that forms a closure
    console.log(name); // use variable declared in the parent function
  }
  return displayName;
}


init();

const fn = init2();
fn();

// output:
// Mozilla
// Mozilla 2
```

## Lazy loading

https://github.com/chenx/Reading/blob/master/JavaScript/Lazy_Loading.md

```
import React, { Suspense } from "react";

const LazyComponent = React.lazy(() => import("./LazyComponent"));

function App() {
  return (
    <>
      <Suspense fallback={<div>Loading...</div>}>
        <LazyComponent />
      </Suspense>
    </>
  );
}

export default App;
```

## Deep copy

https://github.com/chenx/Reading/blob/master/JavaScript/Deep_Copy.md

## Promise

https://github.com/chenx/Reading/blob/master/JavaScript/promise.md

Promise.all, allSettled, race

A Promise object is a pending operation to be fulfilled.

```
const myPromise = new Promise((resolve, reject) => {
  // Simulate an asynchronous operation
  setTimeout(() => {
    const success = true; // or false for rejection
    if (success) {
      resolve("Operation successful!");
    } else {
      reject("Operation failed.");
    }
  }, 2000);
});
```

Promise.all

```
const promise1 = Promise.resolve(3);
const promise2 = 42; // Non-promise values are treated as resolved promises
const promise3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("foo");
  }, 100);
});

Promise.all([promise1, promise2, promise3])
  .then((values) => {
    console.log(values); // Output: [3, 42, "foo"]
  })
  .catch((error) => {
    console.error("One of the promises rejected:", error);
  });
```

## Debouncing and Throttling

https://github.com/chenx/Reading/blob/master/JavaScript/debouncing_and_throttling.md

### Debounce

```
// A debounce function that takes a function and a delay as parameters
function debounce(func, delay) {
    let timer;
    return function(…args) {
        clearTimeout(timer);
        timer = setTimeout(() => {
            // Apply the function with arguments
            func.apply(this, args);
        }, delay);
    };
}
```

### Throttling

```
// A throttle function that takes a function and an interval as parameters
function throttle(func, interval) {
    let isRunning = false;
    return function(…args) {
        if (!isRunning) {
            isRunning = true;
            // Apply the function with arguments
            func.apply(this, args);
            setTimeout(() => {
                isRunning = false;
            }, interval);
        }
    };
}
```

## Implement setInterval by setTimeout

```
let counter = 0;

let cond = {
    stop: false,
};

function incrementCounter() {
    ++ counter;
    console.log('Function executed: ' + counter);

    if (counter >= 5) {
        cond.stop = true;
    }
}

// Or: const setIntervalViaSetTimeout = (fn, interval) => {
function setIntervalViaSetTimeout(fn, interval) {
    setTimeout(() => {
        if (cond.stop) return;
        fn();
        setIntervalViaSetTimeout(fn, interval);
    }, interval);
}

setIntervalViaSetTimeout(incrementCounter, 1000);
```

## Implement traffic lights alternate on by Promise

```
function red() {
    console.log("red");
}

function green() {
    console.log("green");
}

function blue() {
    console.log("blue");
}

function lightOn(colorFn, duration) {
    return new Promise((resolve) => {
        colorFn();
        setTimeout(() => {
            resolve();
        }, duration);
    });
}

// function trafficLights() {
const trafficLights = () => {
    Promise.resolve().then(() => lightOn(red, 3000))
    .then(() => lightOn(green, 2000))
    .then(() => lightOn(blue, 1000))
    .then(() => trafficLights());
}

trafficLights();
```
