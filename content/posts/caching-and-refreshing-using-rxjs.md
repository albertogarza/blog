---
title: "Caching and Refreshing Using RxJS in Angular"
description: "Using RxJS in Angular to keep data around and refreshing it as often as needed can make the task a whole lot simpler."
tags: [angular, rxjs, caching, refreshing]
categories: [front-end development]
date: 2020-07-01T06:00:00-06:00
author: Alberto Garza
draft: false
---

Learning RxJS can be daunting but rewarding. In this post, I will show you how to cache data that doesn't change too often but adding a way to keep it fresh as often as you need to using RxJS only.

There are certainly other ways of doing the same thing, but let's dive into one way I figured out for a recent task I was working on. 

## Scenario

Here's my scenario. I have a list of items that I was querying from an API endpoint every time a component was loaded. I was looking into ways to increase the performance of this component and I spotted this repeated query. It would load the same data every time and updates on it didn't happen often. By potentially caching this data, I would let users get a performance gain of about 1.5 seconds on subsequent loads of the same component!

To demonstrate this, we will use a nice free football (soccer) video API endpoint that returns a list of the latest games and their highlights: `https://www.scorebat.com/video-api/v1/`. Since this endpoint returns quite a few items, we will display only the first three items or games in the array and have it refresh every hour so we do not tax this free system.

Here's how to do it.

### 1. Identify the Observable you'd like to cache and refresh
 
For the sample code we use here, this is:

```typescript
this.http.get("https://www.scorebat.com/video-api/v1/")
```

If you're subscribing to it yourself, you won't be needing the call to the `subscribe()` method.

### 2. Identify the refresh interval 

In this example, we'll be refreshing every hour (`60 * 60 * 1000`), but you can set this number to anything that fits your needs. Just know this number must be in milliseconds. 

### 3. Understand the "magic"

Here's the core code snippet where the magic happens:

```typescript
    this.videos$ = timer(0, 60 * 60 * 1000).pipe(
      switchMap(() => this.http.get("https://www.scorebat.com/video-api/v1/")),
      shareReplay(1),
      tap(console.log)
    );
```

Let's analyze it step by step.

1. The `timer` function. This RxJS function does what its name implies: It sets a timer that will run at a given interval. The first argument of `0` (zero) determines the delay for the very first item to be emitted. The second argument of `60 * 60 * 1000` sets the actual interval. Here the value is expected to be in milliseconds. Hence, we multiply `1000` by `60` to give us 60 seconds and then multiply that result by `60` to give us 60 minutes or 1 hour.
2. The `switchMap` operator. This RxJS operator is the perfect solution for scenarios where you don't care if there was a previous item emitted (i.e. you always want the code to treat the latest item emitted and drop the current item being treated if there's one). Since this describes what we need here, we get to use it.
3. The `shareReplay` operator. This RxJS operator is responsible for caching the last response from the previous operator. We pass in a value of `1` so that it only keeps the last emitted item around all the time.
4. The `tap` operator. We use it here to log to the console whatever we get back for demo purposes. This is not needed and can be removed.

If you're interested to see this in action, feel free to clone my project on [GitHub](https://github.com/albertogarza/angular-caching-and-refreshing-example) or use it live on [Stackblitz](https://stackblitz.com/edit/angular-caching-and-refreshing-example?file=src/app/app.component.ts).
