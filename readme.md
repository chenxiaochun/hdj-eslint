## ESlint 打包配置

整体安装：

```
yarn add @rmb/hdj-eslint -D
```

单独安装：
```
yarn add @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-config-airbnb eslint-config-prettier eslint-import-resolver-alias eslint-import-resolver-webpack eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-prettier eslint-plugin-react prettier -D
```

在本地项目根目录新建两个配置文件：

1. `.eslintrc.js`

```js
import { defineConfig } from 'eslint/config'
import reactPlugin from 'eslint-plugin-react'
import tsEslintPlugin from '@typescript-eslint/eslint-plugin'
import prettierPlugin from 'eslint-plugin-prettier'
import tsParser from '@typescript-eslint/parser'
import importPlugin from 'eslint-plugin-import'
import jsxA11yPlugin from 'eslint-plugin-jsx-a11y'

export default defineConfig([
  {
    files: ['**/*.{js,jsx,ts,tsx}'],
    languageOptions: {
      parser: tsParser,
      parserOptions: {
        ecmaFeatures: {
          jsx: true,
        },
        ecmaVersion: 2018,
        sourceType: 'module',
      },
      globals: {
        Atomics: 'readonly',
        SharedArrayBuffer: 'readonly',
        browser: true,
      },
    },
    plugins: {
      react: reactPlugin,
      '@typescript-eslint': tsEslintPlugin,
      prettier: prettierPlugin,
      import: importPlugin,
      'jsx-a11y': jsxA11yPlugin,
    },
    settings: {
      react: {
        version: 'detect',
      },
      'import/resolver': {
        alias: {
          map: [
            ['src', './src'],
            ['tool', './src/tool'],
            ['store', './src/store'],
            ['component', './src/component'],
            ['@', './src'],
            ['@config/*', './config/*'],
          ],
          extensions: ['.ts', '.js', '.jsx', '.json', '.tsx', '.d.ts'],
        },
      },
    },
    rules: {
      ...reactPlugin.configs.recommended.rules,
      ...importPlugin.configs.recommended.rules,
      ...jsxA11yPlugin.configs.recommended.rules,
      'prettier/prettier': [
        'error',
        {
          eslintIntegration: true,
          stylelintIntegration: true,
          printWidth: 120,
          useTabs: false,
          tabWidth: 2,
          singleQuote: true,
          semi: false,
          trailingComma: 'all',
          jsxBracketSameLine: false,
          endOfLine: 'auto',
        },
      ],
      'import/extensions': [
        'error',
        {
          js: 'never',
          jsx: 'never',
          ts: 'never',
          tsx: 'never',
          json: 'always',
        },
      ],
      'import/prefer-default-export': 0,
      'import/order': ['error', { 'newlines-between': 'always' }],
      'import/no-extraneous-dependencies': ['error', { devDependencies: true }],
      'react/jsx-filename-extension': [1, { extensions: ['.jsx', '.tsx'] }],
      'react/jsx-props-no-spreading': 0,
      'react/function-component-definition': [
        2,
        {
          namedComponents: ['function-expression', 'function-declaration', 'arrow-function'],
          unnamedComponents: ['arrow-function'],
        },
      ],
      'jsx-a11y/label-has-for': 0,
      'jsx-a11y/label-has-associated-control': 0,
      'global-require': 0,
      'no-use-before-define': 'off',
      '@typescript-eslint/no-use-before-define': ['error'],
      'react/prop-types': 0,
      'react/react-in-jsx-scope': 0,
      '@typescript-eslint/consistent-type-imports': 'error',
    },
  },
])

```

2. `prettier.config.js`

```
module.exports = {
  semi: false,
  useTabs: false,
  singleQuote: true,
  trailingComma: 'all',
  jsxBracketSameLine: false,
}
```

3. `stylelint.config.js`

```
yarn add stylelint-config-property-sort-order-smacss stylelint-config-recommended-less stylelint-less stylelint-prettier stylelint-order stylelint-scss postcss-less -D
```

```
import order from 'css-property-sort-order-smacss'
import flattenDeep from 'lodash/flattenDeep.js'
import map from 'lodash/map.js'

const group = map(order, (v, groupName) => ({
  groupName,
  emptyLineBefore: 'never',
  // noEmptyLineBetween: true,
  properties: flattenDeep(v),
}))

export default {
  plugins: ['stylelint-prettier'],
  extends: ['stylelint-config-recommended', 'stylelint-config-property-sort-order-smacss'],
  customSyntax: 'postcss-less',
  rules: {
    'prettier/prettier': [true, { singleQuote: false }],
    'selector-pseudo-class-no-unknown': [
      true,
      {
        ignorePseudoClasses: ['global'],
      },
    ],
    'block-no-empty': true,
    'order/properties-order': group,
    'selector-type-no-unknown': [
      true,
      {
        ignoreTypes: ['ls-layout'],
      },
    ],
    'font-family-no-missing-generic-family-keyword': null,
    // 'at-rule-no-unknown': false,
  },
}
```
