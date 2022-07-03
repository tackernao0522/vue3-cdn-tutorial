# Vue3.js(CDN) （公式) https://v3.ja.vuejs.org/

## 宣言的レンダリング

Vue.jsのコアは、単純なテンプレート構文を使って宣言的にデータをDOMにレンダリングすることを可能にするシステムです。

+ `front/index.html`を作成<br>

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
  <div id="counter">
    Counter: {{ counter }}
  </div>
</body>

<script>
  const Counter = {
    data() {
      return {
        counter: 0
      }
    }
  }

  Vue.createApp(Counter).mount('#counter')
</script>

</html>
```

## `counter`プロパティが1秒毎にインクリメントされたとき、レンダリングされたDOMがどのように変化するか見てみる

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
  <div id="counter">
    Counter: {{ counter }}
  </div>
</body>

<script>
  const Counter = {
    data() {
      return {
        counter: 0
      }
    },
    mounted() {
      setInterval(() => {
        this.counter++
      }, 1000)
    }
  }

  Vue.createApp(Counter).mount('#counter')
</script>

</html>
```

# 文字列の展開に加えて、以下のように要素の属性をバインド(束縛)することもできます:

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
  <div id="bind-attribute">
    <span v-bind:title="message">
      Hover your mouse over me for a few seconds to see my dynamically bound
      title!
    </span>
  </div>
</body>

<script>
  const AttributeBinding = {
    data() {
      return {
        message: 'You loades this page on ' + new Date().toLocaleString()
      }
    }
  }

  Vue.createApp(AttributeBinding).mount('#bind-attribute')
</script>

</html>
```

`v-bind`属性は`ディレクティブと呼ばれます。

## ユーザー入力の制御

ユーザーがアプリケーションと対話できるように、`v-on`ディレクティブを使ってイベントりすなをアタッチし、<br>
インスタンスのメソッドを呼び出すことができます。<br>

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
  <div id="event-handling">
    <p>{{ message }}</p>
    <button v-on:click="reverseMessage">Reverse Message</button>
  </div>
</body>

<script>
  const EventHandling = {
    data() {
      return {
        message: 'Hello Vue.js!'
      }
    },
    methods: {
      reverseMessage() {
        this.message = this.message
          .split('')
          .reverse()
          .join('')
      }
    }
  }

  Vue.createApp(EventHandling).mount('#event-handling')
</script>

</html>
```

+ 上記の方法では、DOMを触るのではなくアプリケーションの状態を更新することに注意してください。<br>
DOMの操作は全てVueによって処理されるので、背後にあるロジックを書くことに集中することができます。<br>

+ Vueはまた、フォームの入力とアプリケーションの状態を双方向にバインディングするための`v-model`ディレクティブも下記のように提供します。<br>

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
  <div id="two-way-binding">
    <p>{{ message }}</p>
    <input v-model="message" />
  </div>
</body>

<script>
  const TwoWayBinding = {
    data() {
      return {
        message: 'Hello Vue!'
      }
    }
  }

  Vue.createApp(TwoWayBinding).mount('#two-way-binding')
</script>

</html>
```

## 条件分岐とループ

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
  <div id="conditional-rendering">
    <span v-if="seen">Now you see me</span>
  </div>
</body>

<script>
  const ConditionalRenering = {
    data() {
      return {
        seen: true
      }
    }
  }

  Vue.createApp(ConditionalRenering).mount('#conditional-rendering')
</script>

</html>
```

+ Vue.jsには他にもかなりの数のディレクティブがあり、それぞれが特定の機能を持っています。<br>
例えば、`v-for`ディレクティブを使えばアイテムのリストを配列内のデータを使って表示することもできます。<br>

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
  <div id="list-rendering">
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </div>
</body>

<script>
  const ListRendering = {
    data() {
      return {
        todos: [
          { text: 'Learn JavaScript' },
          { text: 'Learn Vue' },
          { text: 'Build something awesome' },
        ]
      }
    }
  }

  Vue.createApp(ListRendering).mount('#list-rendering')
</script>

</html>
```

## コンポーネントによる構成

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
  <div id="todo-list-app">
    <ol>
      <todo-item v-for="item in groceryList" v-bind:todo="item" v-bind:key="item.id"></todo-item>
    </ol>
  </div>
</body>

<script>
  const TodoItem = {
    props: ['todo'], // 'todo'(変数）をtemplateと v-bindに指定
    template: `<li>{{ todo.text }}</li>`
  }

  const TodoList = {
    data() {
      return {
        groceryList: [
          { id: 0, text: 'Vegetables' },
          { id: 1, text: 'Cheese' },
          { id: 2, text: 'Whatever else humans are supposed to eat' },
        ]
      }
    },
    components: {
      TodoItem
    }
  }

  const app = Vue.createApp(TodoList)

  app.mount('#todo-list-app')
</script>

