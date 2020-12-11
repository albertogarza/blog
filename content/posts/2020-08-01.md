---
title: "Loading Properties from a Backend in Angular"
description: "Are you trying to load some properties from a backend dynamically? Here you will find how to do it."
tags: [angular, properties, backend, APP_INITIALIZER]
categories: [front-end development]
date: 2020-08-04T06:00:00-06:00
author: Alberto Garza
draft: false
---

Adding values you might need for external services using `environment.ts` and/or `environment.prod.ts` is a great way to get a prototype idea started quite quickly, but it turns out to be the least flexible of all your options. In this article I will explain why using these "environment" files is not optimal and what other alternative we have.

## So what is the real problem with using `environment.ts`?

Let's first get something out in the open: using `environment.ts` is not bad! There's a reason is there and this is in no way trying to "shame" people for using it.

But here's a couple of scenarios where I believe the use of `environment.ts` comes short: 

- What if you have more than one non-prod environment and you don't really want to rebuild something for each environment so that each build artifact has the correct environment info?
- Or even worse, would you want to come up with a new build artifact just because something in your environment is about to change?

If you are in any of the above predicaments, let me shed some light in a feature that Angular offers that makes dynamically loading environment properties a breeze and without introducing the "asynchronousness" that would be inevitable otherwise.


## The Setup (a.k.a. assumptions)

To keep things simple, let's pretend we have a backend with an API endpoint which provides a value. This value happens to be vital for our Angular application startup configuration.

For demo purposes, this "API endpoint" will be replaced by a file on the `assets` folder in the Angular's `src` directory. But keep in mind this resource could be requested from anything else, including a real API endpoint. The request would look something like this:

```text
GET /assets/info
```

The above requests would respond with the following:

```json
{
  "clientId": "some_critical_text"
}
```

For this to happen, you will need to include a file named `info` inside the `assets/` folder.

How would you load the above to an Angular service before the app is running? Let's solve this puzzle.


## The Solution

There are two main parts to the solution:

1. A service that will both make a request to grab that `clientId` as well as keeping the value around for future needs. We'll call this the `InfoService` service.
2. A way to let Angular know that we need to load something as the app initializes, not when the app is already initialized and running.

Let's look at the service first and then at the initialization. We'll wrap up with a way to prove the value is there.

### The `InfoService` Service

Let's pretend that we already created this service named `InfoService` and that it looks like this:

```typescript
import { Injectable } from '@angular/core';

@Injectable()
export class InfoService {

  constructor() { }

}
```

The first part is easy. Let's declare a property to hold the value we'll eventually retrieve:

```typescript
info: AppInfo;
```

Along with its accompanying interface which could be declared in a separate file and whose implementation looks like this:

```typescript
export interface AppInfo {
  clientId: string;
}
```

Next, let's declare and implement a method that Angular will call when it's ready. The purpose of this method is to retrieve the value from the endpoint and save it in `this.info`. 

The concept itself is rather easy to understand. What may not be clear at first is that this method has to return a `Promise<boolean>`. Why, you may ask? We'll take a look at that later. For now, just know that retuning anything other than a `Promise<boolean>` will not work later down the road.

The other concept that you may want to look out for is about HTTP interceptors. If you have an authorization-related interceptor in place for your app, you will want to use the [`HttpBackend`](https://angular.io/api/common/http/HttpBackend) directly instead of the `HttpClient` injectable to make the request. This trick will help you bypass any HTTP interceptors altogether without worrying what happens with the rest of your app that actually needs these interceptors. 

Here's what the implementation of this method looks like along with the constructor needed to inject an instance of `HttpBackend`:

```typescript
  constructor(private httpBackend: HttpBackend) {}

  loadInfo(): Promise<boolean> {
    // bypass HTTP interceptors by using HttpBackend
    const http = new HttpClient(this.httpBackend);

    return (
      http
        .get<AppInfo>('info')
        // convert to Promise per Angular's `useFactory` requirement (not officially documented)
        .toPromise()
        .then(response => {
          // using a class factory to keep AppInfo class getters in place
          this.info = response;
        })
        // returning `true` to satisfy `useFactory` contract (not officially documented)
        .then(_ => Promise.resolve(true))
        .catch(error => {
          console.error('Error loading info', error);
          return Promise.resolve(false);
        })
    );
  }
```

The above snippet will need the following `import` statement to work:

```typescript
import { HttpBackend, HttpClient } from '@angular/common/http';
```

This finishes the `InfoService` declaration and implementation.


### The App Initialization

Now let's make Angular aware of what we're trying to do. This is essential so that Angular runs our code as the app is being initialized.

In the `app.module.ts`, or wherever your root `@NgModule` happens to live, add the following to the `providers` array:

```typescript
      {
        provide: APP_INITIALIZER,
        useFactory: appInitializerFn,
        multi: true,
        deps: [InfoService]
      }
```

The `APP_INITIALIZER` is a [dependency injection token](https://angular.io/guide/glossary#di-token) that is not very well documented or advertised at the time of this writing. However, it is quite handy to communicate to Angular that we need to execute some code as the app is being initialized. `APP_INITIALIZER` needs to be imported from `'@angular/core'`;

Once you have the above in place, you will need to declare and implement the `appInitializerFn` function somewhere. The implementation of this function needs to then call `InfoService.loadInfo()`. To make that call, we will need to inject an instance of `InfoService` using the `deps` array above and making sure that the function gives it a name by including it in the params. Here is what that looks like:

```typescript
const appInitializerFn = (infoService: InfoService) => {
  return () => {
    return infoService.loadInfo();
  };
};
```


### The Proof!

You don't believe the above or want to make sure it works before proceeding? Go ahead an inject this `InfoService` service into a component like the root component and try to log it from the constructor and see for yourself. You will notice that the data from this endpoint will be printed out to the console without hesitation.


## What Does This Solve?

This can solve a multitude of scenarios. I personally implemented this while trying to dynamically load a set of Okta configuration values that changed between each of the environments we had. You may have several firebase environments that need configuring. Who knows! But once you have something like this working, you won't need to worry about building for each environment anymore, which will take a load off your shoulders!

If you're interested to see this in action, please check out my project on [GitHub](https://github.com/albertogarza/angular-using-app-initializer) or on [Stackblitz](https://stackblitz.com/edit/angular-using-app-initializer).
