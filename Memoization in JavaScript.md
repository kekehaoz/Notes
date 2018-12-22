# Memoization in JavaScript

[reference link]([reference link](https://blog.bitsrc.io/understanding-memoization-in-javascript-to-improve-performance-2763ab107092))

```javascript
function meoize(fn) {
  return function (...args) {
    fn.cache = fn.cache || {};
    return fn.cache[args] 
      		 ? fn.cache[args] 
    			 : (fn.cache[args] = fn.apply(this.args);
  }
}

// usage
memoizedFunction = memoize(functionToMemoize);
memoizedFunction(args);

Function.prototype.memoize = function () {
  var self = this;
  return function (...args) {
    self.cache = self.cache || {};
    return self.cache[args] 
    			 ? self.cache[args]
    			 : (self.cache[args] = self(args));
  }
}
// usage
memoizedFunction = functionToMemoize.memoize();
memoizedFunction(args)
```

### When To Use

+ Memoization should be implemented on pure functions
+ Memoization wroks best when dealing with recursive functions which are used to perform heavy operations like GUI rendering, Sprit and animations physics, etc



###### Demo

``` javascript
function sqrt(arg) {
  return Math.sqrt(arg);
}
const memoizedSqrt = memoize(sqrt);
console.log("non-memoized call");
console.log(memoizedSqrt(4));
console.timeEnd("non-memoized call");
console.time("memoized call");
console.log(memoizedSqrt(4));
console.timeEnd("memoized call");
```

###### result

```bash
$ node memoize
2
non-memoized call: 8.958ms
2
memoized call: 1.298ms
```

# 