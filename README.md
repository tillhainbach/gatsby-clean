# Gatsby Clean

Start gatsby with a clean slight...

## Steps-to-reproduce

### 1. Get files

- pull the default starter and delete all src and pages except index and 404.js
- `npm uninstall gatsby-plugin-gatsby-cloud`and delete from config-file
- `npm uninstall

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
  @commitlint/{cli,config-conventional}

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

add commitlint config

```js
// commitlint.config.js
module.exports = { extends: ['@commitlint/config-conventional'] };
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

change source files to `.tsx`-files

```sh
for f in src/**/*; do mv "$f" "${f//.js/.tsx}"; done
```

### 3. Setup eslint

install eslint and plugins

```sh
npm install eslint \
  eslint-config-airbnb \
  eslint-config-airbnb-typescript \
  @typescript-eslint/eslint-plugin \
  @typescript-eslint/parser \
  eslint-plugin-import \
  eslint-plugin-jsx-a11y \
  eslint-plugin-react \
  eslint-plugin-react-hooks \
  eslint-config-prettier \
  eslint-webpack-plugin \
  gatsby-plugin-eslint \
  eslint-plugin-graphql \
  --save-dev
```

add eslint config file
[Typescript Eslint-Overrides](https://stackoverflow.com/questions/57107800/eslint-disable-extends-in-override)

```js
//.eslintrc.js
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: ['plugin:react/recommended', 'airbnb', 'prettier'],
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
  overrides: [
    {
      files: ['*.ts', '*.tsx'],
      extends: [
        'plugin:@typescript-eslint/recommended-requiring-type-checking',
        'airbnb-typescript',
        'prettier',
      ],
      parserOptions: {
        project: ['./tsconfig.json'], // Specify it only for TypeScript files
      },
    },
  ],
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
  stylelint-config-prettier

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

### 6. Setup testing

[Gatsby Unit Testing](https://www.gatsbyjs.com/docs/how-to/testing/unit-testing/)

install dependencies

```sh
npm install --save-dev \
  jest @types/jest \
  babel-jest \
  react-test-renderer @types/react-test-renderer  \
  babel-preset-gatsby \
  identity-obj-proxy \
  @testing-library/react \
  @testing-library/jest-dom \
  jest-styled-components \
  eslint-plugin-jest \
  eslint-plugin-jest-formatting \
  eslint-plugin-testing-library
```

add jest config

```js
// jest.config.js
module.exports = {
  transform: {
    '^.+\\.[jt]sx?$': '<rootDir>/jest-preprocess.js',
  },
  moduleNameMapper: {
    '.+\\.(css|styl|less|sass|scss)$': `identity-obj-proxy`,
    '.+\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$': `<rootDir>/__mocks__/file-mock.js`,
  },
  testPathIgnorePatterns: [`node_modules`, `\\.cache`, `<rootDir>.*/public`],
  transformIgnorePatterns: [`node_modules/(?!(gatsby)/)`],
  globals: {
    __PATH_PREFIX__: ``,
  },
  testURL: `http://localhost`,
  setupFiles: [`<rootDir>/loadershim.js`],
  setupFilesAfterEnv: ['<rootDir>/setup-test-env.js'],
};
```

```js
// jest-preprocess.js
const babelOptions = {
  presets: ['babel-preset-gatsby', '@babel/preset-typescript'],
};

// eslint-disable-next-line import/no-extraneous-dependencies
module.exports = require('babel-jest').createTransformer(babelOptions);
```

```js
// __mocks__/file-mock.js
module.exports = 'test-file-stub';
```

```js
// loadershim.js
global.___loader = {
  enqueue: jest.fn(),
};
```

```js
// setup-test-env.js
// eslint-disable-next-line import/no-extraneous-dependencies
import '@testing-library/jest-dom/extend-expect';
```

mock gatsby

```js
// __mocks__/gatsby.js
const React = require('react');
const gatsby = jest.requireActual('gatsby');
module.exports = {
  ...gatsby,
  graphql: jest.fn(),
  Link: jest.fn().mockImplementation(
    // these props are invalid for an `a` tag
    ({
      activeClassName,
      activeStyle,
      getProps,
      innerRef,
      partiallyActive,
      ref,
      replace,
      to,
      ...rest
    }) =>
      React.createElement('a', {
        ...rest,
        href: to,
      })
  ),
  StaticQuery: jest.fn(),
  useStaticQuery: jest.fn(),
};
```

add jest plugins to eslint config

```js
// .eslintrc.js
module.exports = {
  ...
  plugins: ['jest', 'jest-formatting', 'testing-library'],
  overrides: [
    {
      // enable eslint-plugin-testing-library rules or preset only for matching files!
      files: ['**/__tests__/**/*.[jt]s?(x)', '**/?(*.)+(spec|test).[jt]s?(x)'],
      extends: [
        'plugin:jest/all',
        'plugin:jest-formatting/strict',
        'plugin:testing-library/react',
      ],
    },
  ],
  ...
};
```

### Setup style-components

install deps

```sh
npm install --save styled-components
npm i --save-dev @types/styled-components
```

setup styled-components with typescript

```sh
mkdir src/types
touch src/types/styled.d.ts
mkdir src/styles
touch src/styles/Theme.ts
```

```ts
// styled.d.ts

// import original module declarations
import 'styled-components';

// and extend them!
declare module 'styled-components' {
  export interface DefaultTheme {}
}
```

```ts
// Theme.ts
import { DefaultTheme } from 'styled-components';

const Theme: DefaultTheme = {};

export default Theme;
```
