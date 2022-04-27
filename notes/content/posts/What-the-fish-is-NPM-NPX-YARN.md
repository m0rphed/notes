---
title: "🗿 npm, npx, yarn"
date: 2022-04-26
draft: true
description: "Npm, npx, yarn tips"
slug: "npm-npx-yarn"
tags: ["javascript", "web", "tldr"]
---

![yarn](./npx-demo.gif)

## npm - пакетный менеджер Node.JS

**npm** (менеджер пакетов Node) - это менеджер *зависимостей* / **пакетов**, который вы получаете из коробки при установке `Node.js`.

- у **локальных** *зависимостей* ссылки, находятся в `./node_modules/.bin/`
- у **глобальных** *зависимостей* ссылки, созданные из глобального каталога `bin/` (`/usr/local/bin` -- **в Linux** или в `%AppData%/npm` - в Windows)

- можно запустить локальный пакет с помощью:
    `$ ./node_modules/.bin/your-package`

- можно запустить с помощью команды: `npm run your-package`, добавив в `package.json`:

```json
{
    "name": "your-application",
    "version": "1.0.0",
    "scripts": { // scripts - запускаемые сценарии
        "your-package": "your-package"
        // "your-script-name": "your-package"
    }
}
```

### Install packages / установка пакетов

```bash
npm i <package>           # alias for - `npm install <package>`
npm i arcsecond@latest    # 'arcsecond' is a <package> name, specified tag: 'latest'
npm i arcsecond@4.0.0     # '4.0.0' - package specified version
npm i github:francisrstokes/arcsecond # user: francisrstokes, repository: arcsecond
npm i ./archive.tgz       # install package from tarball (.tgz) archive
npm i https://site.com/archive.tgz # install package from .tgz via HTTPS
```

### Update all packages, or selected packages / обновить все или некоторые пакеты

```bash
npm update               # update production packages
npm update [-g] PACKAGE  # -g - is for global package installation
npm update --dev         # update only developer dependencies
```

### Show, list packages / показать список пакетов

```bash
npm list # lists the installed versions of all dependencies in the project
npm list -g --depth 0 # lists the installed versions of all GLOBAL INSTALLED packages
npm view # lists THE LATEST versions of all dependencies
```

## npx - инструмент запуска сценариев

Чтобы запустить локально установленный пакет, все, что вам нужно - набрать:

`npm install -g npx`

### Возможности npx

- запуск пакета, который ранее не был установлен
- использование cli без глобальной (`-g`) установки
- возможность **запустить код прямо** из *(например)* **GitHub**
    пример запуска кода напрямую из *GitHub gist*:

    ```bash
    $ npx https://gist.github.com/Tynael/0861d31ea17796c9a5b4a0162eb3c1e8
    Need to install the following packages:
        gist:0861d31ea17796c9a5b4a0162eb3c1e8
    Ok to proceed? (y) y
    I was executed from a gist inside the terminal with npx!
    ```

### Пример использования npx: создание React.JS приложения

Показать псевдонимы (релизные тэги) последних версий пакета `create-react-app`:

```bash
$ npm v create-react-app # view latest versions of dependencies
create-react-app@5.0.1 | MIT | deps: 11 | versions: 103
...
dist-tags:
canary: 3.3.0-next.38  next: 5.1.0-next.14
latest: 5.0.1

published 2 weeks ago by iansu <ian@iansutherland.ca>
```

Создать шаблонное приложение в подкаталоге `./sandbox`

```bash
npx create-react-app@next sandbox # 'sandbox' - имя подкаталога 
cd sandbox  # перейти в подкаталог
npm start   # запуск приложение
```

Опции:

```bash
# Execute the binary from a given npm module:
npx module_name command_arguments

# In case a package has multiple binaries, specify the package name along with the binary:
npx --package package_name module_name

# Run a command if exists in the current path or in node_modules/.bin:
npx --no-install command_name command_arguments

# Execute the binary from a given npm module suppressing any output from npx itself:
npx --quiet module_name command_arguments
```

Ещё пару примеров:

```bash
npx degit svelte/t
```

## Yarn - альтернативный пакетный менеджер

![yarn](./yarn_logo.svg)

```bash
# Install a module globally:
yarn global add module_name

# Install all dependencies referenced in the package.json file (the install is optional):
yarn install

# Install a module and save it as a dependency to the package.json file (add --dev to save as a dev dependency):
yarn add module_name@version

# Uninstall a module and remove it from the package.json file:
yarn remove module_name

# Interactively create a package.json file:
yarn init

# Identify whether a module is a dependency and list other modules that depend upon it:
yarn why module_name

# yarn create is basically 'yarn's version of NPX'
yarn create react-app hello
```

| npm                                       | yarn                        |
|------------------------------------------ |-----------------------------|
| npm init                                  | yarn init                   |
| npm install                               | yarn                        |
| npm install gulp --save                   | yarn add gulp               |
| npm install gulp --save-dev --save-exact  | yarn add gulp --dev --exact |
| npm install -g gulp                       | yarn global add gulp        |
| npm update                                | yarn upgrade                |
| ./node_modules/.bin/gulp npm run gulp     | yarn run gulp               |
