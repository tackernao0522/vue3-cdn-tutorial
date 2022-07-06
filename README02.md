# テンプレート構文

Vue.jsではHTMLベースのテンプレート構文を使っているので、コンポーネントインスタンスのデータとレンダリングされたDOMを宣言的に対応させることができます。<br>
全てのVue.jsテンプレートは、使用に準拠しているブラウザやHTMLパーサによってパースできる有効なHTMLです。<br>

内部では、Vueはテンプレートを仮想(Vurtual)DOMのRender関数にコンパイルします。<br>
リアクティブシステムと組み合わせて、Vueは再レンダリングに必要なコンポーネントをインテリジェントに把握でき、<br>
アプリケーションの状態が変わった時に最低限のDOM操作を適用します。<br>

もし、あなたが仮想DOMの概要に詳しく、JavaScriptで直接レンダリングするのを好む場合、<br>
テンプレートの代わりに直接render関数で書くことも可能で、オプションでJSXをサポートしています。<br>

## 展開

<h5>テキスト</h5>

データバインディングのもっとも基本的な形は、"Mustache"構文(二重中括弧)を利用したテキスト展開です:

+ `sample.html`<br>

```html:sample.html
<span>Message: {{ msg }}</span>
```

`mustache`タグは、対応するコンポーネントインスタンスから `msg`プロパティの値に置き換えられます。<br>
また、`msg`プロパティが変更される時、それに応じて更新されます。<br>

`v-once ディレクティブ`を使用することで、データ変更時の更新は行わず、一度だけ展開することができます。<br>
ただし、同じノートのあらゆる他のバインディングが影響を受けることに注意してください:

+ `sample.html`<br>

```html:sample.html
<span v-once>This will never change: {{ msg }}</span>
```

## 生のHTML

2重中括弧の`mustaches`は、データをHTMLではなく、プレーンなテキストとして扱います。<br>
実際のHTMLとして出力するためには、`v-html`ディレクティブを使用する必要があります。<br>

`ハンズオン`<br>

+ `front/index.html`を編集<br>

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
  <div id="example1" class="demo">
    <p>Using mustaches: {{ rawHtml }}</p>
    <p>Using v-html directive: <span v-html="rawHtml"></span></p>
  </div>
</body>

<script>
  const RenderHtmlApp = {
    data() {
      return {
        rawHtml: '<span style="color: red">This should be red.</span>'
      }
    }
  }

  Vue.createApp(RenderHtmlApp).mount('#example1')
</script>

</html>
```

上記の `span`のコンテンツは `rawHtml`プロパティの値に置き換えられ、プレーンな`HTML`として解釈されます。<br>
Vueは、文字列ペースのテンプレートエンジンではないので、`v-html`をテンプレート部品を構成して使用できないことに注意しましょう。<br>
代わりに、UIの再利用や組み合わせのための基礎として、コンポーネントを利用することが好ましいです。<br>

`TIP`<br>

```
XSS 脆弱性を容易に引き起こすので、ウェブサイトで動的に任意のHTMLをレンダリングすることは、非常に危険です。
信頼できるコンテンツだけ HTML 展開を利用してください。
ユーザーから提供されたコンテンツに対しては決して使用してはいけません。
```

## 属性

Mustache は、HTML属性の内部で使用することはできません。<br>
代わりに、`v-bind`ディレクティブを使用してください:

```html:sample.html
<div v-bind:id="dynamicId"></div>
```

バインドされた値が `null`や `undefined`の場合、その属性はレンダリングされた要素には含まれません。<br>

属性が単に存在していることを `true`と示すといった真偽値属性の場合、`v-bind`は少し異なった働きをします。例えば：

```html:sample.html
<button v-bind:disabled="isButtonDisabled">Button</button>
```

`isButtonDisabled`が真の値を持つ場合、`disabled`属性は含められます。<br>
また、`<button disabled="">`と整合性を保つために、値が空文字である場合も含まれます。その他の偽の値の場合、この属性は省略されます。<br>

## JavaScript 式の使用

これまで、テンプレートに単純なキーをバインディングしてきました。<br>
実際にはVue.jsは全てのデータバインディング内部でJavaScript式を完全にサポートしています:

これまで、テンプレートに単純なキーをバインディングしてきました。実際には Vue.jsは全てのデータバインディング内部で JavaScript式を完全にサポートします:

```html:index.html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

