[gulp](http://gulpjs.com)-markdown-to-json
==========================================

[![CircleCI Status][circleci-badge]][circleci]
[![Semistandard Style][semistandard-badge]][semistandard]

> Parse Markdown + YAML, compile Markdown to HTML, wrap it in JSON.

The neutral format of JSON opens new possibilities for your handcrafted content. Send it onward to other plugins such as [gulp-hb][hb]. When their powers combine, you get a static site generator! Pipe to [request][request] to send it to a [search index][algolia] or [import into a CMS][contentful]. Write a plugin to [tap into the stream][plugin] if you need a client library.

Table of Contents
-----------------

- [Install](#install)
- [Usage](#usage)
- [API](#api)
- [Contribute](#contribute)
- [License](#license)

Install
-------

```bash
npm install gulp-markdown-to-json --save-dev
```

### Dependencies

This plugin does not bundle any Markdown parser to keep your options open. BYOM!

Pick one of these or write a new parser for fun!

- [commonmark.js][commonmark.js] ← the best
- [markdown-it][markdown-it]
- [markdown-js][markdown-js]
- [marked][marked]
- [remark][remark]
- [remarkable][remarkable]

Install, configure, and pass a rendering method to this plugin with a source string as it’s first argument. Output goes straight into the JSON file’s `body` property. If your parser requires instantiation pass a context to call it with by defining `context` in the config object. If yet more hoop jumping is required, write a wrapper function such as this example for remark:

```js
function render (string) {
  const remark = require('remark');
  const html = require('remark-html');
  return remark().use(html).process(string).toString();
}
```

> **Note:**
> YAML frontmatter blocks are stripped and handled before Markdown rendering with [front-matter][front-matter]

Usage
-----

**`/gulpfile.js`**

```javascript
const gulp = require('gulp');
const markdownToJSON = require('gulp-markdown-to-json');
const marked = require('marked');

marked.setOptions({
  pedantic: true,
  smartypants: true
});

gulp.task('markdown', () => {
  gulp.src('./content/**/*.md')
    .pipe(markdownToJSON(marked))
    .pipe(gulp.dest('.'))
});
```

Transformed source files flow onward to the destination of your choice with directory structure preserved:

**`/blog/posts/bushwick-artisan.md`**

```md
---
slug: bushwick-artisan
title: Wes Anderson pop-up Bushwick artisan
layout: centered
---

## YOLO
Chia quinoa meh, you probably haven't heard of them sartorial Holowaychuk pickled post-ironic. Plaid ugh vegan, Sixpoint 8-bit sartorial artisan semiotics put a bird on it Mission bicycle rights Club-Mate vinyl.
```

**`/blog/posts/bushwick-artisan.json`**

```json
{
  "slug": "bushwick-artisan",
  "title": "Wes Anderson pop-up Bushwick artisan", 
  "layout": "centered",
  "body": "<h2 id="yolo">YOLO</h2>\n<p>Chia quinoa meh, you probably haven't heard of them sartorial Holowaychuk pickled post-ironic. Plaid ugh vegan, Sixpoint 8-bit sartorial artisan semiotics put a bird on it Mission bicycle rights Club-Mate vinyl.</p>"
}
```

### Consolidated Output

Gather Markdown files before piping with the [gulp-util buffer method][gulp-util] to combine output into a single JSON file. Directory structure is preserved and represented as nested JSON for iteration with [Handlebars.js][handlebars-iterate] and friends. This is handy for navigation and other global content.

The consolidated file is named **`content.json`** by default and optionally renamed.

```javascript
const gulp = require('gulp');
const gutil = require('gulp-util');
const markdownToJSON = require('gulp-markdown-to-json');
const marked = require('marked');

gulp.task('markdown', () => {
  gulp.src('./content/**/*.md')
    .pipe(gutil.buffer())
    .pipe(markdownToJSON(marked, 'blog.json'))
    .pipe(gulp.dest('.'))
});
```

**`blog.json`**

```json
{
  "blog": {
    "posts": {
      "bushwick-artisan": {
        "slug": "bushwick-artisan",
        "title": "Wes Anderson pop-up Bushwick artisan", 
        "layout": "centered",
        "body": "<h2 id="yolo">YOLO</h2>\n<p>Chia quinoa meh, you probably haven't heard of them sartorial Holowaychuk pickled post-ironic. Plaid ugh vegan, Sixpoint 8-bit sartorial artisan semiotics put a bird on it Mission bicycle rights Club-Mate vinyl.</p>"
      }
    }
  },
  "mission": {
    ...
  }
}
```

API
---

### `markdownToJSON((render: Function, filename?: String) | config: Object) => TransformStream`

`config`

- `render` `Function` accepts Markdown source string, returns an escaped HTML string. **Required**
- `context` `Object` to use when calling `render`
- `name` `String` to rename consolidated output file, if using. Default: `content.json`

Contribute
----------

Pull requests accepted!

License
-------

**[MIT](LICENSE)**  
Copyright &copy; 2016 Sparkart Group, Inc.

[circleci]: https://circleci.com/gh/SparkartGroupInc/gulp-markdown-to-json
[circleci-badge]: https://circleci.com/gh/SparkartGroupInc/gulp-markdown-to-json.png?style=shield&circle-token=8bf33da398b8ab296fe670c81b3fecbae1471e25

[semistandard]: https://github.com/Flet/semistandard
[semistandard-badge]: https://img.shields.io/badge/code%20style-semistandard-brightgreen.svg?style=flat

[marked]: https://github.com/chjj/marked
[markdown-js]: https://github.com/evilstreak/markdown-js
[remarkable]: https://github.com/jonschlinkert/remarkable
[markdown-it]: https://github.com/markdown-it/markdown-it
[remark]: https://github.com/wooorm/remark
[commonmark.js]: https://github.com/jgm/commonmark.js
[front-matter]: https://github.com/jxson/front-matter

[hb]: https://github.com/shannonmoeller/gulp-hb
[request]: https://github.com/request/request
[algolia]: https://www.algolia.com/
[contentful]: https://www.contentful.com
[plugin]: https://git.io/v6t5d

[gulp-util]: https://github.com/gulpjs/gulp-util#buffercb
[handlebars-iterate]: http://handlebarsjs.com/#iteration
