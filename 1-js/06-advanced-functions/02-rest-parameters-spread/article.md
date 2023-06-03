# Rest parameters and spread syntax

অনেক জাভাস্ক্রিপ্ট বিল্ট-ইন ফাংশন একটি নির্বিচারে সংখ্যক আর্গুমেন্ট সমর্থন করে।

For instance:

- `Math.max(arg1, arg2, ..., argN)` -- returns the greatest of the arguments.
- `Object.assign(dest, src1, ..., srcN)` -- copies properties from `src1..N` into `dest`.
- ...and so on.

 এই অধ্যায়ে আমরা শিখব কিভাবে একই কাজ করতে হয়। এবং এছাড়াও, প্যারামিটার হিসাবে যেমন ফাংশন অ্যারে পাস কিভাবে.

## Rest parameters `...`

একটি ফাংশনকে যেকোন সংখ্যক আর্গুমেন্ট দিয়ে কল করা যেতে পারে, এটি যেভাবেই সংজ্ঞায়িত করা হোক না কেন।

Like here:
```js run
function sum(a, b) {
  return a + b;
}

alert( sum(1, 2, 3, 4, 5) );
```


"excessive" যুক্তির কারণে কোন ত্রুটি হবে না। তবে অবশ্যই ফলাফলে শুধুমাত্র প্রথম দুটি গণনা করা হবে

বাকি প্যারামিটারগুলিকে তিনটি বিন্দু ব্যবহার করে ফাংশনের সংজ্ঞায় অন্তর্ভুক্ত করা যেতে পারে `...` এর পরে যে অ্যারের নামটি সেগুলিকে ধারণ করবে। বিন্দুগুলির আক্ষরিক অর্থ "একটি অ্যারেতে অবশিষ্ট প্যারামিটারগুলি সংগ্রহ করুন"।

উদাহরণস্বরূপ, অ্যারে `args`-এ সমস্ত আর্গুমেন্ট সংগ্রহ করতে:

```js run
function sumAll(...args) { // args is the name for the array
  let sum = 0;

  for (let arg of args) sum += arg;

  return sum;
}

alert( sumAll(1) ); // 1
alert( sumAll(1, 2) ); // 3
alert( sumAll(1, 2, 3) ); // 6
```

আমরা ভেরিয়েবল হিসাবে প্রথম প্যারামিটারগুলি পেতে বেছে নিতে পারি এবং শুধুমাত্র বাকিগুলি সংগ্রহ করতে পারি।

এখানে প্রথম দুটি আর্গুমেন্ট ভেরিয়েবলে যায় এবং বাকিগুলো `titles` অ্যারেতে যায়:

```js run
function showName(firstName, lastName, ...titles) {
  alert( firstName + ' ' + lastName ); // Julius Caesar

  // the rest go into titles array
  // i.e. titles = ["Consul", "Imperator"]
  alert( titles[0] ); // Consul
  alert( titles[1] ); // Imperator
  alert( titles.length ); // 2
}

showName("Julius", "Caesar", "Consul", "Imperator");
```

````warn header="The rest parameters must be at the end"
The rest parameters gather all remaining arguments, so the following does not make sense and causes an error:

```js
function f(arg1, ...rest, arg2) { // arg2 after ...rest ?!
  // error
}
```

The `...rest` must always be last.
````

## The "arguments" variable

`arguments` নামে একটি বিশেষ অ্যারে-সদৃশ বস্তু রয়েছে যা তাদের সূচক অনুসারে সমস্ত আর্গুমেন্ট ধারণ করে।

For instance:

```js run
function showName() {
  alert( arguments.length );
  alert( arguments[0] );
  alert( arguments[1] );

  // it's iterable
  // for(let arg of arguments) alert(arg);
}

// shows: 2, Julius, Caesar
showName("Julius", "Caesar");

// shows: 1, Ilya, undefined (no second argument)
showName("Ilya");
```


পুরানো সময়ে, ভাষাতে বিশ্রামের পরামিতি বিদ্যমান ছিল না, এবং `arguments` ব্যবহার করে ফাংশনের সমস্ত আর্গুমেন্ট পাওয়ার একমাত্র উপায় ছিল। এবং এটি এখনও কাজ করে, আমরা এটি পুরানো কোডে খুঁজে পেতে পারি।

কিন্তু নেতিবাচক দিক হল যদিও `arguments` উভয়ই অ্যারের মতো এবং পুনরাবৃত্তিযোগ্য, এটি কোনো অ্যারে নয়। এটি অ্যারে পদ্ধতি সমর্থন করে না, তাই আমরা উদাহরণের জন্য `arguments.map(...)` কল করতে পারি না।

এছাড়াও, এটি সর্বদা সমস্ত আর্গুমেন্ট ধারণ করে। আমরা তাদের আংশিকভাবে ক্যাপচার করতে পারি না, যেমন আমরা বাকি প্যারামিটার দিয়ে করেছি।

সুতরাং যখন আমাদের এই বৈশিষ্ট্যগুলির প্রয়োজন হয়, তখন বিশ্রামের পরামিতিগুলিকে অগ্রাধিকার দেওয়া হয়।

````smart header="Arrow functions do not have `\"arguments\"`"
If we access the `arguments` object from an arrow function, it takes them from the outer "normal" function.

