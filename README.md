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
