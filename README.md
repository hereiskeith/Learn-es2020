# Learn-ES2020
Take a sip and dive deep in ES2020
reference: https://dev.to/carlillo/es2020-features-in-simple-examples-1513, https://alligator.io/js/es2020/

## `String.prototype.matchAll`
The matchAll() method returns an iterator of all results matching a string against a regaular expression, including capturing groups

### string.match
We find all words that consist of hexadecimal digits only
```
const string = 'Magic hex numbers: DEADBEEF CAFE';
const regex = /\b\p{ASCII_Hex_Digit}+\b/gu;
for (const match of string.match(regex)) {
  console.log(match);
}
/*
Output:
'DEADBEEF'
'CAFE'
*/
```

### string.matchAll
`string.macthAll` gives you the full information for each individual match, including capturing groups.
```
const string = 'Magic hex numbers: DEADBEEF CAFE';
const regex = /\b\p{ASCII_Hex_Digit}+\b/gu;
for (const match of string.matchAll(regex)) {
  console.log(match);
}
/*
Output:
['DEADBEEF', index: 19, input: 'Magic hex numbers: DEADBEEF CAFE']
['CAFE', index: 28, input: 'Magic hex numbers: DEADBEEF CAFE']
*/

const string = 'Favourite Gitehub repos: tc39/ecma262 v8/v8.dev';
const regex = /\b(?<owner>[a-z0-9]+)\/(?<repo>[a-z0-9\.]+)\b/g;
for (const { 0: occurrence, index, input, groups} of string.matchAll(regex)) {
  console.log(`${occurrence} at ${index} with '${input}'`);
  console.log(`→ owner: ${groups.owner}`);
  console.log(`→ repo: ${groups.repo}`);
}

// Output:
// tc39/ecma262 at 25 with 'Favourite Gitehub repos: tc39/ecma262 v8/v8.dev'
// VM843:5 → owner: tc39
// VM843:6 → repo: ecma262
// VM843:4 v8/v8.dev at 38 with 'Favourite Gitehub repos: tc39/ecma262 v8/v8.dev'
// VM843:5 → owner: v8
// VM843:6 → repo: v8.dev
```

## Promise.allSettled

`Promise.allSettled` returns a promise that is fullfilled with an array of promise state snapshots, but only after all the original promises have settled, i.e. become either fulfilled or rejected.

We say that a promise is settled if it is not pending, i.e. if it is either fulfilled or rejected.

```
const promise1 = new Promise(resolve => resolve('Good'));
const promise2 = new Promise((_, reject) => reject('Bad'));
const promise3 = new Promise(resolve => resolve('Even better'));

/*
Promise.all - If any promise is rejected then "Promise.all"
immediately rejects with that error.
*/

Promise.all([promise1, promise2, promise3])
  .then(res => console.log(res))
  .then(err => console.log(err));
// Result:
// Promise {<rejected>: "Bad"}

Promise.all([promise1, promise3])
  .then(res => console.log(res))
  .then(err => console.log(err));
// Result:
// ["Good", "Even better"]

Promise.allSettled([promise1, promise2, promise3])
  .then(res => console.log(res));
// Result:
// [ {status: "fulfilled", value: "Good"},
//   {status: "rejected", reason: "Bad"},
//   {status: "fulfilled", value: "Even better"}
// ]
  
```

## gobalThis object

The `globalThis` was not standardized before ES10.
In production code you would "standardized" it across multiple platforms on your own by writing this monstrosity:

```
// Solution before ES10:
var getGlobal = function () {
  if (typeof self !== 'undefined') { return self; }
  if (typeof window !== 'undefined') { return window; }
  if (typeof global !== 'undefined') { return global; }
  throw new Error('unable to locate global object');
};

// ES10 added globalThis object which should be used from now on to access global scope on any platform

// Access global array constructor
globalThis.Array(0, 1, 2) // [0, 1, 2]

// Similar to window.v = { value: true } in <= ES10 in browser
globalThis.v = { value: true };

```

## Nullish coalescing Operator
When performing property accesses, it is often desired to provide a default value if the result of that property access is `null` or `undefined`. At present, a typical way to express this intent in JavaScript is by using the || operator.

```
let person = {
  profile: {
    name: "",
    age: 0
  }
};

// falsey values produce surprising results:

console.log(person.profile.name || "Anonymous"); // Anonymous
console.log(person.profile.age || 18); // 18
```

The nullary coalescing operator(double question marks operator) is intended these cases better and serves as an equality check against nullart values (null or undefined). If the expression at the left-hand side of the ?? operator evaluates to undefined or null, its right-hand side is returned.

```
console.log(person.profile.name ?? "Anonymous"); // ""
console.log(person.profile.age ?? 18); // 0
```

## Optional Chaining
Similar to the nullish coalescing operator, JavaScript may not act how we want when dealing with falsy values. We can return a value if what we want is undefined, but what if the path to it is undefined?

By adding a question mark before our dot notation we can make any part of a value’s path optional so we can still interact with it.
```
let person = {};

console.log(person.profile.name ?? "Anonymous"); // Cannot read property 'name' of undefined
console.log(person?.profile?.name ?? "Anonymous"); // 'Anonymous'
console.log(person?.profile?.age ?? 18); // 18
```

Also, many API return either an object or null/undefined, and one may want to extract a property from the result only when it is not null:
```
/*
The Optional Chaining operator is spelled ?. It may appear in three positions:

obj?.prop  // optional static property access
obj?.[expr] // optional dynamic property access
fun?.(...args) // optional function or method call
*/

const book = form.querySelector('input[name=book]');
const value = book ? book.value : undefined;

// Optional Chaining operator
const value = form.querySelector('input[name=book]')?.value
```

When some other value than `undefined` is desired for the missing case, this can usually be handled with the Nullish coalescing operator:

```
const book = {
  hashtags: ['Angular'],
  entities: {
    hashtags: ['React']  
  }
}
const hashtags = book.entities && book.entities.hashtags;

// Optional Chaining Operator
const hashtags = book.entities?.hashtags;
// ['React']

// When some other value than "undefined" is desired for the missing case,
// this can usually be handled with the Nullish calescing operator:

const hashtags = book.entities?.hashtags ?? ['React'];
```
