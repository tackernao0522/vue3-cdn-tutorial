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

# リストレンダリング

## v-for で配列に要素をマッピングする

配列の基づいて、アイテムのリストをレンダリングするために、`v-for`ディレクティブを使用することができます。<br>
`v-for`ディレクティブには、`item in items`の形式の特別な構文が必要で、`items`はソースデータの配列、`item`は繰り返される配列要素の _エイリアス_ です:

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
    <ul>
      <li v-for="item in items">
        {{ item.message }}
      </li>
    </ul>
  </div>
</body>
<script>
  Vue.createApp({
    data() {
      return {
        items: [{ message: 'Foo' }, { message: 'Bar' }]
      }
    }
  }).mount('#app')
</script>

</html>
```

`v-for` ブロック内では、親スコープのプロパティへの完全なアクセスを持っています。<br>
また `v-for` は現在のアイテムに対する配列のインデックスを任意の2つ目の引数としてサポートしています。<br>

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
    <ul>
      <li v-for="(item, index) in items">
        {{ parentMessage}} - {{ index }} - {{ item.message }}
      </li>
    </ul>
  </div>
</body>
<script>
  Vue.createApp({
    data() {
      return {
        parentMessage: 'Parent',
        items: [{ message: 'Foo' }, { message: 'Bar' }]
      }
    }
  }).mount('#app')
</script>

</html>
```

また、区切り文字として `in`の代わりに `of` を使用することができます。<br>
これは JavaScript のイテレータ公文に近いものです:

```html:sample.html
<div v-for="item of items"></div>
```

## オブジェクトの `v-for`

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
    <ul>
      <li v-for="value in myObject">
        {{ value }}
      </li>
    </ul>
  </div>
</body>
<script>
  Vue.createApp({
    data() {
      return {
        myObject: {
          title: 'How to do lists in Vue',
          author: 'Jane Doe',
          publishAt: '2016-04-10'
        }
      }
    }
  }).mount('#app')
</script>

</html>
```

2つ目の引数としてプロパティ名 (すなわちキー) も提供できます:

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
    <ul>
      <li v-for="(value, name) in myObject">
        {{ name }}: {{ value }}
      </li>
    </ul>
  </div>
</body>
<script>
  Vue.createApp({
    data() {
      return {
        myObject: {
          title: 'How to do lists in Vue',
          author: 'Jane Doe',
          publishAt: '2020-03-22'
        }
      }
    }
  }).mount('#app')
</script>

</html>
```

index も提供できます:

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
    <ul>
      <li v-for="(value, name, index) in myObject">
        {{ index}}. {{ name }}: {{ value }}
      </li>
    </ul>
  </div>
</body>
<script>
  Vue.createApp({
    data() {
      return {
        myObject: {
          title: 'How to do lists in Vue',
          author: 'Jane Doe',
          publishAt: '2020-03-22'
        }
      }
    }
  }).mount('#app')
</script>

</html>
```

## _Note_

オブジェクトを反復処理するとき、順序は `Object.keys()` の列挙順のキーに基づいており、全てのJavaScript エンジンの実装で一貫性が保証されていません。<br>

# 状態の維持

Vueが `v-for` でレンダリングされた要素のリストを更新する際、デフォルトでは "その場でパッチを適用する" (in-place patch) 戦略が用いられます。<br>
データのアイテムの順序が変更された場合、アイテムの順序に合わせてDOM要素を移動する代わりに、Vueは各要素にその場でパッチを適用して、その特定のインデックスに何をレンダリングするべきかを確実に反映します。<br>

このデフォルトのモードは効率がいいです。しかしこれは、_レンダリングされたリストが子コンポーネントの状態や、一時的なDOMの状態に依存してないときにだけ適しています（例: フォームのインプットの値）。<br>

Vueが各ノードの識別情報を追跡できるヒントを与えるために、また、先ほど説明したような既存の要素の並び替えができるように、一意な `key` 属性を全てのアイテムに与える必要があります:

```html:sample.html
<div v-for="item in items" :key="item.id">
   <!-- content -->
</div>
```

繰り返されるDOMの内容が単純な場合や、性能向上のためにデフォルトの動作を意図的に頼る場合を除いて、可能な時はいつでも `v-for`に`key`属性を与えることが <a src="https://v3.ja.vuejs.org/style-guide/#%E3%82%AD%E3%83%BC%E4%BB%98%E3%81%8D-v-for-%E5%BF%85%E9%A0%88">推奨されます。</a>

これは Vue がノードを識別する汎用的な仕組みなので、`key` はガイドの後半でわかるように `v-for` に縛られない他の用途もあります。<br>

`_Note_`<br>

オブジェクトや配列のような非プリミティブ値を `v-for` のキーとして使わないでください。<br>
代わりに、文字列や数値を使ってください。<br>

`key` 属性の詳細な使い方は、 key <a src="https://v3.ja.vuejs.org/api/special-attributes.html#key">API ドキュメント</a> を参照してください。<br>

# 配列の変化を検出

## 変更メソッド

Vue は画面の更新もトリガするために、監視された配列の変更メソッドをラップします。<br>
ラップされたメソッドは次のとおりです:

+ `push()`<br>
+ `pop()`<br>
+ `shift()`<br>
+ `unshift()`<br>
+ `splice()`<br>
+ `sort()`<br>
+ `reverse()`<br>

コンソールを開いて前の `items` 配列の例で変更メソッドを呼び出して試してみてください。<br>
例えば `example1.items.push({ message: 'Baz' })`のようにしてみましょう。<br>

## 配列の置き換え

変更メソッドは、名前が示唆するように、それらが呼ばれると元の配列を変更します。<br>
一方で、変更しないメソッドもあります。<br>
例えば、`filter()`、`concat()`、`slice()` は、元の配列を変更せず、_常に新しい配列_を返します。<br>
これらのメソッドを使用する場合は、新しいもので古い配列を置き換えることで変更できます:<br>

```js:sample.js
example1.items = example1.items.filter(item => item.message.match(/Foo/))
```

これにより、Vueが既存のDOM を破棄し、リスト全体を再レンダリングすると思われるかもしれませんが、幸いなことにそうではありません。<br>
VueはDOM要素の再利用を最大化するために幾つかのスマートなヒューリスティックを実装しているので、重複するオブジェクトを含んでいる別の配列で元の配列を置き換えることは、とても効率的な操作です。<br>

## フィルタ/ソートされた結果の表示

元のデータを実際に変更またはリセットせずに、フィルタリングやソートされたバージョンの配列を表示したいことがあります。<br>
このケースでは、フィルタリングやソートされた配列を返す算出プロパティを作ることができます。<br>

例えば:

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
    <ul>
      <li v-for="n in evenNumbers" :key="n">
        {{ n }}
      </li>
    </ul>
  </div>
</body>
<script>
  Vue.createApp({
    data() {
      return {
        numbers: [1, 2, 3, 4, 5]
      }
    },
    computed: {
      evenNumbers() {
        return this.numbers.filter(number => number % 2 === 0)
      }
    }
  }).mount('#app')
</script>

</html>
```

