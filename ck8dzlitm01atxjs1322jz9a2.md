## What is `this` inside foo.bar()?

## The problem

![Code showing - method which is passed as callback loses reference to original obj as this, but calling foo.bar() directly works](https://cdn.hashnode.com/res/hashnode/image/upload/v1585425858545/Ut1FVXRIJ.png)

Calling a method directly works as expected.  
But when we pass a method as callback, it loses reference to the original object (as `this`) when called.

Why is that? **Can the spec help us explain this difference?**

------

## The explanation

## For methods,

⭐️ `foo.​bar()` translates to `foo.bar.call(foo)`.

That is, when a method (function which is accessed as a object property) is called, the object gets passed as `this` inside the function.

So, in this case -  
**What** →  foo is passed as `this` inside bar  
**When** →  if bar is a function and *accessed as object property* (i.e. foo.bar).  

(*Note* - We are using the [Function.call](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) method above to estimate what the language is doing internally, by passing a custom `this` value)

## For functions, 
⭐️ `fn()` translates to `fn.​call(undefined)`   

In case of a normal function call - `this` value within the function will be undefined.

But, there is a slight catch here. In case of non-strict mode, if `this` is set to `undefined` or `null` (as above), then it is internally replaced with the global object.  

Effectively -

| **mode**       | **this value**  |
|------------|---------------|
| strict     | undefined     |
| non-strict | global object |


## Method to function, 

⭐️ Now, if we were to rewrite the method call as a function call, then the value of `this` will change from `foo` (object) to either `undefined` or `global`.

One example of this is rewriting `foo.bar()` using a intermediate variable - ` fn = foo.​bar;  fn()`.

So, if `bar` was referencing other values from foo using `this`, those values will become undefined or resolve to a wrong variable.

🧠 This is exactly the reason why passing methods as callback changes the value of `this` (passed within it). Instead of calling a method directly, callback is actually passed as a function and this function is later called by some other code.

## In other words,

⭐️ When `foo.bar` is called, the function `bar` is not aware that it is "attached" to the object foo. Based on the exact syntax of a function call, if the language can figure out a clear `someThing.someFunction()` structure, then it will happily forward someThing as this.

But, if you take out a function from a object and call it separately, there is no way to figure out which object it was originally attached to. Hence, `this` will be undefined.

-------

## 📖 What does the spec say? 

TLDR - If you would like to see me actually go through the spec, this video might be more interesting.

%[https://youtu.be/RV27K79PDuI]
[Part 1](https://youtu.be/j894fgpStZI) of the video covers previous sections.

-----

![EvaluateCall- if IsPropertyReference, set this to base object](https://cdn.hashnode.com/res/hashnode/image/upload/v1584625813403/gPjSBUHIZ.png)

![IsPropertyReference- reference where base is object or primitive](https://cdn.hashnode.com/res/hashnode/image/upload/v1584625822167/ZmBSyVj95.png)

### PropertyReference

This `foo.bar` structure is defined in the spec as a [PropertyReference](https://tc39.es/ecma262/#sec-ispropertyreference).  
If you were assigning a value to the property of a object or primitive, then anything that is valid as the Left Hand side of the assignment expression is a PropertyReference.

So, PropertyReference = Reference + base is object or primitive

![PropertyReference illustration](https://cdn.hashnode.com/res/hashnode/image/upload/v1585431425436/5XBWmcI5y.png)

Some examples of valid and invalid PropertyReference might make it more clear - 👇

%[https://gist.github.com/bendtherules/3467664a9c567617342b73c3681c1dc7]

### Steps
1. If it is a PropertyReference, then set `thisValue` to base of the PropertyReference (i.e. `foo` in `foo.bar`).  
2. Or else, thisValue is `undefined`.

The obtained `thisValue` is forwarded to the abstract operation Call. Call operation verifies that the resolved value of `foo.bar` is a function and then calls the internal method [function.[[Call]]](https://tc39.es/ecma262/#sec-built-in-function-objects-call-thisargument-argumentslist) with the same thisValue.  
This function.[[call]] internal method is very similar to the public method `function.call`, which we use to call a function with a custom thisValue.

The implementation of this function.[[call]] method is different for different type of callables.  
Rough speaking, there are 4 type of callables (or simply, functions) -

1. Function declaration and expression
2. Arrow function
3. Bound function
4. Proxy, which supports [[call]]

Till now, we have talked about plain functions (type 1), which are defined using the function keyword. Now, let's look at arrow function and bound function. We'll skip proxies for this discussion.

## Bound function and Arrow function -
The function `foo.bar` doesn't need to be a  [simple function object](https://tc39.es/ecma262/#sec-ecmascript-function-objects), but it can be anything with a [[call]] interface like a exotic [bound function](https://tc39.es/ecma262/#sec-bound-function-exotic-objects-call-thisargument-argumentslist) or a proxy.  

**Bound functions** ignore the `thisValue` that was passed in and instead uses internal [[BoundThis]] as the actual `this`. [[BoundThis]] is the custom thisValue that was passed while binding using `Function.bind`.  
That is why one solution to this callback problem is to bind a function to the object before passing it as a callback.

*Note* - Bound functions are called as exotic objects, because they don't follow the normal conventions of a object.

**Arrow function** is a type of function object whose `this` value is resolved from the lexical scope.  
Its [[call]] method ignores the received thisValue (from Call abstract method) and always resolves `this` from its lexical scope, like any other free variable in a closure.

![thisMode in function objects](https://cdn.hashnode.com/res/hashnode/image/upload/v1585433951432/jnD8M_NB3.png)
**👇 thisMode in function objects**

Arrow function is a ordinary function object with internal [[ThisMode]] value set to `lexical`.

This can be another solution to the callback problem - creating a arrow function inside the object constructor will ensure that `this` always resolves to the base object.

----

🎬 That's all! Hope you had a interesting read.  
I would really appreciate if you leave some feedback 🌀