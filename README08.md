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

# プロパティ

## プロパティの型

ここまでは、プロパティを文字列の配列として列挙してきました。<br>

```js:sample.js
props: ['title', 'likes', 'isPublished', 'commentIds', 'author']
```

しかし、通常は全てのプロパティを特定の型の値にしたいと思うでしょう。<br>
このような場合には、プロパティをオブジェクトとして列挙し、プロパティのキーと値にそれぞれプロパティの名前と肩を設定します：<br>

```js:sample.js
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // またはその他のコンストラクタ
}
```

こうすることでコンポーネントがドキュメント化されるだけでなく、間違った方を渡した場合に、ブラウザのJavaScript コンソールで警告の表示もされます。<br>
詳しくはこのページの下にある[プロパティのバリデーション](https://v3.ja.vuejs.org/guide/component-props.html#%E3%83%95%E3%82%9A%E3%83%AD%E3%83%8F%E3%82%9A%E3%83%86%E3%82%A3%E3%81%AE%E3%83%8F%E3%82%99%E3%83%AA%E3%83%86%E3%82%99%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3) にて説明します。<br>

## 動的あるいは動的なプロパティの受け渡し

これまでは、このような形でプロパティが静的な値を渡しているところを見てきましたが：<br>

```html:index.html
<blog-post title="My journey with Vue"></blog-post>
```

次のような、`v-bind` やそのショートカットである `:` 文字を使って動的に割り当てられたプロパティも見たことがあるでしょう:<br>

```html:sample.html
<!-- 変数の値を動的に割り当てます -->
<blog-post :title="post.title"></blog-post>

<!-- 複数の変数を合成した値を動的に割り当てます -->
<blog-post :title="post.title + ' by ' + post.author.name"></blog-post>
```

上の２つの例では、たまたま文字列の値を渡していますが、実際には _任意の_ 型の値をプロパティに渡すことができます。<br>

## 数値の受け渡し

```html:index.html
<!-- `42` は静的な値ですが、これが文字列ではなく JavaScript の式だと -->
<!-- Vue に伝えるためには v-bind を使う必要があります。 -->
<blog-post :likes="42"></blog-post>

<!-- 変数の値を動的に割り当てています。 -->
<blog-post :likes="post.likes"></blog-post>
```

## 真偽値の受け渡し

```html:index.html
<!-- 値のないプロパティは、`true` を意味します。 -->
<!-- プロパティで is-published の型に Boolean を設定してない場合、値は "true" ではなく空文字列になります。 -->
<blog-post is-published></blog-post>

<!-- `false` は静的な値ですが、これが文字列ではなく JavaScript の式だと -->
<!-- Vueに伝えるには、v-bind を使う必要があります。 -->
<blog-post :is-published="false"></blog-post>

<!-- 変数の値を動的に割り当てています。 -->
<blog-post :is-published="post.isPublished"></blog-post>
```

## 配列の受け渡し

```html:index.html
<!-- この配列は静的ですが、これが文字列ではなく JavaScript の式だと -->
<!-- Vue に伝えるには、v-bind を使う必要があります。 -->
<blog-post :comment-ids=[234, 266, 273]"></blog-post>

<!-- 変数の値を動的に割り当てています。 -->
<blog-post :comment-ids="post.commentIds"></blog-post>
```

## オブジェクトの受け渡し

```html:index.html
<!-- このオブジェクトは静的ですが、これが文字列ではなく javaScript の式だと -->
<!-- Vue に伝えるには、v-bind を使う必要があります。 -->
<blog-post
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
></blog-post>

<!-- 変数の値を動的に割り当てています。 -->
<blog-post :author="post.author"></blog-post>
```

## オブジェクトのプロパティの受け渡し

オブジェクトの全てのプロパティをコンポーネントのプロパティ(props)として渡したい場合は、引数無しで `v-bind`を使うことができます（ `:prop-name`の代わりに`v-bind`を使用）。<br>
例えば、`post`オブジェクトが与えられたとします。<br>

```js:sample.js
post: {
  id: 1,
  title: 'My Journey with Vue'
}
```

次のテンプレートは：<br>

```html:sample.html
<blog-post v-bind="post"></blog-post>
```

以下と同等となります：<br>

```html:index.html
<blog-post v-bind:id="post.id" v-bind:title="post.title"></blog-post>
```

## 単方向データフロー

全てのプロパティは、子プロパティと親プロパティの間に _単方向のバインディング_ を形成します: 親のプロパティが更新される場合は子へと流れ落ちますが、その逆はありません。<br>
これにより、子コンポーネントが誤って親の状態を変更すること(アプリのデータフローを理解しづらくすることがあります)を防ぎます。<br>

さらに、親コンポーネントが更新するたびに、子コンポーネントの全てのプロパティは最新の値に更新されます。つまり、子コンポーネント内でプロパティの値を変化させては _いけません_。<br>
変化させた場合、Vueはコンソールで警告を表示します。<br>

プロパティの値を変化させたい場合は主に2つあります：<br>

1. _プロパティを初期値として受け渡し、子コンポーネントにてローカルのデータとして後で利用したいと考える場合。_ この場合、プロパティの値を初期値として使うローカルの data プロパティを定義するのがベストです：<br>

```js:sample.js
props: ['initialCounter'],
data() {
  return {
    counter: this.initialCounter
  }
}
```

2. _プロパティを変換が必要な未加工の値として受け渡す。_ この場合、プロパティの値の使用した算出プロパティを別途定義するのがベストです：<br>

```js:sample.js
props: ['size'],
computed: {
  normalizedSize() {
    return this.size.trim().toLowerCase()
  }
}
```

#### Warning

JavaScript のオブジェクトや配列は参照渡しなので、プロパティが配列やオブジェクトの場合、<br>
子コンポーネント内でオブジェクトや配列自体を変更すると、親の状態に_影響してしまい、_ Vueはこれに対して警告できないことに注意してください。<br>
一般的なルールとして、オブジェクトや配列を含むプロパティの変更は避けるべきです。<br>
これは単方向のデータバインディングが無視されてしまい、望ましくない結果を引き起こす可能性があるからです。<br>

## プロパティのバリデーション

コンポーネントはプロパティ対して、すでに見たように型などの要件を指定することができます。<br>
要件が満たされていない場合、VueはブラウザのJavaScriptコンソールで警告を表示します。<br>
これは、他の人が使用することを意図したコンポーネントを開発する場合に特に便利です。<br>
プロパティのバリデーションを指定するには、文字列の配列の代わりに、`props`の値についてのバリデーション要件をもったオブジェクトを渡します。例えば以下のようなものです:<br>

```js:sample.js
app.component('my-component', {
  props: {
    // 基本的な型チェック（`null` と `undefined` は全てのバリデーションをパスします）
    propA: Number,
    // 複数の型の許容
    propB: [string, Number],
    // 文字列型を必須で要求する
    propC: {
      type: String,
      required: true
    },
    // デフォルト値つきの数値型
    propD: {
      type: Number,
      default: 100
    },
    // デフォルト値付きのオブジェクト型
    propE: {
      type: Object,
      // オブジェクトもしくは配列のデフォルト値は
      // 必ずファクトリ関数(それを生み出すための関数)を返す必要があります。
      default() {
        return { message: 'hello' }
      }
    },
    // カスタムバリデーション関数
    propF: {
      validator(value) {
        // プロパティの値は、必ずいずれかの文字列でなければならない
        return ['success', 'warning', 'danger'].includes(value)
      }
    },
    // デフォルト値つきの関数型
    propG: {
      type: Function,
      // オブジェクトや配列のデフォルトとは異なり、これはファクトリ関数ではありません。これは、デフォルト値としての関数を取り扱います。
      default() {
        return 'Default function'
      }
    }
  }
})
```

プロパティのバリデーションが失敗した場合、Vueはコンソールに警告を表示します(開発用ビルドを利用している時)。<br>

#### Note

プロパティのバリデーションはコンポーネントのインスタンスが生成される_前_に行われるため、インスタンスのプロパティ（例えば `data`, `computed` など)を `default` 及び `validator` 関数の中で利用することはできません。<br>

