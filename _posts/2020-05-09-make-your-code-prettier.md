---
layout: post
title: Make your code prettier
image: aligned-gophers.jpg
categories: [code-format, angular]
---

[Prettier](https://prettier.io/) is an opinionated code formater. I use it to assure a uniform format throughout a codebase with zero effort from contributers.
In the end I don't really care if a codebase uses single or double quotes, spaces or tabs or whatever. But I _do_ care that it is the same everywhere in a project. With this mindset, I am very happy to accept whatever defaults some tool defines, becaue this once again means less setup effort. Here's how we can do this in an Angular project, where tslint is already setup:

1. Install prettier `npm install --save-dev --save-exact prettier`
2. Install a config that disables conflicting tslint rules: `npm install --save-dev tslint-config-prettier`
3. Install a plugin to run prettier with tslint: `npm install --save-dev tslint-plugin-prettier`
4. Create a .prettierignore file and add entries like `node_modules` or `package.json`, that you don't want to be affected by prettier
5. Optional: Create a .prettierrc file and add your desired code format, e.g.:
```json
{
  "printWidth": 120,
  "singleQuote": true,
  "useTabs": false,
  "tabWidth": 2,
  "semi": true,
  "bracketSpacing": true
}
```
6. Update the `extends` property of your tslint.json: `"extends": ["tslint:recommended", "tslint-plugin-prettier", "tslint-config-prettier"]`
7. Add the following entry to the `rules` section of tslint.json: `"prettier": true`
8. Run `npx tslint-config-prettier-check ./tslint.json` and remove all the conflicting entries in tslint.json
9. Done! You can now run `prettier **` to format your whole project. 

## Automatically running prettier
Obviously we don't want to manually run prettier every time we write code. We have several options to automate this task. One would be to install an IDE/editor plugin that formats code on save. But I don't trust peoples editor settings and therefore prefer to add a pre-commit hook that runs prettier. This is quite easy to do. Just run `npm install -D pretty-quick husky` and then update your `package.json` with this entry:
```json
{
  "husky": {
    "hooks": {
      "pre-commit": "pretty-quick --staged"
    }
  }
}
```

TADAAA 🎺 that's it, you will never have to worry about code format again 🥳
