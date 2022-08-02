# プロパティでない属性

このページは [コンポーネントの基本](https://v3.ja.vuejs.org/guide/component-basics.html) が読まれていることが前提となっています。<br>
コンポーネントを扱ったことのない場合はこちらのページを先に読んでください。<br>

プロパティでない属性とは、コンポーネントに渡される属性や異ベントリスナのうち、[props](https://v3.ja.vuejs.org/guide/component-props.html) や [emits](https://v3.ja.vuejs.org/guide/component-custom-events.html#%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%A0%E3%82%A4%E3%83%98%E3%82%99%E3%83%B3%E3%83%88%E3%81%AE%E5%AE%9A%E7%BE%A9) で定義されたものを除いたものをいいます。<br>
よくある例としては、コンポーネント要素タグに付与する `class`、`style`、`id`などの属性があります。<br>
これらの属性には `$attrs`プロパティでアクセスできます。<br>

## 属性の継承

ただ一つのルート要素を持つコンポーネントの場合、プロパティでない属性はルート要素にそのまま追加されます。<br>
例えば date-picker コンポーネントの場合は次のような形になります。<br>

```js:sample.js
app.component('date-picker', {
  template: `
    <div class="date-picker">
      <input type="datetime-local" />
    </div>
  `
})
```

`data-status`属性を用いて date-picker コンポーネントの状態を定義するような場合には、この属性はルート要素(すなわち `div.date-picker` )に適用されます。<br>

```html:sample.html
<!-- プロパティでない属性とともに用いられる Date-picker コンポーネント -->
<date-picker data-status="activated"></date-picker>

<!-- 実際には以下のような形でレンダリングされます -->
<div class="date-picker" data-status="activated">
  <input type="datetime-local" />
</div>
```

イベントリスナーについても同様なことが言えます。<br>

```html:sample.html
<date-picker @chenge="submitChange"></date-picker>
```

```js:sample.js
app.component('date-picker', {
  created() {
    console.log(this.$attrs) // { onChange: () => {} }
  }
})
```

このような実装は、`date-picker` のルート要素で `change` イベントを扱うケースなどで役に立つでしょう。<br>

```js:sample.js
app.component('date-picker', {
  template: `
    <select>
      <option value="1">Yesterday</option>
      <option value="2">Today</option>
      <option value="3">Tomorrow</option>
    </select>
  `
})
```

この場合、`change` イベントリスナは、親から子へ渡され、`<select>`要素本来の `change` イベントにより発火されます。<br>
特段、`date-picker` からの明示的なイベント処理を記述する必要はありません。<br>

```html:sample.html
<div id="date-picker" class="demo">
    <date-picker @change="showChange"></date-picker>
</div>
```

```js:sample.js
const app = Vue.createApp({
  methods: {
    showChange(event) {
      console.log(event.target.value) // 選択された opion の値が表示される
    }
  }
})
```

## 属性の継承の無効化

コンポーネントのオプション内で、`inheritAttrs: false` を指定することで、属性の継承を_無効化_することも可能です。<br>

一般的に属性の継承の無効化は、ルート要素ではない別の要素に属性を適用したいようなケースで利用される場面が考えられるでしょう。<br>

`inheritAttrs` オプションを `false` に設定すると属性の継承は無効化されますが、コンポーネントの `$attrs` プロパティを使って、`props` や `emits` といったコンポーネントのプロパティを除く全ての属性（例えば`class` や `style`、`v-on` など）を選択した要素に適用できます。<br>

[前のセクション](https://v3.ja.vuejs.org/guide/component-attrs.html#%E5%B1%9E%E6%80%A7%E3%81%AE%E7%B6%99%E6%89%BF)で利用した date-picker のコンポーネント例を用いて、プロパティでない属性の全てをルートの `div`要素ではなく `input` 要素に適用する場合、`v-bind`を用いて簡略的に記述することも可能です。<br>

```js:sample.js
app.component('date-picker', {
  inheritAttrs: false,
  template: `
    <div class="date-picker">
      <input type="datetime-local" v-bind="$attrs" />
    </div>
  `
})
```

このように記述することで、`data-status` 属性は、`input`要素に適用されるようになります。<br>

```html:sample.html
<!-- プロパティでない属性とともに用いられる Date-picker コンポーネント -->
<date-picker data-status="activated"></date-picker>

<!-- 実際に以下のような形でレンダリングされます -->
<div class="date-picker">
  <input type="datetime-local" data-status="activated" />
</div>
```

## ルート要素が複数の場合の属性の継承

コンポーネントのルート要素が1つ出なく複数のルート要素からなる場合には、暗黙の属性の継承は行われません。<br>
`$attrs`を用いた明示的なアサインを行わない場合、ランタイム上で warning が発行されます。<br>

```html:sample.html
<custom-layout id="custom-layout" @click="changeValue"></custom-layout>
```

```js:sample.js
// これは warning を発行します
app.component('custom-layout', {
  template: `
    <header>...</header>
    <main>...</main>
    <footer>...</footer>
  `
})

// <main> 要素に $attrs で属性を渡しているため、warnings は発行されません
app.component('custom-layoute', {
  template: `
    <header>...</header>
    <main v-bind="$attrs">...</main>
    <footer>...</footer>
  `
})
```