Here's an example:

```js run
function f() {
  let showArg = () => alert(arguments[0]);
  showArg();
}

f(1); // 1
```

আমরা মনে রাখি, তীর ফাংশনগুলির নিজস্ব `this` নেই। এখন আমরা জানি যে তাদের কাছে বিশেষ `arguments` অবজেক্টও নেই।
````


## Spread syntax [#spread-syntax]

We've just seen how to get an array from the list of parameters.

But sometimes we need to do exactly the reverse.

For instance, there's a built-in function [Math.max](mdn:js/Math/max) that returns the greatest number from a list:

```js run
alert( Math.max(3, 5, 1) ); // 5
```

Now let's say we have an array `[3, 5, 1]`. How do we call `Math.max` with it?

Passing it "as is" won't work, because `Math.max` expects a list of numeric arguments, not a single array:

```js run
let arr = [3, 5, 1];

*!*
alert( Math.max(arr) ); // NaN
*/!*
```

And surely we can't manually list items in the code `Math.max(arr[0], arr[1], arr[2])`, because we may be unsure how many there are. As our script executes, there could be a lot, or there could be none. And that would get ugly.

*Spread syntax* to the rescue! It looks similar to rest parameters, also using `...`, but does quite the opposite.

When `...arr` is used in the function call, it "expands" an iterable object `arr` into the list of arguments.

For `Math.max`:

```js run
let arr = [3, 5, 1];

alert( Math.max(...arr) ); // 5 (spread turns array into a list of arguments)
```

We also can pass multiple iterables this way:

```js run
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];

alert( Math.max(...arr1, ...arr2) ); // 8
```

We can even combine the spread syntax with normal values:


```js run
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];

alert( Math.max(1, ...arr1, 2, ...arr2, 25) ); // 25
```

Also, the spread syntax can be used to merge arrays:

```js run
let arr = [3, 5, 1];
let arr2 = [8, 9, 15];

*!*
let merged = [0, ...arr, 2, ...arr2];
*/!*

alert(merged); // 0,3,5,1,2,8,9,15 (0, then arr, then 2, then arr2)
```

In the examples above we used an array to demonstrate the spread syntax, but any iterable will do.

For instance, here we use the spread syntax to turn the string into array of characters:

```js run
let str = "Hello";

alert( [...str] ); // H,e,l,l,o
```

The spread syntax internally uses iterators to gather elements, the same way as `for..of` does.

So, for a string, `for..of` returns characters and `...str` becomes `"H","e","l","l","o"`. The list of characters is passed to array initializer `[...str]`.

For this particular task we could also use `Array.from`, because it converts an iterable (like a string) into an array:

```js run
let str = "Hello";

// Array.from converts an iterable into an array
alert( Array.from(str) ); // H,e,l,l,o
```

The result is the same as `[...str]`.

But there's a subtle difference between `Array.from(obj)` and `[...obj]`:

- `Array.from` operates on both array-likes and iterables.
- The spread syntax works only with iterables.

So, for the task of turning something into an array, `Array.from` tends to be more universal.


## Get a new copy of an array/object

Remember when we talked about `Object.assign()` [in the past](info:object-copy#cloning-and-merging-object-assign)?

It is possible to do the same thing with the spread syntax.

```js run
let arr = [1, 2, 3];
let arrCopy = [...arr]; // spread the array into a list of parameters
                        // then put the result into a new array

// do the arrays have the same contents?
alert(JSON.stringify(arr) === JSON.stringify(arrCopy)); // true

// are the arrays equal?
alert(arr === arrCopy); // false (not same reference)

// modifying our initial array does not modify the copy:
arr.push(4);
alert(arr); // 1, 2, 3, 4
alert(arrCopy); // 1, 2, 3
```

Note that it is possible to do the same thing to make a copy of an object:

```js run
let obj = { a: 1, b: 2, c: 3 };
let objCopy = { ...obj }; // spread the object into a list of parameters
                          // then return the result in a new object

// do the objects have the same contents?
alert(JSON.stringify(obj) === JSON.stringify(objCopy)); // true

// are the objects equal?
alert(obj === objCopy); // false (not same reference)

// modifying our initial object does not modify the copy:
obj.d = 4;
alert(JSON.stringify(obj)); // {"a":1,"b":2,"c":3,"d":4}
alert(JSON.stringify(objCopy)); // {"a":1,"b":2,"c":3}
```

This way of copying an object is much shorter than `let objCopy = Object.assign({}, obj);` or for an array `let arrCopy = Object.assign([], arr);` so we prefer to use it whenever we can.


## Summary

When we see `"..."` in the code, it is either rest parameters or the spread syntax.

There's an easy way to distinguish between them:

- When `...` is at the end of function parameters, it's "rest parameters" and gathers the rest of the list of arguments into an array.
- When `...` occurs in a function call or alike, it's called a "spread syntax" and expands an array into a list.

Use patterns:

- Rest parameters are used to create functions that accept any number of arguments.
- The spread syntax is used to pass an array to functions that normally require a list of many arguments.

Together they help to travel between a list and an array of parameters with ease.

All arguments of a function call are also available in "old-style" `arguments`: array-like iterable object.
