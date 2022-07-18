# イベントハンドリング

## イベントの購読

`v-on` ディレクティブを使いことで、DOM イベントの購読、イベント発火時の JavaScript の実行が可能になります。<br>
これは通常`@`に省略することができます。<br>
`v-on:click="methodName"` もしくは `@click="methodName"` と書いて使用します。<br>

例:

```html:index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
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
      <button @click="counter += 1">Add 1</button>
      <p>The button above has been clicked {{ counter }} times.</p>
    </div>
  </body>

  <script>
    Vue.createApp({
      data() {
        return {
          counter: 0,
        }
      },
    }).mount('#app')
  </script>
</html>
```

## メソッドイベントハンドラ

多くのイベントハンドラのロジックより複雑になっていくので、`v-on`属性の値に JavaScript 式を記述し続けるのは現実的ではありません。<br>
そのため、`v-on`は呼び出したいメソッドの名前も受け付けます。<br>

```html:index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
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
      <!-- `greet` は、あらかじめ定義したメソッドの名前 -->
      <button @click="greet">Greed</button>
    </div>
  </body>

  <script>
    Vue.createApp({
      data() {
        return {
          name: 'Vue.js',
        }
      },
      methods: {
        greet(event) {
          // メソッド内の `this` は、Vue インスタンスを参照します
          alert('Hello ' + this.name + '!')
          // `event` は、 ネイティブ DOM イベントです
          if (event) {
            alert(event.target.tagName)
          }
        },
      },
    }).mount('#app')
  </script>
</html>
```

## インラインメソッドハンドラ

メソッド名を直接指定する代わりに、インライン JavaScript 式でメソッドを指定することもできます:

```html:index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
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
      <button @click="say('hi')">Say hi</button>
      <button @click="say('what')">Say what</button>
    </div>
  </body>

  <script>
    Vue.createApp({
      methods: {
        say(message) {
          alert(message)
        },
      },
    }).mount('#app')
  </script>
</html>
```

時には、インラインステートメントハンドラでオリジナルの DOM イベントを参照したいこともあるでしょう。<br>
特別な`$event`編集を使うことでメソッドに DOM イベントを渡すことができます:<br>

```html:index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
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
      <button @click="warn('Form cannot be submitted yet.', $event)">
        Submit
      </button>
    </div>
  </body>

  <script>
    Vue.createApp({
      methods: {
        warn(message, event) {
          // ネイティブイベントを参照しています
          if (event) {
            event.preventDefault()
          }
          alert(message)
        }
      }
    }).mount('#app')
  </script>
</html>
```

## 複数イベントハンドラ

イベントハンドラ内では間まで区切ることで、複数のメソッドを設定することができます:

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
    <button @click="one($event), two($event)">
      Submit
    </button>
  </div>
</body>

<script>
  Vue.createApp({
    methods: {
      one(event) {
        // one($event)のハンドラーロジック
        console.log(event)
      },
      two(event) {
        // two($event)のハンドラーロジック
        console.log(event)
      }
    }
  }).mount('#app')
</script>

</html>
```

## イベント修飾子

イベントハンドラ内での `event.preventDefault()` または `event.stopPropagation()`の呼び出しは、様々な場面で共通に必要になります。<br>
これらはメソッド内部で簡単に呼び出すことができますが、DOMイベントの込み入った処理を行うよりも、純粋なデータロジックだけになっている方がより良いでしょう。<br>

この問題に対応するために、Vueは `v-on` のために `_イベント修飾子(event modifiers)を提供しています。<br>
修飾子は、ドット(.)で表示されるディテクティブを接尾辞を思い返してください。<br>

+ `.stop`<br>
+ `.prevent`<br>
+ `.capture`<br>
+ `.self`<br>
+ `.once`<br>
+ `.passive`<br>

```html:sample.html
<!-- クリックイベントの伝播が止まります -->
<a @click.stop="doThis"></a>

<!-- submit イベントによってページがリロードされません -->
<form @submit.prevent="onSubmit"></form>

<!-- 修飾子はつなげることができます -->
<a @click.stop.prevent="doThat"></a>

<!-- 値を指定せず、修飾子だけ利用することもできます -->
<form @submit.prevent></form>

<!-- イベントリスナを追加するときにキャプチャモードで使います -->
<!-- 言い換えれば、内部要素を対象とするイベントは、その要素によって処理される前にここで処理されます -->
<div @click.capture="doThis">...</div>

<!-- event.target が要素自信のときだけ、ハンドラが呼び出されます -->
<div @click.self="doThat">...</div>
```

`_TIP_`<br>

修飾子を使用するとき、関連するコードが同じ順序で生成されるため注意してください。<br>
したがって、`@click.prevent.self`を使用すると _要素自身とその子要素に対するクリックのデフォルトアクション_ を防ぐことはできますが、`@click.self.prevent` は要素自身に対するクリックのデフォルトアクションを防ぐだけです。<br>

```html:sample.html
<!-- 最大1回、クリックイベントはトリガされます -->
<a @click.once="doThis"></a>
```