これらの式は、現在のアクティブなインスタンスのデータスコープ内でJavaScriptとして評価されます。<br>
制限として、それぞれのバインディングは、`単一の式`だけ含むことができるというものです。<br>
なので、以下は動作しません:

```html:sample.html
<!-- これは文であり、式ではありません: -->
{{ var a = 1 }}

<!-- フロー制御はいずれも動作しません。参考演算子を使用してください。 -->
{{ if (ok) { return message } }}
```

## ディレクティブ

ディレクティブは `v-` から始まる特別な属性です。<br>
ディレクティブの仕事は、属性値の式が変化したときに、リアクティブに副作用をDOMに適用することです。<br>
イントロダクションで見た例を振り返ってみましょう:

```html:Sample.html
<p v-if="seen">Now you see me</p>
```

ここでの `v-if` ディレクティブは `seen` 式の値が真か否かに基づいて、`<p>`要素を削除/挿入します。<br>

## 引数

ディレクティブの中には "引数"を取るものもあります。<br>
これはディレクティブ名の後にコロンで表記します。<br>
例えば、`v-bind` ディレクティブは、リアクティブにHTML属性を更新します: <br>

```html:sample.html
<a v-bind:href="url">...</a>
```

ここでの `href`は式 `url`の値を要素の`href`属性へバインドすることを `v-bind`ディレクティブへ伝える引数です。<br>

別の例で `v-on` ディレクティブを見てみましょう。これはDOMのイベントを受け取ります:

```html:sample.html
<a v-on:click="doSomething">...</a>
```

ここでの引数は受け取りたいイベント名です。イベントハンドリングの詳細については後ほど説明します。<br>

## 動的引数

角括弧で囲むことでJavaScript式をディレクティブの引数に使うこともできます:

```html:sample.html
<!-- 動的引数には、"動的引数の式の制約" のセクションで御術されるように、いくつかの誓約がある点に注意してください -->
<a v-bind:[attributeName]="url">...</a>
```

ここで`attributeName`はJavaScript式として動的に評価され、その評価結果が引数の最終的な値として使われます。<br>
例えば、コンポーネントインスタンスが`"href"`という値の`attributeName`という`data`プロパティを持つ場合、このバインディングは`v-bind:href`と等しくなります。<br>

同様に、動的なイベント名にハンドラをバインドするために動的引数を使うこともできます:

```html:sample.html
<a v-on:[eventName]="doSomething">...</a>
```

この例では、`eventName`の値が`"focus"`だとすると、`v-on:[eventName]`は`v-on:focues`と等しくなります。<br>

## 修飾子

修飾子(Modifier)はドットで表記された特別な接尾語で、ディレクティブが特別な方法でバインドされるということを示します。<br>
例えば `.prevent`修飾子は`v-on`ディレクティブに、イベントがトリガされた際 `event.preventDefault()`を呼ぶように伝えます:

```html:sample.html
<form v-on:submit.prevent="onSubmit">...</form>
```

後ほど `v-on` 及び `v-model`を読む際、修飾子の他の例を見るでしょう。<br>

## 省略記法

`v-` 接頭辞は、テンプレート内のVue独自の属性を識別するための目印となっています。<br>
これは既存のマークアップに対して、Vue.jsを利用して動的な振る舞いを適用する場合に便利ですが、頻繁に利用されるディレクティブに対しては冗長に感じることがあるでしょう。<br>
同じく全てのテンプレートをVueで管理してシングルページアプリケーション(SPA)を作る場合、`v-`接頭辞をつける必要性は低いものになるでしょう。<br>
そのため、Vueは2つの最もよく使われるディレクティブ`v-bind`と`v-on`に対して特別な省略記法を提供しています:

## v-bind 省略記法

```html:sample.html
<!-- 完全な構文 -->
<a v-bind:href="url">...</a>

<!-- 省略記法 -->
<a :href="url">...</a>

<!-- 動的引数の省略記法 -->
<a :[key]="url">...</a>
```

## v-on 省略記法

```html:sample.html
<!-- 完全な構文 -->
<a v-on:click="doSomething">...</a>

<!-- 省略記法 -->
<a @click="doSomething">...</a>

<!-- 動的引数の省略記法 -->
<a @[event]="doSomething">...</a>
```

