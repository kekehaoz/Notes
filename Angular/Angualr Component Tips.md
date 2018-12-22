# Angualr Component Tips

[reference link](https://netbasal.com/angular-components-tips-and-tricks-d9e725871d58)

### Tip1

```typescript
export function getOptionTemplate(isMulti = false) {
  return `
     <div class="dato-option">
       ${isMulti ? '<input type="checkbox">' : ''}
       <ng-content></ng-content>
     </div>
  `
}

@Component({
  selector: 'dato-option',
  template: getOptionTemplate(),
})
export class DatoOptionComponent {
}
```

```typescript
@Component({
  selector: 'dato-option-multi',
  template: getOptionTemplate(true)
})
export class DatoOptionMultiComponent extends DatoOptionComponent {
 }
```

Use function to judge and generate template model

### Tip2

```typescript
@ContentChildren(DatoOptionComponent) options: QueryList<DatoOptionComponent>;
@ContentChildren(DatoOptionMultiComponent) optionsMulti: QueryList<DatoOptionMultiComponent>;
```

```typescript
@Component({
  selector: 'dato-option-multi',
  template: getOptionTemplate(true),
  providers: [{provide: DatoOptionComponent, useExisting: DatoOptionMultiComponent}]
})
export class DatoOptionMultiComponent extends DatoOptionComponent {
}
```

```typescript
// with above we can write this
@ContentChildren(DatoOptionComponent) options: QueryList<DatoOptionComponent>;
```

Use DI and typescript to solve the different @ContentChildren inject problem

### Tip3

```typescript
@Component({
  selector: 'dato-option:not([multi])',
  ...
})
export class DatoOptionComponent implements OnInit {
}
```

```typescript
@Component({
  selector: 'dato-option[multi]',
  ...
})
export class DatoOptionMultiComponent extends DatoOptionComponent {
}

```

Use attribute selector or others to be the @Component selector