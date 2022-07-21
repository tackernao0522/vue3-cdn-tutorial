# フォーム入力バインディング

## 基本的な使い方

_form_ の input要素や textarea要素、select要素に双方向データバインディングを付与するためには、`v-model` を使用することができます。<br>
`v-model` は、要素を更新する適切な方法を入力の種類に基づき自動的に選択します。<br>
少し魔法のようですが、本来 `v-model` は糖衣公文(syntax sugar)であり、ユーザーの入力イベントに応じてデータを更新し、さらにエッジケースに対する特別な配慮をしてくれます。<br>

#### _TIP_

`v-model`はフォーム要素の`value`属性や`checked`属性、`selected`属性の初期値を無視します。<br>
`v-model`は常に、現在アクティブなインスタンスの`data`を信頼できる情報源として扱います。<br>
初期値の宣言はJavaScript側、コンポーネントの`data`オプション内で行ってください。<br>

内部的には、`v-model`は異なるinput要素に対し異なるプロパティを使用し、異なるイベントを送出します。<br>

+ textおよびtextarea要素には、`value`プロパティと`input`プロパティとイベントを用います<br>

+ チェックボックスおよびラジオボタンには、`checked`プロパティと`change`イベントを用います<br>

+ selectフィールドには、`value`プロパティと`change`イベントを用います<br>

#### _Note_

[IME](https://ja.wikipedia.org/wiki/%E3%82%A4%E3%83%B3%E3%83%97%E3%83%83%E3%83%88%E3%83%A1%E3%82%BD%E3%83%83%E3%83%89) を必要とする言語（中国語、日本語、韓国語など）においては、IMEによる入力中に`v-model`が更新を行わないことに気づくでしょう。<br>
このような更新にも対応したい場合、`v-model`をつかう代わりに`input`イベントリスナーと`value`のバインディングを使ってください。<br>

例: <br>

```html:sample.html
<input v-model="message" placeholder="edit me" />
<p>Message is: {{ message }}</p>
```

全体: <br>

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
    <input v-model="message" placeholder="edit me" />
    <p>Messsage is: {{ message }}</p>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        message: ''
      }
    }
  }).mount('#app')
</script>

</html>
```

複数行テキスト<br>

例: <br>

```html:sample.html
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<br />
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```

全体: <br>

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
    <span>Multiline message is:</span>
    <p style="white-space: pre-line;">{{ message }}</p>
    <br>
    <textarea v-model="message" placeholder="add multiple lines"></textarea>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        message: ''
      }
    }
  }).mount('#app')
</script>

</html>
```

textareaへの挿入は機能しません。代わりに `v-model`を用いてください。<br>

```html:sample.html
<!-- bad -->
<textarea>{{ text }}</textarea>

<!-- good -->
<textarea v-model="text"></textarea>
```

チェックボックス<br>

単一のチェックボックスと boolean値<br>

例: <br>

```html:sample.html
<input type="checkbox" id="checkbox" v-model="checked" />
<label for="checkbox">{{ checkbox }}</label>
```

全体:

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
    <input type="checkbox" id="checkbox" v-model="checked" />
    <label for="checkbox">{{ checked }}</label>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        checked: false
      }
    }
  }).mount('#app')
</script>

</html>
```

同じ配列にバインドされた複数のチェックボックス:

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
    <input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
    <label for="jack">Jack</label>
    <input type="checkbox" id="john" value="John" v-model="checkedNames" />
    <label for="john">John</label>
    <input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
    <label for="mike">Mike</label>
    <br />
    <span>Checked names: {{ checkedNames }}</span>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        checkedNames: []
      }
    }
  }).mount('#app')
</script>

</html>
```

ラジオ: <br>

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
    <input type="radio" id="one" value="One" v-model="picked" />
    <label for="one">One</label>
    <br>
    <input type="radio" id="two" value="Two" v-model="picked" />
    <label for="two">Two</label>
    <br>
    <span>Picked: {{ picked }}</span>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        picked: ''
      }
    }
  }).mount('#app')
</script>

</html>
```

セレクト <br>

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
    <select v-model="selected">
      <option disabled value="">Please select one</option>
      <option>A</option>
      <option>B</option>
      <option>C</option>
    </select>
    <span>Selected: {{ selected }}</span>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        selected: ''
      }
    }
  }).mount('#app')
</script>

</html>
```

#### Note

`v-model`の式の初期値がいずれのオプションとも一致しない場合、`<select>`要素は未選択の状態でレンダリングされます。<br>
これにより iOS では最初のアイテムが選択できなくなります。<br>
なぜなら iOSはこのような場合に `change` イベントを発火させないためです。<br>
したがって、上記の例で示したように、 `value`を持たない `disabled` なオプションを追加しておくことをおすすめします。<br>

