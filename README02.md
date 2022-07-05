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
