## 算出プロパティとウォッチャ

#### 算出プロパティ

テンプレート内の式は非常に便利ですが、シンプルな操作のためのものです。<br>
テンプレートにたくさんロジックを入れすぎると、テンプレートが肥大化してメンテナンスしづらくなる可能性があります。<br>
例えば、配列が入っているオブジェクトがあります:

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
  <div id="computed-basics">
    <p>Has published books:</p>
    <span>{{ author.books.length > 0 ? 'Yes' : 'No' }}</span>
  </div>
</body>

<script>
  const App = {
    data() {
      return {
        author: {
          name: 'John Doe',
          books: [
            'Vue 2 - Advanced Guide',
            'Vue 3 - Basic Guide',
            'Vue 4 - The Mystery'
          ]
        }
      }
    }
  }

  Vue.createApp(App).mount('#computed-basics')
</script>

</html>
```

これだと、テンプレートはもうシンプルでも宣言的でもありません。<br>
`author.books`を元に計算しているとわかるまで少し時間がかかります。<br>
この計算を何回もテンプレートに含めたい場合、問題はさらに悪化します。<br>

そのため、リアクティブなデータを含む複雑なロジックには`算出プロパティ`を使うべきです。<br>

## 基本的な例


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
  <div id="computed-basics">
    <p>Has published books:</p>
    <span>{{ publishedBooksMessage }}</span>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        author: {
          name: 'John Doe',
          books: [
            'Vue 2 - Advanced Guide',
            'Vue 3 - Basic Guide',
            'Vue 4 - The Mystery',
          ]
        }
      }
    },
    computed: {
      // 算出 getter 関数
      publishedBooksMessage() {
        // `this` は vm インスタンスを指す
        return this.author.books.length > 0 ? 'Yes' : 'No'
      }
    }
  }).mount('#computed-basics')
</script>

</html>
```

ここでは `publishedBooksMessage`をいう算出プロパティを宣言します。<br>

このアプリケーションの `data`にある `books` 配列の値を変更してみると、それに応じて `publishedBooksMessage`がどのように変わるか分かるでしょう。<br>

通常のプロパティと同じように、テンプレート内で算出プロパティにデータバインドできます。<br>
Vueは `vm.publishedBooksMessage`が`vm.author.books`に依存していると分かっているので、<br>
`vm.author.books`が変更されると`vm.publishedBooksMessage`に依存するバインディングを更新します。<br>
そして、一番良いのは、この依存関係を宣言的に作成していることです。<br>
算出 `getter`関数には副作用がないので、テストや理解するのが容易になります。<br>

## 算出プロパティ vs メソッド

式の中でメソッドを呼び出せば同じことができると気づいたかもしれません:

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
  <div id="computed-basics">
    <p>Has published books:</p>
    <span>{{ calculateBooksMessage() }}</span>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        author: {
          name: 'John Doe',
          books: [
            'Vue 2 - Advanced Guide',
            'Vue 3 - Basic Guide',
            'Vue 4 - The Mystery',
          ]
        }
      }
    },
    methods: {
      calculateBooksMessage() {
        return this.author.books.length > 0 ? 'Yes' : 'No'
      }
    }
  }).mount('#computed-basics')
</script>

