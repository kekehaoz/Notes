# Transducers: Effcient Data Processing Pipelines in JavaScript

[reference link](https://medium.com/javascript-scene/transducers-efficient-data-processing-pipelines-in-javascript-7985330fe73d)

> Transduce: Derived from the 17th century scientific latin, "transductionem" means "to change over convert". It is further derived from "transducere/traducere", which means "to lead along or across, transfer".

###### A transducer is a composable higeher-order reducer. It takes a reducer as input, and returns another reducer.

Transducers are:

* Composable using simple function composition
* Efficient for large collectoins or infinite stream: Only enumerates over the elements once, regardless of the number of operations in pipline
* Able to transduce over any enumerable source(e.g., arrays, trees, stream, graphs, etc...)
* Usable for either lazy or eager evaluation with no changes to the transducer pipeline

```javascript
const friends = [
  { id: 1, name: 'Sting', nearMe: true },
  { id: 2, name: 'Radiohead', nearMe: true },
  { id: 3, name: 'NIN', nearMe: false },
  { id: 4, name: 'Echo', nearMe: true },
  { id: 5, name: 'Zeppelin', nearMe: false }
]

const isNearMe = ({ nearMe }) => nearMe;
const getName = ({ name }) => name;
const getFriendsNearMe = compose(
  filter(isNearMe),
  map(getName)
)
const result = toArray(getFriendsNearMe, friends);
```

Tranducers don't do anything until you tell them to start and feed them some data to process, whch is why we need `toArray()`. It supplies the transducible process and tells the transducer to transduce the result into a new array. **You could tell it to transduce to a stream, or an observable, or anything you like, instead of calling toArray()**

***



First, You can compose effects like this:

```javascript
const double = x => x*2;
const isEven = x => x%2 === 0;

const arr = [1,2,3,4,5,6];

const result = arr
	.filter(isEven)
	.map(double)
;

console.log(result);
// [4, 8, 12]
```

What if you want to filter and double a potentially infinite stream of numbers, such as a drone's telemetry data(无人机遥测数据)?

Arrays can't be infinite, and each stage in the array processing requires you to process the entire array before a single value can flow throught the next stage in the pipeline. The same limitation means that composition using array methods will have degraded performance because a new array will need to be created and a new collection iterated over for each stage in the composition.



### Transducer signatures looks like this

`reducer = (accumulator, current) => accumulator`

`transducer = reducer => reducer`

`transducer =((accumulator, current) => accumulator) => (accumulator, current) => accumulator)`

Most transducers will need to be partially applied to some arguments to specialize them. For example, a map transducer might look like this:

```javascript
map = transfrom => reducer => reducer
//Or
map = (a => b) => step => reducer
```

A map transducer takes a mapping function(called a transform) and a reducer(called the step function), and returns a new reducer.` The step function is a reducer to call when we've produced a new value to add to the accumulator in the next step`

###### example

```javascript
const compose = (...fns) => x => fns.reduceRight((y, f) => f(y), x);


const map = f => step => (a, c) => step(a, f(c));

const filter = predicate => step => (a, c) => predicate(c) ? step(a, c) : a;

const isEven = n => n % 2 === 0;
const double = n => n * 2;
// const test = n => n - 1;

const doubleEvents = compose(
  filter(isEven),
  map(double),
  // map(test)
); // x => fns.reduceRight((y, f) => f(y), x);

const arrayConcat = (a, c) => a.concat([c]);

const xform = doubleEvents(arrayConcat);
// x = (a, c) => a.concat([c])
// y = (a, c) => x(a, f(c))
// z = (a, c) => predicate(c) ? y(a, c) : a

const result = [1,2,3,4,5,6].reduce(xform, []); // [4, 8, 12];

console.log(result);
```

Map applies a function to the values inside some context.In this case, the context is the transducer pipeline. 

`const map = f => step => (a, c) => step(a, f(c));`

You can use it like this:

```javascript
const double = x => x * 2;

const doubleMap = map(double);

const step = (a, c) => console.log(c);

doubleMap(step)(0, 4); // 8
doubleMap(step)(0, 21); // 42
```

`const filter = predicate => step => (a, c) => predicate(c) ? step(a, c) : a;`

Filter takes a predicate function and only passes throught the values that match the predicate. Otherwise, the returned reducer returns the accumulator, unchanged.

Since both of these functions take a reducer and return a reducer, we can compose them with simple function composition:

```JavaScript
const compose = (...fns) => x => fns.reduceRight((y, f) => f(y), x);

const dobuleEvents = compose(
	filter(isEvent),
	map(double)
);
```

This will also return a transducer, which means we must supply a final step function in order to tell the transducer how to accmulate the result:

```javascript
const arrayConcat = (a, c) => a.concat([c]);

const xform = doubleEvents(arrayConcat);
```

The result of this call is a standard reducer that we can pass directly to any compatible reduce API. The second argument represents the initial value of the reduction.



### Transducers Compose Top-to-Bottom

Transducers under standard function composition(`f(g(x))`)apply top to bottom / left to right rather than bottom-to-top/right-to-left. In other words, using normal function composition, compose(f, g) means "compose f after g". Transducers wrap around other transducers under composition. In other, a transducer says "I'm going to do my thing, and then call the next transducer in the pipeline", which has the effect of turning the execution stack inside out.

Imagine you have a stack of papers, the top labeled, f, the next, g, and the next h. For each sheet, take the sheet off the top of the stack and place it onto the top of a new adjacent stack. When you're done, you'll have a stack whose sheets relabeled h, then g, then f.



### Transducer Rules

As with most things in software, transducers and transducing process need to obey some rules:

1. Initializaiton: Given no initial accumulator value, a transducer must call the step function to produce a valid initial vlaue to act on. The value should represent the empty state.For example, an accumulator that accumulates an array shoud supply an empty array when its step function is called with no arguments.
2. Early termination: A process that uses transducers must check for  and stop when it receives a reduced accumulator value.Additionally, a transducer step function that uses a nested reduce must check for and convey reduced values when they are encountered.
3.  Completion([optional]): Some transducing process never complete, but those that do should call the completion function to produce a final value and /or flush state, and stateful transducers should supply a completion operation that cleans up any accumulated resources and potentially produces one final value

#### Initialization

Let's make sure the map operation obeys the initialization law.

`const map = f => step => (a = step(), c) => (step(a, f(c)))`

If there is no value for `a`(the accumulator), we' ll create one by asking the next reducer in the chain to produce it. Eventually, it will reach the end of the pipeline and (hopefully) create a valid initial value for us.

Remember this rule: When called with no arguments, a reducer should always return a valid initial(empty) value fron the reduction. It's generally a good idea to obey this rule for any reducer function, including reducers for React or Redux.

#### Early Termination

It's possible to signal to other transducers in the pipeline that we're done reducing, and they should not expect to process any more values. Upon seeing a `reduced` value, other transducers may decide to stop adding to the collection, and the transducing process(as controlled by the final step() function) may decide to stop enumerating over values.



```javascript
const reduced = v => ({
  get is Reduced() {
    return true;
  },
  valueOf: () => v,
  toString: () => `Reduced(${ JSON.stringify(v) })`
});
```



```javascript
import { compose, filter, map, into } from 'ramada';

const isEven = n => n % 2 === 0;
const double = n => n * 2;

const doubleEvents = compose(
	filter(isEven),
	map(double)
);

const arr = [1,2,3,4,5,6];

const result = into([], doubleEvens, arr);

console.log(result); // [4, 8, 12]
```



​         

### Transducing

It's possible to transduce over lots of different types of data, but the process can be generalized:

```typescript
import curry = (
	f, arr = []
) => (...args) => (
	a => a.length === f.length ?
  	f(...a) :
  	curry(f, a)
)([...arr, ...args]);

const transduce = curry((step, initial, xform, foldable) =>
	foldable.reduce(xform(step), initial)
);
```











