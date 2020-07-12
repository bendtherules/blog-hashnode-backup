## How does in-built Array iterator work?

We use `[...someArray]` or `array.values()` to get multiple values out of a array.  And we kind of know how it works - it just returns us all the values from the array.  Internally, both of them uses the iterator protocol - which is already defined on array prototype.

But do we really know the details of how in-built Array iterator works? Can you write a precise polyfill to implement iterator protocol on Array?

## A little about the iterator protocol

When we use spread operator, like `[...someArray]` - it treats someArray as a general iterable, not specifically as an array.

1. It tries to get a iterator from the iterable, using `iter = someArray[Symbol.iterator]()`. 

2. Then it consumes all the values from the iterable by calling `iter.next()` repeatedly.  
    This method `.next()` returns response in this structure `{value: "something", done: true|false}`.
    
3. While values are available, it returns `done: true`; after all values are finished, it returns `done: false`. After this, the consumer (i.e. spread operator) should stop asking for more values.

This is how the iterable/iterator stuff works.  

In our case, Iterable is `someArray` - which has a special property `Symbol.iterator` whose value is a method. Calling this method returns a iterable (`iter`), which has `.next()` method. Calling this method returns all the values one-by-one.

## On arrays

Array has in-built support for iterable/iterator protocol, through prototype. Array.prototype has a method with key `Symbol.iterator`, which creates a ArrayIterator. 

ArrayIterator internally contains these properties -
* [[ IteratedArrayLike ]] - which points back to the actual array.  
( Remember, “this” within the ArrayIterator is the iterator object itself, not the actual array. So, it has to store a link to the array during creation )

* [[ ArrayLikeNextIndex ]] - the next index whose value should be returned. Default is `0`.  
  
* [[ ArrayLikeIterationKind ]] - For normal purpose, it is `“value”`.  
In general, it can be `“key”`, `“value”` or `“key+value”`. This allows reuse of the same iterator mechanism for array.keys(), .values() and .entries().

| syntax          | kind        |
|-----------------|-------------|
| `...arr`        | "value"     |
| `arr.keys()`    | "key"       |
| `arr.values()`  | "value"     |
| `arr.entries()` | "key+value" |


## ⭐️ Array Iterator.next( )

Now that we have the in-built array iterator, the most important thing is knowing how `iter.next()` will return the array values one-by-one. Let's look at how that works, in details -

(  Say, `arr` = [[IteratedArrayLike]], `index` = [[ArrayLikeNextIndex]], `kind` =  [[ArrayLikeIterationKind]] )

0. If `arr` is `undefined`, return `{value: undefined, done: true}`  
(This is a special step, caused by step 2.a. It will reach here if you call `.next()` even after iterator has finished.)

1. If `index < arr.length`, ( i.e. while values are available)   
	a. set `key` = `index`  
	
	b. If `kind` is `“key”`, return `{value: key, done: false}`  
	(incase of arr.keys(), just return the index. It doesn't even try to access the value.)  
	
	c. Set `value` = `arr[index]`  
	
	d. If kind is “value”, return `{value: value, done: false}`  
	(for arr.values() or ...arr, just return value at that index)  
	
	e. If kind is “key+value”, return `{value: [key, value], done: false}`  
	(for arr.entries(), return array of [key, value])  
	
	f. Set [[ArrayLikeNextIndex]] = index + 1  
	(⭐️ 1️⃣  Always increment key to next index. This sequential index is used to get the next value, irrespective of holes in that position (whether that index exists or not). It doesn't skip the empty/non-existent indexes.)

2. Else, (i.e. when `index >= arr.length` - reached end of array)

	a. Set `[[IteratedArrayLike]]` = `undefined`.  
	(⭐️ 2️⃣  Yes, once it reaches the end - it sets linked array to undefined. This is to ensure the once the iterator has finished, it will never return any more value. This undefined array is handled in step 0.  
	If this was not done and if array length was increased before next call, then it would again return new values after saying `done:true` earlier.)

	b. Return `{value: undefined, done: false}`

## 🤯 Implications

1. `[...arr]` converts sparse array to dense array. (Reason -⭐️ 1️⃣ above)

```js
foo = [10, , 30, 40]; // [10, <hole>, 30, 40]

bar = [...foo]; // [10, undefined, 30, 40]

// ✅ sparse array
foo.hasOwnProperty(1); // false       
// ⚠️ dense array
bar.hasOwnProperty(1); // true

// ⚠️ array.keys() - returns the empty indexes
Object.keys(foo) // 0, 2, 3
foo.keys(); // 0, 1, 2, 3
```

2. Array iterator will never return more values after it has finished once - even if the array has more values now. (Reason - ⭐️ 2️⃣ above)

```js
arr = [10]
iter = arr[Symbol.iterator]()

iter.next() // 10 - this was the last value

// but,modify array
arr.push(20)
// ⭐️ uses updated length, since iterator is not finished
iter.next() // {value: 20, done: false}

iter.next() // {value: undefined, done: true} - end of iterator

// push again after iterator has ended
arr.push(30)
// ⭐️ doesn't use updated length
iter.next() // {value: undefined, done: true}
```

That's all. [Follow me on LinkedIn](https://www.linkedin.com/in/bendtherules) for more updates.