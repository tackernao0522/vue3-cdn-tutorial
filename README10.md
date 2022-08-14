# スロット

このページはすでに[コンポーネントの基本](https://v3.ja.vuejs.org/guide/component-basics.html)を読んでいる事を前提としています。<br>
コンポーネントをよく知らない方はそちらを先にお読みください。<br>

[Learn slot basics with a free lesson on Vue School](https://vueschool.io/lessons/vue-3-component-slots?friend=vuejs)<br>

## スロットコンテンツ

Vueには [web Components spec draft](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md)にヒントを得たコンテンツ配信 APIが実装されており、`<slot>`要素をコンテンツ配信の受け渡し口として利用します。<br>

これを使う事で次のようなコンポーネントを作成することができます：<br>

```html:sample.html
<todo-button>
  Add todo
</todo-button>
```

そして、`<todo-button>`のテンプレートはこうなります:<br>

```html:sample.html
<!-- todo-button コンポーネントのテンプレート -->
<button class="btn-primary">
  <slot></slot>
</button>
```

コンポーネントをレンダリングする時、`<slot></slot>`は「Add todo」に置換されるでしょう：<br>

```html:sample.html
<!-- レンダリングされた HTML -->
<button class="btn-primary">
  Add todo
</button>
```

文字列はあくまで序の口です！スロットには、HTMLを含む任意のテンプレートコードを含めることが出来ます:<br>

```html:sample.html
<todo-button>
  <!-- Font Awesome のアイコンを追加 -->
  <i class="fas fa-plus"></i>
  Add todo
</todo-button>
```

あるいは他のコンポーネントを入れることも出来ます:<br>

```html:sample.html
<todo-button>
  <!-- コンポーネントを使ってアイコンを追加 -->
  <font-awesome-icon name="plus"></font-awesome-icon>
  Add todo
</todo-button>
```

もしも、`<todo-button>`のテンプレートが`<slot>`要素を含まない場合、開始タグと終了タグの間にある任意のコンテンツは破棄されます。<br>

```html:sample.html
<!-- todo-button コンポーネントのテンプレート -->

<button class="btn-primary">
  Create a new item
</button>
```

```html:sample.html
<todo-button>
  <!-- 次の行のテキストはレンダリングされません -->
  Add todo
</todo-button>
```

## レンダリングスコープ

スロットの中でデータを扱いたい場合はこうします：<br>

```html:sample.html
<todo-button>
  Delete a {{ item.name }}
</todo-button>
```

このスロットは、テンプレートの残りの部分と同じインスタンスのプロパティ（つまり、同じ"スコープ"）にアクセスできます。<br>

`<todo-button>`のスコープにアクセスすることは_できません_。例えば、`action`へのアクセスは動作しないでしょう：<br>

```html:sample.html
<todo-button action="delete">
  Clicking here will {{ action }} an item
  <!---
  `action`は undefined になります。なぜなら、このコンテンツは
  <todo-button> コンポーネントの中で定義されるのではなく、
  <todo-button> コンポーネントに渡されるからです
  -->
</todo-button>
```

ルールとしては、以下は覚えておいてください：<br>

親のテンプレート内の全てのものは親のスコープでコンパイルされ、子のテンプレート内の全てのものは子のスコープでコンパイルされる。<br>

## フォールバックコンテンツ

スロットに対して、コンテンツがない場合にだけレンダリングされるフォールバック（つまり、デフォルトの）コンテンツを指定すると便利な場合があります。<br>
例えば、`<submit-button>` コンポーネントにおいて:<br>

```html:sample.html
<button type="submit">
  <slot></slot>
</button>
```

ほとんどの場合には `<button>` の中に「Submit」という文字をレンダリングしたいかもしれません。「Submit」をフォールバックコンテンツにするには、`<slot>`タグの中に記述します。<br>

```html:sample.html
<button type="submit">
  <slot>Submit</slot>
</button>
```

そして、親コンポーネントからスロットのコンテンツを指定せずに `<submit-button>` を使うと：<br>

```html:index.html
<submit-button></submit-button>
```

フォールバックコンテンツの「Submit」がレンタリングされます：<br>

```html:index.html
<button type="submit">
  Submit
</button>
```

しかし、もしコンテンツを指定すると:<br>

```html:index.html
<submit-button>
  Save
</submit-button>
```

指定されたコンテンツが代わりにレンダリングされます：<br>

```html:index.html
<button type="submit">
  Save
</button>
```

## 名前付きスロット

複数のスロットがあると便利なときもあります。例えば、`<base-laout>` コンポーネントが下記のようなテンプレートだとしましょう:<br>

```html:index.html
<div class="container">
  <header>
    <!-- ここにヘッダコンテンツ -->
  </header>
  <main>
    <!-- ここにメインコンテンツ -->
  </main>
  <footer>
    <!-- ここにメインコンテンツ -->
  </footer>
</div>
```

こういった場合のために、`<slot>` 要素は `name` という特別な属性を持っていて、それぞれのスロットにユニークなIDを割り当てることによってコンテンツがどこにレンダリングされるべきかを決定することができます：<br>

```html:sample.html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

`name` のない `<slot>` 要素は、暗黙的に「default」という名前を持ちます。<br>

名前付きスロットにコンテンツを渡すには、`<template>`に対して `v-slot`ディレクティブを使い、スロット名をその引数として与える必要があります：<br>

```html:sample.html
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <template v-slot:default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

これにより、`<template>` 要素の中身は全て対応するスロットに渡されます。<br>

レンダリングされる HTML は以下のようになります：<br>

```html:sample.html
<div class="container">
  <header>
    <main>
      <p>A paragraph for the main content.</p>
      <p>And another one.</p>
    </main>
    <footer>
      <p>Here's some contact info</p>
    </footer>
  </header>
</div>
```

** v-slot は ([一つ例外](https://v3.ja.vuejs.org/guide/component-slots.html#%E3%83%86%E3%82%99%E3%83%95%E3%82%A9%E3%83%AB%E3%83%88%E3%82%B9%E3%83%AD%E3%83%83%E3%83%88%E3%81%97%E3%81%8B%E3%81%AA%E3%81%84%E5%A0%B4%E5%90%88%E3%81%AE%E7%9C%81%E7%95%A5%E8%A8%98%E6%B3%95) を除き) `<template>` にしか指定できないことに注意してください。<br>

## スコープ付きスロット

スロットコンテンツから、子コンポーネントの中だけで利用可能なデータにアクセスできると便利なことがあります。<br>
コンポーネントがアイテムの配列をレンダリングするために使われていて、レンダリングされた各アイテムをカスタムできるようにしたい、などは典型的な例です。<br>

例えば、以下のようなコンポーネントがあり、todoアイテムのリストを内部に持ってます。<br>

```js:sample.js
app.component('todo-list', {
  data() {
    return {
      items: ['Feed a cat', 'Buy milk']
    }
  },
  template: `
    <ul>
      <li v-for="(item, index) in items">
        {{ item }}
      </li>
    </ul>
  `
})
```

```html:sample.html
<todo-list>
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```

+ `html:index.html`を編集<br>

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
    <todo-list>
      <i class="fas fa-check"></i>
      <span class="green">{{ item }}</span>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

しかし、これは動作しません。というのも、`item` にアクセスすることができるのは `<todo-list>` コンポーネントだけですが、スロットのコンテンツはその親コンポーネントから提供されているためです。<br>

親コンポーネント内でスロットコンテンツとして `item` を使えるようにするためには、これを `<slot>` 要素の属性として束縛します:<br>

```html:sample.html
<ul>
  <li v-for="( item, index ) in items">
    <slot :item="item"></slot>
  </li>
</ul>
```

+ `html:index.html`を編集<br>

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
    <todo-list>
      <ul>
        <li v-for="( item, index) in items">
          <slot :item="item"></slot>
        </li>
      </ul>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

必要な数の属性を `slot` に束縛できます:<br>

```html:sample.html
<ul>
  <li v-for="( item, index ) in items">
    <slot :item="item" :index="index" :another-attribute="anotherAttribute"></slot>
  </li>
</ul>
```

+ `html:index.html`を編集<br>

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
    <todo-list>
      <ul>
        <li v-for="( item, index ) in items">
          <slot :item="item" :index="index" :another-attribute="anotherAttribute"></slot>
        </li>
      </ul>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

`<slot>` 要素に束縛された属性は、_スロットプロパティ_と呼ばれます。<br>
これで、親スコープ内で `v-slot`に値を渡して、渡されたスロットプロパティの名前を定義できます:<br>

```html:sample.html
<todo-list>
  <template v-slot:default="slotProps">
    <i class="fas fa-check"></i>
    <span class="green">{{ slotProps.item }}</span>
  </template>
</todo-list>
```

+ `html:index.html`を編集<br>

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
    <todo-list>
      <template v-slot:default="slotProps">
        <i class="fas fa-check"></i>
        <span class="green">{{ slotProps.item }}</span>
      </template>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

この例では、すべてのスロットプロパティを持つオブジェクトの名前を `slotProps`にしましたが、あなたの好きな名前を使うことができます。<br>

## デフォルトスロットしかない場合の省略記法

上の例のようにデフォルトスロットだけの場合は、コンポーネントのタグをスロットのテンプレートとして使うことができます。<br>
つまり、コンポーネントに対して `v-slot`を直接使えます:<br>

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

    li {
      color: green;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <todo-list v-slot:default="slotProps">
      <i class="fas fa-check"></i>
      <span class="green">{{ slotProps.item }}</span>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

さらに短くすることもできます。未指定のコンテンツがデフォルトスロットのものとみなされるのと同様に、引数のない `v-slot` もデフォルトコンテンツを参照しているみなされます:<br>

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

    li {
      color: green;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <todo-list v-slot="slotProps">
      <i class="fas fa-check"></i>
      <span class="green">{{ slotProps.item }}</span>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

デフォルトスロットに対する省略記法は、名前付きスロットと混在させることが＿できない_点に注意してください。スコープの曖昧さにつながるためです：<br>

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

    li {
      color: green;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <todo-list v-slot="slotProps">
      <i class="fas fa-check"></i>
      <span class="green">{{ slotProps.item }}</span>

      <template v-slot:other="otherProps">
        slotProps is NOT available here
      </template>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

複数のスロットがある場合は常に全てのスロットに対して `<template>`ベースの構文を使用してください:<br>

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

    li {
      color: green;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <todo-list>
      <template v-slot:default="slotProps">
        <i class="fas fa-check"></i>
        <span class="green">{{ slotProps.item }}</span>
      </template>

      <template v-slot:other="otherSlotProps">
        ...
      </template>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

## スロットプロパティの分割代入

内部的には、スコープ付きスロットはスロットコンテンツを単一引数の関数で囲むことで動作させています：<br>

```js:sample.js
function (slotProps) {
  // ... スロットコンテンツ ...
}
```

これは、`v-slot` の値が関数定義の引数部分で有効な任意の JavaScript式を受け付けることを意味します。<br>
そのため、特定のスロットプロパティを取得するために [ES2015の分割代入](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#%E3%82%AA%E3%83%96%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%81%AE%E5%88%86%E5%89%B2%E4%BB%A3%E5%85%A5)を使うこともできます：<br>

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

    li {
      color: green;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <todo-list v-slot="{ item }">
      <i class="fas fa-check"></i>
      <span class="green">{{ item }}</span>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

こうするとテンプレートはよりきれいになります。特に、スロットが多くのプロパティを提供している場合はそうです。<br>
また、プロパティをリネームする（例えば、`item` から `todo`）など別の可能性も開けます:<br>

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

    li {
      color: green;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <todo-list v-slot="{ item: todo }">
      <i class="fas fa-check"></i>
      <span class="green">{{ todo }}</span>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

スロットプロパティが未定義だった場合のフォールバックを定義することもできます：<br>

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

    li {
      color: green;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <todo-list v-slot="{ item = 'Placeholder' }">
      <i class="fas fa-check"></i>
      <span class="green">{{ item }}</span>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

## 名前付きスロットの省略記法

`v-on` や `v-bind` と同様に `v-slot`にも省略記法があり、引数の前のすべての部分（`v-slot:`）を特別な記号 `#` で置き換えます。<br>
例えば、`v-slot:header` は `#header` に置き換えることができます：<br>

```html:sample.html
<base-layout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <template #default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

しかし、他のディレクティブと同様に、省略記法は引数がある場合にのみ利用できます。<br>
これは、次のような構文が不正ということを意味します:<br>

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

    li {
      color: green;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <!-- 警告が出ます -->
    <todo-list #="{ item }">
      <i class="fas fa-check"></i>
      <span class="green">{{ item }}</span>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```

代わりに、省略記法を使いたい場合には、常にスロット名を指定する必要があります:<br>

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

    li {
      color: green;
    }
  </style>
</head>

<body>
  <div id="app" class="demo">
    <todo-list #default="{ item }">
      <i class="fas fa-check"></i>
      <span class="green">{{ item }}</span>
    </todo-list>
  </div>
</body>

<script>
  const app = Vue.createApp({})
  app.component('todo-list', {
    data() {
      return {
        items: ['Feed a cat', 'Buy milk']
      }
    },
    template: `
      <ul>
        <li v-for="(item, index) in items">
          {{ item }}
        </li>
      </ul>
    `
  })

  app.mount('#app');
</script>

</html>
```
