---
title: Practical Javascript
date: "2019-10-26T13:40:44-04:00"
description: "Lessons I learned when deepening my JavaScript knowledge"
---
## Intro to JS experience

Like many developers I have dabbled in JavaScript. Also like many developers, I have not taken the time to master the language. My previous experience relied on the use of frameworks, like Vue and React, or libraries like JQuery. While these tools make front end development easier I found them to be abstractions that did not help me gain an understanding of JavaScript itself. For this reason, I decided to pursue learning the language and its application to front end development without the use of external frameworks or libraries. It was my hope that deepening my knowledge of ‘pure’ JS would improve my overall front end development ability when incorporated with frameworks and libraries.

## My Approach

To achieve this goal I followed my general approach to learning. I break learning down into two parts, theory and application, trying to make each component as small as possible so that there is a tight feedback loop. These incremental applications of theory will expose areas where I haven’t fully grasped a concept and make sure I address these before I progress to subsequent topics.

## Key things I learned

**Development process:** Most of my work focusses on the backend. As such, I do a lot of trial and error through TDD and IRB. Working in the DOM meant that I could use a console in the browser as a sandbox. `console.log()`, `console.table()` and changing colours was extremely helpful.

**Events, errors and `this`:** The easiest way to know what these values are is to use `console.log`. Doing so has the cost of a browser refresh and is really helpful for determining what you are working with and help solidify scoping. Many modern browsers also provide a list of properties and methods available to the object that has been logged.

**Event loop:** The event loop is very different to the request/response cycle of an app as it is always looping. Promises and async await really helped me in understanding the event loop and the order in which events would fire, though I did find this particularly difficult.

**CSS:** I know this is a post on learning JavaScript but I think one of my biggest lessons is that CSS can do a lot. There were a number of situations where my default reaction was to write a function that could have been achieved with good CSS. My main challenge here was my limited CSS knowledge. I also found that poorly organised and implemented CSS would cause troubles for me later on. This was frequently due to improperly scoped or named IDs and classes.

## Resources used

[Javscript30](https://javascript30.com/), [Eloquent Javascript](https://eloquentjavascript.net/), [Nodeschool.io](http://Nodeschool.io), [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
