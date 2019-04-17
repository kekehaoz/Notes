# transducer 中文版

Transducer 是由高阶reducer组成的，它以一个reducer作为参数，并返回另一个reducer

特性：

* 可以组合使用简单的函数组合
* 对于较大的数据和无限的数据流（无人机遥测数据）有较高效率：无论管道中的操作数量有多少，只会枚举元素一次
* 能够处理各种可枚举数据源
* Usable for either lazy or eager evaluation with no changes to transducer pipeline



你可以组合任意数量的transducers来形成一个新的transducer



### 为什么使用Transducers

1. 这个只能用在数组，如果是无限的数据或者一个类似朋友的朋友的社交图
2. 每次使用链式调用，JavaScript都会创建一个中间数组，如果数据量很大，则会降低性能。使用transducers则不会创建中间数组，而是一管道流式传输
3. ？

为了从数组中生成单个完全变换的值，必须首先通过第一个管道运行整个字符串，从而成生一个全新的数组，进而让第二个管道处理，因此必须要等所有的值通过所有的管道才能获得一个结果

如果不使用transducers应该怎么做：？

* Pull： lazy evaluation
* Push： eager evaluation

Pull API 会等到使用者要求下一个值才开始计算。比如generator function

Push API 通过枚举数据源并将其



函数签名

```
reducer = (accumulator, current) => accumulator
transducer = reducer => reducer
```



示例



提前终止

通过一个reduced的值，其他transducers 可以停止enumerating



transducing



