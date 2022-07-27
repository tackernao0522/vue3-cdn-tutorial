# コンポーネントの登録

## コンポーネント名

コンポーネントを登録する際は、必ず名前をつけてください。<br>
例えば、グローバル登録の場合は以下のようになります: <br>

```js:sample.js
const app = Vue.createApp({...})

app.component('my-component-name', {
  /* ... */
})
```

`app.component`の第一引数がコンポーネント名になります。上記の例では、"my-component-name"がコンポーネント名です。<br>

コンポーネントにつける名前は使用箇所によって異なります。<br>
DOMを直接操作する場合(文字列テンプレートや [単一ファイルコンポーネント](https://v3.ja.vuejs.org/guide/single-file-component.html) を除く )は、[W3C rules](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name) に従ったカスタムタグ名を強く推奨します: <br>

1. 全て小文字<br>
2. ハイフンを含める(複数の単語をハイフンを用いて繋げる)<br>

こうすることで、既に存在するそして将来的に定義される HTML要素とコンフリクトを回避する助けになります。<br>

その他のコンポーネント名の推奨項目は [スタイルガイド](https://v3.ja.vuejs.org/style-guide/#%E5%9F%BA%E5%BA%95%E3%82%B3%E3%83%B3%E3%83%9B%E3%82%9A%E3%83%BC%E3%83%8D%E3%83%B3%E3%83%88%E3%81%AE%E5%90%8D%E5%89%8D-%E5%BC%B7%E3%81%8F%E6%8E%A8%E5%A5%A8) で確認することができます。<br>

## 命名のケース（Name Casing)

コンポーネントを文字列テンプレートが単一ファイルコンポーネントで定義する際は、名前の付け方に2つのオプションがあります: <br>

ケバブケース <br>

```js:sample.js
app.component('my-component-name', {
  /* ... */
})
```

コンポーネントをケバブケースで定義する場合は、そのカスタム要素を参照する際も `<my-component-name>` のようにケバブケースを用いなければなりません。<br>

パスカルケース<br>

```js:sample.js
app.component('MyComponentName', {
  /* ... */
})
```

コンポーネントをパスカルケースで定義する場合は、そのカスタム要素を参照する際どちらのケースも用いることができます。<br>
`<my-component-name>` と `<MyComponentName>` のどちらも利用可能です。<br>
ですが、DOM内で使用する場合（つまり、文字列テンプレート以外の場合）はケバブケースの方が適している点に注意してください。<br>

## グローバル登録

ここまでは `app.component` だけを使ってコンポーネントを作成しました: <br>

```js:sample.js
Vue.createApp({...}).component('my-component-name', {
  // ... options ...
})
```

これらのコンポーネントはアプリケーションへの _グローバル登録_ とされています。<br>
つまり、あらゆるコンポーネントインスタンスのテンプレート内で使用できます: <br>

```js:sample.js
const app = Vue.createApp({})

app.component('component-a', {
  /* ... */
})
app.component('component-b', {
  /* ... */
})
app.component('component-c', {
  /* ... */
})

app.mount('#app')
```

```html:sample.html
<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```

これは全てのサブコンポーネントにも当てはまります、つまりこれらの３つのコンポーネント全てが相互に使用することができます。

## ローカル登録

グローバル登録はしばしば理想的ではありません。例えば、Webpackのようなビルドシステムを利用した場合、全てのコンポーネントをグローバル登録すると、使用していないコンポーネントも最終ビルドに含まれてしまいます。<br>
これはユーザーがダウンロードしなければならないJavaScriptの量を不必要に増やしてしまう事になります。<br>

以下のケースでは、プレーン JavaScript としてコンポーネントを定義することができます:<br>

```js:sample.js
const ComponentA = {
  /* ... */
}
const ComponentB = {
  /* ... */
}
const ComponentC = {
  /* ... */
}
```

次に `components`オプション内に使用したいコンポーネントを定義します:<br>

```js:sample.js
const app = Vue.create({
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

`components` オブジェクト内のそれぞれのプロパティは、キーがカスタム要素の名前になり、値はコンポーネントのオプションオブジェクトが含まれます。<br>

_ローカル登録されたコンポーネントはサブコンポーネントでは利用できます_ という点に注意してください。<br>
例えば、`ComponentA`を`ComponentB`内で使用可能にしたいときは、以下のように使用する必要があります: <br>

```js:sample.js
const ComponentA = {
  /* ... */
}

const ComponentB = {
  components: {
    'component-a': ComponentA
  }
  // ...
}
```

または、BabelとWebpackのようなものを用いて ES2015 モジュールを利用して場合は、このようになります:<br>

```js:sample.js
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  }
  // ...
}
```

ES2015 以降の場合、`ComponentA`のような変数名をオブジェクト内に配置することは`ComponentA: ComponentA`の省略記法にあたり、変数の名前は次のどちらも意味する事に注意して下さい: <br>

+ テンプレート内で使用されるカスタム要素名<br>
+ コンポーネントオプションを含む変数の名前<br>

## モジュールシステム

もし `import / require` を使用したモジュールシステムを使わないのであれば、このセクションはスキップすることができます。<br>
そうでなければ、いくつかのインストラクションとコツを教えます。<br>

## モジュールシステム内のローカル登録

もしまだこの記事を読んでいるということは、Webpack や Babelのようなモジュールシステムを利用している可能性が高いでしょう。<br>
このような場合は、それぞれのコンポーネントを独自のファイルに持つ `components` ディレクトリを作成することをおすすめします。<br>
例えば、 `componentB.js` もしくは `ComponentB.vue` ファイルの場合:<br>

```vue:Component.vue
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  }
  // ...
}
```

これで `ComponentA` と `ComponentC` が `ComponentB` のテンプレート内で利用できるようになりました。<br>
