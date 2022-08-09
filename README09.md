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

# カスタムイベント

このページは[コンポーネントの基本](https://v3.ja.vuejs.org/guide/component-basics.html)が読まれていることが前提となっています。<br>
コンポーネントを扱ったことのない場合こちらのページを先に読んでください。<br>

## イベント名

コンポーネントやプロパティと同じように、イベント名は大文字と小文字を自動的に変換します。<br>
子コンポーネントからキャメルケース（camelCase)でイベントを発行すると、親コンポーネントではケバブケース（kebab-case)のリスナーを追加できるようyになります：<br>

```js:sample.js
this.$emit('myEvent')
```

```html:sample.html
<my-component @my-event="doSomething"></my-component>
```

[プロパティの形式](https://v3.ja.vuejs.org/guide/component-props.html#%E3%83%95%E3%82%9A%E3%83%AD%E3%83%8F%E3%82%9A%E3%83%86%E3%82%A3%E3%81%AE%E5%BD%A2%E5%BC%8F-%E3%82%AD%E3%83%A3%E3%83%A1%E3%83%AB%E3%82%B1%E3%83%BC%E3%82%B9-vs-%E3%82%B1%E3%83%8F%E3%82%99%E3%83%95%E3%82%99%E3%82%B1%E3%83%BC%E3%82%B9)と同じように、DOM内テンプレートを使っている場合は、ケバブケース(kebab-case)のイベントリスナを使うことをお勧めします。<br>
文字列テンプレートを使っている場合は、この制約は適用されません。<br>

[カスタムイベント無料ビデオ](https://vueschool.io/lessons/defining-custom-events-emits?friend=vuejs)<br>

発行されたイベントは、`emits` オプションを介して、コンポーネントで定義することができます。<br>

```js:sample.js
app.component('custom-form', {
  emits: ['inFocus', 'submit']
})
```

ネイティブイベント（例、`click` など）が `emits` オプションで定義されている場合、ネイティブイベントリスナの_代わりに_コンポーネントのイベントが使われます。<br>

#### TIP

コンポーネントの動作を実証するために、全ての発行されたイベントを定義することをおすすめします。<br>

## 発行されたイベントを検証する

プロパティの型検証と同様に、発行されたイベントは、配列構文ではなくオブジェクト構文で定義されている場合に検証できます。<br>

検証を追加するために、イベントには、`$emit` 呼び出しに渡された引数を受け取る関数が割り当てられ、イベントが有効かどうかを示す真偽値を返します。<br>

```js:sample.js
app.component('custom-from', {
  emits: {
    // No validation
    click: null,

    // Validate submit event
    submit: ({ email, password }) => {
      if (email && password) {
        return true
      } else {
        console.warn('Invalid submit event payload!')
        return false
      }
    }
  },
  methods: {
    submitForm(email, password) {
      this.$emit('submit', {email, password })
    }
  }
})
```

## `v-model` の引数

デフォルトでは、コンポーネントの `v-model`はプロパティとして`modelValue`を使用し、イベントとして `update:modelValue`を使用します。<br>
`v-model`引数を渡してこれらの名前の変更が出来ます。<br>

```html:sample.html
<my-component v-model:title="bookTitle"></my-component>
```

この場合、子コンポーネントは `title` プロパティを期待し、同期するために `update:title` イベントを発行します。<br>

```js:sample.js
app.component('my-component', {
  props: {
    title: String
  },
  emits: ['update:title'],
  template: `
    <input
      type="text"
      :value="title"
      @input="$emit('update:title', $event.target.value)"
    >
  `
})
```

## 複数の `v-model`のバインディング

以前 `v-model` [引数](https://v3.ja.vuejs.org/guide/component-custom-events.html#v-model-%E3%81%AE%E5%BC%95%E6%95%B0)で学習した特定のプロパティとイベントをターゲットにする機能を活用することで、単一のコンポーネントインスタンスに対して、複数の v-model バインディングを作成できるようになりました。<br>

それぞれの v-modelは、コンポーネントに追加オプションを必要とせず、異なるプロパティに同期します。<br>

```html:index.html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <script src="https://unpkg.com/vue@next"></script>

  <style>
    .demo {
      font-family: sans-serif;
      border: 1px solid #eee;
      border-radius: 2px;
      padding: 20px 30px;
      margin-top: 1em;
      margin-bottom: 40px;
      user-select: none;
      overflow-x: auto;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <p>First name: {{ firstName }}</p>
    <p>Last name: {{ lastName }}</p>
    <user-name v-model:first-name="firstName" v-model:last-name="lastName"></user-name>
  </div>
</body>

<script>
  const UserName = {
    props: {
      firstName: String,
      lastName: String
    },
    template: `
    <input
      type="text"
      :value="firstName"
      @input="$emit('update:firstName', $event.target.value)">

    <input
      type="text"
      :value="lastName"
      @input="$emit('update:lastName', $event.target.value)">
  `
  };

  const HelloVueApp = {
    components: {
      UserName,
    },
    data() {
      return {
        firstName: 'John',
        lastName: 'Doe',
      };
    },
  };

  Vue.createApp(HelloVueApp).mount('#app');
</script>

</html>
```

## `v-model` 修飾子の処理

フォーム入力バインディングについて学習していたときに、`v-model`に[組み込み修飾子](https://v3.ja.vuejs.org/guide/forms.html#%E4%BF%AE%E9%A3%BE%E5%AD%90) - `.trim`、`.number`、および `.lazy` があることがわかりました。<br>
ただし、場合によっては、独自のカスタム修飾子を追加することもできます。<br>

`v-model` バインディングによって提供される文字列の最初の文字を大文字にするカスタム修飾子の例、`capitalize`を作成してみましょう。<br>

コンポーネント `v-model`に追加された修飾子は、`modelModifiers` プロパティを介してコンポーネントに提供されます。<br>

以下の例では、デフォルトで空のオブジェクトになる `modelModifires` プロパティを含むコンポーネントを作成しました。<br>

コンポーネントの `created` ライフサイクルフックがトリガーされると、`modelModifires` プロパティには `capitalize` が含まれ、その値は `true`になります。これは、`v-model` バインディングに `v-model.capitalize="myText" が設定されているためです。<br>

```html:sample.html
<my-component v-model.capitalize="myText"></my-component>
```

```js:sample.js
app.component('my-component', {
  props: {
    modelValue: String,
    modelModifires: {
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  template: `
    <input type="text"
      value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)">
  `,
  created() {
    console.log(this.modelModifires) // { capitalize: true }
  }
})
```

プロパティを設定したので、`modelModifires` オブジェクトのキーを確認し、発行された値を変更するハンドラーを記述できます。<br>
以下のコードでは `<input />` 要素が `input` イベントを発生させるたびに文字列を大文字にします。<br>

```html:sample.html
<div id="app">
  <my-component v-model.capitalize="myText"></my-component>
  {{ myText }}
</div>
```

```js:sample.js
const app = Vue.createApp({
  data() {
    return {
      myText: ''
    }
  }
})

app.component('my-component', {
  props: {
    modelValue: String,
    modelModifires: {
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  methods: {
    emitValue(e) {
      let value = e.target.value
      if (this.modelModifires.capitalize) {
        value = value.charAt(0).toUpperCase() + value.slice(1)
      }
      this.$emit('update:modelValue', value)
    }
  },
  template: `<input
    type="text"
    :value="modelValue"
    @input="emitValue">
  `
})

app.mount('#app')
```

引数を持つ `v-model` バインディングの場合、生成されるプロパティ名は`arg + "Modifiers" になります。<br>

```html:index.html
<my-component v-model:description.capitalize="myText"></my-component>
```

```js:sample.js
app.component('my-component', {
  props: ['description', 'descriptionModifiers'],
  emits: ['update:description'],
  template: `
    <input type="text"
      :value="description"
      @input="$emit('update:description', $event.target.value)">
  `,
  created() {
    console.log(this.descriptionModifiers) // { capitalize: true }
  }
})
```
