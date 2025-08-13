# Chapter 1 : Preparing for Full-Stack Development

## Setting up a project with Vite

1. Mở terminal trong VS Code.
2. Chạy: `npm create vite@5.0.0`
3. Chọn y để cài đặt.
4. Chọn framework React (nếu hỏi tên project, hủy và chạy lại kèm dấu . để dùng folder hiện tại).
5. Chọn variant JavaScript.
6. Chạy npm install để cài dependencies.

## Setting up ESLint and Prettier to enforce best practices and code style

### Installing the necessary dependencies

Tóm gọn các package:

- **prettier**: Tự động format code theo style đã chọn.
- **eslint**: Phân tích code, bắt lỗi, enforce best practices.
- **eslint-config-react**: Bật các rule ESLint dành cho **React**.
- **eslint-config-prettier**: Tắt các rule style của ESLint để **Prettier** lo phần format.
- **eslint-plugin-jsx-a11y**: Kiểm tra **accessibility (a11y)** trong JSX (như alt text, aria-\*…).

### Configuring Prettier

Tóm gọn cấu hình Prettier:

1. Tạo file **`.prettierrc.json`** ở thư mục gốc.
2. Thêm config:

```json
{
  "trailingComma": "all",
  "tabWidth": 2,
  "printWidth": 80,
  "semi": false,
  "jsxSingleQuote": true,
  "singleQuote": true
}
```

3. Lưu lại → Prettier sẽ tự format code khi lưu file theo các quy tắc trên.

### Configuring the Prettier extension

1. Mở **Settings** (Windows/Linux: `File → Preferences → Settings`, macOS: `Code → Settings → Settings`).
2. Chọn tab **Workspace** để lưu config vào `.vscode/settings.json` trong project.
3. Tìm **editor format on save** → bật checkbox.
4. Tìm **editor default formatter** → chọn **Prettier - Code formatter**.
5. Test: mở `.prettierrc.json`, thêm khoảng trắng thừa rồi lưu → Prettier sẽ tự format lại theo config.

### Creating a Prettier ignore file

1. Tạo file **`.prettierignore`** ở thư mục gốc.
2. Thêm nội dung để bỏ qua code build:

```
dist/
```

> `node_modules/` đã được Prettier tự động bỏ qua.

### Configuring ESLint

1. Xóa file **`.eslintrc.cjs`** mặc định.
2. Tạo file **`.eslintrc.json`** với nội dung:

```json
{
  "root": true,
  "env": { "browser": true },
  "parserOptions": { "ecmaVersion": "latest", "sourceType": "module" },
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:react/jsx-runtime",
    "plugin:jsx-a11y/recommended",
    "prettier"
  ],
  "settings": { "react": { "version": "detect" } },
  "overrides": [{ "files": ["*.js", "*.jsx"] }]
}
```

3. Tạo file **`.eslintignore`**:

```
dist/
vite.config.js
```

> `node_modules/` được ESLint tự bỏ qua.

4. Chạy linter:

```bash
npx eslint src
```

→ Sẽ có lỗi do rule mới áp dụng.

5. Tự fix lỗi:

```bash
npx eslint src --fix
```

→ Chạy lại `npx eslint src` nếu không thấy output tức là sạch lỗi.

### Adding a new script to run our linter

1. Thêm script `lint` vào **package.json**:

```bash
npm pkg set scripts.lint="eslint src"
```

2. Chạy:

```bash
npm run lint
```

→ Hoạt động giống `npx eslint src`.

Tiếp theo: cấu hình **Husky** để tự chạy ESLint & Prettier trước khi commit code. 🚀

### Setting up Husky to make sure we commit proper code

Tóm gọn setup **[Husky](https://www.npmjs.com/package/husky)** + **[lint-staged](https://www.npmjs.com/package/lint-staged)** để auto check code trước khi commit:

1. Cài đặt dev dependencies:

```bash
npm install --save-dev husky@8.0.3 lint-staged@15.1.0
```

2. Thêm vào **`package.json`** sau **`devDependencies`**:

```json
"lint-staged": {
  "**/*.{js,jsx}": [
    "npx prettier --write",
    "npx eslint --fix"
  ]
}
```

3. Khởi tạo Git và commit ban đầu:

```bash
git init
git add package.json
git commit -m "chore: initial commit"
```

4. Thêm script prepare để cài Husky tự động khi clone project:

```bash
npm pkg set scripts.prepare="husky install"
```

5. Chạy prepare lần đầu:

```bash
npm run prepare
```

6. Thêm pre-commit hook chạy lint-staged:

```bash
npx husky add .husky/pre-commit "npx lint-staged"
```

7. Thêm và commit code:

```bash
git add .
git commit -m "chore: basic project setup"
```

✅ Kết quả: Mỗi lần commit, Husky sẽ gọi lint-staged → chạy **Prettier** + **ESLint** để format & fix lỗi trước khi code được commit.

### Setting up commitlint to enforce a standard for our commit messages

Tóm gọn setup **commitlint** để kiểm tra commit message:

1. Cài đặt:

```bash
npm install --save-dev @commitlint/cli@18.4.3 @commitlint/config-conventional@18.4.3
```

2. Tạo file **`.commitlintrc.json`**:

```json
{
  "extends": ["@commitlint/config-conventional"]
}
```

3. Thêm hook kiểm tra commit message với Husky:

```bash
npx husky add .husky/commit-msg 'npx commitlint --edit ${1}'
```

4. Test:

```bash
git add .
git commit -m "no type"            # ❌ bị chặn
git commit -m "wrong: type"         # ❌ bị chặn
git commit -m "chore: configure commitlint" # ✅ hợp lệ
```

**Cấu trúc commit hợp lệ** theo [Conventional Commits](https://www.conventionalcommits.org/):

```
type(scope): description
```

- **type** (bắt buộc):

  - `fix`: Sửa bug
  - `feat`: Thêm tính năng
  - `refactor`: Tái cấu trúc code
  - `build`: Thay đổi build/dependencies
  - `ci`: Thay đổi CI/CD config
  - `docs`: Chỉ sửa docs
  - `perf`: Tối ưu hiệu năng
  - `style`: Format code
  - `test`: Thêm / sửa test

- **scope** (tùy chọn): Dùng trong monorepo để chỉ module/app bị thay đổi.

- **description**: Mô tả ngắn gọn nội dung commit.
