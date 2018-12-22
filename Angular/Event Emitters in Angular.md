# Event Emitters in Angular

[reference link](https://netbasal.com/event-emitters-in-angular-13e84ee8d28c)

>  Data flows into your component via property bindings and flows out of your component through event bindings

Child component notify his parent about something with the `Output` decorator with `EventEmitter` to create a custom event.

```typescript
@Component({
  selector: 'add-todo',
  template: `<input type="text" placeholder="Add todo.." [formControl]="control">
             <button (click)="add.next(control.value)">Add</button>
`,
})
export class AddTodoComponent {
  control : FormControl = new FormControl("");
  @Output() add = new EventEmitter();
}
```

The parent

```typescript
<add-todo (add)="addTodo($event)"></add-todo>
```



### EventEmitter source code

```typescript
export declare class EventEmitter<T> extends Subject<T> {
    __isAsync: boolean;
    constructor(isAsync?: boolean);
    emit(value?: T): void;
    subscribe(generatorOrNext?: any, error?: any, complete?: any): any;
}
```

> Event Emitters are just Subjects

You can pass a boolean to `EventEmitter` that will determine whether to send events in a synchronous or asynchronous way(The default is synchronous)



#### With Rx 

Because `EventEmitter` are `Subjects` ,we can use all Rx goodness. For example, emit an event only if we have a value

```typescript
@Output() add = new EventEmitter().pipe(filter(v => !!v));
```

Use any `Subject`

```typescript
@Output() add = new BehaviorSubject("Awesome").pipe(filter(v => !!v));
```



### EventEmitters !== DOM events

Unlike DOM events Angular custom events do not bubble.

#### The solutions

###### 1. Keep passing the event up the tree

###### 2. Use native DOM events

###### 3. Shared Service



