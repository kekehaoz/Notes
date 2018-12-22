# Component Styles

### 使用组件styles

形式如下

```typescript
@Component({
  selector: 'app-root',
  template: `
    <h1>Tour of Heroes</h1>
    <app-hero-main [hero]="hero"></app-hero-main>
  `,
  styles: ['h1 { font-weight: normal; }']
})
export class HeroAppComponent {
/* . . . */
}
```

这样添加的style只会在组件内生效



##### 特殊的选择器

`:host` 伪类选择器

​	用于选择该组件元素，在hero-details组件内定义，则选择的是`<app-hero-detail>`

​	:host selector is the only way to target the host element

```css
:host(.active) {
  border-width: 3px;
}
```

:host-context

:host-context()在当前组件宿主元素的祖先中查找CSS类，直到根节点位置

```css
:host-context(.theme-light) h2{
  background-color: #eef;
}
```

只有当祖先元素存在theme-light类时，才会把样式应用到组件内部的h2标签里样式模块化

- 设置 styles 或 styleUrls 元数据 
-  内联在模板的 HTML 中 
-  通过 CSS 文件导入 

以上方法添加的样式只会影响该组件内部，不会影响组件外部 外部及全局样式文件需要在angular.json文件中配置，在它的style区注册这些全局文件 

### View encapsulation

通过@Component的mode字段可以选择CSS样式封装类型：

* ShadowDom
* Native 使用shadow DOM一个现已弃用的版本 [learn about the changes]([learn about the changes](https://hayato.io/2016/shadowdomv1/))
* Emulated(default)
* None Angular不进行view封装，直接将CSS添加到全局作用域



##### Shadow DOM

[原文链接]([原文地址](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM))

Shadow dom用于将dom封装成一个组件，将结构，样式，行为隐藏，并和其他代码进行分离![4AE9DE5D-9107-474B-A81D-F7E133C1ACE1](/var/folders/yp/2wsryqqx6j74vny03kgrplrc0000gp/T/com.yinxiang.Mac/WebKitDnD.miYyyi/4AE9DE5D-9107-474B-A81D-F7E133C1ACE1.png)

cheats

* Shadow host: The regular DOM node that the shadow DOM is attached to.
* Shadow tree: The DOM tree inside the shadow DOM.
* Shadow boundary: the place where the shadow DOM ends, and the regualr DOM begins.