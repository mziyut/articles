---
title: Remix プロジェクトに Vitest を導入する
tags:
  - Node.js
  - テスト
  - TypeScript
  - Remix
  - Vitest
private: false
updated_at: '2024-12-05T07:03:49+09:00'
id: dfaeb4b05befab7da673
organization_url_name: foundingbase
slide: false
ignorePublish: false
---

## はじめに

- 開発中の [Remix](https://remix.run) Project に Vitest を導入しました
- Vitest + @testing-library/react で component のテストも実行できるように設定しています

## Vitest のセットアップ

基本 [Getting Started | Guide | Vitest](https://vitest.dev/guide/) の記載の通り設定します。

### パッケージのインストール

```sh
npm install -D vitest
```

Remix は Vite を使用しているため上記コマンドで Vitest を導入するだけ基本テストは実行可能となります。
必要に応じて `package.json` 等を書き換えてください。

```diff_json:package.json
 {
   "scripts": {
+     "test": "vitest"
-     "test": "echo \"Error: no test specified\" && exit 1"
   }
 }
```

### `vitest.config.ts` の作成

`vitest.config.ts` を作成し Vitest 用の設定を行います。
上記セクションで基本動きますと記載したとおり、動きますが Remix の VitePlugin をロードしている影響でテストが即時終了しなかったりと不都合が生じてしまいます。

Vitest は `vitest.config.ts` があれば `vitest.config.ts` を読みに行くため `vitest.config.ts` を作成します。
※ `vitest.config.ts` がなければ `vite.config.ts` を見に行きます。

Remix template の `vite.config.ts` は以下のようになっているので Vitest 実行に必要な設定だけをぬきだします。

```ts:templates/remix/vite.config.ts
import { vitePlugin as remix } from "@remix-run/dev";
import { defineConfig } from "vite";
import tsconfigPaths from "vite-tsconfig-paths";

declare module "@remix-run/node" {
  interface Future {
    v3_singleFetch: true;
  }
}

export default defineConfig({
  plugins: [
    remix({
      future: {
        v3_fetcherPersist: true,
        v3_relativeSplatPath: true,
        v3_throwAbortReason: true,
        v3_singleFetch: true,
        v3_lazyRouteDiscovery: true,
      },
    }),
    tsconfigPaths(),
  ],
});
```

```ts:vitest.config.ts
/// <reference types="vitest" />
import { defineConfig } from "vite";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [tsconfigPaths()],
  test: {
    // 設定すべき項目があれば記載。
  }
});
```

### `@testing-library` の設定

`@testing-library/react` 等を導入して component のテストを実行できるようにします。

#### 必要なパッケージのインストール

実行には下記パッケージが必要になります。

- `@testing-library/react`
- `@testing-library/jsdom`
- `jsdom`

```sh
npm i --save-dev @testing-library/react @testing-library/jsdom jsdom
```

### `vitest-setup.js` の作成

Vitest の assertion を拡張するために `vitest-setup.js` を定義し ` "@testing-library/jest-dom/vitest"` を import します。
import する際、誤って ` "@testing-library/jest-dom` を import しないよう注意してください。

```js:vitest-setup.js
import "@testing-library/jest-dom/vitest";
```

#### `vitest.config.ts` に設定を追加

先ほど作成した、 `vitest-setup.js` を `setupFiles` として読み込むように `vitest.config.ts` に設定を追加します。

```diff_typescript:vitest.config.ts
  /// <reference types="vitest" />
  import { defineConfig } from "vite";
  import tsconfigPaths from "vite-tsconfig-paths";

  export default defineConfig({
    plugins: [tsconfigPaths()],
    test: {
-     // 設定すべき項目があれば記載。
+     environment: "jsdom",
+     setupFiles: ["./vitest-setup.js"],
    }
  });
```

## テストを書いてみる

Vitest の設定が完了したらテストを書いてみましょう。
下記のような ファイルがあったとします。

```tsx:example.tsx
export const Example = () => {
  return <p>Hello World!</p>;
};
```

Example component を表示した際に `Hello World!` が出現するかをテストするには以下のように記載できます。

```tsx:example.test.tsx
import { render } from "@testing-library/react";
import { describe, expect, it } from "vitest";

import { Example } from "./example";

describe("Example component", () => {
  it("renders correctly", () => {
    const { getByText } = render(<Example />);

    expect(getByText("Hello World!")).toBeInTheDocument();
  });
});
```

## Ref

https://vitest.dev/guide/

https://testing-library.com/docs/svelte-testing-library/setup/

https://github.com/testing-library/jest-dom/blob/main/src/vitest.js

https://github.com/testing-library/jest-dom/blob/main/src/index.js