算出プロパティが使えない状況ではメソッドを使うこともできます。(例: 入れ子になった v-for のループの中): <br>

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
    <ul v-for="numbers in sets">
      <li v-for="n in even(numbers)" :key="n">
        {{ n }}
      </li>
    </ul>
  </div>
</body>
<script>
  Vue.createApp({
    data() {
      return {
        sets: [[1, 2, 3, 4, 5], [6, 7, 8, 9, 10]]
      }
    },
    methods: {
      even(numbers) {
        return numbers.filter(number => number % 2 === 0)
      }
    }
  }).mount('#app')
</script>

</html>
```

## 範囲付き `v-for`

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
    <span v-for="n in 10">{{ n }}</span>
  </div>
</body>
<script>
  Vue.createApp({

  }).mount('#app')
</script>

</html>
```

## `<template>` での `v-for`

テンプレート `v-if` と同様に、複数の要素のブロックをレンダリングするために、`v-for` で `<template>` タグを使うこともできます。例:

```html:sample.html
<ul>
  <template v-for="item in items" :key="item.msg">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## `v-for` と `v-if`

_TIP_ <br>

`v-if` と `v-for` を同時に利用することは _推奨されません。_ 詳細については <a src="https://v3.ja.vuejs.org/style-guide/#v-for-%E3%81%A8%E4%B8%80%E7%B7%92%E3%81%AB-v-if-%E3%82%92%E4%BD%BF%E3%81%86%E3%81%AE%E3%82%92%E9%81%BF%E3%81%91%E3%82%8B-%E5%BF%85%E9%A0%88">スタイルガイド</a>を参照ください。<br>

```html:sample.html
<!-- インスタンスに "todo" プロパティが定義されていないため、エラーが発生します。 -->

<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo.name }}
</li>
```

これは、`v-for` を `<template>` タグで囲み、移動させることで修正できます: <br>

```html:sample.html
<template v-for="todo in todos" :key="todo.name">
  <li v-if="!todo.isComplete">
    {{ todo.name }}
  </li>
</template>
```

## コンポーネントと `v-for`

このセクションでは、<a src="https://v3.ja.vuejs.org/guide/list.html#v-for-%E3%81%A8-v-if:~:text=%E3%81%93%E3%81%AE%E3%82%BB%E3%82%AF%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%A7%E3%81%AF%E3%80%81-,%E3%82%B3%E3%83%B3%E3%83%9D%E3%83%BC%E3%83%8D%E3%83%B3%E3%83%88,-%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6%E3%81%AE%E7%9F%A5%E8%AD%98">コンポーネント</a>についての知識を前提としています。<br>
もしわからなければ、このセクションを遠慮なく飛ばして、理解した後に戻ってきてください。<br>

通常の要素のように、カスタムコンポーネントで直接 `v-for` を使うことができます: <br>

```html:sample.html
<my-components v-for="item in items" :key="item.id"></my-components>
```

ただし、コンポーネントは自身の隔離されたスコープを持っているため、これによってコンポーネントにデータが自動的に渡されることはありません。<br>
繰り返されたデータをコンポーネントに渡すには、プロパティも使用する必要があります: <br>

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
    <form v-on:submit.prevent="addNewTodo">
      <label for="new-todo">Add a todo</label>
      <input v-model="newTodoText" id="new-todo" placeholder="E.g. Feed the cat" />
      <button>Add</button>
    </form>
    <ul>
      <todo-item v-for="(todo, index) in todos" :key="todo.id" :title="todo.title" @remove="todos.splice(index, 1)">
      </todo-item>
    </ul>
  </div>
</body>

<script>
  const app = Vue.createApp({
    data() {
      return {
        newTodoText: '',
        todos: [
          {
            id: 1,
            title: 'Do the dishes'
          },
          {
            id: 2,
            title: 'Take out the trash'
          },
          {
            id: 3,
            title: 'Mow the lawn'
          },
        ],
        nextTodoId: 4
      }
    },
    methods: {
      addNewTodo() {
        this.todos.push({
          id: this.nextTodoId++,
          title: this.newTodoText
        })
        this.newTodoText = ''
      }
    }
  })

  app.component('todo-item', {
    template: `
    <li>
      {{ title }}
      <button @click="$emit('remove')">Remove</button>
    </li>
  `,
    props: ['title'],
    emits: ['remove']
  })

  app.mount('#app')
</script>

</html>
```