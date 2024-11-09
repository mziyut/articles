---
title: Vitest の Browser Mode (+ Playwright) を使ってブラウザテストを実行する
tags:
  - Node.js
  - テスト
  - TypeScript
  - Playwright
  - Vitest
private: true
updated_at: '2024-11-10T00:03:34+09:00'
id: da1d20dccb5ca67db4b0
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

Vitest には Browser Mode が実験的な機能として実装されています。
Browser Mode の実行には Playwright または WebdriverIO を利用しますが今回は Playwright 使います。

## Browser Mode の設定

:::note warn
はじめにも記載しましたが、 (2024/11/09現在) Browser Mode は実験的な機能です。
仕様の大幅な変更等も考えられるため使用する際は理解したうえで使用してください。
:::

### 必要なパッケージの インストール

今回は、 Vitest 側が用意してくれている `vitest init browser` を実行して環境を用意します。
なお、手動インストールすることも可能です。

また、`vitest init browser` は `npm init` 前のディレクトリで実行することも可能です。
今回は、 `TypeScript`, `playwright`, `chromium`, `vanilla`, `yes` と選択してセットアップをすすめます。

```sh
> npx vitest init browser
Need to install the following packages:
vitest@2.1.4
Ok to proceed? (y) y

◼ This utility will help you set up a browser testing environment.

? Choose a language for your tests › - Use arrow-keys. Return to submit.
❯   TypeScript - Use TypeScript.
    JavaScript
? Choose a browser provider. Vitest will use its API to control the testing environment › - Use arrow-keys. Return to submit.
❯   playwright - Playwright relies on Chrome DevTools protocol. Read more: https://playwright.dev
    webdriverio
    preview
? Choose a browser › - Use arrow-keys. Return to submit.
❯   chromium
    firefox
    webkit
? Choose your framework › - Use arrow-keys. Return to submit.
❯   vanilla - No framework, just plain JavaScript or TypeScript.
    vue
    svelte
    react
    preact
    solid
    marko
✔ Install Playwright browsers (can be done manually via 'pnpm exec playwright install')? … yes
◼ Installing packages...
◼ @vitest/browser, playwright


added 124 packages in 8s

23 packages are looking for funding
  run `npm fund` for details

✔ Created a config file for browser tests vitest.config.ts

✔ Added "test:browser" script to your package.json.

◼ Installing Playwright dependencies with `npx playwright install --with-deps`...
◼ Add "@vitest/browser/providers/playwright" to your tsconfig.json "compilerOptions.types" field to have better intellisense support.

✔ Created example test file in vitest-example/HelloWorld.test.ts
  You can safely delete this file once you have written your own tests.

◼ All done! Run your tests with npm run test:browser
npm notice
npm notice New minor version of npm available! 10.7.0 -> 10.9.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v10.9.0
npm notice To update run: npm install -g npm@10.9.0
npm notice
mziyut@29CC29058-MBP ~/W/g/m/p/t/vitest-browser-mode (main)>

```

実行が完了すると以下ファイルが作成されます。

今回は、 `npm init` 前のディレクトリ内で `npx vitest init browser` を実行したため Workspace に関連するファイルは生成されていません。 すでに Vitest が導入されていたりすると Vite workspace 設定のファイルも生成されます。Workspace については下記 URL から確認してください。

https://vitest.dev/guide/workspace.html

合わせて `vitest-example` 内にはサンプルテストも同梱されています。

```sh
> tree -L 1
.
├── node_modules
├── package-lock.json
├── package.json
├── vitest-example
└── vitest.config.ts

3 directories, 3 files
```

```json:package.json
{
  "devDependencies": {
    "@vitest/browser": "^2.1.4",
    "playwright": "^1.48.2"
  },
  "scripts": {
    "test:browser": "vitest"
  }
}
```

```ts:vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    browser: {
      enabled: true,
      name: 'chromium',
      provider: 'playwright',
      // https://playwright.dev
      providerOptions: {},
    },
  },
})
```

```ts:HelloWorld.ts
export default function HelloWorld({ name }: { name: string }): HTMLDivElement {
  const parent = document.createElement('div')

  const h1 = document.createElement('h1')
  h1.textContent = 'Hello ' + name + '!'
  parent.appendChild(h1)

  return parent
}
```

```ts:HelloWorld.test.ts
import { expect, test } from 'vitest'
import { getByText } from '@testing-library/dom'
import HelloWorld from './HelloWorld.js'

test('renders name', () => {
  const parent = HelloWorld({ name: 'Vitest' })
  document.body.appendChild(parent)

  const element = getByText(parent, 'Hello Vitest!')
  expect(element).toBeInTheDocument()
})

```

## Vitest を実行する

それでは Vitest の設定が完了したので実際にテストを動かしてみましょう。
`package.json` の `script` に `test:browser` が追加されているのでそのまま実行します。

テスト実行後 CLI 上にはテスト実行結果が表示されます。
別途立ち上がったブラウザ (Chromium) にはテスト内容が表示されます。

```sh
> npm run test:browser

> test:browser
> vitest

The CJS build of Vite's Node API is deprecated. See https://vite.dev/guide/troubleshooting.html#vite-cjs-node-api-deprecated for more details.

 DEV  v2.1.4 /Users/mziyut/Workspace/github.com/mziyut/playground/tools/vitest-browser-mode
      Browser runner started by playwright at http://localhost:63315/

 ✓ vitest-example/HelloWorld.test.ts (1)
   ✓ renders name

 Test Files  1 passed (1)
      Tests  1 passed (1)
   Start at  23:55:23
   Duration  6.09s (transform 0ms, setup 0ms, collect 52ms, tests 2ms, environment 0ms, prepare 204ms)


 PASS  Waiting for file changes...
       press h to show help, press q to quit
```

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/c0105586-9da9-f856-bfac-33d22f0b3334.png)

## Ref

https://vitest.dev/guide/browser/why.html

https://github.com/mziyut/playground/pull/19
