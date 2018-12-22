# NgRx

### Actons

Actions express  *unique events* that happen throughout your application.

##### Action interface

```typescript
interface Action {
  type: string;
}
```

```javascript
{
  type: '[Login Page] Login',
   payload: {
     username: string;
     password: string;
   }
}
```

Action with *payload* in *Login page*

##### Action rules

* Upfront - write actions before developing features to understand and gain a shared knowledge of the feature being implemented.
* Divide - categorize actions based on the event source
* Many
* Event Driven
* Descriptive - provide context that are targeted to a unique event with more detailed information you can use to aid in debugging with the developer tools

```typescript
import { Action } from '@ngrx/store';

export class Login implements Action {
  readonly type = '[Login Page] Login';
  
  constructor(public payload: {username: string; password: string}) {}
}
```

Instantiate a new instance of the action to use when dispatching

```typescript
store.dispatch(new Login({ username: 'test', password: 'test'}));
```

##### Creating action unions

```typescript
import { Action } from '@ngrx/store';

export enum ActionTypes {
	Login = '[Login Page] Login'      
}

export class Login implements Action {
  readonly type = ActionTypes.Login;
  
  constructor(public payload: { username: string; password: string}) { }
}

export type Union = Login;
```



### Reducers

Reducers in NgRx are responsible for handling transltions from one state to the next state in your application.

```javascript
import { Action } from '@ngrx/store';
 
export enum ActionTypes {
  IncrementHome = '[Scoreboard Page] Home Score',
  IncrementAway = '[Scoreboard Page] Away Score',
  Reset = '[Scoreboard Page] Score Reset',
}
 
export class IncrementHome implements Action {
  readonly type = ActionTypes.IncrementHome;
}
 
export class IncrementAway implements Action {
  readonly type = ActionTypes.IncrementAway;
}
 
export class Reset implements Action {
  readonly type = ActionTypes.Reset;
 
  constructor(public payload: { home: number; away: number }) {}
}
 
export type ActionsUnion = IncrementHome | IncrementAway | Reset;
```

Actions which are needed in next

##### Defining the state shape & Setting the initial state

```typescript
import * as Scoreboard from './scoreboard.actions';

export interface State {
  home: number;
  away: number;
}

export const initialState: State = {
  home: 0,
  away: 0,
};
```

##### Creating the reducer function

```typescript
export function reducer(
	state = initialState,
  action: Scoreboard.ActionsUnion
  ): State {
  switch (action.type) {
    case Scoreboard.ActionTypes.IncrementHome: {
      return {
        ...state,
        home: state.home + 1,
      };
    }
 
    case Scoreboard.ActionTypes.IncrementAway: {
      return {
        ...state,
        away: state.away + 1,
      };
    }
 
    case Scoreboard.ActionTypes.Reset: {
      return action.payload; // typed to { home: number, away: number }
    }
 
    default: {
      return state;
    }
  }
}
```

Reducers use switch statements in combination with TypeScript's discriminated unions defined in your actions to provide type-safe processing of actions in a reducer.Switch statements use type unions to determine to the correct shape of the action being consumed in each case.

When an action is dispatched, *all registered reducers* receive the action. For this reasion, each switch statement always includes a default case that returns the rpevious state when the reducer function doesn't need to handle the action

##### Registering root state

The `StoreModule.forRoot()`registers the global provides for your application, including the `Store` service you inject into your components and services to dispatch actions and select pices of state.

```typescript
import { NgModule } from '@angular/core';
import { StoreModule } from '@ngrx/store';
import { scoreboardReducer } from './scoreboard.reducer';

@NgModule({
  imports: [StoreModule.forRoot({ game: scoreboardReducer })],
})
export class AppModule {}
```

##### Register feature state

`ScoreboarModule`

```typescript
import { NgModule } from '@angular/core';
import { StoreModule } from '@ngrx/store';
import { scoreboardReducer } from './scoreboard.reducer';

@NgModule({
  imports: [StoreModule.forFeature('game', scoreboardReducer)],
})
export class ScoreboardModule {}
```

