---
title: >-
  @eslint/migrate-config を使って legacy ESLint configuration file format を new
  ESLint configuration file format に変換する
tags:
  - JavaScript
  - Node.js
  - ESLint
private: true
updated_at: '2024-11-03T07:24:02+09:00'
id: f85cdfac702e4dea5aee
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

ESLint v8.21.0 から追加された Flat Config。
ESLint v9 から Flat Config がデフォルトとして扱われるようになりました。

ESLint v9 で従来の legacy ESLint configuration file format を使いたい場合は、 `ESLINT_USE_FLAT_CONFIG` を `false` に設定すれば利用可能ですが、 `@eslint/migrate-config` を利用すれば、簡単に移行することができます。

##　注意

`@eslint/migrate-config` は 2024/11 時点以下のファイルフォーマットのみ対応しています。

- eslintrc
- eslintrc.json
- eslintrc.yml

`.eslintrc.js`, `.eslintrc.cjs`, `.eslintrc.mjs` でも移行することはできますが、完全に移行できないことに注意する必要があります。

## 使用方法

`npx` を利用すればパッケージを追加せず利用することが可能です。

```sh
npx @eslint/migrate-config .eslintrc.json
```

## 移行検証

いくつか `@eslint/migrate-config` を利用して移行してみました。

### `remix-run/remix/templates/remix`

`remix-run/remix/templates/remix`　内の `templates/remix/.eslintrc.cjs` を移行してみます。

```sh
> npx @eslint/migrate-config .eslintrc.cjs
Need to install the following packages:
@eslint/migrate-config@1.3.3
Ok to proceed? (y) y


Migrating .eslintrc.cjs

WARNING: This tool does not yet work great for .eslintrc.(js|cjs|mjs) files.
It will convert the evaluated output of our config file, not the source code.
Please review the output carefully to ensure it is correct.


Wrote new config to ./eslint.config.mjs

You will need to install the following packages to use the new config:
- globals
- @eslint/compat
- @eslint/js
- @eslint/eslintrc

You can install them using the following command:

npm install globals @eslint/compat @eslint/js @eslint/eslintrc -D
```

`.eslintrc.cjs` から `eslint.config.mjs` が生成されました。
また、 console　に記載されている通り、 `globals`　等のパッケージを追加する必要があります。

#### 移行前のファイル

https://github.com/remix-run/remix/blob/b7d280140b27507530bcd66f7b30abe3e9d76436/templates/remix/.eslintrc.cjs#L1-L84

#### 移行後のファイル

```js:eslint.config.mjs
import globals from "globals";
import { fixupConfigRules, fixupPluginRules, fixupConfigRules } from "@eslint/compat";
import react from "eslint-plugin-react";
import jsxA11Y from "eslint-plugin-jsx-a11y";
import typescriptEslint from "@typescript-eslint/eslint-plugin";
import _import from "eslint-plugin-import";
import tsParser from "@typescript-eslint/parser";
import path from "node:path";
import { fileURLToPath } from "node:url";
import js from "@eslint/js";
import { FlatCompat } from "@eslint/eslintrc";

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);
const compat = new FlatCompat({
    baseDirectory: __dirname,
    recommendedConfig: js.configs.recommended,
    allConfig: js.configs.all
});

export default [{
    ignores: ["!**/.server", "!**/.client"],
}, ...compat.extends("eslint:recommended"), {
    languageOptions: {
        globals: {
            ...globals.browser,
            ...globals.commonjs,
        },

        ecmaVersion: "latest",
        sourceType: "module",

        parserOptions: {
            ecmaFeatures: {
                jsx: true,
            },
        },
    },
}, ...fixupConfigRules(compat.extends(
    "plugin:react/recommended",
    "plugin:react/jsx-runtime",
    "plugin:react-hooks/recommended",
    "plugin:jsx-a11y/recommended",
)).map(config => ({
    ...config,
    files: ["**/*.{js,jsx,ts,tsx}"],
})), {
    files: ["**/*.{js,jsx,ts,tsx}"],

    plugins: {
        react: fixupPluginRules(react),
        "jsx-a11y": fixupPluginRules(jsxA11Y),
    },

    settings: {
        react: {
            version: "detect",
        },

        formComponents: ["Form"],

        linkComponents: [{
            name: "Link",
            linkAttribute: "to",
        }, {
            name: "NavLink",
            linkAttribute: "to",
        }],

        "import/resolver": {
            typescript: {},
        },
    },
}, ...fixupConfigRules(compat.extends(
    "plugin:@typescript-eslint/recommended",
    "plugin:import/recommended",
    "plugin:import/typescript",
)).map(config => ({
    ...config,
    files: ["**/*.{ts,tsx}"],
})), {
    files: ["**/*.{ts,tsx}"],

    plugins: {
        "@typescript-eslint": fixupPluginRules(typescriptEslint),
        import: fixupPluginRules(_import),
    },

    languageOptions: {
        parser: tsParser,
    },

    settings: {
        "import/internal-regex": "^~/",

        "import/resolver": {
            node: {
                extensions: [".ts", ".tsx"],
            },

            typescript: {
                alwaysTryTypes: true,
            },
        },
    },
}, {
    files: ["**/.eslintrc.cjs"],

    languageOptions: {
        globals: {
            ...globals.node,
        },
    },
}];
```

実行完了後に移行前のファイル `.eslintrc.cjs` は手動で削除してください。

```sh
rm .eslintrc.cjs
```

### `mziyut/honkit-plugin-prism`

`mziyut/honkit-plugin-prism` の `.eslintrc` も移行してみます。
https://github.com/mziyut/honkit-plugin-prism/blob/main/.eslintrc

```sh
> npx @eslint/migrate-config .eslintrc

Migrating .eslintrc

Wrote new config to ./eslint.config.mjs

You will need to install the following packages to use the new config:
- @eslint/js
- @eslint/eslintrc

You can install them using the following command:

npm install @eslint/js @eslint/eslintrc -D
```

#### 移行前のファイル

```yaml:.eslintrc
---
extends: eslint-config-semistandard
rules:
  padded-blocks: 0
  space-before-function-paren: 0
```

#### 移行後のファイル

```js:eslint.config.mjs
import path from "node:path";
import { fileURLToPath } from "node:url";
import js from "@eslint/js";
import { FlatCompat } from "@eslint/eslintrc";

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);
const compat = new FlatCompat({
    baseDirectory: __dirname,
    recommendedConfig: js.configs.recommended,
    allConfig: js.configs.all
});

export default [...compat.extends("eslint-config-semistandard"), {
    rules: {
        "padded-blocks": 0,
        "space-before-function-paren": 0,
    },
}];
```

## Ref

https://www.npmjs.com/package/@eslint/migrate-config

https://github.com/mziyut/honkit-plugin-prism/blob/main/.eslintrc

https://github.com/remix-run/remix/blob/main/templates/remix/.eslintrc.cjs#L1-L84

https://remix.run/docs/hi/main/guides/templates
