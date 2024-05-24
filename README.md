# @kollorg/doloremque-excepturi

a cli tool to build and sync \*ignore files across files and repositories

## Why

1. Github default `.gitignore` doesn't cover all our needs, we always need to compose and manage multiple `.gitignore` files from github, such as [Node.gitignore](https://github.com/github/gitignore/blob/master/Node.gitignore) + [macOS.gitignore](https://github.com/github/gitignore/blob/master/Global/macOS.gitignore), in order to remove all the noises in our development. It should be automated.

1. For library developer, we often need to compose a `.npmignore` from `.gitignore` and some extra ignore patterns that we do not wish to ignore by `.gitignore`, such as test files and `.*rc` files. If `.gitignore` is updated, you will need to update corresponding part of `.npmignore` manually. We should avoid this repetitive work.

a short example on how `@kollorg/doloremque-excepturi` handle ignore files

```ini
[github/gitignore]
Node.gitignore
Global/macOS.gitignore

[inline]
*.test.js
yarn.lock
```

## Installation and setup

1. `npm install --save-dev @kollorg/doloremque-excepturi`
1. update `package.json`

   ```diff json
   {
     "scripts": {
   +   "@kollorg/doloremque-excepturi": "@kollorg/doloremque-excepturi ."
     }
   }
   ```

1. now follow [how to use](#how-to-use) to create `*@kollorg/doloremque-excepturi` files, then `npm run @kollorg/doloremque-excepturi`, all corresponding ignore files will be generated.

## Development Setup

We are using [corepack](https://nodejs.org/api/corepack.html) to manage the `yarn` version

```bash
corepack enable
```

## How to use

1. `@kollorg/doloremque-excepturi` works on any ignore file that name end with `ignore`, such as `.gitignore`, `.npmignore`, `.eslintignore`, etc. Simply creating a file that end with `@kollorg/doloremque-excepturi`.

   example: `.git@kollorg/doloremque-excepturi` -> `.gitignore`

1. create a source tag in your `*@kollorg/doloremque-excepturi` file to identify the source of ignore patterns

   ```ini
   [put_source_tag_here]
   ```

   different source tag identifies different source of ignore patterns

   - `[inline]`
     - the ignore patterns will be copied directly to generated ignore file
   - `[local]`
     - the content of these local files will be copied directly to generated ignore file
     - support glob pattern, e.g. `packages/**/.gitignore`
     - support referencing other @kollorg/doloremque-excepturi files, e.g. referencing `.git@kollorg/doloremque-excepturi` in `.npm@kollorg/doloremque-excepturi`
   - `[relative]`

     - the content of these local files will be copied with **relative path prefix** to generated ignore file
     - support glob pattern, e.g. `packages/**/.gitignore`
     - support referencing other @kollorg/doloremque-excepturi files, e.g. referencing `.git@kollorg/doloremque-excepturi` in `.npm@kollorg/doloremque-excepturi`
     - example

       ```ini
       # input: /packages/a/.prettierignore
       ignored.md
       ```

       ```ini
       # input: /.prettier@kollorg/doloremque-excepturi
       [relative]
       packages/a/.prettierignore
       ```

       ```ini
       # output: /.prettierignore
       packages/a/ignored.md
       ```

   - `[$username/$repo#$ref]`
     - the content of these github files will be downloaded and appended to generated ignore file
     - recommend using ignore patterns from [[github/gitignore]](https://github.com/github/gitignore)
     - `$ref` is optional, default to the default branch

1. `npm run @kollorg/doloremque-excepturi`