## 型の検査

`type` は、次のネイティブコンストラクタのいずれかです：<br>

+ String<br>
+ Number<br>
+ Boolean<br>
+ Array<br>
+ Object<br>
+ Date<br>
+ Function<br>
+ Symbol<br>

さらに、`type`はカスタムコンストラクタ関数であってもよく、アサーションは`instanceof`チェックにより行われます。<br>
例えば、以下のようなコンストラクタ関数が存在するとします：<br>

```js:sample.js
function Person(firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}
```

このように利用することができます：<br>

```js:sample.js
app.component('blog-post', {
  props: {
    author: Person
  }
})
```

上の例では、`author`プロパティの値が`new Person`によって作成されたものかどうか検証されます。<br>

## プロパティの形式(キャメルケース vs ケバブケース)

HTMLの属性名は大文字小文字を区別しないので、ブラウザは全ての大文字を小文字として解釈します。<br>
つまり、DOM(HTML)テンプレート内においては、キャメルケースのプロパティ名はケバブケース(ハイフンで区切ったもの)を使う必要があります。<br>

```js:sample.js
const app = Vue.createApp({})

app.component('blog-post', {
  // JavaScript 内ではキャメルケース
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
```

```html:index.html
<!-- HTML 内ではケバブケース -->
<blog-post post-title="hello!"></blog-post>
```
