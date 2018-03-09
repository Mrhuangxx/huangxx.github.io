# hexo-renderer-nunjucks

[Nunjucks][] renderer plugin for [Hexo][].

[Hexo]: https://hexo.io
[Nunjucks]: https://mozilla.github.io/nunjucks/

[![npm Version][badge-npm]][package]
[![ISC License][badge-license]][license]
[![Dependencies Status][badge-david]][david]
[![devDependencies Status][badge-david-dev]][david]

[badge-david]: https://img.shields.io/david/morrisallison/hexo-renderer-nunjucks.svg?style=flat-square
[badge-david-dev]: https://img.shields.io/david/dev/morrisallison/hexo-renderer-nunjucks.svg?style=flat-square
[badge-license]: https://img.shields.io/badge/license-ISC-blue.svg?style=flat-square
[badge-npm]: https://img.shields.io/npm/v/hexo-renderer-nunjucks.svg?style=flat-square
[david]: https://david-dm.org/morrisallison/hexo-renderer-nunjucks
[license]: https://github.com/morrisallison/hexo-renderer-nunjucks/blob/master/LICENSE
[package]: https://www.npmjs.com/package/hexo-renderer-nunjucks

## Installation

```bash
$ npm install hexo-renderer-nunjucks --save
```

## Development

The plugin can be built by running:

```bash
$ npm install && npm run build
```

## Configuration

Nunjucks can be configured by editing the main `_config.yml` file using the `nunjucks` namespace.
Configuration options can be found in [Nunjucks API documentation](http://mozilla.github.io/nunjucks/api.html#configure).

  - *Note:* `autoescape` is `false` by default.

```yml
nunjucks:
  autoescape: true
```

## File Extensions

This plugin supports the following file extensions.
  * `.njk`
  * `.nunjucks`
  * `.j2` (Jinja 2)
