# Exploring web-workers(what, how, when).

Googling web workers also yeilds names such as service workers, worklets, etc.

They are kinda similar and not similar at all. The main thing: they all run in the background(in a separate thread).

At it's core, web workers are used to run code in a separate browser thread. We can use this functionality to offload compute intensive, background-appropriate tasks into a separate thread.

## Initial questions:

- Is it faster than default engine?
- How many background tasks I can spawn? As many as I can(until user's resources run out)
- Is it lightweight? What's the cost of creating a background task?
- What happens to a background task when a website is closed abruptly?

## Workers I have heard of:

- web workers
- service workers
- worklets
- dedicated workers
- shared workers

## What?

They run on another thread. Thus, the code needs to be in a separate file(not necessarily since we can use inline workers).

## Init Code:

we need to create a worker object

```js
const worker = new Worker("filename.js"); // filename.js will be downloaded
```

We have created a worker, now we need to run it using worker.postMessage(undefined | JSON | string);. Sending a message starts the worker.

## Communication:

Message passing mechanism

Parent page <-> worker

postMessage(JSON | string)

Example:

```js
// Main.js

const worker = new Worker("worker.js");
worker.addEventListener("message", (e) => {
  e.data;
}); // response by worker after getting executed
worker.postMessage("hello"); // sending message to worker

// worker.js

self.addEventListener("message", (e) => {
  e.data;
}); // worker receives a message
self.postMessage("m"); // sending message back to parent page
```

## Performance:

Message passed are copied, not shared.

`worker.terminate()` to stop the worker(from parent)

`self.close()` to stop the worker(within worker)

Transferrable objects:

Structured cloning algo. You can pass File, Blob, ArrayBugger, JSON in/out of workers.

Structured cloning copies data. There's an alternative(for perf) known as transferable objects(TO from now on).

Zero copy. Data transferred from one context(?) to another.
It kinda behaves like ownership model in rust. Once you pass TO to a worker from parent, that TO will no longer be usable in parent.

To use TO:

`worker.postMessage(arrayBuffer, [arrayBuffer])` // first arg: data, and second: the list of items that should be transferred

`window.postMessage(arrayBuffer, targetOrigin, [arrayBuffer])`

The 2nd arg **must** be an array buffer.

Peculiar example:

`worker.postMessage({data: int8View, moreData: anotherBuffer}, [int8View.buffer, anotherBuffer]);` // what's int8View.buffer? it's an array buffer

## Environment:

In worker, `self` and `this` refer to global scope for the worker.

## Features:

The following features are available to workers: navigator, location, xmlhttprequest, ability to spawn other web workers.

Workers cannot access the DOM, window, document, parent object.

`importScripts()` to import scripts within workers.

## Subworkers:

Workers can spawn child workers.

> Keep in mind most browsers spawn separate processes for each worker. Before you go spawning a worker farm, be cautious about hogging too many of the user's system resources.

Example: https://html.spec.whatwg.org/multipage/workers.html#delegation

## Inline workers:

Blob() to create a inline worker script(technically file is a blob). // need to explore what blobs are?

Lots of info about inline workers. Not sure whether inline is more preferred or not.

## Uses:

- Prefetching and/or caching data for later use(need to do this asap!!!)
- code syntax highlighting(what!!!)
- Analyzing video or audio data(might be good for beatoven?)
- Processing large data

---

## Service workers:

Service workers are workers that act as a proxy between your client and the server.
Client <-> Service Worker <-> Server/CDN

is it associated with PWAs only? I don't think so. But a crucial part of PWAs.

Service workers are a bit different.

(vs web-workers)
ChatGPT says:

> In summary, Service Workers are a more powerful and versatile form of background script, but with a broader scope and greater impact on the web page. Web Workers are more suited to tasks that require a separate thread of execution, but do not need access to the web page or its resources.

You can use service workers for offline support(they can cache assets and data), background sync(even after a page has been closed says ChatGPT, need to verify).

---

## Worklets

> The main difference between a Worklet and a Service Worker or a Web Worker is that Worklets are designed for specific, high-performance tasks that need to run off the main thread.

Image manipulation, animation, layouts.

---

Other(to be explored later):

Web workers with WASM

Shared workers

GPU workers

> It seems these tech are not the most loved(or atleast not most used)

## Links:

- Basics of workers: https://web.dev/workers-basics/
- More on TO: https://developer.chrome.com/blog/transferable-objects-lightning-fast/
- Caching with service workers: https://web.dev/service-worker-caching-and-http-caching/
- Ben awad on why he stopped using service workers(check the comments): https://www.youtube.com/watch?v=JJSloXLTyNg
- Library to work with web workers: https://github.com/GoogleChromeLabs/comlink
