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
