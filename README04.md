# 条件付きレンダリング

## v-if

`v-if` ディレクティブは、ブロックを条件に応じてレンダリングしたい場合に使用されます。<br>
ブロックは、ディレクティブの式が真を返す場合のみレンダリングされます。<br>

```html:sample.html
<h1 v-if="awesome">Vue is awesome!</h1>
```

これは、`v-else` で "else ブロック"を追加することも可能です:

```html:sample.html
<h1 v-if="awesome">Vue is awesome!</h1>
<h1 v-else>Oh no 😢</h1>
```

## `<template>`での`v-if`による条件グループ

`v-if` はディレクティブなので、単一の要素に付加する必要があります。<br>
しかし、1要素よりも多くの要素と切り替えたい場合はどうでしょうか？<br>
このケースでは、非表示ラッパー（wrapper）として提供される、`<template>`要素で`v-if`を使用できます。<br>
最終的にレンダリングされる結果は、`<template>`要素は含まれません。<br>

```html:sample.html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

## `v-else`

`v-if`に対して "else block"を示すために、`v-else` ディレクティブを使用できます:<br>

```html:sample.html
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```

`v-else` 要素は、`v-if` または `v-else-if` 要素の直後になければなりません。<br>
それ以外の場合は認識されません。<br>

## `v-else-if`

`v-else-if` は、名前が示唆するように、`v-if` の "else if block" として機能します。また、複数回団結することもできます:<br>

```html:sample.html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

`v-else` と同様に、`v-else-if` 要素は `v-if` 要素または `v-else-if` 要素の直後になければなりません。<br>

## `v-show`

条件的に要素を表示するための別のオプションは `v-show` です。<br>
使用方法はほとんと同じです:

```html:sample.html
<h1 v-show="ok">Hello!</h1>
```

違いは `v-show`による要素は常にレンダリングされて DOMに維持するということです。<br>
`v-show` はシンプルに要素の `display` CSSプロパティを切り替えます。<br>

`v-show` は `<template>` 要素をサポートせず、`v-else` とも連動しないということに注意してください。<br>

## `v-if` VS `v-show`

`v-if`は、イベントリスナと子コンポーネント内部の条件ブロックが適切に破棄され、<br>
そして切り替えられるまでの間再作成されるため、"リアル"な条件レンダリングです。<br>

`v-if` は 遅延レンダリング（lazy）です。初期表示において false の場合、何もしません。<br>
条件付きブロックは、条件が最初に true になるまでレンダリングされません。<br>

一方で、`v-show` はとてもシンプルです。要素は初期条件に関わらず常にレンダリングされ、<br>
シンプルなCSSペースの切り替えとして保存されます。<br>

一般的に、`v-if` はより高い切り替えコストを持っているのに対して、`v-show` はより高い初期レンダリングコストを持っています。<br>
そのため、とても頻繁に何かを切り替える必要があれば `v-show` を選び、条件が実行時に変更することがほとんどない場合は、`v-if`を選びます。<br>

## `v-if と `v-for`

### Note

`v-if` と `v-for` を同時に利用することは _推奨されません。_ 詳細については スタイルガイド (https://v3.ja.vuejs.org/style-guide/#v-for-%E3%81%A8%E4%B8%80%E7%B7%92%E3%81%AB-v-if-%E3%82%92%E4%BD%BF%E3%81%86%E3%81%AE%E3%82%92%E9%81%BF%E3%81%91%E3%82%8B-%E5%BF%85%E9%A0%88)を参照ください。<br>

`v-if` と `v-for` が同じ要素に両方使われる場合、`v-if` が先に評価されます。<br>
詳細については リストレンダリングのガイド(https://v3.ja.vuejs.org/guide/list.html#v-for-%E3%81%A8-v-if)を参照してください。<br>
