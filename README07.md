# コンポーネントの基本

## 基本例

Vue コンポーネントの例を次に示します: <br>

```js:sample.js
// Vue アプリケーションを作成します
const app = Vue.createApp({})

グローバルな button-counter というコンポーネントを定義します
app.component('button-counter', {
  data() {
    return {
      count: 0
    }
  },
  template: `
    <button @click="count++">
      You clicked me {{ count }} times.
    </button>`
})
```

### INFO

ここでは単純な例を示していますが、典型的な Vue アプリケーションでは文字列テンプレートではなく単一ファイルコンポーネントを使用します。
詳しくは [こちら](https://v3.ja.vuejs.org/guide/single-file-component.html) で解説しています。<br>

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
  <div id="app">
    <button-counter></button-counter>
  </div>
</body>

<script>
  const app = Vue.createApp({})

  app.component('button-counter', {
    data() {
      return {
        count: 0
      }
    },
    template: `
      <button v-on:click="count++">
          You clicked me {{ count }}
      </button>`
  })

  app.mount('#app')
</script>

</html>
```

コンポーネントは再利用可能なインスタンスなので、`data`、`computed`、`watch`、`methods`、そしてライフサイクルフックのようなルートインスタンスと同様のオプションが利用可能です。<br>

## コンポーネントの再利用

コンポーネントは必要なだけ何度でも再利用できます: <br>

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
  <div id="app">
    <button-counter></button-counter>
    <button-counter></button-counter>
    <button-counter></button-counter>
  </div>
</body>

<script>
  const app = Vue.createApp({})

  app.component('button-counter', {
    data() {
      return {
        count: 0
      }
    },
    template: `
      <button v-on:click="count++">
          You clicked me {{ count }} times.
      </button>`
  })

  app.mount('#app')
</script>

</html>
```

ボタンをクリックすると、それぞれが独自の `count` を保持することに注意してください。<br>
これはコンポーネントを使用する度に新しいコンポーネントの _インスタンス_ が作成されるためです。<br>

## コンポーネントの構成

一般的にアプリケーションはネストされたコンポーネントのツリーに編成されます: <br>

例えば、ヘッダー、サイドバー、コンテンツエリアなどのコンポーネントがあり、それぞれには一般的にナビゲーションリンクやブログ投稿などのコンポーネントが含まれています。<br>

これらのコンポーネントをテンプレートで使用するには、Vueがそれらを認識できるように登録する必要があります。<br>
コンポーネントの登録には _グローバル_ と _ローカル_ の2種類があります。<br>
これまでは、アプリケーションの `component` メソッドを利用してグローバルに登録してきだだけです: <br>

```js:sample.js
const app = Vue.createApp({})

app.component('my-component-name', {
  // ... オプション ...
})
```

グローバルに登録されたコンポーネントは、アプリケーション内のどのコンポーネントのテンプレートでも使うことができます。<br>

とりあえずコンポーネント登録についてはこれで以上ですが、このページを読み終えて十分に理解できたら、後から戻ってきて [コンポーネント登録](https://v3.ja.vuejs.org/guide/component-registration.html) の完全なガイドを読むことをお勧めします。<br>

## プロパティを用いた子コンポーネントへのデータの受け渡し

先ほど、プログ投稿用のコンポーネントの作成について触れました。<br>
問題は、表示する特定の投稿のタイトルや内容のようなデータを作成したコンポーネントに渡さなければそのコンポーネントは役に立たないということです。<br>
プロパティはここで役立ちます。<br>

プロパティはコンポーネントに登録できるカスタム属性です。値がプロパティ属性に渡されると、そのコンポーネントインスタンスのプロパティになります。<br>
先ほどのブログ投稿用のコンポーネントにタイトルを渡すためには、`props`オプションを用いてこのコンポーネントが受け取るプロパティのリストの中に含めることができます:

```js:sample.js
const app = Vue.createApp({})

app.component('blog-post', {
  props: ['title'],
  template: `<h4>{{ title }}</h4>`
})

app.mount('#blog-post-demo')
```

プロパティ属性に値が渡されると、渡されるとそのコンポーネントインスタンスのプロパティになります。<br>
そのプロパティの値は、他のコンポーネントのプロパティと同じように、テンプレート内でアクセスができます。<br>

コンポーネントは必要に応じて多くのプロパティを持つことができ、デフォルトでは任意のプロパティに任意の値を渡すことができます。<br>

プロパティが登録されたら、次のようにカスタム属性としてデータをプロパティに渡すことができます: <br>

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
  <div id="app">
    <blog-post title="My journey with Vue"></blog-post>
    <blog-post title="Blogging with Vue"></blog-post>
    <blog-post title="Why Vue is so fun"></blog-post>
  </div>
</body>

<script>
  const app = Vue.createApp({})

  app.component('blog-post', {
    props: ['title'],
    template: `<h4>{{ title }}</h4>`
  })

  app.mount('#app')
</script>

</html>
```

しかしながら、一般的なアプリケーションではおそらく `data`に投稿の配列を持っています: <br>

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
  <div id="app">
    <blog-post v-for="post in posts" :key="post.id" :title="post.title"></blog-post>
  </div>
</body>

<script>
  const App = {
    data() {
      return {
        posts: [
          { id: 1, title: 'My journey with Vue' },
          { id: 2, title: 'Blogging with Vue' },
          { id: 3, title: 'Why Vue is do fun' },
        ]
      }
    }
  }
  const app = Vue.createApp(App)

  app.component('blog-post', {
    props: ['title'],
    template: `<h4>{{ title }}</h4>`
  })

  app.mount('#app')
</script>

</html>
```

上記では、`v-bind` を用いて動的にプロパティを渡すことができるとわかります。<br>
これはレンダリングする内容が事前に分からない場合に特に便利です。<br>

とりあえずプロパティについてはこれで以上ですが、このページを読み終えて十分に理解できたら、後から戻ってきて [プロパティ](https://v3.ja.vuejs.org/guide/component-props.html) の完全なガイドを読むことをおすすめします。<br>

## 子コンポーネントのイベントを購読する

`<blog-post>`コンポーネントは開発する中で、いくつかの機能で親コンポーネントとの通信が必要になるかもしれません。<br>
例えば、ページの他の部分の大きさはそのままで、ブログ記事のテキストを拡大するアクセシビリティ機能を実装すること決めるかもしれません。<br>

親コンポーネントでは、`postFontSize` データプロパティを追加することでこの機能をサポートすることができます: <br>

```js:sample.js
const App = {
  data() {
    return {
      posts: [
        /* ... */
      ],
      postFontSize: 1
    }
  }
}
```

これは全てのプログ投稿のフォントサイズを制御するためにテンプレート内で使用できます: <br>

```html:sample.html
<div id="blog-posts-events-demo">
    <div :style="{ fontSize: postFontSize + 'em' }">
      <blog-post
        v-for="post in posts"
        :key="post.id"
        :title="post.title"
      ></blog-post>
    </div>
</div>
```

それでは、全ての投稿の内容の前にテキストを拡大するボタンを追加します: <br>

```js:sample.js
app.component('blog-post', {
  props: ['title'],
  template: `
    <div class="blog-post">
      <h4>{{ title }}</h4>
      <button>
        Enlarge text
      </button>
    </div>
  `
})
```

問題は、このボタンが何もしないことです:<br>

```html:sample.html
<button>
  Enlarge text
</button>
```

ボタンをクリックすると、全ての投稿のテキストを拡大する必要があることにを親に伝える必要があります。<br>
この問題を解決するために、コンポーネントインスタンスはカスタムイベントの仕組みを提供しています。<br>
親は、ネイティブDOMイベントでの場合と同様に、`v-on`や`@`を用いて子コンポーネントのインスタンスでのイベントを購読することができます: <br>

```html:index.html
<blog-post ... @enlarge-text="postFontSize += 0.1"></blog-post>
```

そして子コンポーネントはビルドインの `$emit` [メソッド](https://v3.ja.vuejs.org/api/instance-methods.html#emit) にイベントの名前を渡して呼び出すことで、イベントを発行することができます:<br>

```html:index.html
<button @click="$emit('enlargeText)">
  Enlarge text
</button>
```

`@enlarge-text="postFontSize += 0.1" リスナによって、親コンポーネントはこのイベントを受け取り `postFontSize` の値を更新することができます。<br>

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
    <div :style="{ fontSize: postFontSize + 'em' }">
      <blog-post
        v-for="post in posts"
        :key="post.id"
        :title="post.title"
        @enlarge-text="postFontSize += 0.1"
      ></blog-post>
    </div>
  </div>
</body>

<script>
  const App = Vue.createApp({
    data() {
      return {
        posts: [
          {id: 1, title: 'My journey with Vue'},
          {id: 2, title: 'Blogging with Vue'},
          {id: 3, title: 'Why Vue is so fun'},
        ],
        postFontSize: 1,
      }
    }
  })

  App.component('blog-post', {
    props: ['title'],
    template: `
      <div class="blog-post">
        <h4>{{ title }}</h4>
        <button
          @click="$emit('enlargeText')"
        >
          Enlarge text
        </button>
      </div>
    `
  })

  App.mount('#app')
</script>

</html>
```

コンポーネントの `emits` オプションにより発行されたイベントを一覧することができます: <br>

```js:sample.js
app.component('blog-post', {
  props: ['title'],
  emits: ['enlargeText']
})
```

これにより、コンポーネントが排出する全てのイベントをチェックし、オプションでそれらを[検証する](https://v3.ja.vuejs.org/guide/component-custom-events.html#%E7%99%BA%E8%A1%8C%E3%81%95%E3%82%8C%E3%81%9F%E3%82%A4%E3%83%98%E3%82%99%E3%83%B3%E3%83%88%E3%82%92%E6%A4%9C%E8%A8%BC%E3%81%99%E3%82%8B) ことができます。<br>

## イベントと値を発行する

イベントを特定の値と一緒に発行すると便利な場合があります。例えば、テキストをどれだけ大きく表示するかを `<blog-post>`コンポーネントの責務とさせたいかもしれません。<br>
そのような場合、`$emit`の第二引数を使ってこの値を渡すことができます: <br>

```html:sample.html
<button @click="$emit('enlargeText', 0.1)">
  Enlarge text
</button>
```

そして親でこのイベントを購読すると、`$event`を用いて排出されたイベントの値にアクセスすることができます: <br>

```html:sample.html
<blog-post ... @enlarge-text="postFontSize += $event"></blog-post>
```

または、イベントハンドラがメソッドの場合: <br>

```html:sample.html
<blog-post ... @enlarge-text="onEnlargeText"></blog-post>
```

値はそのメソッドの第一引数として渡されます: <br>

```js:sample.js
methods: {
  onEnlargeText(enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

## コンポーネントで `v-model`を使う

カスタムイベントは `v-model` で動作するカスタム入力を作成することもできます。<br>
このことを覚えておいてください: <br>

```html:sample.html
<input v-model="searchText">
```

これは以下と同じことです: <br>

```html:sample.html
<input :value="searchText" @input="searchText = $event.target.value" />
```

コンポーネントで使用する場合、`v-model` は代わりにこれを行います: <br>

```html:sample.html
<custom-input
  :model-value="searchText"
  @update:model-value="searchText = $event"
></custom-input>
```

### _WARNING_

ここでは in-DOM テンプレートを使用しているため、`model-value` をケバブケースで表記していることに注意してください。<br>
ケバブケースの属性とキャメルケースの属性に関しては　[DOMテンプレートの構文解析の注意点](https://v3.ja.vuejs.org/guide/component-basics.html#dom-%E3%83%86%E3%83%B3%E3%83%95%E3%82%9A%E3%83%AC%E3%83%BC%E3%83%88%E3%83%8F%E3%82%9A%E3%83%BC%E3%82%B9%E6%99%82%E3%81%AE%E6%B3%A8%E6%84%8F) の章で詳しく解説されています。<br>

これが実際に機能するためには、テンプレート内の `<input>`は以下でなければなりません:<br>

+ `value` 属性を `modelValue` プロパティにバインドする<br>
+ `input` では、 `update:modelValue` イベントを新しい値と共に発行する<br>

以下のようになります：<br>

```js:sample.js
app.component('custom-input', {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  template: `
    <input
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
    />
  `
})
```

これで `v-model` はこのコンポーネントで完璧に動作します: <br>

```html:sample.html
<custom-input v-model="searchText"></custom-input>
```
このコンポーネント内で `v-model` を実装するもう一つの方法は `cumputed` プロパティの機能を使ってゲッターとセッターを定義することです。<br>
`get`メソッドは`modelValue`プロパティを返して、`set`メソッドは対応するイベントを発行する必要があります。<br>

```js:sample.js
app.component('custom-input', {
  props: ['modelValue'],
  emits: ['update:modelValue'],
  template: `
    <input v-model="value">
  `,
  computed: {
    value: {
      get() {
        return this.modelValue
      },
      set(value) {
        this.$emit('update:modelValue', value)
      }
    }
  }
})
```

とりあえずカスタムイベントについてはこれで以上ですが、このページを読み終えて十分に理解できたら、後から戻ってきて [カスタムイベント](https://v3.ja.vuejs.org/guide/component-custom-events.html) の完全なガイドを読むことをおすすめします。<br>

## スロットによるコンテンツ配信

HTML要素のように、コンポーネントに要素を渡すことができると便利なことがよくあります。例えば以下の通り: <br>

```html:sample.html
<alert-box>
  Something bad happened.
</alert-box>
```

これは以下のようにレンダリングされるでしょう。: <br>

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

    .demo-alert-box {
      padding: 10px 20px;
      background: #f3beb8;
      border: 1px solid #f09898;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <alert-box>
      Something bad happend
    </alert-box>
  </div>
</body>

<script>
  const App = Vue.createApp({})

  App.component('alert-box', {
    template: `
      <div class="demo-alert-box">
        <strong>Error!</strong>
        <slot></slot>
      </div>
    `
  })

  App.mount('#app')
</script>

</html>
```

これは、Vueのカスアム `<slot>`要素で達成できます:<br>

```js:sample.js
App.component('alert-box', {
  template: `
    <div class="demo-alert-box">
      <strong>Error!</strong>
      <slot></slot>
    </div>
  `
})
```

上で見た通り、コンテンツを配置したいところにプレースホルダとして `<slot>` を使います。それだけです。終わりです！<br>

とりあえずスロットについてはこれで以上ですが、このページを読み終えて十分に理解できたら、後から戻っってきて [スロット](https://v3.ja.vuejs.org/guide/component-slots.html) の完全なガイドを読むことをおすすめします。<br>


## 動的なコンポーネント

タブ付きのインターフェースのように、コンポーネント間を動的に切り替えると便利なことがあります: <br>

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

    .tab-button {
      padding: 6px 10px;
      border-top-left-radius: 3px;
      border-top-right-radius: 3px;
      border: 1px solid #ccc;
      cursor: pointer;
      background: #f0f0f0;
      margin-bottom: -1px;
      margin-right: -1px;
    }

    .tab-button:hover {
      background: #e0e0e0;
    }

    .tab-button.active {
      background: #e0e0e0;
    }

    .demo-tab {
      border: 1px solid #ccc;
      padding: 10px;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <button v-for="tab in tabs" v-bind:key="tab" v-bind:class="['tab-button', { active: currentTab === tab }]"
      v-on:click="currentTab = tab">
      {{ tab }}
    </button>

    <component v-bind:is="currentTabComponent" class="tab"></component>
  </div>
</body>

<script>
  const App = Vue.createApp({
    data() {
      return {
        currentTab: 'Home',
        tabs: ['Home', 'Posts', 'Archive']
      }
    },
    computed: {
      currentTabComponent() {
        return 'tab-' + this.currentTab.toLowerCase()
      }
    }
  })

  App.component('tab-home', {
    template: `
      <div class="demo-tab">
        Home component
      </div>
    `
  })
  App.component('tab-posts', {
    template: `
      <div class="demo-tab">
        Posts component
      </div>
    `
  })
  App.component('tab-archive', {
    template: `
      <div class="demo-tab">
        Archive component
      </div>
    `
  })

  App.mount('#app')
</script>

</html>
```

上記は Vueの `<component>`要素に特別な `is`属性を持たせることで実現しています: <br>

```html:sample.html
<!-- コンポーネントは currentTabComponent に変更があったときに変更されます -->
<component :is="currentTabComponent"></component>
```

上記の例では、`currentTabComponent`は以下のいずれかを含むことができます：<br>

+ 登録されたコンポーネントの名前、または<br>
+ コンポーネントのオプションオブジェクト<br>

完全なコードを試すには [この例](https://codepen.io/team/Vue/pen/oNXaoKy)、登録された名前ではなくコンポーネントのオプションオブジェクトをバインドしている例は [こちらのバージョン](https://codepen.io/team/Vue/pen/oNXapXM) を参照してください。<br>

また、 `is`属性を使って通常のHTML要素を作ることもできます。<br>

とりあえず動的なコンポーネントについてはこれで以上ですが、このページを読み終えて十分に理解できたら、後から戻ってきて [動的 & 非同期コンポーネント](https://v3.ja.vuejs.org/guide/component-dynamic-async.html) の完全なガイドを読むことをおすすめします。<br>

## DOM テンプレートパース時の注意

VueのテンプレートをDOMに直接書いている場合、VueはDOMからテンプレート文字列を取得する必要があります。<br>
これはブラウザのネイティブなHTML解析の動作に起因するいくつかの注意点につながります。<br>

#### _TIP_

以下の制限は、DOMで直接テンプレートを書いている場合にのい適用されることに注意してください。<br>
次のソースから文字列テンプレートを使っている場合には適用されません: <br>

+ 文字列テンプレート (例: template: '...') <br>
+ [単一ファイルコンポーネント ( .vue )](https://v3.ja.vuejs.org/guide/single-file-component.html) <br>
+ `<script type="text/x-template">`<br>

## 要素の配置制限

`<ul>`、`<ol>`、`<table>`、`<select>` のようないくつかのHTML要素にはその内側でどの要素が現れるかに制限があり、<br>
`<li>`、`<tr>`、`<option>` のようないくつかの属性は他の特定の要素の中にしか現れません。<br>

このような制限を持つ属性を含むコンポーネントを使用すると問題が発生することがあります。例えば:<br>

```html:sample.html
<table>
  <blog-post-row></blog-post-row>
</table>
```

このカスタムコンポーネント `<blog-post-row>`は無効なコンテンツとして巻き取られ、最終的にレンダリングされた出力でエラーが発生します。<br>
回避策として特別な `is` [属性](https://v3.ja.vuejs.org/api/special-attributes.html#is) を使うことができます: <br>

```html:sample.html
<table>
  <tr is="vue:blog-post-row"></tr>
</table>
```

#### _TIP_

ネイティブのHTML要素で使われるとき、Vueコンポーネントとして解釈されるためには `is`の値の前に `vue:`をつける必要があります。<br>
これはネイティブの [カスタマイズされた組み込み要素](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements-customized-builtin-example) との混同を避けるために必要です。<br>

## 大文字小文字の違いを無視

HTMLの属性名は大文字小文字を区別しないので、ブラウザは全ての大文字を小文字として解釈します。<br>
つまり、DOM内テンプレートを使用している場合、キャメルケースのプロパティ名やイベントハンドラのパラメータはそれと同等のケバブケース(ハイフンで区切られた記法)を使用する必要があります:<br>

```js:sample.js
// JavaScript ではキャメルケース

app.component('blog-post', {
  props: ['postTitle'],
  template: `
    <h3>{{ postTitle }}
  `
})
```

```html:sample.html
<!-- HTML ではケバブケース -->

<blog-post post-title="hello!"></blog-post>
```

とりあえずDOMテンプレートパース時の警告についてはこれで以上です。そして実は、Vueの本質の最後となります。おめでとうがざいます！<br>
まだまだ学ぶべきことはありますが、まずは一休みして自分でVueを実際に使って楽しいものを作ってみることをおすすめします。<br>

理解したばかりの知識に慣れてきたら、サイドバーのコンポーネントの詳細セクションの他のページと同様に、[動的 & 非同期コンポーネント](https://v3.ja.vuejs.org/guide/component-dynamic-async.html)　の完全なガイドを読むために戻ってくることをおすすめします。<br>
