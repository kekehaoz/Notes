### Advanced TypeScripts Types

[reference link](<https://levelup.gitconnected.com/advanced-typescript-types-with-examples-1d144e4eda9e>)

#### Record

`Record`: it allows you to created a type `map` and is great for creating composite interfaces. To type a variable as `Record`, you have to pass a string as key and some type for its corresponding value

```typescript
const SERVICE: Record<string, string> = {
  doorToDoor: "deliver at door",
  airDelivery: "flying in",
  specialDelivery: "special, delivery"
}


export interface ProductInCart {
  id: string;
  amount: number;	
  label?: string;
}

export class CartModel {
  /** If products' value dont have all ProductInCart required properties, it will mark the error*/
  products: Record<string, ProductInCart> = {};
  error?: Error = undefined;
}
```



#### Partial and Required

##### Partial

`Partial` type makes all properties in the object optional.

Use Partial:

![img](https://cdn-images-1.medium.com/max/1600/1*11cUeBpNXN7Wf9I_xmdw6A.png)

The result same as:

![img](https://cdn-images-1.medium.com/max/800/1*5tHlioPY6tgAnr8fE3B-Tg.png)

##### Requried

`Requried`  make all properties of a described object required

All fields in OwnProps are requried

![img](https://cdn-images-1.medium.com/max/1600/1*bspXbS_te7-x_c_dckM6Cg.png)



#### Pick and Omit

Pick and Omit are flexible ways to re-use your interfaces

##### Pick

`Pick` helps you to use a defined alreay interface but take from the object only those keys that you need

##### Omit

`Omit` which isn't predefined in the Typescript `lib.d.ts` but is easy to defined with `Pick` and `Exclude` It excludes the properties that you don't want to take from an interface

![img](https://cdn-images-1.medium.com/max/1200/1*AJL4dNcuLV4fHB7lbKZ91g.png)



![img](https://cdn-images-1.medium.com/max/1600/1*47qUKESj3sL5Jhma5kZQ0Q.png)

