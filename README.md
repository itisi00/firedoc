FireDoc
======

API Doc generator based on [YUIDoc](https://github.com/yui/yuidoc). We use this tool to document a large JavaScript game engine [Fireball](http://github.com/fireball-x/fireball) at [docs-zh.fireball-x.com/api](http://docs-zh.fireball-x.com/api/) and self-document firedoc itself at:

- English: http://fireball-x.github.io/firedoc/ or http://fireball-x.github.io/firedoc/en
- 中文: http://fireball-x.github.io/firedoc/zh

[![NPM](https://nodei.co/npm/firedoc.png?stars&downloads)](https://nodei.co/npm/firedoc/)
[![NPM](https://nodei.co/npm-dl/firedoc.png)](https://nodei.co/npm/firedoc/)

[![npm Version](https://img.shields.io/npm/v/firedoc.svg?style=flat-square)](https://www.npmjs.org/package/firedoc)
[![Dependency Status](https://img.shields.io/david/fireball-x/firedoc.svg?style=flat-square)](https://david-dm.org/fireball-x/firedoc)

Overview
--------

FireDoc is forked from YUIDoc and added some powerful enhanced features at [Syntax Guide](GUIDE.md).

> YUIDoc is a [Node.js](http://nodejs.org/) application used at build time to
> generate API documentation for JavaScript code. YUIDoc is comment-driven and supports a wide
> range of JavaScript coding styles. The output of YUIDoc is API documentation formatted as a
> set of HTML pages including information about methods, properties, custom events and
> inheritance for JavaScript objects.

> YUIDoc was originally written for the YUI Project; it uses YUI JavaScript and CSS in the
> generated files and it supports common YUI conventions like Custom Events. That said,
> it can be used easily and productively on non-YUI code.

Installation
------------

```sh
$ npm install -g firedoc
```

Usage
-------

```sh
$ firedoc source-path --lang
```

`--lang` option is required for multi-language description. Currently firedoc supports `--en` and `--zh` language option. Adding those option will generate docs for that specific language.

`--markdown` or `--md` is optional flag, which lets you get the markdown-based documentation to
directly host at Github or Bitbucket. [Firedoc](https://github.com/fireball-x/firedoc)'s github
hosted documentation is generated by itself.

Themes
------------

By default the firedoc provides the following 3 themes:

1. [default](https://github.com/fireball-x/firedoc/tree/master/themes/default) the default theme of firedoc
2. [default(zh)](https://github.com/fireball-x/firedoc/tree/master/themes/default_zh) the Chinese version of default
3. [markdown](https://github.com/fireball-x/firedoc/tree/master/themes/markdown) the default markdown theme for firedoc

#### Specify a theme locally

```
firedoc source-path --theme [path/to/your/theme]
```

#### Install a theme remotely

Firedoc supports that you can install a theme from [Github](https://github.com), [Bitbucket](https://bitbucket.org) or any other valid url.

```sh
$ firedoc-theme install https://github.com/fireball-x/firedoc-theme-notab
```

The above command will install the theme [firedoc-theme-notab](https://github.com/fireball-x/firedoc-theme-notab) into installed firedoc directory in your machine. Then you would be able to use the theme just like this:

```sh
$ firedoc source-path --theme firedoc-theme-notab
```

However if the remote url has a same basename with what you have installed in your machine, then you can specify a different name to install it:

```sh
$ firedoc-theme install url --name different-theme-name
```

Or just run:

```sh
$ firedoc-theme uninstall firedoc-theme-notab
```

Even you are able to clear all installed themes by the following commands:

```sh
$ firedoc-theme clear
```

Test
-------------

To run test
```sh
$ npm test
```

Documentation
-------------

* [User Guides](GUIDE.md)
* [Change Logs](https://github.com/fireball-x/firedoc/releases)
* [API Docs(markdown)](docs)
* [API Docs(html)](http://fireball-x.github.io/firedoc/)
* [中文文档](http://fireball-x.github.io/firedoc/zh/)

Contributing
------------

Please see the [CONTRIBUTING.md](CONTRIBUTING.md).

License
-------

This software is free to use under the Yahoo Inc. BSD license. See the [LICENSE file](LICENSE) for license text and copyright information.
