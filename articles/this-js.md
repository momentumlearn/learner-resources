# What is `this`?

> Q: May we spend a little bit more time going over the fundamentals of how `this` works and how to identify what it’s referring to? I’d love for someone who is a bit more familiar with the concept to explain it like I’m 5.

The simplest explanation of `this` is that it's defined when a function is called. If the function is an attribute of an object, and that object is used when calling it, like `object.coolFunction()`, then `this` is the object. If the function is not an attribute of an object, then `this` is `null`.

Check it out:

```js
const person = {
  name: "Amiro",
  sayHello: function () {
    console.log("Hello from " + this.name)
  }
}
person.sayHello() // => prints "Hello from Amiro"
const sayHello = person.sayHello
sayHello() // what happens here? What is "this" when we call this function?
```

Here's where it gets super hard: callbacks. Here's an example. Assume there's a button element stored in the variable `button`.

```js
button.addEventListener('click', person.sayHello)
```

When the button is clicked, the function stored in `person.sayHello` is called, but it's important to note that it's called like a function, not like an attribute of `person`. So `this` is null when you click the button.

There's two ways to set `this` permanently so that it's what you want it to be at all times: the method `.bind()` and using arrow functions.

## `.bind()`

All functions have a method `.bind()` that takes one argument. `.bind()` returns a new function that does the same thing, but it will have the variable `this` bound to that argument. An example:

```js
function sayHello() {
  console.log("hello from " + this.name)
}

const sayHelloFromAmiro = sayHello.bind({name: 'Amiro'})
sayHelloFromAmiro() // => prints "hello from Amiro"
```

`{name: 'Amiro'}` was bound to `this` when we called `.bind({name: 'Amiro'})` on `sayHello`.

## Arrow functions

When you make a function using the `() => {}` syntax, known as _the arrow syntax_, `this` is bound at the time you create the function. This is really useful if you have a function on an object you want to act as an event listener.

Think back to the example of a DOM button element called `button`. Here it is with arrow functions:

```js
const person = {
  name: "Amiro",
  sayHello: () => {
    console.log("Hello from " + this.name)
  }
}
person.sayHello() // => prints "Hello from Amiro"
button.addEventListener('click', person.sayHello)
```

This time, we'll get what we expect when we click `button`.
