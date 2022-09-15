---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: /coedo_5th_01.jpg
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Nuxt モジュールの作り方を知って<br>開発した機能を再利用しよう
  Presentation slides for VueFes 2022

# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS (experimental)
css: unocss
---

<div class="mt-50 font-black drop-shadow-md" style="text-shadow: rgba(0, 0, 0, 0.5) 0 0 4px">

# Nuxt モジュールの作り方を知って<br>開発した機能を再利用しよう

田中弘治@Co-Edo

</div>

---
tit:e: About me
layout: profile
image: /kohji_vector_white.png
class: 
---
# たなかこうじ @Co-Edo

Vue.js と Nuxt を愛するフロントエンドエンジニア

I ♥ TypeScript / JavaScript.

[コワーキングスペース茅場町 Co-Edo](https://www.coworking.tokyo.jp/) 運営代表者

[株式会社ダイレクトサーチジャパン](https://direct-search.jp./) 代表取締役

* 　<codicon-github-inverted /> [monsat](https://github.com/monsat)
* 　<icon-park-solid-twitter /> [@ktanaka](https://twitter.com/ktanaka)
* 　<simple-icons-zenn /> [coedo](https://zenn.dev/coedo) (Zenn)
* 　<ic-baseline-article /> [kohji.blog](https://kohji.blog)

---
layout: image
image: /vuefes-ss-nuxt-module.png
---

---
---

# Vue.js, Nuxt, プラグイン, モジュール

* Vue.js
  * クールな JavaScript フレームワーク
* Nuxt
  * クールな Web アプリケーション（メタ）フレームワーク

<div v-click>

## プラグインとモジュール

* Vue.js プラグイン
  * Vue.js のアプリケーションを拡張する
* Nuxt プラグイン
  * ランタイムに動作し Nuxt アプリケーションを拡張する
* Nuxt モジュール
  * Nuxt 起動時に動作し、アプリケーションや Nuxt 自体を拡張する
  * ビルドタイムとランタイムのどちらも拡張可能

</div>

---
---

# なぜ Nuxt モジュール ?

## 複数のプロジェクト間で使用可能

共通の設定やコンポーネント等の使用が可能

## Nuxt 自体の拡張が可能

Nuxt は高機能な一方で、コアはとてもシンプルに作られている

## ビルドタイムの動作を拡張可能

例）外部ファイルを読み込んでコンテンツに加えたい

## プラグイン自体を拡張可能

例）プラグインのインストール前に何らかの処理をしたい


---
layout: center
---

# Nuxt モジュールの使い方

---
---

# `nuxt.config.ts` に記述するのみ

```ts {all|5}
// nuxt.config.ts
import { defineNuxtConfig } from 'nuxt/config'
import BasicAuth from 'nuxt-basic-authentication-module'

export default defineNuxtConfig({
  modules: [
    '@nuxtjs/example', // npm のパッケージ名で記述
    './modules/example', // プロジェクト内のローカルモジュール
    ['./modules/example', { token: '123' }], // オプションを指定する場合
    BasicAuth, // これも可

    // インラインで記述することもできる
    async (inlineOptions, nuxt) => { },
  ]
})
```

---
layout: center
---

# モジュール開発、最初の一歩

---
---

# モジュールの開発 → コードやロジックの再利用

どのプロジェクトでも同じような設定で Vue プラグインを読み込む場合

毎回似たような認証のロジックを書いている場合

いつも使用する `ref()` や `computed()` がある場合

汎用的に使えるコンポーネントを作成済の場合

## モジュールにしておくと使い回しやすい

たとえば

* Plugins の再利用
* Composables の再利用
* Components 等 の再利用
* ぜんぶ組み合わせて再利用

---
---

# Nuxt モジュールの正体

開発モードでの起動 (`nuxi dev`) 時や  
本番用のビルド (`nuxi build`, `nuxi generate`) 時に実行される  
**非同期関数**

```ts {all|7-9}
// modules/module.ts

export default async (inlineOptions, nuxt) => {
  console.log(inlineOptions.foo) // `bar`
  console.log(nuxt.options.dev) // true or false

  nuxt.hook('ready', async nuxt => {
    console.log('Nuxt is ready')
  })
}
```

<v-click>

`nuxt.hook()` で、特定のタイミングをフックできる

</v-click>

---
layout: center
---

# **Nuxt Kit** が神！

---
---

# Nuxt Kit

ビルドやランタイムのフックや、コアの機能を活用するためのユーティリティ  
**Nuxt モジュールの作成がとても効率的に！**

<div class="grid grid-cols-2 text-[0.85em]">
<div>

* Compatibility
  * `checkNuxtCompatibility(constraints)`
  * `assertNuxtCompatibility(constraints)`
  * `hasNuxtCompatibility(constraints)`
  * `isNuxt2()`, `isNuxt3()`, `getNuxtVersion()`
* Imports, Components
  * `addImports(imports: Import | Import[])`
  * `addImportsDir(dirs: string | string[])`
  * `addComponentsDir(dir)`
  * `addComponent(componentObject)`
* Plugins, Templates
  * `addPlugin(pluginOptions, { append? })`
  * `addPluginTemplate(pluginOptions, { append? })`
  * `addTemplate(templateOptions)`

</div>
<div>

* Server
  * `addServerHandler (handler)`
  * `addDevServerHandler (handler)`
* Logging, Resolving
  * `useLogger(scope?)`
  * `resolvePath (path, resolveOptions?)`
  * `resolveAlias (path, aliases?)`
  * `findPath (paths, resolveOptions?)`
  * `createResolver (base)`
* Builder
  * `extendViteConfig(callback, options?)`
  * `addVitePlugin(vitePlugin, options?)`
  * `extendWebpackConfig(callback, options?)`

</div>
</div>

---
---

# ビルドやランタイムの特定の機能をフックする

* App Hooks
* Nuxt Hooks



---
---

# App Hooks

```ts
export interface RuntimeNuxtHooks {
  'app:created': (app: App<Element>) => HookResult
  'app:beforeMount': (app: App<Element>) => HookResult
  'app:mounted': (app: App<Element>) => HookResult
  'app:rendered': (ctx: AppRenderedContext) => HookResult
  'app:redirected': () => HookResult
  'app:suspense:resolve': (Component?: VNode) => HookResult
  'link:prefetch': (link: string) => HookResult
  // App Error
  'app:error': (err: any) => HookResult
  'app:error:cleared': (options: { redirect?: string }) => HookResult
  'app:data:refresh': (keys?: string[]) => HookResult
  // Page
  'page:start': (Component?: VNode) => HookResult
  'page:finish': (Component?: VNode) => HookResult
  // Vue.js
  'vue:setup': () => void
  'vue:error': (...args: Parameters<Parameters<typeof onErrorCaptured>[0]>) => HookResult
}
```

---
---

# Nuxt Hooks

たくさんあるので[ソースコード](https://github.com/nuxt/framework/blob/main/packages/schema/src/types/hooks.ts#L68)で確認しましょう

```ts
export interface NuxtHooks {
  // Kit
  'kit:compatibility': (compatibility: NuxtCompatibility, issues: NuxtCompatibilityIssues) => HookResult

  // nuxt
  'app:resolve': (app: NuxtApp) => HookResult
  'app:templates': (app: NuxtApp) => HookResult
  'app:templatesGenerated': (app: NuxtApp) => HookResult
  'builder:generateApp': () => HookResult
  'pages:extend': (pages: NuxtPage[]) => HookResult
  'build:manifest': (manifest: Manifest) => HookResult

  // Auto imports
  'imports:sources': (presets: ImportPresetWithDeprecation[]) => HookResult
  'imports:extend': (imports: Import[]) => HookResult
  'imports:dirs': (dirs: string[]) => HookResult

  /** @deprecated Please use `imports:sources` hook */
  'autoImports:sources': (presets: ImportPresetWithDeprecation[]) => HookResult
  /** @deprecated Please use `imports:extend` hook */
  'autoImports:extend': (imports: Import[]) => HookResult
  /** @deprecated Please use `imports:dirs` hook */
  'autoImports:dirs': (dirs: string[]) => HookResult

  // Components
  'components:dirs': (dirs: ComponentsOptions['dirs']) => HookResult
  'components:extend': (components: Component[]) => HookResult

  // @nuxt/builder
  'build:before':
  (builder: Builder, buildOptions: NuxtOptions['build']) => HookResult
  'builder:prepared': (builder: Builder, buildOptions: NuxtOptions['build']) => HookResult
  'builder:extendPlugins': (plugins: NuxtOptions['plugins']) => HookResult
  'build:templates': (templates: {
    templateVars: Record<string, any>,
    templatesFiles: NuxtTemplate[],
    resolve: (...args: string[]) => string
  }) => HookResult
  'build:extendRoutes': (routes: any[], resolve: (...args: string[]) => string) => HookResult
  'build:done': (builder: Builder) => HookResult
  'watch:restart': (event: { event: string, path: string }) => HookResult
  // 'watch:fileChanged': (builder: Builder, fileName: string) => HookResult
  'builder:watch': (event: WatchEvent, path: string) => HookResult

  // nitropack
  'nitro:config': (nitroConfig: NitroConfig) => HookResult
  'nitro:init': (nitro: Nitro) => HookResult
  'nitro:build:before': (nitro: Nitro) => HookResult

  // @nuxt/cli
  'generate:cache:ignore': (ignore: string[]) => HookResult
  'config': (options: NuxtConfig) => HookResult
  'run:before': (options: { argv: string[], cmd: { name: string, usage: string, description: string, options: Record<string, any> }, rootDir: string }) => HookResult

  // nuxi
  'build:error': (error: Error) => HookResult
  'prepare:types': (options: { references: TSReference[], declarations: string[], tsConfig: TSConfig }) => HookResult

  // @nuxt/core
  'ready': (nuxt: Nuxt) => HookResult
  'close': (nuxt: Nuxt) => HookResult
  'modules:before': (moduleContainer: ModuleContainer, modules?: any[]) => HookResult
  'modules:done': (moduleContainer: ModuleContainer) => HookResult
  // 'webpack:done': () => HookResult

  // @nuxt/server
  'render:before': (server: Server, renderOptions: NuxtOptions['render']) => HookResult
  'render:setupMiddleware': (app: any) => HookResult
  'render:errorMiddleware': (app: any) => HookResult
  'render:done': (server: Server) => HookResult
  'listen': (listenerServer: HttpServer | HttpsServer, listener: any) => HookResult
  'server:nuxt:renderLoading': (req: IncomingMessage, res: ServerResponse) => HookResult
  'render:route': (url: string, result: RenderResult, context: any) => HookResult
  'render:routeDone': (url: string, result: RenderResult, context: any) => HookResult
  'render:beforeResponse': (url: string, result: RenderResult, context: any) => HookResult

  // @nuxt/vue-renderer
  'render:resourcesLoaded': (resources: any) => HookResult
  'vue-renderer:context': (renderContext: any) => HookResult
  'vue-renderer:spa:prepareContext': (renderContext: any) => HookResult
  'vue-renderer:spa:templateParams': (templateParams: Record<string, any>) => HookResult
  'vue-renderer:ssr:prepareContext': (renderContext: any) => HookResult
  'vue-renderer:ssr:context': (renderContext: any) => HookResult
  // '_render:context': (nuxt: Nuxt) => HookResult
  // 'render:routeContext': (renderContext: any) => HookResult
  'vue-renderer:ssr:csp': (cspScriptSrcHashes: string[]) => HookResult
  'vue-renderer:ssr:templateParams': (templateParams: Record<string, any>, renderContext: any) => HookResult

  // @nuxt/webpack
  'webpack:config': (webpackConfigs: Configuration[]) => HookResult
  'webpack:devMiddleware': (middleware: (req: IncomingMessage, res: ServerResponse, next: (err?: any) => any) => any) => HookResult
  'webpack:hotMiddleware': (middleware: (req: IncomingMessage, res: ServerResponse, next: (err?: any) => any) => any) => HookResult
  'build:compile': (options: { name: string, compiler: Compiler }) => HookResult
  'build:compiled': (options: { name: string, compiler: Compiler, stats: Stats }) => HookResult
  'build:resources': (mfs?: Compiler['outputFileSystem']) => HookResult
  'server:devMiddleware': (middleware: Middleware) => HookResult
  'bundler:change': (shortPath: string) => void
  'bundler:error': () => void
  'bundler:done': () => void
  'bundler:progress': (statesArray: any[]) => void

  // @nuxt/generator
  'generate:before': (generator: Generator, generateOptions: NuxtOptions['generate']) => HookResult
  'generate:distRemoved': (generator: Generator) => HookResult
  'generate:distCopied': (generator: Generator) => HookResult
  'generate:route': ({ route, setPayload }: { route: any, setPayload: any }) => HookResult
  'generate:page': (page: {
    route: any,
    path: string,
    html: string,
    exclude: boolean,
    errors: string[]
  }) => HookResult
  'generate:routeCreated': ({ route, path, errors }: { route: any, path: string, errors: any[] }) => HookResult
  'generate:extendRoutes': (routes: any[]) => HookResult
  'generate:routeFailed': ({ route, errors }: { route: any, errors: any[] }) => HookResult
  'generate:manifest': (manifest: any, generator: Generator) => HookResult
  'generate:done': (generator: Generator, errors: any[]) => HookResult

  'export:before': (generator: Generator) => HookResult
  'export:distRemoved': (generator: Generator) => HookResult
  'export:distCopied': (generator: Generator) => HookResult
  'export:route': ({ route, setPayload }: { route: any, setPayload: any }) => HookResult
  'export:routeCreated': ({ route, path, errors }: { route: any, path: string, errors: any[] }) => HookResult
  'export:extendRoutes': ({ routes }: { routes: any[] }) => HookResult
  'export:routeFailed': ({ route, errors }: { route: any, errors: any[] }) => HookResult
  'export:done': (generator: Generator, { errors }: { errors: any[] }) => HookResult

  // vite
  'vite:extend': (viteBuildContext: { nuxt: Nuxt, config: ViteInlineConfig }) => HookResult
  'vite:extendConfig': (viteInlineConfig: ViteInlineConfig, env: { isClient: boolean, isServer: boolean }) => HookResult
  'vite:serverCreated': (viteServer: ViteDevServer, env: { isClient: boolean, isServer: boolean }) => HookResult
}
```

---
layout: center
---

# 実践てきなモジュール開発例

---
layout: center
---

# ある日、上司から

---
layout: talk-left
image: /business_man1_1_smile.png
---

# サブスク解約機能の実装ありがとう
## 追加でお願いしたいんだけど…

---
layout: talk-right
image: /face_smile_man1.png
---

# （嫌な予感…）
## はい、なんでしょうか？

---
layout: talk-left
image: /business_man1_1_smile.png
---

# 誤クリックで解約できてしまうので
## 「確認画面」を挟んでほしい

---
layout: talk-right
image: /face_smile_man1.png
---

# （ほっ…）
## 分かりました。他のプロジェクト用に
## Nuxt モジュールにしておきますね

---
layout: talk-right
image: /face_smile_man1.png
---

# **VueUse** に確か…
## `useConfirmDialog` というのがあった

---
layout: talk-right
image: /face_smile_man1.png
---

# `useConfirmDialog` を使って…

<div v-click>

## できた！

</div>

---
---

# 確認画面コンポーネント

（本題ではありませんが）コンポーネントを作成します。

## 

```html
<script setup lang="ts">
import { useConfirmDialog } from '@vueuse/core'
const { isRevealed, reveal, confirm, cancel, onReveal, onConfirm, onCancel } = useConfirmDialog()
</script>

<template>
  <button @click="reveal">サブスクを解約する</button>

  <dialog :open="isRevealed">
    <h2>解約しますか？</h2>
    <div>
      <button @click="cancel">いいえ</button>
      <button @click="confirm">はい</button>
    </div>
  </dialog>
</template>
```

---
layout: image
image: /vuefes-ss-normal.png
class: contain
---


---
layout: image
image: /vuefes-ss-vueuse.png
---


---
layout: talk-right
image: /face_smile_man1.png
---

# Nuxt モジュールにするために
## 外側から呼び出しやすいようにしよう

---
---

# 確認画面コンポーネント

（モジュールにした際、利用しやすいように）コンポーネントを変更します。

## 

```html
<script setup lang="ts">
type Emits = {
  (e: 'confirm', result: boolean): void
  (e: 'cancel'): void
}
const emit = defineEmits<Emits>()
const { isRevealed, reveal, confirm, cancel, onReveal, onConfirm, onCancel } = useConfirmDialog()
onConfirm(() => emit('confirm', true))
onCancel(() => emit('cancel'))
</script>

<template>
  <button v-bind="$attrs" @click="reveal">
    <slot />
  </button>
  <dialog :open="isRevealed">
    <h2>解約しますか？</h2>
    <div>
      <button @click="cancel">いいえ</button>
      <button @click="confirm">はい</button>
    </div>
  </dialog>
</template>
```

---
---

# 確認画面コンポーネントの呼び出し方

Page コンポーネント等、呼び出し側で任意の処理ができるようになりました

```html
<script setup lang="ts">
// Page Component
const completeCancel = (result: boolean) => {
  console.log('解約完了', result)
}
const canceled = () => {
  console.log('キャンセルされました')
}
</script>

<template>
  <div>
    <CancelConfirm
      @confirm="completeCancel"
      @cancel="canceled"
    >
      サブスクを解約する
    </CancelConfirm>
  </div>
</template>
```

---
layout: talk-right
image: /face_smile_man1.png
---

# モジュールにするには<br>何から始めればいいのだろう？

<div v-click>

## お。なんか便利そうなのがあった

</div>

---
---

# Nuxt モジュール開発の始め方

便利なコマンドが用意されています。

```bash
$ npx nuxi init -t module nuxt-your-module-name
```

<div v-click>

次のようなフォルダ構成の環境ができます。

<div class="pa-2 bg-dark-600">

* 　<TreeFolder> src</TreeFolder> ・・・モジュールを格納するディレクトリ
  * 　<cib-typescript /> module.ts
  * 　<TreeFolder> runtime</TreeFolder>
    * <cib-typescript /> plugin.ts
* 　<TreeFolder> playground</TreeFolder> ・・・動作確認用 Nuxt アプリケーション
  * 　<ph-file-vue-fill /> app.vue
  * <cib-typescript /> nuxt.config.ts
* 　<lucide-file-json-2 /> package.json

</div>
</div>

<style>
.slidev-code {
  font-size: 1.25em !important;
}
</style>

---
layout: talk-right
image: /face_smile_man1.png
---

# `src` ディレクトリで開発し

<div v-click>

## `yarn dev` すると `playground` 以下の Nuxt アプリで動作確認できるのね

</div>

---
---

# `playground/nuxt.config.ts` の中身

```ts
import { defineNuxtConfig } from 'nuxt/config'
import MyModule from '..'

export default defineNuxtConfig({
  modules: [
    MyModule
  ],
  myModule: {
    addPlugin: true
  }
})
```

<v-click>

先頭で開発中のモジュールを読み込んでいる

```ts
import MyModule from '..' // ← 上位のフォルダを読むように設定されている
```

開発しやすい！

</v-click>

---
---

# `src/module.ts` の中身

```ts {all|14-16|17|5}
import { resolve } from 'path'
import { fileURLToPath } from 'url'
import { defineNuxtModule, addPlugin } from '@nuxt/kit'

export interface ModuleOptions {
  addPlugin: boolean
}

export default defineNuxtModule<ModuleOptions>({
  meta: {
    name: 'my-module',
    configKey: 'myModule' // nuxt.config.ts で使用可能な key
  },
  defaults: {
    addPlugin: true
  },
  setup (options, nuxt) {
    if (options.addPlugin) {
      const runtimeDir = fileURLToPath(new URL('./runtime', import.meta.url))
      nuxt.options.build.transpile.push(runtimeDir)
      addPlugin(resolve(runtimeDir, 'plugin'))
    }
  }
})
```

---
---
# options

`defaults` の内容を `nuxt.options[configKey]` で上書き

`inlineOptions` があれば `inlineOptions` で上書き（最優先）

# defineNuxtModule

* オプションを適宜マージ
* hooks 等を addHooks
* setup() を実行
* NuxtModule を返す

---
layout: talk-right
image: /face_smile_man1.png
---

# `src/runtime/components` に `CancelConfirm.vue` を保存して


## `src/module.ts` を変更しよう

---
---

# Nuxt モジュールでコンポーネントを読み込む

`src/runtime/components` に `CancelConfirm.vue` を保存し `src/module.ts` を変更

```ts
import { defineNuxtModule, createResolver, addComponentsDir } from '@nuxt/kit'
export interface ModuleOptions {
  prefix?: string
}

export default defineNuxtModule<ModuleOptions>({
  meta: {
    name: 'coedo-cancel-confirm',
    configKey: 'coedoCancelConfirm'
  },
  defaults: {
    prefix: 'coedo'
  },
  setup (options, nuxt) {
    const { resolve } = createResolver(import.meta.url)
    await addComponentsDir({
      path: resolve('./runtime/components'),
      prefix: options.prefix,
      global: true
    })
  }
})
```

---
---

# 動作確認

`playground/app.vue` で（Prefix をつけて）コンポーネントを読み込む

```html
<script setup lang="ts">
// Page Component
const completeCancel = (result: boolean) => {
  console.log('解約完了', result)
}
const canceled = () => {
  console.log('キャンセルされました')
}
</script>

<template>
  <div>
    <h1>解約確認モジュール</h1>
    <CoedoCancelConfirm
      @confirm="completeCancel"
      @cancel="canceled"
    >
      サブスクを解約する
    </CoedoCancelConfirm>
  </div>
</template>
```

---
layout: talk-right
image: /face_smile_man1.png
---

# できた 🎉
## 意外と簡単だった

---
layout: center
---

# しかし

---
layout: talk-left
image: /business_man1_1_smile.png
---

# ついでに「解約が減るように」して
## ほら、最近うえから「解約を減らせ」ってうるさく言われてて。できるよね？

---
layout: talk-right
image: /face_smile_man1.png
---

# （分かりました。実装します）
## やれやれ、こういうこと言う上司の下では働きたくないな。依頼もふわっとしてるし

---
layout: center
---

# そして


---
layout: talk-right
image: /face_smile_man1.png
---

# できた
## 動作確認するか

---
---

# 作ったもの

<div class="w-3/4 mx-auto">
  <video controls>
    <source src="/vuefes-movie-cancel.mp4" type="video/mp4">
  </video>
</div>

---
---

# 完成したモジュール

<div class="pa-2 bg-dark-600">

* 　<TreeFolder> src</TreeFolder>
  * 　<cib-typescript /> module.ts
  * 　<TreeFolder> runtime</TreeFolder>
    * 　<TreeFolder> components</TreeFolder>
      * 　<ph-file-vue-fill /> CancelConfirm.vue
      * 　<ph-file-vue-fill /> LastOffer.vue ・・・・・追加
      * 　<ph-file-vue-fill /> NormalConfirm.vue ・・・追加
* 　<TreeFolder> playground</TreeFolder>
  * 　<ph-file-vue-fill /> app.vue
  * <cib-typescript /> nuxt.config.ts
* 　<lucide-file-json-2 /> package.json

</div>

---
---

# コンポーネント追加だけで対応できたので<br> `module.ts` の変更はありません

今回は `src/runtime/components` 以下のコンポーネントがインポートされるようにしていましたので、 `module.ts` の変更は不要です。

別途 Composables 等を読み込む場合でも `'imports:dirs'` をフックし、同じように実装できます。

また、今回は不要でしたが、たとえば次のような対応も可能です。

* オプションの内容によって動作を変える
* ルーティングをフックして、特別なルーティングを加える
  * `/news` と `/news/[slug].vue` で、お知らせ一覧やお知らせページを表示するなど
* ビルド時に外部のファイルを fetch してきてコンテンツに反映する
  * json や markdown ファイルを読み込むなど

---
layout: center
---

というわけで

# 実践てきな🤔モジュール開発例

でした

---
---

# みんなでエコシステムを広げていきましょう



<div class="my-30 text-center">

<logos-vitejs class="mx-10 text-[640%]" />
<logos-vue class="mx-10 text-[640%]" />
<logos-nuxt-icon class="mx-10 text-[640%]" />

</div>

---
---

# おまけ: npm パッケージとして公開する

せっかく作った Nuxt モジュールです。
ほかの Nuxt ユーザーのために公開しましょう。

## 加えるもの

* 汎用的なオプションの用意
* README.md
* テスト (Vitest)

## `np` が便利

`npx np` で、煩わしいバージョニングから NPM へのデプロイまですべて行えます。

[![npx np](/npx_np.png)](https://zenn.dev/rdlabo/articles/1fda37f6e1025999e86e)

---
layout: center
---

# さいごに

---
---

# 今回実装した解約防止機能は<br>「消費者契約法」により違法です

また、サブスクリプションサービスを申し込む際の “最終確認画面”において、次の事項を簡単に確認できるよう表示する必要があります

* 提供するサービスの期間・回数等に関する事項
* 提供するサービスの料金に関する事項
* **キャンセル・解約に関する事項**

<div class="my-8"></div>

## 利用者がハッピーになる機能を実装し<br>みんなで Vue.js や Nuxt のエコシステムを広げていきましょう！

[コワーキングスペース茅場町 Co-Edo](https://www.coworking.tokyo.jp/) は **Slack のみで解約可能** なシンプル設計です😎

---
layout: talk-left
image: /business_man1_1_smile.png
---

# え？ あの機能は使えないの？
## ダークパターン？なにそれ美味しいの？

---
layout: center
---

# ~~実践てきなモジュール開発例~~

ではなく

<div v-click>

# 実践できないモジュール開発例

でした！

</div>

<div class="text-right tracking-wide">

田中弘治 @ Co-Edo
　<icon-park-solid-twitter /> [@ktanaka](https://twitter.com/ktanaka)

</div>
