# Frontend 场景题

## Vite, Webpack

FE build tools.

## script in header of bottom of body
```
HTML -> DOM ---|
               |--  combined render tree -> layout (JS) -> render -> display
CSS -> CSSOM --|
```

## Only show one toast message

- global variable to mark a toast has been shown
- debouncing and throttling
- handling errors in one place. e.g., Promise.All

## Reduce number of if-else

- Strategy pattern
- Table driven (Map)
- OOP polymorphism
- State-driven pattern

## FE performance metrics

RAIL: 
- Google 2015
- Response (100ms), Animation (16ms), Idle (50ms), Load (1-3s)
- Metrics
  - FCP (First Contentful Paint)
  - LCP (Largest Contentful Paint)
  - FID (First Input Delay)
  - TTI (Time To Interactive)
  - TBT (Total Block Time)
  - CLS (Cumulative Layout Shift)

Web vitals
- Google 2020
- Metrics
  - LCP (loading)
  - FID (interactivity)
  - CLS (visual stableness)
- Tools
  - Lighthouse (chrome extension)
  - webpagetest.org
  - Chrome Dev Tools
    
## SPA load optimization

Slow reasons:
- network is slow
- file size is big
- repeated resource request
- rendering blocked

Solutions
- DNS cache
- CDN
- local cache
- file compression, gzip;
- Lazy loading; separation of package
- include images in a sprite (CSS sprite)
- page optimization, move script from header to end of body, use defer/async in header; batch processing of DOM (document fragment: finish building DOM node and attach finally)
- SSR

## FE monitoring

- Event tracking: performance, user interactions, errors
- Factors
  - Purpose;
  - Plan and design;
  - Event tracking;
  - Data upload;
  - Storage;
  - Data analysis, visualization, dashboard, reporting
  - Monitoring and alerting;
  - Improvement
 
## Process user reported issues

- Feedback channel
  - user info
  - environment info: browser version, OS etc
  - reproduce path, screenshot, logs
  - impacted user type
- Reproduce issue
- Analyze root cause
  - network
  - browser version, extension
  - cache
  - bug
- Fix
- Feedback to user
- Documentation

## Share cookie between different domains

- possible for subdomains of the same top domain
- impossible for different top domains

## Layout

Flow layout

Box layout

Flex layout
- flex: 1
  - flex-grow, flex-shrink, flex-basis

Table layout

## Refactor codebase

## Remove unused code

## Internationalization

## Micro frontend vs iframe

- performance
- integration and interactivity
- UI consistency
- SEO visibility
- security

## Peak QPS optimization

- vertical scaling: hardware
- horizontal scaling: load balance
- CDN
- cache
- database
- code
- async

## number overflow

BigInt

## Determine client type

- user-agent
- css media query

## Large file upload

## Adaption to mobile devices

- viewport
- css media query
- rem
- flexible.js

## Lazy load of image

## cookie fields

key, value, expires, path, domain

## login by scanning 2D code

## DNS

## Watermark

- explicit, implicit

## Domain model

## SEO optimization

## Handle failure of static resource loading failure

- multiple path (CDN, resource site)
- local cache
- dynamically generate resource

## Mobile client load/refresh by dragging

## Decide if DOM node is visible

getBoundingClientRect()

## script tag attributes

## SPA hash router

## React router

## SSO (Single Sign On)

## Page white screen diagnosis

- network
- server side
- client side
- browser compatibility
- 3rd party resources
- cache
- others: CORS, cross domain, security, etc.

## JS execute 1 million tasks and not freeze

- web workers
- requestAnimationFrame

## ESlint

## Vitual scrolling

## React Router vs browser history API

## Inline and block level html elements

## requestIdleCallback

A web API

## DocumentFragment API

## FE theme color change

CSS, JS, media query, local storage

```
:root { --primary-color: #000; --text-color: #fff; }[data-theme="dark"] { ... }
```

## Style isolation

Modulization.

## Prevent spider

- robots.txt
- CAPTCHA
- User agent
- flow anlaysis
- Firewall
- Dyanamically render content by JS
- IP blacklist
- Limit visit frqeuency
- API throttling
- HTTPS
- Change website structure

