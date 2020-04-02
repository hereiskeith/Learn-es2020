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
string.macthAll gives you the full information for each individual match, including capturing groups.
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

Promise.allSettled returns a promise that is fullfilled with an array of promise state snapshots, but only after all the original promises have settled, i.e. become either fulfilled or rejected.

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