複数個のセレクト (配列にバインド) : <br>

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
    <select v-model="selected" multiple>
      <option disabled value="">Please select one</option>
      <option>A</option>
      <option>B</option>
      <option>C</option>
    </select>
    <span>Selected: {{ selected }}</span>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        selected: ''
      }
    }
  }).mount('#app')
</script>

</html>
```

動的なオプションを `v-for` によりレンダリング: <br>

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
    <select v-model="selected">
      <option v-for="option in options" v-bind:value="option.value">{{ option.text }}</option>
    </select>
    <span>Selected: {{ selected }}</span>
  </div>
</body>

<script>
  Vue.createApp({
    data() {
      return {
        selected: 'A',
        options: [
          { text: 'One', value: 'A' },
          { text: 'Two', value: 'B' },
          { text: 'Three', value: 'C' },
        ]
      }
    }
  }).mount('#app')
</script>

</html>
```

## 値のバインディング

ラジオやチェックボックス、セレクトの option において、`v-model` でバインディングされる値は通常は静的な文字列（チェックボックスの場合は boolean）です： <br>

```html:sample.html
<!-- チェックされているとき `picked` は文字列 "a" になります -->
<input typw="radio" v-model="picked" value="a" />

<!-- `toggle` は true または　false のどちらかです -->
<input type="checkbox" v-model="toggle" />

<!-- 最初のオプションが選択されているとき `selected` は文字列 "abc"です -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

しかし、現在アクティブなインスタンスの動的なプロパティに値をバインドしたいときがあるかもしれません。<br>
それを実現するには `v-bind` を使用できます。<br>
さらに、`v-bind`を使用することで入力値に文字列以外の値もバインドできるようになります。<br>

## チェックボックス

```html:sample.html
<input type="checkbox" v-model="toggle" true-value="yes" false-value="no" />
```

```js:sample.js
// チェックされているとき:
vm.toggle === 'yes'
// チェックされていないとき:
vm.toggle === 'no'
```

#### Tip

`true-value` と `false-value` 属性は input の `value` 属性には影響を及ぼしません。<br>
なぜならブラウザはチェックされていないチェックボックスをフォーム送信内容には含めないためです。<br>
2つの値（例: "yes" または "no"）のうち1つが必ず送信されることを保証するには、代わりにラジオボタンを使用してください。<br>


## ラジオ

```html:sample.html
<input type="radio" v-model="pick" v-bind:value="a" />
```

```js:sample.js
// チェックされるとき:
vm.pick === vm.a
```

## セレクトオプション

```html:sample.html
<select v-model="selected">
  <!-- インラインオブジェクトリテラル -->
  <option :value="{ number 123 }">123</option>
</select>
```

```js:sample.js
// 選択されているとき:
typeof vm.selected // => 'object'
vm.selected.number // => 123
```

## 修飾子

### `.lazy`

デフォルトでは `v-model`は各 `input` イベント後に入力値とデータを同期します(上述の IME入力の例外はあります)。<br>
`lazy`を加えることで、`change` イベント後に同期するよう変更できます。<br>

```html:sample.html
<!-- "input" ではなく "change" イベント後に同期されます -->
<input v-model.lazy="msg" />
```

### `.number`

ユーザー入力を自動的に number へ型キャストさせたい場合は、`v-model` で管理している input に `number` 修飾子を加えることができます。<br>

```html:sample.html
<input v-model.number="age" type="text" />
```

これは入力タイプが `text` の場合によく使われます。<br>
入力タイプが `number` の場合、Vueは生の文字列を自動的に数値へ変換できるため、`v-model` に `.number` を追加する必要はありません。<br>
値が `parseFloat()` で解析できない場合は、元の値が返されます。<br>

### `.trim`

ユーザー入力から空白を自動で取り除きたい場合は、`v-model` で管理している input に `trim` 修飾子を加えることができます。<br>

```html:sample.html
<input v-model.trim="msg" />
```

## コンポーネントの `v-model`

HTML 組み込みの input タイプが、常にあなたのニーズに適っているとは限りません。<br>
幸運にも、Vueコンポーネントによって、動作を隅々までカスタマイズ可能な再利用性のある入力フォームを自作することができます。<br>
それらのフォームに `v-model` を使うことも可能です！<br>
詳しくは、コンポーネントガイドの [カスタム input](https://v3.ja.vuejs.org/guide/component-basics.html#%E3%82%B3%E3%83%B3%E3%83%9B%E3%82%9A%E3%83%BC%E3%83%8D%E3%83%B3%E3%83%88%E3%81%A6%E3%82%99-v-model-%E3%82%92%E4%BD%BF%E3%81%86) を参照してください。<br>