## Communication between tabs

- Broadcast channel
- Service workers
- Shared worker
- Localstorage
- iframe postMessage

## Render 100,000 items without blocking rendering

- requesestAnimationFrame
- DocumentFragment

## Build FE framework from scratch

## Login state: Cookie, Session, Token

## 100. Why Vite is faster than Webpack

webpack: 
- bundling, then start dev server
- implemented by Node.js (ms)
- re-compile when change

vite: 
- start dev server, compile dependency next;
- use ES module supported by browser
- implemented by esbuild in Go (ns)
- hot refresh: request changed module only

## Prevent others from seeing my source / JS

- debugger add break points.
- obfuscation

## Optimize images

- Choose best image type
- Compression
- Sprite
- Icons: use icon font
- base64 image (< 10k)
- CDN cache
- Laze load: Intersection Observer API

## OAuth2.0 authorization

Access Token

## Set request timeout

## Node.js use multi-core CPU

## Backend returns large tree-like data, how to handle

async, batch processing, virtual rendering, handle by level

## Componentization principals

Similar to OOP

## Increase page load speed

- resource
- data
- code execution
- rendering

## 117. Event tracking SDK design

## FE authorization design

## Low code platform design

## Webpack optimization

## IndexedDB size limit

## Browser storage types

- Cookie
- Web storage
  - Local storage
  - Session storage
- Indexed DB
- Cache storage

## Canvas vs SVG

Canvas - pixel based

SVG - vector based

## 129. React avoid unnecessary rendering

## Global style avoid naming conflict

- namespace
- BEM naming
- CSS preprocessor
- CSS module
- CSS-in-JS

## React performance optimization

## Intercept web request

## Cross-page communication

- URL param
- cookie
- Local/session storage
- IndexDB
- postmessage API
- Shared worker
- Broadcast channel API
- Web socket

## Change 3rd party npm

## The Event Loop cycle

- Execute synchronous code: The call stack is processed until it is empty.
- Process microtasks: After the call stack is clear, the event loop runs all pending microtasks until the microtask queue is empty.
  Common microtasks include:
```
    Promise callbacks (.then(), .catch(), .finally()).
    await statements.
    queueMicrotask() calls.
    Callbacks from MutationObserver.
```
- Perform rendering: Once the microtask queue is exhausted, the browser may perform a rendering update to reflect any changes made to the DOM. This ensures that any DOM changes queued within microtasks are rendered before the next macrotask runs.

- Execute one macrotask: The event loop then picks **one** task from the macrotask queue and executes it. After that single macrotask completes, the event loop returns to the beginning of the cycle, starting with step 2 to process any new microtasks that may have been created. Common macrotasks include:
```
    setTimeout() and setInterval() callbacks.
    UI events (e.g., click, scroll).
    Parsing HTML.
    I/O operations. 
```

## What happens after Microtask queue is empty

- 在清空微任务后，事件循环会继续处理宏任务。
- 宏任务处理完毕后，会处理动画帧队列 (e.g. RequestAnimationFrame) 和空闲回调队列。
- 如果所有队列都为空，浏览器会进入空闲状态，等待下一次事件发生。

## Map vs WeakmMap

Key Types:
- Map: Keys can be of any data type (primitives, objects, functions, etc.).
- WeakMap: Keys must be objects. Primitive values are not allowed as keys.

Memory Management (Garbage Collection):
- Map: Holds strong references to its keys. 
- WeakMap: Holds weak references to its keys.

Iteration and Size:
- Map: Provides methods for iteration (.keys(), .values(), .entries(), forEach()) and a .size property to get the number of entries.
- WeakMap: Does not provide methods for iteration or a .size property.

WeakMap is used when: 
- caching data associated with DOM nodes 
- storing private data for objects in a way that respects their lifecycle

## Bind, Call and Apply

https://medium.com/@omergoldberg/javascript-call-apply-and-bind-e5c27301f7bb

Bind 
- creates a new function that, when called, has its this keyword set to the provided value.

Call 
- calls a function with a given this value and arguments provided individually.

Apply
- calls a function with a given this value and arguments provided as an array.
- Call and Apply are similar, only difference is whether to pass in args invidually or as an array
