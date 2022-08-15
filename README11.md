# Provide / inject

このページは既に[コンポーネントの基本](https://v3.ja.vuejs.org/guide/component-basics.html)を読んでいる事を前提としています。<br>
コンポーネントを初めて使う方はそちらを先にお読みください。<br>

通常、親コンポーネントから子コンポーネントにデータを渡すとき、[props](https://v3.ja.vuejs.org/guide/component-props.html)を使います。<br>
深くネストされた幾つかのコンポーネントがあり、深い階層にあるコンポーネントが浅い階層にあるコンポーネントの何かしらのデータのみを必要がありますが、時にそれは面倒になります。<br>

そのような場合は、`provide` と `inject`のペアを利用できます。<br>
コンポーネント階層の深さに関係なく、親コンポーネントは、その全ての子階層へ依存関係を提供するプロパイダとして機能することができます。<br>
この機能は2つの機能からなります:親コンポーネントは、データを提供するためのオプション `provide`を持ち、子コンポーネントはそのデータを利用するためのオプション `inject` を持っています。<br>

例えば、このような構造があるとします:<br>

```
Root
└─ TodoList
   ├─ TodoItem
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

もし todo-items のサイズを `todoListStatistics`に渡したい場合、プロパティをこのように渡します: `TodoList` -> `TodoListFooter` -> `TodoListStatistics`。<br>
provide/inject を利用すると、これを直接的に行えます。<br>

```js:sample.js
const app = Vue.createApp({})

app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide: {
    user: 'John Doe'
  },
  template: `
    <div>
      {{ todos.length }}
      <!-- rest of the template -->
    </div>
  `
})

app.component('todo-list-statistics', {
  inject: ['user'],
  created() {
    console.log(`Injected property: ${this.user}`) // > Injected property: John Doe
  }
})
```

ただし、ここでコンポーネントのインスタンスプロパティを提供しようとしても、うまく動作しないでしょう:<br>

```js:sample.js
app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide: {
    todoLength: this.todos.length // this will result in error `Cannot read property 'length' of undefined`
  },
  template: `
    ...
  `
})
```

コンポーネントのインスタンスプロパティにアクセスするためには、<br>
`provide`をオブジェクトを返す関数へ変換する必要があります：<br>

```js:sample.js
app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide() {
    return {
      todoLength: this.todos.length
    }
  },
  template: `
    ...
  `
})
```

こうすることで、子コンポーネントが依存している何かを変更したり削除したりしてしまう事を恐れる事なく、より安全にコンポーネントを開発し続けることができます。<br>
これらのコンポーネント間のインターフェースは、propsと同じく、明確に定義されています。<br>

実際、依存関係の注入は、いわば「長距離な props」のように考えることがきます。御述の点を除きます：<br>

+ 親コンポーネントは、提供したプロパティをし孫コンポーネントのどこで使用しているか知る必要がありません<br>
+ 子コンポーネントは、注入されたプロパティがどこから提供されたものなのか知る必要がありません<br>

## リアクティブと連携する

前述の例では、リスト `todos`を変更しても、その変更は注入された `todoLength`に反映されません。<br>
これは、`provide/inject`の束縛(binding) がデフォルトでリアクティブでないことが原因です。<br>
`ref` で定義されたプロパティや `reactive`で作成されたオブジェクトを `provide`に渡すことにより、この振る舞いを変更することができます。<br>
この場合、素先コンポーネントをリアクティブにするためには、Composition APIの `computed` で定義したプロパティ `todoLength` を割り当てる必要があります。<br>

```js:sample.js
app.component('todo-list', {
  // ...
  provide() {
    return {
      todoLength: Vue.computed(() => this.todos.length)
    }
  }
})

app.component('todo-list-statistics', {
  inject: ['todoLength'],
  created() {
    console.log(`Injected property: ${this.todoLength.value}`) // > Injected property: 5
  }
})
```

こうすると、`todos.length` へのあらゆる変更は、`todoLength` が注入されたコンポーネントに正しく反映されます。<br>
`computed`については、[算出プロパティとウォッチのセクション](https://v3.ja.vuejs.org/guide/reactivity-computed-watchers.html#%E7%AE%97%E5%87%BA%E3%83%97%E3%83%AD%E3%83%91%E3%83%86%E3%82%A3)を、そして `reactive` の provide/inject の詳細については、[Composition API のセクション](https://v3.ja.vuejs.org/guide/composition-api-provide-inject.html#%E3%83%AA%E3%82%A2%E3%82%AF%E3%83%86%E3%82%A3%E3%83%95%E3%82%99) をご覧ください。<br>
