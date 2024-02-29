
# Cấu hình cơ bản Eslint + Prettier + Husky + Lint Staged + Commit Lint để bắt đầu dự án</h1>

## I. Cấu hình Eslint

### 1. Cài đặt:

```bash
yarn add --dev prettier eslint-plugin-prettier eslint-config-prettier @typescript-eslint eslint-plugin
```
```eslint-plugin-prettier```: Plugin dùng để chạy Prettier dưới dạng các quy tắc (rule) của ESLint.
```eslint-config-prettier```: Để tránh conflict Eslint với Prettier, plugin này giúp tắt tất cả các quy tắc không cần thiết hoặc có thể xung đột.

### 2. Cấu hình mở rộng cho file ```.eslintrc.json```, ví dụ:
```json
{
  "extends": [
    "next/core-web-vitals",
    "plugin:@typescript-eslint/recommended",
    "eslint-config-prettier"
  ],
  "parser": "@typescript-eslint/parser",
  "plugins": ["prettier", "@typescript-eslint"],
  "rules": {
    "react/prop-types": "error",
    "react-hooks/rules-of-hooks": "error",
    "eol-last": ["error", "always"],
    // "quotes": ["error", "prefer-single"],
    // "jsx-quotes": ["error", "prefer-single"],
    "@typescript-eslint/no-var-requires": 0,
    "@typescript-eslint/no-non-null-assertion": "off",
    "semi": ["error", "never"],
    "max-lines": ["error", 500],
    "comma-dangle": ["error", "always-multiline"],
    "no-unused-vars": "off",
    "@typescript-eslint/no-unused-vars": "error",
    "no-process-env": "off",
    "react-hooks/exhaustive-deps": "off",
    "@next/next/no-img-element": "off",
    "import/no-anonymous-default-export": [
      "error",
      {
        "allowArray": false,
        "allowArrowFunction": false,
        "allowAnonymousClass": false,
        "allowAnonymousFunction": false,
        "allowCallExpression": true, // The true value here is for backward compatibility
        "allowLiteral": false,
        "allowObject": true
      }
    ],
    "prettier/prettier": [
      "error",
      {
        "printWidth": 80,
        "semi": false,
        "no-mixed-spaces-and-tabs": ["error", "smart-tabs"],
        // "no-unused-vars": ["warn"],
        "singleQuote": true,
        "jsxSingleQuote": true,
        "endOfLine": "auto",
        "trailingComma": "all"
      }
    ]
  },
  "ignorePatterns": ["vfs_fonts.ts"]
}

```

## II. Cài đặt Husky

### 1. Cài đặt:

```bash
yarn add --dev husky
```
```bash
yarn husky install
```
Sau khi install husky sẽ tạo ra folder ```.husky```.

Để husky có thể tự động install khi chay yarn, thêm vào script của ```package.json```:
```json
{
    "dev": "yarn lint-fix && next dev -p 3000",
    "start": "yarn lint-fix && next start -p 3000",
    "build": "yarn lint-fix && next build",
    "build:stag": "yarn lint-fix && next build",
    "build:prod": "yarn lint-fix && next build",
    "lint": "next lint",
    "postinstall": "husky install",
    "lint-fix": "next lint --dir . --fix"
}
```

## III. Cài đặt Lint Staged

### 1. Cài đặt:

```bash
yarn add --dev lint-staged
```
### 2. Tạo file ```.lintstagedrc.js``` trong thư mục gốc để setup các nhiệm vụ, ví dụ:
```js
const path = require('path')

const buildEslintCommand = (filenames) =>
  `next lint --fix --file ${filenames
    .map((f) => path.relative(process.cwd(), f))
    .join(' --file ')}`

module.exports = {
  '*.{js,jsx,ts,tsx}': [buildEslintCommand],
}
```

### 3. Trong folder ```.husky``` tạo file ```pre-commit``` để cài đặt chạy lint-staged.
```
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"
yarn lint-staged

```

## IV. Cài đặt Commit Lint

### 1. Cài đặt
```bash
yarn add --dev @commitlint/config-conventional @commitlint/cli
```

### 2. Tạo file ```.commitlintrc.js``` ngoài thư mục gốc để cấu hình các quy ước:
```js
module.exports = { extends: ['@commitlint/config-conventional'] };
```

### 3. Trong folder ```.husky``` tạo file ```commit-msg``` để cài đặt chạy commitlint:
```
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx --no-install commitlint --edit "$1"
```

### 4. Commit sẽ theo quy ước: \<type>: \<subject>

**Type** sẽ theo các định nghĩa bên dưới:

```build```: Changes that affect the build system or external dependencies (example scopes: gulp, broccoli, npm).<br>
```ci```: Changes to our CI configuration files and scripts (example scopes: Gitlab CI, Circle, BrowserStack, SauceLabs).<br>
```chore```: add something without touching production code (Eg: update npm dependencies).<br>
```docs```: Documentation only changes.<br>
```feat```: A new feature.<br>
```fix```: A bug fix.<br>
```perf```: A code change that improves performance.<br>
```refactor```: A code change that neither fixes a bug nor adds a feature.<br>
```revert```: Reverts a previous commit.<br>
```style```: Changes that do not affect the meaning of the code (Eg: adding white-space, formatting, missing semi-colons, etc).<br>
```test```: Adding missing tests or correcting existing tests.


**Subject** là nội dung commit.


## Getting Started

First, run the development server:

```bash
yarn
# and
yarn dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

You can start editing the page by modifying `app/page.tsx`. The page auto-updates as you edit the file.

This project uses [`next/font`](https://nextjs.org/docs/basic-features/font-optimization) to automatically optimize and load Inter, a custom Google Font.

## Learn More

To learn more about Next.js, take a look at the following resources:

- [Next.js Documentation](https://nextjs.org/docs) - learn about Next.js features and API.
- [Learn Next.js](https://nextjs.org/learn) - an interactive Next.js tutorial.

You can check out [the Next.js GitHub repository](https://github.com/vercel/next.js/) - your feedback and contributions are welcome!

## Deploy on Vercel

The easiest way to deploy your Next.js app is to use the [Vercel Platform](https://vercel.com/new?utm_medium=default-template&filter=next.js&utm_source=create-next-app&utm_campaign=create-next-app-readme) from the creators of Next.js.

Check out our [Next.js deployment documentation](https://nextjs.org/docs/deployment) for more details.
