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
    props: ['todo'],
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
