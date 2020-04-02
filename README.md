# Learn-ES2020
Take a sip and dive deep in ES2020
reference: https://dev.to/carlillo/es2020-features-in-simple-examples-1513, https://alligator.io/js/es2020/

## `String.prototype.matchAll`
The matchAll() method returns an iterator of all results matching a string against a regaular expression, including capturing groups

```
// 1. string.match
// We find all words that consist of hexadecimal digits only
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
