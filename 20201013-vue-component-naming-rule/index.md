# Vue 組件命名學


<!--more-->

## 命名的方式
- `my-first-component` kebab-case (串燒)
- `myFirstComponent` camelCase (駝鋒)
- `MyFirstComponent` PascalCase (pascal 是一門程式語言)


## 宣告組件的命名沒有限制

可以使用三種命名的任一種

```javascript
Vue.component('my-first-component', { /* ... */ })
// or
Vue.component('myFirstComponent', { /* ... */ })
// or
Vue.component('MyFirstComponent', { /* ... */ })
```

## 模版的命名

### 用 HTML 作為 View 的模版

- 只能用 `kebab-case`
- 不能 self closing (一定要加上 closing tag `</my-first-component>`)

```html
<my-first-component></my-first-component>
```

### 用 template 作為模版

- 不管宣告時的命名方式，皆可以用 `kebab-case`
- 也可以用 `camelCase` 或 `PascalCase`，但**須與宣告的命名方式相同** (如：都是camelCase)
- 可以 self closing

```javascript
Vue.component('my-first-component', { /* ... */ })
// or
Vue.component('myFirstComponent', { /* ... */ })
// or
Vue.component('MyFirstComponent', { /* ... */ })
new Vue({
    el: '#app',
    template: `
        <div>
            <my-first-component/>
        </div>
    `,
})
```

```javascript
Vue.component('myFirstComponent', { /* ... */ })

new Vue({
    el: '#app',
    template: `
        <div>
            <myFirstComponent/>
        </div>
    `,
})
```

```javascript
Vue.component('MyFirstComponent', { /* ... */ })

new Vue({
    el: '#app',
    template: `
        <div>
            <MyFirstComponent/>
        </div>
    `,
})
```

## 結論

- 均使用 `kebab-case` 準沒錯
- template模版可以 self closing，HTML模版不行
- 宣告組件的命名沒有限制
- HTML模版必用 `kebab-case`，template 模版可以使用同宣告組件的命名方式

## 參考資料

- [Vue 官方網站](https://vuejs.org/v2/guide/components-registration.html#Name-Casing)
- [精通 VueJS 前端開發完全指南](https://hiskio.com/courses/145/lectures/5403)
