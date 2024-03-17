---
title: Getting Started | Guide
---

# Getting Started

## Overview

Vitest is a next generation testing framework powered by Vite.

You can learn more about the rationale behind the project in the [Why Vitest](/guide/why) section.

## Trying Vitest Online

You can try Vitest online on [StackBlitz](https://vitest.new). It runs Vitest directly in the browser, and it is almost identical to the local setup but doesn't require installing anything on your machine.

## Adding Vitest to Your Project

<CourseLink href="https://vueschool.io/lessons/how-to-install-vitest?friend=vueuse">Learn how to install by Video</CourseLink>

::: code-group
```bash [npm]
npm install -D vitest
```
```bash [yarn]
yarn add -D vitest
```
```bash [pnpm]
pnpm add -D vitest
```
```bash [bun]
bun add -D vitest
```
:::

:::tip
Vitest 1.0 requires Vite >=v5.0.0 and Node >=v18.00
:::

It is recommended that you install a copy of `vitest` in your `package.json`, using one of the methods listed above. However, if you would prefer to run `vitest` directly, you can use `npx vitest` (the `npx` command comes with npm and Node.js).

The `npx` command will execute the command either from a local `node_modules/.bin` installing any packages needed in order for the command to run. By default, npx will check whether command exists in $PATH, or in the local project binaries, and execute that. If command is not found, it will be installed prior to execution.

## Writing Tests

As an example, we will write a simple test that verifies the output of a function that adds two numbers.

``` js
// sum.js
export function sum(a, b) {
  return a + b
}
```

``` js
// sum.test.js
import { expect, test } from 'vitest'
import { sum } from './sum'

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3)
})
```

::: tip
By default, tests must contain ".test." or ".spec." in their file name.
:::

Next, in order to execute the test, add the following section to your `package.json`:

```json
{
  "scripts": {
    "test": "vitest"
  }
}
```

Finally, run `npm run test`, `yarn test`, or `pnpm test`, depending on your package manager, and Vitest will print this message:

```txt
✓ sum.test.js (1)
  ✓ adds 1 + 2 to equal 3

Test Files  1 passed (1)
    Tests  1 passed (1)
  Start at  02:15:44
  Duration  311ms (transform 23ms, setup 0ms, collect 16ms, tests 2ms, environment 0ms, prepare 106ms)
```

Learn more about the usage of Vitest, see the [API](https://vitest.dev/api/) section.

## Configuring Vitest

One of the main advantages of Vitest is its unified configuration with Vite. If present, `vitest` will read your root `vite.config.ts` to match with the plugins and setup as your Vite app. For example, your Vite [resolve.alias](https://vitejs.dev/config/shared-options.html#resolve-alias) and [plugins](https://vitejs.dev/guide/using-plugins.html) configuration will work out-of-the-box. If you want a different configuration during testing, you can:

- Create `vitest.config.ts`, which will have the higher priority
- Pass `--config` option to CLI, e.g. `vitest --config ./path/to/vitest.config.ts`
- Use `process.env.VITEST` or `mode` property on `defineConfig` (will be set to `test` if not overridden) to conditionally apply different configuration in `vite.config.ts`

Vitest supports the same extensions for your configuration file as Vite does: `.js`, `.mjs`, `.cjs`, `.ts`, `.cts`, `.mts`. Vitest does not support `.json` extension.

If you are not using Vite as your build tool, you can configure Vitest using the `test` property in your config file:

```ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    // ...
  },
})
```

::: tip
Even if you do not use Vite yourself, Vitest relies heavily on it for its transformation pipeline. For that reason, you can also configure any property described in [Vite documentation](https://vitejs.dev/config/).
:::

If you are already using Vite, add `test` property in your Vite config. You'll also need to add a reference to Vitest types using a [triple slash directive](https://www.typescriptlang.org/docs/handbook/triple-slash-directives.html#-reference-types-) at the top of your config file.

```ts
/// <reference types="vitest" />
import { defineConfig } from 'vite'

export default defineConfig({
  test: {
    // ...
  },
})
```

See the list of config options in the [Config Reference](../config/)

::: warning
If you decide to have two separate config files for Vite and Vitest, make sure to define the same Vite options in your Vitest config file since it will override your Vite file, not extend it. You can also use `mergeConfig` method from `vite` or `vitest/config` entries to merge Vite config with Vitest config:

:::code-group
```ts [vitest.config.mjs]
import { defineConfig, mergeConfig } from 'vitest/config'
import viteConfig from './vite.config.mjs'

export default mergeConfig(viteConfig, defineConfig({
  test: {
    // ...
  }
}))
```

```ts [vite.config.mjs]
import { defineConfig } from 'vite'
import Vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [Vue()],
})
```

But we recommend to use the same file for both Vite and Vitest instead of creating two separate files.
:::

## Workspaces Support

Run different project configurations inside the same project with [Vitest Workspaces](/guide/workspace). You can define a list of files and folders that define your workspace in `vitest.workspace` file. The file supports `js`/`ts`/`json` extensions. This feature works great with monorepo setups.

```ts
import { defineWorkspace } from 'vitest/config'

export default defineWorkspace([
  // you can use a list of glob patterns to define your workspaces
  // Vitest expects a list of config files
  // or directories where there is a config file
  'packages/*',
  'tests/*/vitest.config.{e2e,unit}.ts',
  // you can even run the same tests,
  // but with different configs in the same "vitest" process
  {
    test: {
      name: 'happy-dom',
      root: './shared_tests',
      environment: 'happy-dom',
      setupFiles: ['./setup.happy-dom.ts'],
    },
  },
  {
    test: {
      name: 'node',
      root: './shared_tests',
      environment: 'node',
      setupFiles: ['./setup.node.ts'],
    },
  },
])
```

## Command Line Interface

In a project where Vitest is installed, you can use the `vitest` binary in your npm scripts, or run it directly with `npx vitest`. Here are the default npm scripts in a scaffolded Vitest project:

<!-- prettier-ignore -->
```json
{
  "scripts": {
    "test": "vitest",
    "coverage": "vitest run --coverage"
  }
}
```

To run tests once without watching for file changes, use `vitest run`.
You can specify additional CLI options like `--port` or `--https`. For a full list of CLI options, run `npx vitest --help` in your project.

Learn more about the [Command Line Interface](/guide/cli)

## IDE Integrations

We also provided a official extension for Visual Studio Code to enhance your testing experience with Vitest.

[Install from VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=vitest.explorer)

Learn more about [IDE Integrations](/guide/ide)

## Examples

| 示例                    | 源代码                                                                                  | 演练场                                                                                                                              |
| ----------------------- | --------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `basic`                 | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/basic)                 | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/basic?initialPath=__vitest__)                 |
| `graphql`               | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/graphql)               | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/graphql?initialPath=__vitest__)               |
| `lit`                   | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/lit)                   | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/lit?initialPath=__vitest__)                   |
| `mocks`                 | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/mocks)                 | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/mocks?initialPath=__vitest__)                 |
| `nextjs`                | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/nextjs)                | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/nextjs?initialPath=__vitest__)                |
| `puppeteer`             | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/puppeteer)             |                                                                                                                                     |
| `react-enzyme`          | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/react-enzyme)          | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/react-enzyme?initialPath=__vitest__)          |
| `react-mui`             | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/react-mui)             | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/react-mui?initialPath=__vitest__)             |
| `react-storybook`       | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/react-storybook)       | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/react-storybook?initialPath=__vitest__)       |
| `react-testing-lib-msw` | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/react-testing-lib-msw) | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/react-testing-lib-msw?initialPath=__vitest__) |
| `react-testing-lib`     | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/react-testing-lib)     | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/react-testing-lib?initialPath=__vitest__)     |
| `react`                 | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/react)                 | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/react?initialPath=__vitest__)                 |
| `ruby`                  | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/ruby)                  | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/ruby?initialPath=__vitest__)                  |
| `solid`                 | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/solid)                 | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/solid?initialPath=__vitest__)                 |
| `svelte`                | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/svelte)                | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/svelte?initialPath=__vitest__)                |
| `vitesse`               | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/vitesse)               | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/vitesse?initialPath=__vitest__)               |
| `vue-jsx`               | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/vue-jsx)               | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/vue-jsx?initialPath=__vitest__)               |
| `vue`                   | [GitHub](https://github.com/vitest-dev/vitest/tree/main/examples/vue)                   | [Play Online](https://stackblitz.com/fork/github/vitest-dev/vitest/tree/main/examples/vue?initialPath=__vitest__)                   |

## Projects using Vitest

- [unocss](https://github.com/unocss/unocss)
- [unplugin-auto-import](https://github.com/antfu/unplugin-auto-import)
- [unplugin-vue-components](https://github.com/antfu/unplugin-vue-components)
- [vue](https://github.com/vuejs/core)
- [vite](https://github.com/vitejs/vite)
- [vitesse](https://github.com/antfu/vitesse)
- [vitesse-lite](https://github.com/antfu/vitesse-lite)
- [fluent-vue](https://github.com/demivan/fluent-vue)
- [vueuse](https://github.com/vueuse/vueuse)
- [milkdown](https://github.com/Saul-Mirone/milkdown)
- [gridjs-svelte](https://github.com/iamyuu/gridjs-svelte)
- [spring-easing](https://github.com/okikio/spring-easing)
- [bytemd](https://github.com/bytedance/bytemd)
- [faker](https://github.com/faker-js/faker)
- [million](https://github.com/aidenybai/million)
- [Vitamin](https://github.com/wtchnm/Vitamin)
- [neodrag](https://github.com/PuruVJ/neodrag)
- [svelte-multiselect](https://github.com/janosh/svelte-multiselect)
- [iconify](https://github.com/iconify/iconify)
- [tdesign-vue-next](https://github.com/Tencent/tdesign-vue-next)
- [cz-git](https://github.com/Zhengqbbb/cz-git)

<!--
For contributors:
We no longer accept new entries to this list a this moment.
Thanks for choosing Vitest!
-->

## Using Unreleased Commits

If you can't wait for a new release to test the latest features, you will need to clone the [vitest repo](https://github.com/vitest-dev/vitest) to your local machine and then build and link it yourself ([pnpm](https://pnpm.io/) is required):

```bash
git clone https://github.com/vitest-dev/vitest.git
cd vitest
pnpm install
cd packages/vitest
pnpm run build
pnpm link --global # you can use your preferred package manager for this step
```

Then go to the project where you are using Vitest and run `pnpm link --global vitest` (or the package manager that you used to link `vitest` globally).

## Community

If you have questions or need help, reach out to the community at [Discord](https://chat.vitest.dev) and [GitHub Discussions](https://github.com/vitest-dev/vitest/discussions).
