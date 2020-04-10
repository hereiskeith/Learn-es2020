# Learn-ES2020
Take a sip in ES2020 and dive deep in JavaScript

<p><br /></p>

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
This works well for the common case of null and undefined values, but there are a number of falsy values that might produce surprising results.
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

## Dynamic Import

If you had a file full of utility functions, some of them may rarely be used and importing all of their dependencies could just be a waste of resources. Now we can use async/await to dynamically import our dependencies when we need them.

```
// Previously
const add = (num1, num2) => num1 + num2;

export { add };

// with es20

const doMath = async (num1, num2) => {
  if (num1 && num2) {
    const math = await import('./math.js');
    console.log(math.add(5, 10));
  };
};

doMath(4, 2);
```

### Private Class Variable

One of the main purposes of classes is to contain our code into more reusable modules. Because you'll create a class to will be used in many places that you may not want everything inside it to be available globally.

Now by adding a hash symbol in front of your variable or function we can preserve them entirely for internal use inside the class inside the class.

```
class Message {
  #message: "Nice!"
  
  greet() {
    console.log(this.#message)
  }
}

const greeting = new Message();

greeting.greet(); // Nice!

console.log(greeting.#message); // Private name #message is not defined
```

### BigInt
We won’t go into the technical details, but because of how JavaScript handles numbers, when you go high enough things start to get a bit wonky. The largest number JavaScript can handle is 2^53, which we can see with MAX_SAFE_INTEGER.
```
const maxNum = Number.MAX_SAFE_INTEGER;

console.log(maxNum); // 9007199254740991

// Anything above that and things start to get a little weird…
console.log(max + 1); // 9007199254740992
console.log(max + 2); // 9007199254740992
console.log(max + 3); // 9007199254740994
console.log(Math.pow(2, 53) == Math.pow(2, 53) + 1); // true
```
We can get around this with the new BigInt datatype. By throwing the letter ‘n’ on the end we can start using and interacting with insanely large numbers. We’re not able to intermix standard numbers with BigInt numbers, so any math will need to be also done with BigInts.

```
const bigNum = 100000000000000000000000000000n;

console.log(bigNum * 2n); // 200000000000000000000000000000n
```

BigInt is the 7th primitive type and It is an arbitrary-precision integer. The variables can now represent ²⁵³ numbers and not just max out at 9007199254740992.

```
// BigInt constructor
const larger = 9007199254740991n;
const integer = BigInt(9007199254740991); //9007199254740991n
const same = BigInt("9007199254740991"); //9007199254740991n

//Typeof
typeof 10; // "number"
typeof(10n); // "bigint"

//Equality
10n === BigInt(10); // true
10n == 10; // true

// Math operation only works within its own type
20n / 10n; // 2n
20n / 2; // Uncaught TypeError: Cannot mix BigInt and other types```
