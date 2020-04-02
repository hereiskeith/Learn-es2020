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
```