</html>
```

同じ関数を算出プロパティではなくメソッドとして定義することもできます。<br>
結果だけ見れば、この2つのアプローチはまったく同じになりますが、_算出プロパティはリアクティブな依存関係に基づいてキャッシュされる_という違いがあります。<br>
算出プロパティはリアクティブな依存関係の一部が変更された場合のみ再評価されるのです。<br>
つまり、`author.books`が変更されなければ、算出プロパティの`publishedBooksMessage`に複数回アクセスしても関数は再実行されず、前回計算した結果がすぐに返されます。<br>
<br>

以下の算出プロパティは `Date.now()`がリアクティブな依存関係ではないので、一度も更新されないことになります。<br>

```js:sample.js
computed: {
  now() {
    return Date.now()
  }
}
```

それに対してメソッドは、再レンダリングが起こるたびに＿常に_関数を実行します。<br>

どうしてキャッシングが必要なのでしょうか？ 巨大な配列をループして、たくさんの計算を必要とする算出プロパティ `list`があるとします。<br>
また、`list`に依存している別の算出プロパティがあるかもしれません。<br>
キャッシングがなければ、`list`の`getter`関数を必要以上に何度も実行することになってしまいます。<br>
キャッシングしたくない場合は、代わりに `method`を使用してください。<br>

## 算出 Setter 関数

算出プロパティはデフォルトでは`getter`関数のみですが、必要に応じて`setter`関数を設定することもできます:

```js:sample.js
// ...
computed: {
  fullName: {
    // getter 関数
    get() {
      return this.firstName + ' ' + this.lastName
    },
    // setter 関数
    set(newValue) {
      const names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

この状態で `vm.fullName = 'John Doe' を実行すると `setter`関数が呼び出され、その結果 `vm.firstName` と `vm.lastName` が更新されます。<br>

## ウォッチャ

ほとんどの場合、算出プロパティの方が適切ですが、カスタムウォッチャが必要な場合もあります。<br>
そのためVueは、データの変更に反応するためのより汎用的な方法を、`watch`オプションによって提供しています。<br>
これはデータを変更するのに応じて非同期処理や重い処理を実行したい場合に最も便利です。<br>

例:

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
  <div id="watch-example">
    <p>
      Ask a yes/no question:
      <input v-model="question" />
    </p>
    <p>{{ answer }}</p>
  </div>
</body>

<!-- ajax ライブラリや汎用ユーティリティメソッドのコレクションなどの -->
<!-- 豊富なエコシステムが既に存在するため、それらを再発明しないことで -->
<!-- Vue のコアは小規模なまま保たれています。これは、使い慣れたものを -->
<!-- 自由に使うことができる、ということでもあります。 -->

<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script>
  const watchExampleVM = Vue.createApp({
    data() {
      return {
        question: '',
        answer: 'Questions usually contain a question mark. ;-)'
      }
    },
    watch: {
      // question が変わるたびに、この関数が実行される
      question(newQuestion, oldQuestion) {
        if (newQuestion.indexOf('?') > -1) {
          this.getAnswer()
        }
      }
    },
    methods: {
      getAnswer() {
        this.answer = 'Thinking...'
        axios
          .get('https://yesno.wtf/api')
          .then((response) => {
            this.answer = response.data.answer
          })
          .catch((error) => {
            this.answer = 'Error! Could not reach the API. ' + error
          })
      }
    }
  }).mount('#watch-example')
</script>

</html>
```

このケースでは `watch` オプションを使用することで、非同期処理(APIへのアクセス)の実行と、その処理を実行する条件を設定できています。<br>
このようなことは算出プロパティではできません。<br>

`watch` オプションに加え、命令的な `vm.$watch API`(https://v3.ja.vuejs.org/api/instance-methods.html#watch) を使うこともできます。<br>

## 算出プロパティ VS 監視プロパティ

Vue は現在のアクティブなインスタンスでのデータの変更を観察して反応するための、より汎用的な方法: _監視プロパティ(watched property)を提供します。<br>
他のデータに基づいて変更しなければならないデータがある場合(AngularJSをやっていた人だとなおさら)、`watch`のコールバックよりも算出プロパティを使うのがベターです。<br>
次の例を考えてみましょう:

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
  <div id="demo">
    {{ fullName }}
  </div>
</body>

<script>
  const vm = Vue.createApp({
    data() {
      return {
        firstName: 'Foo',
        lastName: 'Bar',
        fullName: 'Foo Bar'
      }
    },
    watch: {
      firstName(val) {
        this.fullName = val + ' ' + this.lastName
      },
      lastName(val) {
        this.fullName = this.firstName + ' ' + val
      }
    }
  }).mount('#demo')
</script>

</html>
```

上記のコードは命令的だし冗長ですね。算出プロパティで書き換えたものと比べてみましょう:

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
  <div id="demo">
    {{ fullName }}
  </div>
</body>

<script>
  const vm = Vue.createApp({
    data() {
      return {
        firstName: 'Foo',
        lastName: 'Bar',
      }
    },
    computed: {
      fullName() {
        return this.firstName + ' ' + this.lastName
      }
    }
  }).mount('#demo')
</script>

</html>
```

ずっといいですよね？<br>

# クラスをスタイルのバインディング

## HTML クラスのバインディング

### オブジェクト構文

`:class` `( `v-bind:class` の略) にオブジェクトを渡すことでクラスを動的に切り替えることができます:

```html:sample.html
<div :class="{ active: isActive }"></div>
```

上記の構文は、`active` クラスの有無がデータプロパティ `isActive` の真偽性(https://developer.mozilla.org/ja/docs/Glossary/Truthy)によって決まることを意味しています。<br>

オブジェクトにさらにフィールドを持たせることで複数のクラスを切り替えることができます。<br>
加えて、`:class` ディレクティブはプレーンな `class`属性と共存できます。<br>
つまり、次のようなテンプレートと:

```html:sample.html
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div>
```

次のようなデータがあったとすると:

```js:sample.js
data() {
  return {
    isActive: true,
    hasError: false
  }
}
```

このようにレンダリングされます:

```html:sample.html
<div class="static active"></div>
```

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
    <div class="static" :class="{ active: isActive, 'text-danger': hasError }"></div>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        isActive: true,
        hasError: false
      }
    }
  }).mount('#app')
</script>

</html>
```

※ 検証ツールで確認してみる<br>

`isActive` もしくは `hasError` が変化するとき、クラスリストはそれに応じて更新されます。<br>
例えば、`hasError`が`true`になった場合、クラスリストは `"static active text-danger"` になります。<br>

束縛されるオブジェクトはインラインでなくてもかまいません:

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
    <div :class="classObject"></div>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        classObject: {
          active: true,
          'text-danger': false
        }
      }
    }
  }).mount('#app')
</script>

