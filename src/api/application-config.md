# アプリケーション構成

すべての Vue アプリケーションは、そのアプリケーションの構成設定を含む `config` オブジェクトを公開します:

```js
const app = createApp({})

console.log(app.config)
```

アプリケーションをマウントする前に、以下に列挙したプロパティを変更することができます。

## errorHandler

- **型:** `Function`

- **デフォルト:** `undefined`

- **使用方法:**

```js
app.config.errorHandler = (err, vm, info) => {
  // エラーハンドリング
  // `info` は、Vue 固有のエラー情報です。例: ライフサイクルフック
  // エラーが見つかった際の処理
}
```

コンポーネントの Render 関数とウォッチャに捕捉されなかったエラーのハンドラを割り当てます。ハンドラには、アプリケーションのインスタンスとエラーが渡されて呼び出されます。

> エラートラッキングサービスの [Sentry](https://sentry.io/for/vue/) ならびに [Bugsnag](https://docs.bugsnag.com/platforms/browsers/vue/) は公式に連携のためのオプションを用意しています。

## warnHandler

- **型:** `Function`

- **デフォルト:** `undefined`

- **使用方法:**

```js
app.config.warnHandler = function(msg, vm, trace) {
  // `trace` は、コンポーネント階層のトレースです。
}
```

Vue の warning のためのカスタムハンドラを割り当てます。開発環境でのみ動き、プロダクションでは動作しないことに注意してください。

## globalProperties

- **型:** `[key: string]: any`

- **デフォルト:** `undefined`

- **使用方法:**

```js
app.config.globalProperties.foo = 'bar'

app.component('child-component', {
  mounted() {
    console.log(this.foo) // 'bar'
  }
})
```

アプリケーション内のあらゆるコンポーネントのインスタンスからアクセスできるグローバルなプロパティを追加します。名称が競合した場合、コンポーネントのプロパティが優先されます。

これは、 Vue 2.x における `Vue.prototype` 拡張を置き換えることができます:

```js
// Before
Vue.prototype.$http = () => {}

// After
const app = createApp({})
app.config.globalProperties.$http = () => {}
```

## isCustomElement

- **型:** `(tag: string) => boolean`

- **デフォルト:** `undefined`

- **使用方法:**

```js
// 'ion-' から始まる要素は、Custom Element として認識されます。
app.config.isCustomElement = tag => tag.startsWith('ion-')
```

Vue の外部にて定義された(Web Components API を利用した場合などの)Custom Element を認識する方法を指定します。条件にコンポーネントがマッチした場合は、ローカルならびにグローバルでの登録を必要とせず、`Unknown custom element` の警告をスローしません。

> この関数では、全てのネイティブの HTML ならびに SVG のタグをマッチさせる必要はありません。Vue のパーサが自動的にこのチェックを行います。

::: tip Important
この設定オプションは、ランタイムコンパイラを使うときにのみ尊重されます。ランタイム限定ビルドを使う場合、 `isCustomElement` は代わりにビルドの設定で `@vue/compiler-dom` に渡す必要があります。例えば、 [vue-loader の `compilerOptions` オプション](https://vue-loader.vuejs.org/options.html#compileroptions) を経由して渡します。
:::

## optionMergeStrategies

- **型:** `{ [key: string]: Function }`

- **デフォルト:** `{}`

- **使用方法:**

```js
const app = createApp({
  mounted() {
    console.log(this.$options.hello)
  }
})

app.config.optionMergeStrategies.hello = (parent, child, vm) => {
  return `Hello, ${child}`
}

app.mixin({
  hello: 'Vue'
})

// 'Hello, Vue'
```

カスタムオプションのマージ戦略を定義します。

マージ戦略は、親インスタンスと子インスタンスで定義されたオプションの値をそれぞれ第 1 引数と第 2 引数として受け取ります。アプリケーションコンテキストのインスタンスは、第 3 引数として渡されます。

- **参照:** [Custom Option Merging Strategies](../guide/mixins.html#custom-option-merge-strategies)

## performance

- **型:** `boolean`

- **デフォルト:** `false`

- **使用方法**:

コンポーネントの初期化で `true` に設定することで、ブラウザの devtool 内の performance/timeline パネルにて、レンダリングおよびパッチにおけるパフォーマンスの追跡が可能となります。development モード並びに[performance.mark](https://developer.mozilla.org/en-US/docs/Web/API/Performance/mark) API が有効なブラウザでのみ機能します。
