---
tags:
  - javascript
  - liquidjs
  - note
---
# Intro to Liquid
- LiquidJS is a simple, expressive and safe [Shopify](https://github.com/Shopify/liquid) / GitHub Pages compatible template engine in pure JavaScript. The purpose of this repo is to provide a standard Liquid implementation for the JavaScript community. Liquid is originally implemented in Ruby and used by GitHub Pages, Jekyll and Shopify, see [Differences with Shopify/liquid](https://liquidjs.com/tutorials/differences.html).
	- LiquidJS syntax is relatively simple. There’re 2 types of markups in LiquidJS:
		- **Tags**. A tag consists of a tag name and optional arguments wrapped between `{%` and `%}`.
		- **Outputs**. An output consists of a value and a list of filters, which is optional, wrapped between `{{` and `}}`.
- ## Outputs
	- Used to output variables and can be transformed by filters into HTML.
	- For example, this is how to insert the value of `username` into the input's value:
```html
<input type="text" name="user" value="{{username}}">
```
- We can transform the value with filters before output:
```html
{{ username | append: ", welcome to LiquidJS!" }}

// Chaining filters
{{ username | append: ", welcome to LiquidJS" | capitalize }}
```
- ## Tags
	- Independent tags that are typically implemented in shopify/liquid are listed below:

|Category|Purpose|Tags|
|---|---|---|
|Iteration|iterate over a collection|for, cycle, tablerow|
|Control Flow|control the execution branch of template rendering|if, unless, elsif, else, case, when|
|Variable|define and alter variables|assign, increment, decrement, capture, echo|
|File|include another template or extend a layout template|render, include, layout|
|Language|temporarily disable LiquidJS syntax|# (inline comment), raw, comment, liquid|

- # Options

[](https://github.com/harttle/liquidjs/edit/master/docs/source/tutorials/options.md "Improve this doc")

The [Liquid](https://liquidjs.com/api/classes/Liquid.html) constructor accepts a plain object as options to define the behavior of LiquidJS. All of these options are optional thus we can specify any of them, for example the `cache` option:

```javascript
const { Liquid } = require('liquidjs')
const engine = new Liquid({
    cache: true
})
```

> **API Document**
> 
> Following is an overview for all the options, for exact types and signatures please refer to [LiquidOptions | API](https://liquidjs.com/api/interfaces/LiquidOptions.html).

## [](https://liquidjs.com/tutorials/options.html#cache "cache")cache

**cache** is used to improve performance by caching previously parsed template structures, specially in cases when we’re repeatedly parse or render files.

It’s default to `false`. When setting to `true` a default LRU cache of size 1024 will be enabled. And certainly it can be a number which indicates the size of cache you want.

Additionally, it can also be a custom cache implementation. See [Caching](https://liquidjs.com/tutorials/caching.html) for details.

## [](https://liquidjs.com/tutorials/options.html#Partials-Layouts "Partials/Layouts")Partials/Layouts

**root** is used to specify template directories for LiquidJS to lookup and read template files. Can be a single string and an array of strings. See [Render Files](https://liquidjs.com/tutorials/render-file.html) for details.

**layouts** is used to specify template directories for LiquidJS to lookup files for `{% layout %}`. Same format as `root` and will default to `root` if not specified.

**partials** is used to specify template directories for LiquidJS to lookup files for `{% render %}` and `{% include %}`. Same format as `root` and will default to `root` if not specified.

**relativeReference** is set to `true` by default to allow relative filenames. Note that relatively referenced files are also need to be within corresponding root. For example you can reference another file like `{% render ../foo/bar %}` as long as `../foo/bar` is also within `partials` directory.

## [](https://liquidjs.com/tutorials/options.html#dynamicPartials "dynamicPartials")dynamicPartials

> Note: for historical reasons, it’s named dynamicPartials but it also works for layouts.

**dynamicPartials** indicates whether or not to treat filename arguments in [include](https://liquidjs.com/tags/include.html), [render](https://liquidjs.com/tags/render.html), [layout](https://liquidjs.com/tags/layout.html) tags as a variable. Defaults to `true`. For example, render the following snippet with scope `{ file: 'foo.html' }` will include the `foo.html`:

```liquid
{% include file %}
```

Setting `dynamicPartials: false`, LiquidJS will try to include the file named `file`, which is weird but allows simpler syntax if your template relations are static:

```liquid
{% liquid foo.html %}
```

> **Common Pitfall**
> 
> LiquidJS defaults this option to `true` to be compatible with shopify/liquid, but if you’re from [eleventy](https://github.com/11ty/eleventy) it’s set to `false` by default (see [Quoted Include Paths](https://www.11ty.dev/docs/languages/liquid/#quoted-include-paths)) which I believe is trying to be compatible with Jekyll.

## [](https://liquidjs.com/tutorials/options.html#Jekyll-include "Jekyll include")Jekyll include

v9.33.0

[jekyllInclude](https://liquidjs.com/api/interfaces/LiquidOptions.html#jekyllInclude) is used to enable Jekyll-like include syntax. Defaults to `false`, when set to `true`:

- Filename will be static: `dynamicPartials` now defaults to `false` (instead of `true`). And you can set `dynamicPartials` back to `true`.
- Use `=` instead of `:` to separate parameter key-values.
- Parameters are under `include` variable instead of current scope.

For example in the following template, `name.html` is not quoted, `header` and `"HEADER"` are separated by `=`, and the `header` parameter is referenced by `include.header`. More details please check out [include](https://liquidjs.com/tags/include.html).

```liquid
// entry template
{% include article.html header="HEADER" content="CONTENT" %}

// article.html
<article>
  <header>{{include.header}}</header>
  {{include.content}}
</article>
```

## [](https://liquidjs.com/tutorials/options.html#extname "extname")extname

**extname** defines the default extension name to be appended into filenames if the filename has no extension name. Defaults to `''` which means it’s disabled by default. By setting it to `.liquid`:

```liquid
{% render "foo" %}  there's no extname, adds `.liquid` and loads foo.liquid
{% render "foo.html" %}  there is an extname already, loads foo.html directly
```

> **Legacy Versions**
> 
> Before 2.0.1, `extname` is set to `.liquid` by default. To change that you need to set `extname: ''` explicitly. See [#41](https://github.com/harttle/liquidjs/issues/41) for details.

## [](https://liquidjs.com/tutorials/options.html#fs "fs")fs

**fs** is used to define a custom file system implementation which will be used by LiquidJS to lookup and read template files. See [Abstract File System](https://liquidjs.com/tutorials/render-file.html#Abstract-File-System) for details.

## [](https://liquidjs.com/tutorials/options.html#globals "globals")globals

**globals** is used to define global variables available to all templates even in cases of [render tag](https://liquidjs.com/tags/render.html). See [3185](https://github.com/harttle/liquidjs/issues/185) for details.

## [](https://liquidjs.com/tutorials/options.html#jsTruthy "jsTruthy")jsTruthy

**jsTruthy** is used to use standard JavaScript truthiness rather than the Shopify.

it defaults to false. For example, when set to true, a blank string would evaluate to false with jsTruthy. With Shopify’s truthiness, a blank string is true.

## [](https://liquidjs.com/tutorials/options.html#outputEscape "outputEscape")outputEscape

[outputEscape](https://liquidjs.com/api/interfaces/LiquidOptions.html#outputEscape) can be used to automatically escape output strings. It can be one of `"escape"`, `"json"`, or `(val: unknown) => string`, defaults to `undefined`.

- For untrusted output variables, set `outputEscape: "escape"` makes them be HTML escaped by default. You’ll need [raw](https://liquidjs.com/filters/raw.html) filter for direct output.
- `"json"` is useful when you’re using LiquidJS to create valid JSON files.
- It can even be a function which allows you to control what variables are output throughout LiquidJS. Please note the input can be any type other than string, e.g. an filter returned an non-string value.

## [](https://liquidjs.com/tutorials/options.html#Date "Date")Date

**timezoneOffset** is used to specify a different timezone to output dates, your local timezone will be used if not specified. For example, set `timezoneOffset: 0` to output all dates in UTC/GMT 00:00.

**preserveTimezones** is a boolean effects only literal timestamps. When set to `true`, all literal timestamps will remain the same when output. This is a parser option, so Date objects passed to LiquidJS as data will not be affected. Note that `preserveTimezones` has a higher priority than `timezoneOffset`.

**dateFormat** is used to specify a default format to output dates. `%A, %B %-e, %Y at %-l:%M %P %z` will be used if not specified. For example, set `dateFormat: %Y-%m-%dT%H:%M:%S:%LZ` to output all dates in [JavaScript Date.toJson()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toJSON) format.

## [](https://liquidjs.com/tutorials/options.html#Trimming "Trimming")Trimming

**greedy**, **trimOutputLeft**, **trimOutputRight**, **trimTagLeft**, **trimTagRight** options are used to eliminate extra newlines and indents in templates around Liquid Constructs. See [Whitespace Control](https://liquidjs.com/tutorials/whitespace-control.html) for details.

## [](https://liquidjs.com/tutorials/options.html#Delimiter "Delimiter")Delimiter

**outputDelimiterLeft**, **outputDelimiterRight**, **tagDelimiterLeft**, **tagDelimiterRight** are used to customize the delimiters for LiquidJS [Tags and Filters](https://liquidjs.com/tutorials/intro-to-liquid.html). For example with `outputDelimiterLeft: <%=, outputDelimiterRight: %>` we are able to avoid conflicts with other languages:

```ejs
<%= username | append: ", welcome to LiquidJS!" %>
```

## [](https://liquidjs.com/tutorials/options.html#Strict "Strict")Strict

**strictFilters** is used to assert filter existence. If set to `false`, undefined filters will be skipped. Otherwise, undefined filters will cause a parse exception. Defaults to `false`.

**strictVariables** is used to assert variable existence. If set to `false`, undefined variables will be rendered as empty string. Otherwise, undefined variables will cause a render exception. Defaults to `false`.

**lenientIf** modifies the behavior of `strictVariables` to allow handling optional variables. If set to `true`, an undefined variable will _not_ cause an exception in the following two situations: a) it is the condition to an `if`, `elsif`, or `unless` tag; b) it occurs right before a `default` filter. Irrelevant if `strictVariables` is not set. Defaults to `false`.

**ownPropertyOnly** hides scope variables from prototypes, useful when you’re passing a not sanitized object into LiquidJS or need to hide prototypes from templates. Defaults to `true`.

> **Nonexistent Tags**
> 
> Nonexistent tags always throw errors during parsing and this behavior cannot be customized.

## [](https://liquidjs.com/tutorials/options.html#Parameter-Order "Parameter Order")Parameter Order

Parameter orders are ignored by default, for example `{% for i in (1..8) reversed limit:3 %}` will always perform `limit` before `reversed`, even if `reversed` occurs before `limit`. To make parameter order respected, set **orderedFilterParameters** to `true`. Its default value is `false`.