</html>
```

# アプリケーションとコンポーネントのインスタンス

## アプリケーションインスタンスの作成

全ての Vue アプリケーションは `createApp` 関数で新しい`アプリケーションインスタンス(application instance)` を作成するところから始まります:<br>

```js:sample.js
const app = vue.createApp({
  /* options */
})
```

アプリケーションインスタンスは、そのアプリケーション内のコンポーネントが使えるグローバル（コンポーネント、ディレクティブ、プラグインなど）を登録するために使われます。<br>
詳しいことはガイドの公判で説明しますが、簡単な例をあげると:<br>

```js:sample.js
const app = Vue.createApp({})
app.component('SearchInput', SearchInputComponent)
app.directive('focus', FocusDirective)
app.use(LocalePlugin)
```

アプリケーションインスタンスが公開するほとんどのメソッドは、<br>
同じインスタンスを返すので、チェーンすることができます:

```js:sample.js
Vue.createApp({})
  .component('SearchInput', SearchInputComponent)
  .directive('focus', FocusDirective)
  .use(LocalePlugin)
```

# ルートコンポーネント

`createApp`に渡されたオプションは、`ルートコンポーネント`の設定に使われます。<br>
このコンポーネントは、アプリケーションを`マウント`する際に、レンダリングの起点として使われます。<br>

アプリケーションはDOM要素にマウントする必要があります。<br>
例えば、Vueアプリケーションを `<div id="app"></div>`にマウントしたい場合、`#app`を渡す必要があります:

```js:sample.js
const RootComponents = {
  /* オプション */
}
const app = Vue.createApp(RootComponent)
const vm = app.mount('#app')
```

ほとんどのアプリケーションメソッドとは異なり、`mount`はアプリケーションを返しません。<br>
代わりにルートコンポーネントのインスタンスを返します。<br>

厳密には`MVVM パターン`とは関係ないですが、Vueの設計はその影響を受けています。<br>
慣習として、コンポーネントのインスタンスを参照するのに`vm`(ViewModelの略)という変数を使うことがよくあります。<br>

このページの全ての例では１つのコンポーネントしか必要としていませんが、<br>
実際の多くのアプリケーションでは再利用可能なコンポーネントを入れ子にしたツリー状に構成されています。<br>
例えば、Todoアプリケーションのコンポーネントツリーは次のようになります。<br>

```
Root Component
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

各コンポーネントは独自のコンポーネントインスタンス `vm`を持ちます。<br>
`TodoItem`のような一部のコンポーネントでは、一度に複数のインスタンスがレンダリングされる可能性があります。<br>
このアプリケーションの全てのコンポーネントインスタンスは、同じアプリケーションインスタンスを共有します。<br>

とりあえず、ルートコンポーネントは他のコンポーネントとは何も違いはないことを認識しておいてください。<br>
設定オプションは同じで、対応するコンポーネントインスタンスの振る舞いも同じです。<br>

## コンポーネントインスタンスのプロパティ

このガイドの前半で `data` プロパティについて説明しました。<br>
`data` で定義されたプロパティは、コンポーネントインスタンスを介して公開されます:

```js:sample.js
const app = Vue.createApp({
  data() {
    return { count: 4 }
  }
})

const vm = app.mount('#app')

console.log(vm.count)  // => 4
```

他にもコンポーネントインスタンスにユーザ定義のプロパティを追加する様々なコンポーネントオプション、<br>
`methods`、`props`、`computed`、`inject`、`setup` などがあります。<br>
このガイドでは、それぞれについて後で詳しく説明します。<br>
コンポーネントインスタンスの全てのプロパティは、それらがどのように定義されているかに関わらず、コンポーネントのテンプレートからアクセスできます。<br>

Vue はコンポーネントインスタンスを介した `$attrs` や `$emit` などいくつかの組み込みプロパティも公開しています。<br>
これらのプロパティは、全て `$` プレフィックスとなっており、ユーザ定義のプロパティ名と衝突を避けるようになっています。<br>

## ライフサイクルフック

各コンポーネントインスタンスは、作られるときに一連の初期化ステップを通ります。<br>
例えば、データ監視の設定、テンプレートのコンパイル、DOMへのインスタンスのマウント、データ変更時のDOM更新などが必要になります。<br>
また、ユーザが特定の段階で独自のコードを追加できるように `ライフサイクルフック` と呼ばれる関数の実行をします。<br>

例えば `created` フックは、インスタンスの作成後にコードを実行するために使用できます:

```js:sample.js
Vue.createApp({
  date() {
    return { count: 1 }
  },
  created() {
    // `this` は vm インスタンスを指す
    console.log('count is: ' + this.count) // => "count is: 1"
  }
})
```

`mounted`、`updated`、`unmounted` のように、インスタンスのライフサイクルの他の段階で呼ばれる他のフックもあります。<br>
全てのライフサイクルフックは、呼び出し元である現在アクティブなインスタンスを指す `this` コンテキストとともに呼ばれます。<br>

<h5>Tip</h5>
`アロー関数`をオプションのプロパティやコールバックに使用しないでください。<br>
これは例えば、`created: () => console.log(this.a)` や `vm.$watch('a', newValue => this.myMethod())`のようなことです。<br>
アロー関数は `this` を持たないため、`this` は持たないため、`this`は他の変数のように親のスコープ内を辞書検索され、<br>
しばしば `Uncaught TypeError: Cannot read property of undefined` や `Uncaught TypeError: this.myMethod is not a function`のようなエラーを起こします。<br>

## ライフサイクルダイアグラム

参照: https://v3.ja.vuejs.org/guide/instance.html#%E3%83%A9%E3%82%A4%E3%83%95%E3%82%B5%E3%82%A4%E3%82%AF%E3%83%AB%E3%83%95%E3%83%83%E3%82%AF (ライフサイクルダイアグラム
) <br>