</html>
```

これは同じ結果をレンダリングします。オブジェクトを返す 算出プロパティに束縛することもできます。<br>
これは一般的で強力なパターンです:

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
    <div :class="classObject"></div>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        isActive: true,
        error: null
      }
    },
    computed: {
      classObject() {
        return {
          active: this.isActive && !this.error,
          'text-danger': this.error && this.error.type === 'fatal'
        }
      }
    }
  }).mount('#app')
</script>

</html>
```

## 配列構文

`:class` に配列を渡してクラスのリストを適用することができます：

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
    <div :class="[activeClass, errorClass]"></div>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        activeClass: 'active',
        errorClass: 'text-danger'
      }
    }
  }).mount('#app')
</script>

</html>
```

これは次のようにレンダリングされます:

```html:sample.html
<div class="active text-danger"></div>
```

リスト内のクラスを条件に応じて切り替えたい場合は、三項演算子式を使って実現することができます:

```html:sample.html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

この場合 `errorClass` は常に適用されますが、`activeClass` クラスは　`isActive` が真と評価されるときだけ適用されます。<br>

しかしながら、これでは複数の条件付きクラスがあると少し冗長になってしまいます。<br>
そのため、配列構文の内部ではオブジェクト構文を使うこともできます：

```html:sample.html
<div :class="[{ active: isActive }, errorClass]"></div>
```

## コンポーネントにおいて

単一のルート要素を持つカスタムコンポーネントで `class` 属性を使用すると、それらのクラスがこの要素に追加されます。<br>
この要素の既存のクラスは上書きされません。<br>

例えば、このコンポーネントを宣言して:

```js:sample.js
const app = Vue.createApp({})

app.component('my-component'), {
  template: `<p class="foo bar">Hi!</p>`
}
```

使用するときに幾つかのクラスを追加したとします：

```html:sample.html
<div id="app">
  <my-component class="baz boo"></my-component>
</div>
```

以下のHTMLがレンダリングされます:

```html:sample.html
<p class="foo bar baz boo">Hi</p>
```

クラスバインディングに対しても同様です:

```html:sample.html
<my-component :class="{ active: isActive }"></my-component>
```

`isActive` が評価されるときは、以下のHTMLがレンダリングされます:

```html:sample.html
<p class="foo bar active">Hi</p>
```

コンポーネントに複数のルート要素がある場合、どのコンポーネントがこのクラスを受け取るかを定義する必要があります。<br>
これは `$attrs`コンポーネントプロパティを使って行うことができます:

```html:sample.html
<div id="app">
    <my-component class="baz"></my-component>
</div>
```

```js:sample.js
const app = Vue.createApp({})

app.component('my-component', {
  template: `
    <p :class="$attrs.class">Hi!</p>
    <span>This is a child component</span>
  `
})
```

コンポーネント属性の継承については、プロパティでない属性(https://v3.ja.vuejs.org/guide/component-attrs.html)のセクションを参照してください。<br>

## インラインスタイルのバインディング

### オブジェクト構文

`:style` 向けのオブジェクト構文は非常に簡単です。JavaScript オブジェクトということを除けば、ほとんどCSSのように見えます。<br>
CSSのプロパティ名には、キャメルケース(camelCase)またはケバブケース(kebab-case: クォートともに使うことになります)のどちらでも使用することができます:<br>

```html:samle.html
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
    <div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
  </div>
</body>
<script>
  Vue.createApp({
    data() {
      return {
        activeColor: 'red',
        fontSize: 30
      }
    }
  }).mount('#app')
</script>

</html>
```

テンプレートをクリーンにするために、直接 style オブジェクトに束縛するのは、よいアイデアです:

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
    <div :style="styleObject"></div>
  </div>
</body>
<script>
  Vue.createApp({
    data() {
      return {
        styleObject: {
          color: 'red',
          fontSize: '13px'
        }
      }
    }
  }).mount('#app')
</script>

</html>
```

ここでもまた、オブジェクト構文はしばしばオブジェクトを返す算出プロパティと併せて使用されます。<br>

## 配列構文

`:style` 向けの配列構文は、同じ要素に複数のスタイルオブジェクトを適用することができます:

```html:sample.html
<div :style="[baseStyles, overridingStyles]"></div>
```

## 自動プレフィックス

`:style` でベンダープレフィックス(https://developer.mozilla.org/ja/docs/Glossary/Vendor_Prefix) が必要なCSSプロパティを使用するとき、Vueは自動的に適切なプレフィックスを追加します。<br>
Vueは現在のブラウザでどのスタイルプロパティがサポートされているかを実行時に確認することでこれを行います。<br>
ブラウザが特定のプロパティをサポートしていない場合は、様々なプレフィックスのバリデーションがテストされて、サポートされているものを見つけようとします。<br>

## 複数の値

style プロパティに複数の（接頭辞付き）値の配列を設定できます。例えば次のようになります:

```html:sample.html
<div :style="{ displey: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

これは、配列内でブラウザがサポートしている最後の値だけをレンダリングします。<br>
この例では、flexboxの接頭されていないバーションをサポートしているブラウザでは `display: flex`をレンダリングします。<br>