これらは普通のHTMLとはちょっと違うように見えるかもしれません。ですが `:` や `@` は属性名に利用可能な文字で、Vue のサポートしている全てのブラウザで正しくパースすることができます。<br>
加えて、最終的にレンダリングされるマークアップにそれらは現れません。省略記法の構文の利用は完全に任意ですが、後でその使用方法について詳しく学んだ時に便利に感じることでしょう。<br>

## 注意事項

### 動的引数の値の制約

動的引数は、`null`を除くと`string`に評価されることが想定されています。<br>
特殊値 `null`は、明示的にバインディングを削除するのに使われます。<br>
その他の`string`以外の値は、警告を発生させます。<br>

### 動的引数の式の制約

動的引数の式には構文上の制約があります。<br>
というのも、スペースや引用符のような一部の文字は、HTMLの属性名としては不正な文字だからです。例えば、以下は不正です:

```html:sample.html
<!-- これはコンパイラ警告を引き起こします -->
<a v-bind:['foo' + bar]="value">...</a>
```

回避策は複雑な式を算出プロパティで置き換える方法です、Vueの最も基本的な機能の１つであり後ほど説明します。<br>

`in-DOM`テンプレート（すなわち、HTMLファイルに直接かかれるテンプレート）を使う場合、ブラウザが強制的に属性名を小文字にするため、キー名を大文字にするのは避けるべきです:<br>

```html:sample.html
<!-- in-DOM テンプレートの中では、v-bind:[someattr]に変換されます。インスタンスに "someattr"プロパティがない場合、このコードは動作しません。 -->
<a v-bind:[someAttr]="value">...</a>
```

## JavaScript 式の制約

テンプレート式はサンドボックス化されていて、`Math`や`Date`といったグローバルオブジェクトの制限リストにだけアクセスできます。<br>
テンプレート式内でユーザーが定義したグローバルオブジェクトにアクセスしようとしてはいけません。<br>

# データプロパティとメソッド

## データプロパティ

コンポーネントの `data`オプションは関数です。Vueは新しいコンポーネントのインスタンスを作成する際に、この関数を呼び出します。<br>
これはオブジェクトを返すもので、Vueはオブジェクトをそのアクティブシステムでラップして、コンポーネントのインスタンスに`$data`として格納します。<br>
便宜上、そのオブジェクトのトップレベルのプロパティは、コンポーネントのインスタンスを介して直接公開されます:<br>

```js:sample.js
const app = Vue.createApp({
  data() {
    return { count: 4 }
  }
})

const vm = app.mount('#app')

console.log(vm.$data.count) // => 4
console.log(vm.count) // => 4

// vm.count に値を代入すると、$data.countも更新
vm.count = 5
console.log(vm.$data.count) // => 5

// ... 逆もまだ同様
vm.$data.count = 6
console.log(vm.count) // => 6
```

+ `front/index.html`を編集<br>

```html:index.html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <script src="https://unpkg.com/vue@next"></script>
</head>

<body>
  <div id="app">
    {{ count }} <!-- result: 6 -->
  </div>
</body>

<script>
  const app = Vue.createApp({
    data() {
      return {
        count: 4
      }
    }
  })

  const vm = app.mount('#app')

  console.log(vm.$data.count) // => 4
  console.log(vm.count) // => 4

  // vm.count に値を代入すると、$data.count も更新
  vm.count = 5
  console.log(vm.$data.count) // => 5

  // ... 逆もまた同様
  vm.$data.count = 6
  console.log(vm.count)
</script>

</html>
```
これらのインスタンスプロパティは、インスタンスの初回作成時にのみ追加されます。<br>
そのため、`data`関数から返されたオブジェクトに、それらが全て含まれていることを確認する必要があります。<br>
必要に応じて、必要な値がまだ利用できないプロパティには、`null`や`undefined`、またはその他のプレースホルダーの値を使ってください。<br>

新しいプロパティを`data`に含めずに、コンポーネントのインスタンスに直接追加することはできます。<br>
しかし、このプロパティはリアクティブな`$data`オブジェクトによって支えられていないので、Vueのリアクティブシステムによって、自動的に追跡されることはありません。<br>

Vueは、コンポーネントのインスタンスを介して自身のビルドインAPIを公開する際に、`$`をプレフィックスとして使います。<br>
また、内部プロパティのために`_`を予約しています。<br>
トップレベルの`data`プロパティの名前に、これらの文字から始まる名前を使うことは避けるべきです。<br>


## メソッド

