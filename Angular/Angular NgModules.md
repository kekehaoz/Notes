# Angular Modules

### Types of Feature Modules

##### Domain

Domain feature modules deliver a user experience dedicated to a particular application domain.

Domain feature modules consist mostly of declarations. Only the top component is exported.

Domain feature modules rarely have providers. When they do, the lifetime of the provided service should be the same as the lifetime of the module

Domain feature modules  should mostly be lazy load by a lager featrue module like Routed feature modules

##### Routed

Routed featrue modules are domain feature modules whose top components are the targets of router navigation routes.

All lazy-loaded modules are routed feature modules by definition.

##### Routing

A routing module providers routing configuration for another modules and separates routing concerns from its companion module(with forRoot & forChild).

##### Service

Serivce modules provide utility services such as adata access and messaging Ideally, they consist entirely of providers and have no declarations

##### Widget

A widget module makes components, directives and pipes available to external modules. Many third-party UI component libraries are widget modules.

A widget modules should consist entirely of declarations, most of them exported.



### Singleton Service

##### forRoot()

if a module provides both providers and declarations(components, directives, pipes) then loading it in a child injector such as a route, would duplicate the provider instances. The duplication of providers would cause issuses as they would shadow the rout instances, which are probably meant to b singletons.



Angular provides a way to sperate providers out of the module so that same module can be imported into the root module with providers and child modules without providers

1. Create a static method forRoot() on the module
2. Place the providers into the forRoot() method as follows

```typescript
static forRoot(config: UserServiceConfig): ModuleWIthProviders {
  return {
    ngModule: CoreModule,
    providers: [
      {provide: UserServiceConfig, useValue: config}
    ]
  }
}
```



```typescript
import { CoreModule } from './core/core.module';

@NgModule({
  imports: [
    CoreModule.forRoot({userName: 'Miss Marple'})
  ]
})
export class AppModule { }
```