`AppModule`

```typescript
import { NgModule } from '@angular/core';
import { StoreModule } from '@ngrx/store';

@NgModule({
  imports: [StoreModule.forRoot({}), ScoreboardModule],
})
export class AppModule {}
```

Whether your feature states are loaded eagerly or lazily depends on the needs of your application. You use feature states to build up your state object over time and through different feature areas.

### Selectors

Selectors are pure functions used for obtaining slices of store state. @ngrx/store provides a few helper functions for optimizing this selection. Selectors provide many features when selecting slices of state.

* Portable
* Memoization
* Composition
* Testable
* Type-safe

When using the createSelector and createFeatureSelector functions @ngrx/store keeps track of the lasted arguments in which your selector function was invoked.

Because selectors are pure functions, the last resut can be returned when the arguments match without reinvoking your selector function.

```typescript
// reducers.ts
import { createSelector } from '@ngrx/store';

export interface FeatureState {
  counter: number;
}

export interface AppState {
  featrue: FeatureState;
}

export const selectFeature = (state: AppState) => state.feature;

export const selectFeatureCount = createSelector(
	selectFeature,
	(state: FeatureState) => state.counter;
)
```

##### Using selectors for multiple pieces of state

```javascript
// reducers.ts
import { createSelector } from '@ngrx/store';

export interface User {
  id: number;
  name: string;
}

export interface Book {
  id: number;
  userId: number;
  name: string;
}

export interface AppState {
  selectedUser: User;
  allBooks: Book[];
}

export const selectUser = (state: AppState) => state.selectedUser;
export const selectAllBooks = (state: AppState) => state.allBooks;

export const selectVisibleBooks = createSelector(
  selectUser,
  selectAllBooks,
  (selectedUser: User, allBooks: Books[]) => {
    if (selectedUser && allBooks) {
      return allBooks.filter((book: Book) => book.userId === selectedUser.id);
    } else {
      return allBooks;
    }
  }
);
```

#### Using selectors with props

We can add the multiply factor as a `prop`

`createSelector(s1: SelectorWithProps, projector: (s1: S1, props: Props) => Result) : MemoizedSelectorWithProps`



```typescript
export const getCount = createSelector(
	getCounterValue,
	(counter, props) => counter * props.multiply
);
```

```ty
ngOnInit() {
  this.couter = this.store.pipe(select(fromRoot.getCount, { multiply: 2}));
}
```

> A Selector only keeps the prvious input arguments in its caceh. If you re-use this selector with another multiply facotr, the selector would always have to re-evaluate its value. In order correctly memoize the selector, wrap the selector inside a factory function to create different instances of the selector.

```typescript
export const getCount = () =>
	createSelector(
  	(state, props) => state.counter[props.id],
    (counter, props) => counter * props.multiply
  )
```

```typescript
ngOnInit() {
  this.counter2 = this.store.pipe(select(fromRoot.getCount(), { id: 'counter2', multiply: 2}))
  this.counter4 = this.store.pipe(select(fromRoot.getCount(), { id: 'counter4', multiply: 4}))
  this.counter6 = this.store.pipe(select(fromRoot.getCount(), { id: 'counter6', multiply: 6}))
}
```

#### Selecting Feature States

```typescript
import { createFeatureSelector } from '@ngrx/store';

export interface FeatureState {
  counter: number;
}

export interface AppState {
  feature: FeatureState;
}


export const selectFeature = createFeatureSelector<AppState, FeatureState>('feature');


export const selectFeatureCount = createSelector(
	selectFeature,
  (state: FeatureState) => state.counter
)
```

#### Resetting Memozied Selecltors





#### Advanced Usage

##### Select a non-empty state using pipeable operators

We can selcet the data is only intersted with selector

```typescript
import { map, filter } from 'rxjs/operators';
import { select } from '@ngrx/store';

store
	.pipe(
		select(selectValues),
  	filter(val => val !== undefined)
  )
	.subscribe();
```

##### Extracting a pipeable operator





