他の修飾子をは違って、ネイティブDOMイベント専用ではありますが、`.once`修飾子を [コンポーネントイベント](https://v3.ja.vuejs.org/guide/component-custom-events.html "コンポーネントイベント") でも使用することができます。<br>
まだコンポーネントについて読んでいないなら、今は気にする必要はありません。<br>

Vueは `addEventListener` の `passive` [オプション](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters "passive オプション") に対応する `.passive` 修飾子も提供しています。<br>

```html:sample.html
<!-- `onScroll` が `event.preventDefault()` を含んでいたとしても -->
<!-- スクロールイベントのデフォルトの挙動（つまりスクロール）は -->
<!-- イベントの完了を待つことなく直ちに発生するようになります -->
<div @scroll.passive="onScroll">...</div>
```

`.passive` 修飾子は特にモバイルでのパフォーマンスを改善するのに有用です。<br>

#### `_TIP_`

`.passive` と `.prevent` を一緒に使わないでください。<br>
`.prevent` は無視され、ブラウザにはおそらく警告が表示されます。<br>
`.passive` はイベントのデフォルトの挙動を妨げないことをブラウザに伝達することを思い出してください。<br>

## キー修飾子

キーボードイベントを購読するにあたって、特定のキーのチェックが必要になることがあります。<br>
Vueでは、`v-on` または `@`に対してキー修飾子を追加することができます:

```html:index.html
<!-- `key` が `Enter` のときだけ、`vm.submit()` が呼ばれます -->
<input @keyup.enter="submit" />
```

[keyboardEvent.key](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values) で公開されている任意のキー名は、ケバブケースに変換することで修飾子として直接使用できます。<br>

```html:index.html
<input @keyup.page-down="onPageDown" />
```

上の例では、ハンドラは `$event.key` が `'PageDown'` に等しい場合だけ呼ばれます。<br>

## キーコード

+ `.enter`<br>
+ `.tab`<br>
+ `.delete` ("Delete" と "Backspace" キー両方をキャプチャします)<br>
+ `.esc`<br>
+ `.space`<br>
+ `.up`<br>
+ `.down`<br>
+ `.left`<br>
+ `.right`<br>

## システム修飾子キー

次の修飾子を使用すると、対応するキーが押されたときにのみマウスもしくはキーボードのイベントリスナをトリガできます: <br>

+ `.ctrl`<br>
+ `.alt`<br>
+ `.shift`<br>
+ `.meta`<br>

#### _Note_

注意: Machintosh キーボードの場合、meta はコマンドキー (⌘) です。<br>
Windowsのキーボードでは、meta はウィンドウキー (⊞) です。<br>
Sun Microsystemsのキーボードでは、メタは実践のダイヤモンド (◆)とマークされています。<br>
特定のキーボードでは、特にMITやLispマシンのキーボード、メタのような後継機には"META"と表示されます。<br>
Symbolics のキーボードでは、"META" または "Meta"というラベルが付いています。<br>

例: <br>

```html:index.html
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

#### _TIP_

修飾子キーは通常のキーとは異なり、 `keyup` イベントで使用する場合、イベント発生時に押されていなければならないことに注意してください。<br>
言い換えると、`keyup.ctrl` は `ctrl`を押しながら何かのキーを離したときにのみ、トリガされます。<br>
`ctrl` キーだけを離しても、トリガされません。<br>

## `.exact` 修飾子

`.exact` 修飾子はイベントを引き起こすために必要なシステム修飾子の正確な組み合わせを制御します。<br>

```html:sample.html
<!-- これは Ctrl に加えて Alt や Shift キーが押されていても発行されます -->
<button @click.ctrl="onClick">A</button>

<!-- これは Ctrl キーが押され、他のキーが押されてないときだけ発行されます -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- これは システム修飾子が押されてないときだけ発行されます -->
<button @click.exact="onClick">A</button>
```

## マウスボタンの修飾子

+ `.left`<br>
+ `.right`<br>
+ `.middle`<br>

これらの修飾子は、イベントのトリガのハンドリングを、特定のマウスのボタンのみに制御します。<br>

## なぜ _HTML_ にリス名を記述するのですか

これまで説明してきたようなイベント監視のアプローチは、"関心の分離"という古き良きルールを破っているのではないか、と心配されるかもしれません。安心してください。<br>
全てのVueハンドラ関数と式は、現在のViewを扱う ViewModel に厳密に閉じています。<br>
それによってメンテナンスが難しくなることはありません。<br>
実際、`v-on` または `@` を利用することでいくつかの利点があります。<br>

1. HTML テンプレートを眺めることで、JSコード内のハンドラ関数を探すことを容易にします<br>

2. JS内のイベントりすなを手作業でアタッチする必要がないので、ViewModelを DOM依存のない純粋なロジックにできます。<br>
これはテスタビリティを向上させます。<br>

3. ViewModelが消去されるときに、すべてのイベントリスナは自動で削除されます。<br>
手動でそれらの消去を行うことを気にする必要はありません。<br>
