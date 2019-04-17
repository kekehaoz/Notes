# Exploring Angular DOM manipulation techniques using ViewContianerRef

[reference link](https://blog.angularindepth.com/exploring-angular-dom-abstractions-80b3ebcfc02)

The new Angular version runs on different platforms—in a browser, on a mobile platform or inside a web worker.**So a level of abstraction is required to stand between platform specific API and the framework interfaces** In angular these abstractions come in a form of the following reference types: `ElementRef`,` TemplateRef`,` ViewRef`, `ComponentRef` and `ViewContainerRef`.

### @ViewChild & @ViewChildren

Angular provides a mechanism called DOM queries.The latter returns multiple references as QueryList object.

##### ViewChild Description

View queries are set before the ngAfterViewInit callback is called.

`@ViewChild([reference from template], {read: [reference type]})`

###### Metadata Properties:

- selector - the directive type or the name used for querying.
- read - read a different token from the queried elements.

Supported selectors include:

- any class with @Component or @Directive decorator
- a template reference variable **as string**
- any **provider** defined in the child component tree of the current
- Any provider defined through a string token(@ViewChild('someToken')  someTokenVal:any)
- a TemplateRef (<ng-template></ng-template>  @ViewChild(TemplateRef) template;)

Read property is not always required, since angular can infer the reference type by the type of the DOM element. For example, if it’s a simple html element like `span`, angular returns `ElementRef.` If it’s a `template` element, it returns `TemplateRef`. Some references, like `ViewContainerRef` cannot be inferred and have to be asked for specifically in `read` parameter. Others, like `ViewRef` cannot be returned from the DOM and have to be constructed manually.



### ElementRef

It only holds the native element it's associated with. Accessing native DOM element is discouraged by Angualr team. Not only it poses security risk, but it also creates tight coupling between your application and rendering layers which makes is difficult to run an app on multiple platforms.

`ElementRef` can be returned for any DOM element using `ViewChild`  decorator. But since all components are hosted inside a custom DOM element and all directives are applied to DOM elements and all directives areapplied to DOM elements, component and directive classes can obtain an instance of `ElementRef` associated with theire host element throught DI mechanism



### TemplateRef

It's a group of DOM elements  that are reused in view across the app. With [HTML5 standard introduced template tag](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) a browser parses `html` and creates `DOM` tree but not renders it. It then can be accessed through `content` property.



### ViewRef

This type of abstraction represents an angualr View. In angular workd a View is fundamental building block of the applicatonUI. **Angular philosophy encourages developers to see UI as a composition of Views, not as a tree of standalone html tags**.

Angular support two types of views:

- Embedded Views which are linked to a Template
- Host Views which are linked to a Component

##### Creating embedded view

```typescript
ngAfterVIewInit() {
  let view = this.tpl.createEmbeddedVIew(null);
}
```

##### Creating host view

A component can be creaed dynamically using `ComponentFactoryResolve`

```typescript
constructor(private injector: Injector,
            private r: ComponentFactoryResolve) {
  let factory = this.r.resolveComponentFactory(ColorComponent);
  let componentRef = factory.create(injector);
  let view = componentRef.hostView;
}
```

**In Angular, each component is bound to a prticular instance of a injector**, so we're passing the current injector instance when creating the component. Don't forget that components that are instantiated dynamically must be added to EntryComponents of a module or hosting component.



### ViewContianerRef

Represents a contianer where one or more views can be attached.

**Any DOM element can be used as a view container.**

Usually, a good candidate to mark a place where a `ViewContianer` should be created is `ng-container` element.	



### ngTemplateOutlet and ngComponentOutlet

##### ngTemplateOutlet

This is one marks a DOM element as a ViewContainer and inserts an embedded view created by a template in it with out the need to explicitly doing this in component class.

```html
<span>I am first span</span>
<ng-container [ngTemplateOutlet]="tpl"></ng-container>
<span>I am last span</span>
<ng-template #tpl>
 	<span>I am span in template</span>
</ng-template>
```

##### ngComponentOutlet

This directive is analogues to `ngTemplateOutlet` with the difference that it creates a host view (instantiates a component), not an embedded view.

```html
<ng-container *ngComponentOutlet="ColorComponent"></ng-container>
```







