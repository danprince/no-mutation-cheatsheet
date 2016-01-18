# No Mutation Cheatsheet
A quick reference for alternatives to mutable methods for Javascript collections.

## Arrays
Arrays have lots of methods that will introduce side effects into your functions. It's possible to avoid the following _mutating_ methods, with a mixture of `slice`, `concat`, `map`, `reduce` and `filter`.

### pop/shift
`pop` and `shift` remove the last and first element from an array, respectively.

```js
// remove the first element
return arr.slice(1);

// remove the last element
return arr.slice(0, -1);
```

### push/unshift
`push` and `unshift` add an element to the end and start of an array, respectively.

```js
// add element at the end
return arr.concat([element]);

// add element at the start
return [element].concat(arr);
```

### reverse
`reverse` reverses the ordering of an array in-place.

```js
return arr.reduce(function(reversed, item) {
  return [item].concat(reversed);
}, []);
```

### sort
It's possible to re-implement `sort` in a side effect free way --- as shown with reverse --- but it requires a lot more code.

A simpler alternative is to copy the array, then use the mutable sort method on the new copy.

```js
function copy(arr) {
  return arr.map(function(item) {
    return item;
  });
}

return copy(arr).sort();
```

### splice
`splice` is a multipurpose method for removing and inserting items into an array.

```js
// to remove `count` items at `index`
var before = arr.slice(0, index),
    after  = arr.slice(index + count);

return before.concat(after);

// insert items at `index`
var before = arr.slice(0, index),
    after  = arr.slice(index);

before
  .concat(items)
  .concat(after);
```

### forEach
`forEach` is the odd-one-out here, as the method itself creates no side effects. However, it does _encourage_ them, in the same way `for`, `for-in` and `while` loops do.

Here are some common patterns that use these looping constructs with side effects.

```js
// transform each element in an array
arr.forEach(function(item, index) {
  arr[index] = item * 2;
});
return arr;

// without side effects
return arr.map(function(item) {
  return item * 2;
});
```

```js
// remove some items from an array
var kept = [];
arr.forEach(function(item) {
  if(item < 5) {
    kept.push(item);
  }
});
return kept;

// without side effects
return arr.filter(function(item) {
  return item < 5;
});
```

```js
// sum an array of numbers
var total = 0;
nums.forEach(function(num) {
  total += num;
});

// without side effects
var total = nums.reduce(function(total, num) {
  return total + num;
}, 0);
```

## Objects
Unlike arrays, the interface for objects is minimal and most of our interactions with them are syntactic. Unfortunately, this makes it much harder to work with them in an immutable way.

Rather than looking at alternatives to existing methods we'll look at some strategies for creating new objects rather than mutating them. The essential quality here is that we have to make sure that the updated version is not a reference to the original object.

The simplest way is to create new objects with the appropriate properties changed.

```js
const counter = { count: 0 };
return { count: counter.count + 1 };
```

This can work well for simple cases, but the more properties in your object, the more code you'll have to write each time you want to change it. Instead we can create a new object with the properties we care about and let `Object.assign` handle the rest.

```js
const counter = { count: 0 };
return Object.assign({}, counter, { count: counter.count + 1 });
```

A new syntax proposal for ES7 makes use of the spread syntax (`...`) which can help us translate this example into much more readable code.

```js
return { ...counter, count: counter.count + 1 };
```

This syntax is available for use in [Babel][1].

[1]: http://babeljs.io

