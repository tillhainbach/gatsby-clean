# Gatsby Clean

Start gatsby with a clean slight...

## Steps-to-reproduce

### 1. Get files

- pull the default starter and delete all src and pages except index and 404.js
- `npm uninstall gatsby-plugin-gatsby-cloud`and delete from config-file

update prettier config file

```json
{
  "singleQuote": true,
  "printWidth": 80,
  "proseWrap": "always"
}
```

### 2. Update dependencies

- `ncu -u && npm install && npm audit fix`

### Setup git hooks

install simple-git-hooks, commitlint, lint-staged

```sh
npm install -D simple-git-hooks \
  lint-staged \
  commitlint
```

add config to `package.json`

```json
// package.json
  "simple-git-hooks": {
    "pre-commit": "npx lint-staged",
    "commit-msg": "npx --no-install commitlint --edit $1"
  },
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": "eslint --cache --fix",
    "*.{css,js,jsx,tsx,sass,scss}": "stylelint",
    "*.{js,jsx,ts,tsx,css,md,json,yaml}": "prettier --write"
  }
```

### 2. Install Typescript

Resources:
[Gatsby & Typescript](https://www.gatsbyjs.com/docs/how-to/custom-configuration/typescript/)
[Gatsby Typescript Plugin](https://www.gatsbyjs.com/plugins/gatsby-plugin-typescript/)

install typescript

```sh
  npm install -D typescript
```

add typescript config file

```json
//.tsconfig.json
{
  "compilerOptions": {
    "target": "esnext",
    "module": "esnext",
    "lib": ["dom", "es2017"],
    "jsx": "react",
    "strict": true,
    "esModuleInterop": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "noEmit": true,
    "skipLibCheck": true,
    "moduleResolution": "node"
  },
  "include": ["./src/**/*"]
}
```

add typescript plugin to gatsby config plugins

```js
// gatsby-config.js
{
  resolve: `gatsby-plugin-typescript`,
  options: {
    isTSX: true, // defaults to false
    jsxPragma: `jsx`, // defaults to "React"
    allExtensions: true, // defaults to false
  },
}
```

add type-check script to package.json

```json
// package.json
{
  "scripts": {
    "type-check": "tsc --noEmit",
    "type-check:watch": "npm run type-check -- --watch"
  }
}
```

### 3. Setup eslint

install eslint and plugins

```sh
npm install eslint \
  eslint-config-airbnb-typescript \
  @typescript-eslint/eslint-plugin \
  @typescript-eslint/parser \
  eslint-plugin-import \
  eslint-plugin-jsx-a11y \
  eslint-plugin-react \
  eslint-plugin-react-hooks \
  eslint-config-prettier \
  --save-dev
```

add eslint config file

```js
//.eslintrc.js
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: ['plugin:react/recommended', 'airbnb-typescript', 'prettier'],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    project: ['./tsconfig.eslint.json'],
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 12,
    sourceType: 'module',
  },
  plugins: ['react', '@typescript-eslint'],
  rules: {
    // note you must disable the base rule as it can report incorrect errors
    'no-use-before-define': 'off',
    '@typescript-eslint/no-use-before-define': ['error'],
    'react/require-default-props': ['off'],
  },
};
```

### 4. Setup Stylelint

install stylelint and plugins /config

```sh
npm install -D stylelint \
  stylelint-scss \
  stylelint-order \
  stylelint-config-standard \
  stylelint-config-recommended \
  stylelint-processor-styled-components \
  stylelint-config-styled-components \
  stylelint-config-prettier \

```

add stylelint config file

```json
// .stylelintrc.json
{
  "processors": ["stylelint-processor-styled-components"],
  "extends": [
    "stylelint-config-recommended",
    "stylelint-config-styled-components",
    "stylelint-config-prettier"
  ],
  "plugins": ["stylelint-scss", "stylelint-order"],
  "rules": {
    "selector-max-id": 0,
    "comment-empty-line-before": ["always", { "except": ["first-nested"] }],
    "declaration-block-single-line-max-declarations": 1,
    "declaration-property-value-disallowed-list": { "/^border/": ["none"] },
    "max-nesting-depth": 2,
    "scss/dollar-variable-pattern": "^_?[a-z]+[\\w-]*$",
    "scss/at-extend-no-missing-placeholder": true,
    "order/order": [
      "declarations",
      { "type": "at-rule" },
      { "type": "at-rule", "hasBlock": true },
      "rules"
    ]
  }
}
```