コンポーネントのインスタンスにメソッドを追加するには、`methods`オプションを使います。<br>
これは必要なメソッドを含むオブジェクトでなければなりません:

```js:sample.js
const app = Vue.createApp({
  data() {
    return { count: 4 }
  },
  methods: {
    increment() {
      // `this` はコンポーネントインスタンスを参照
      this.count++
    }
  }
})

const vm = app.mount('#app')

console.log(vm.count) // => 4

vm.incremant()

console.log(vm.count) // => 5
```

Vueは、`methods` の `this` を自動的に束縛して、常にコンポーネントのインスタンスを参照します。<br>
これにより、メソッドがイベントリスナーやコールバックとして使われる際に、正しい`this`の値を保持することができます。<br>
Vueが適切ね `this`の値を束縛するのを防ぐため、`methods`を定義する際にはアロー関数を使うのは避けるべきです。<br>

コンポーネントのインスタンスの他の全てのプロパティと同様に、`methods`はコンポーネントのテンプレート内からアクセスできます。<br>
テンプレート内からはよくイベントリスナーとして使われます。<br>

```html:sample.html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <script src="https://unpkg.com/vue@next"></script>
</head>

<body>
  <div id="app">
    <button @click="increment">Up vote</button>
      {{ count }}
  </div>
</body>

<script>
  const app = Vue.createApp({
    data() {
      return { count: 4 }
    },
    methods: {
      increment() {
        // `this` はコンポーネントインスタンスを参照
        this.count++
      }
    }
  })

  const vm = app.mount('#app')

  console.log(vm.count) // => 4
</script>

</html>
```

上の例では、`<button>`がクリックされると、`increment`メソッドが呼ばれます。<br>

また、テンプレートから直接メソッドを呼び出すこともできます。後で説明しますが、通常は代わりに算出プロパティを使うのが良いです。<br>
しかし、メソッドを使うことは算出プロパティが実行可能なオプションではない場合に役に立ちます。<br>
テンプレートがJavaScriptの式をサポートしていれば、どこでもメソッドを呼び出すことができます:

```html:sample.html
<span :title="toTitleDate(date)">
  {{ formatDate(date) }}
</span>
```

`toTitleDate` や `formatDate` メソッドがどれかリアクティブなデータにアクセスすると、あたかもテンプレートで直接使われていたかのように、それはレンダリングの依存関係として追跡されます。<br>

テンプレートから呼び出されたメソッドは、データの変更や非同期処理の発火などの副作用があってはなりません。<br>
もしそのようなことをしたくなったら、代わりにライフサイクルフック(https://v3.ja.vuejs.org/guide/instance.html#%E3%83%A9%E3%82%A4%E3%83%95%E3%82%B5%E3%82%A4%E3%82%AF%E3%83%AB%E3%83%95%E3%83%83%E3%82%AF)を使うべきです。<br>

## Debounce（デバウンス）と Throttle（スロットル）

Vueは、`Debounce`や`Throttle`のサポートが組み込まれていませんが、Lodah(https://lodash.com/)などのライブラリを使って実装することができます。<br>

コンポーネントが一度しか使われない場合には、`methods`の中で直接`Debounce`を適用することができます:<br>

```html:sample.html
<script src="https://unpkg.com/lodash@4.17.20/lodash.min.js"></script>
<script>
  Vue.createApp({
    methods: {
      // Lodashによる Debounce
      click: _.debounce(function() {
        // ... クリックに反応
      }, 500)
    }
  }).mount('#app')
</script>
```

しかし、この方法ではコンポーネントが再利用される場合に、全てのコンポーネントが同じ`Debounce`関数を共有するため、問題が起こる可能性があります。<br>
コンポーネントのインスタンスをお互いに独立させるために、`created` ライフサイクルフックに`Debounce`関数を追加することができます:

```js:sample.js
<script src="https://unpkg.com/lodash@4.17.20/lodash.min.js"></script>
<script>
  app.component('save-button', {
    created() {
      // Lodash によるDebounce
      this.debouncedClick = _.debounce(this.click, 500)
    },
    unmounted() {
      // コンポーネントが削除されたらタイマーをキャンセル
      this.debouncedClick.cancel()
    },
    methods: {
      click() {
        // ... クリックに反応 ...
      }
    },
    template: `
    <button @click="debouncedClick">
      Save
    </button>
  `
  })
</script>
```
