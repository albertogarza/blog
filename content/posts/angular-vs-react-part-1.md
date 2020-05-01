---
title: "Angular vs React"
description: "Experts say speed and size matter. I say there are more things to consider when pick the right front-end framework or library for your next project."
tags: [angular, reactjs, frameworks, libraries]
categories: [front-end development]
date: 2020-04-30T22:50:16-06:00
author: Alberto Garza
draft: false
---

<!-- TODO: add a cool image here -->

## Which one should you pick?

Angular started it all and React is the hottest thing today. But how do they compare and how would you pick the best solution to your needs and circumstances? I will give you my unbiased (yeah, right!) opinion here.

## What to consider

When picking the right solution to your project, there are several aspects that must consider. It is very common to read experts analyze speed, community, and cocumentation as important aspects to consider before adopting a solution out of the many competing options. This is great advice, but in addition to that, I must add the following aspects that I consider are important to think about too:

* Framework vs Library
* Surroundings
* Previous experience

In this post, I will describe what I mean by "Framework vs Library". Look for the next few posts where I will explore "Surroundings" and "Previous experience".

## Framework vs Library

The best and shortest way to describe a library and a framework is the following:

> A library is called by your code; a framework calls your code.

I must admit that this is a matter of personal coding taste. Over the years that I have been teaching Angular and React, I have come to realize that different programmers have different code taste buds. Not everybody likes to "kneel down" before a framework and "offer sacrifices to their" framework. Some like freedom or flexibility to do whatever they want.

Ok, enough of religious references here. But read me out for a bit. Think of your last attempt to write some boiler plate code on your own just because you were too busy learning something new or because you thought you were too good to be using someone else's code. If you succeeded and enjoyed writing it and maintaining it, good for you! You must really love libraries or you must be writing your own! On the other hand, if that was a painful experience for you because you had deadlines to meet and a life to live, welcome to the club. You must really love frameworks. Wait, you don't yet? You should definitely look into them.

Frameworks take some really long time to get used to. As said earlier, frameworks call your code. Because of this nature, you must understand how to use a framework to get the full benefits it has to offer.

Frameworks also tend to be opinionated. You got an opinion on how a framework should work? You should look into contributing to their project or just use a library. If you approach a framework with the wrong attitude, chances are you're going to get frustrated with it and throw it away. And this brings me back to my previous point: yes, you will have to spend hours learning how to use it before you can actually use it.

Frameworks have the advantage of leading you to happy paths. They're generally written by developers who "have been there and done that." And out of countless hours of misery (and hair loss, perhaps), but also out of their abundant kindness for their fellow programmers after them, they put their lessons together into a sound solution for you so you can benefit from the fruits of their labor. How cool is that?

### Enough of generalities

What is Angular, then? Is it a framework or a library? And what is React? Let's take a look at what they call themselves and then analyze their getting started codes to prove or disprove their claims.

Angular calls itself a framework:

![Angular is a framework](/images/angular-is-a-framework.png "Angular is a framework")

React calls itself a library:

![React is a library](/images/react-is-a-library.png "React is a library")

Boy, that was hard, wasn't it?!? Well, let's do something easier now: let's look at sample code from their getting started sections.

### Angular's Getting Started Code Analysis

What evidences can we find in [Angular's Getting Started section](https://angular.io/start) that will help us determine whether or not it is a framework? 

Frameworks are easy to spot because they take control away from you, the apps developer. The very first sign of this happening in Angular is when you first learn about [Components](https://angular.io/start#components):

> app-root (orange box) is the application shell. This is the first component to load and the parent of all other components. You can think of it as the base page.

You might wonder, but how does the `app-root` component get loaded? This is where some of that control is taken away from you. All you have to do is take a deep breath, relax, add that as an HTML tag somewhere inside of the `<body` tags of your `index.html` file, and rest assured that Angular will work out its magic and somehow tie that non-HTML tag back to the `app-root` component. See? It wasn't that hard, was it?

Also, Dependency Injection (DI) is a very common technique for frameworks to implement. It's a way for you to "ask" the framework to give you an instance of something your code depends on. Angular does provide dependencies via DI. A clear example can be found in the [Routing section](https://angular.io/start/start-routing) of their Getting Started. The very first time you'd see dependency injection in action if you aren't familiar with Angular is when you are introduced to [using route information](https://angular.io/start/start-routing#using-route-information). Here's how DI looks like in Angular:

```typescript
export class ProductDetailsComponent implements OnInit {
  product;

  constructor(
    private route: ActivatedRoute,
  ) { }

}
```

In the above snippet, we're asking Angular to provide an instance of `ActivatedRoute`, which is an Angular service that provides more insights about the current route the user selected that prompted the `ProductDetailsComponent` class to attempt to display itself. 

But who is responsible to create a new instance of the `ProductDetailsComponent` class? Well, this is answered by the fact that we learned earlier in this post: frameworks call your code, not the other way around. By the time Angular needs to create a new instance of `ProductDetailsComponent`, it should already have an instance of `ActivatedRoute` and should know that it is available for injection.

Angular is full of examples like the above. Yes, some control is taken from you. But it can be quite liberating to not have to worry about that control you think you need. In reality, you don't need it.


### React's Getting Started Code Analysis

Let's now compare Angular against React's getting started code. Their [Simple Component](https://reactjs.org/#a-simple-component) is, in fact, quite simple:

```jsx harmony
class HelloMessage extends React.Component {
  render() {
    return (
      <div>
        Hello {this.props.name}
      </div>
    );
  }
}

ReactDOM.render(
  <HelloMessage name="Taylor" />,
  document.getElementById('hello-example')
);
```

React is very clear about how your component gets attached or rendered. You call their `ReactDOM.render()` method and tell it where you want your component to be. 

Did you catch the fact that you have to call React's code, and not the other way around? 

React is full of examples like the above. There's rarely any "magic" going on with it. It usually does what you code it to do. Period.

## Conclusion

Unless you've dabbled with a framework, it is really hard to grasp how it feels like coding in one (or coding to one). There's generally a very deep learning curve coming up to speed in a framework, but that investment pays itself off as the time goes by as frameworks tend to make developers more productive over time by saving them time from programming aspects that they would otherwise need to create plumbing for. Libraries are quick to get up to speed on them, but generally tend to absorb more of your time as repetitive coding tasks are your responsibility and this freedom (and ignorance) can lead you to dangerous patterns that will probably need some deep-dive fixing at some points in the life of the project.
